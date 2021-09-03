---
title: Copia de seguridad de los discos de máquina virtual de un dispositivo GPU de Azure Stack Edge Pro con PowerShell
description: Describe cómo hacer copias de seguridad de los datos de discos de máquina virtual que se ejecutan en el dispositivo de GPU de Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2021
ms.author: alkohli
ms.openlocfilehash: 93d12db01c71d078ab25cf0745d5ae661220153c
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113106588"
---
# <a name="back-up-vm-disks-on-azure-stack-edge-pro-gpu-via-azure-powershell"></a>Copia de seguridad de los discos de máquina virtual en la GPU de Azure Stack Edge Pro mediante Azure PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo crear copias de seguridad de los discos de máquina virtual en un dispositivo GPU de Azure Stack Edge Pro con Azure PowerShell.

> [!IMPORTANT]
> El uso de este procedimiento está concebido para máquinas virtuales que están detenidas. Para realizar copias de seguridad de máquinas virtuales en ejecución, se recomienda usar una herramienta de copia de seguridad de terceros.

## <a name="workflow"></a>Flujo de trabajo

En los pasos siguientes se resume el flujo de trabajo de alto nivel para hacer una copia de seguridad de un disco de máquina virtual en el dispositivo:

1. Pare la VM.
1. Tome una instantánea del disco de VM.
1. Copie la instantánea en una cuenta de almacenamiento local como VHD.
1. Cargue el VHD en un destino externo.

## <a name="prerequisites"></a>Requisitos previos

Antes de realizar una copia de seguridad de las máquinas virtuales, asegúrese de que:

- Tiene acceso a un cliente que se usará para conectarse al dispositivo.
    - El cliente ejecuta un [sistema operativo compatible](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
    - El cliente está configurado para conectarse a la instancia local de Azure Resource Manager del dispositivo según las instrucciones que se indican en [Conexión a Azure Resource Manager en el dispositivo](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="verify-connection-to-local-azure-resource-manager"></a>Comprobación de la conexión a Azure Resource Manager local

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]

## <a name="back-up-a-vm-disk"></a>Copia de seguridad de un disco de máquina virtual

### <a name="az"></a>[Az](#tab/az)

1. Obtenga una lista de las máquinas virtuales que se están ejecutando en el dispositivo. Identifique la máquina virtual que quiere detener y el grupo de recursos correspondiente.

    ```powershell
    Get-AzVM
    ```

    A continuación, se incluye una salida de ejemplo:

    ```powershell
    PS C:\Users\user> Get-AzVM
    
    ResourceGroupName            Name Location         VmSize OsType                NIC
    -----------------            ---- --------         ------ ------                ---
    MYASEAZRG                  myazvm dbelocal Standard_D1_v2  Linux           myaznic1
    MYASERG           myasewindowsvm2 dbelocal Standard_D1_v2  Linux myasewindowsvm2nic
    
    PS C:\Users\user> 
    ```

1. Configure algunos parámetros.

    ```powershell
    $ResourceGroupName = "<Resource group name>"
    $VmName = "<VM name>"
    ```   
1. Pare la VM.

    ```powershell
    Stop-AzVM –ResourceGroupName $ResourceGroupName -Name $VmName
    ```

    A continuación, se incluye una salida de ejemplo:

    ```powershell
    PS C:\Users\user> Stop-AzVM -ResourceGroupName myaserg -Name myasewindowsvm2      
    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
        
    OperationId : 8a2fa7ea-99d0-4f9f-b8ca-e37389cd8413
    Status      : Succeeded
    StartTime   : 6/28/2021 11:51:33 AM
    EndTime     : 6/28/2021 11:51:50 AM
    Error       :
    
    PS C:\Users\user>
    ```

    También puede detener la máquina virtual desde Azure Portal.
 

2. Tome una instantánea del disco de máquina virtual y guárdela en un grupo de recursos local. Puede usar este procedimiento tanto para los discos de datos como del sistema operativo.

   1. Obtenga la lista de los discos del dispositivo o de un grupo de recursos específico. Anote el nombre del disco del que se va a realizar la copia de seguridad.

        ```powershell
        $Disk = Get-AzDisk -ResourceGroupName $ResourceGroupName
        $Disk.Name
        ```   
        A continuación, se incluye una salida de ejemplo:

        ```output
        PS C:\Users\user> $Disk = Get-AzDisk -ResourceGroupName myaserg
        PS C:\Users\user> $Disk.Name
        myasewindowsvm2_disk1_2a066432056446669368969835d5e3b3
        myazdisk1
        myvmdisk2
        PS C:\Users\user>
        ```
   1. Cree un grupo de recursos local para que sirva como destino de la instantánea de VM. La ubicación se establece como `dbelocal`.

        ```powershell
        New-AzResourceGroup -ResourceGroupName <Resource group name> -Location dbelocal 
        ``` 

        ```output
        PS C:\Users\user> New-AzResourceGroup -ResourceGroupName myaseazrg1 -Location dbelocal
        
        ResourceGroupName : myaseazrg1
        Location          : dbelocal
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/.../resourceGroups/myaseazrg1
        
        PS C:\Users\user>
        ```

   1. Configure algunos parámetros.

      ```powershell
      $DiskResourceGroup = <Disk resource group>
      $DiskName = <Disk name>
      $SnapshotName = <Snapshot name>
      $DestinationRG = <Snapshot destination resource group>
      ```

   3. Establezca la configuración de la instantánea y tómela.

        ```powershell
        $Disk = Get-AzDisk -ResourceGroupName $DiskResourceGroup -DiskName $DiskName
        $SnapshotConfig = New-AzSnapshotConfig -SourceUri $Disk.Id -CreateOption Copy -Location 'dbelocal'
        $Snapshot = New-AzSnapshot -Snapshot $SnapshotConfig -SnapshotName $SnapshotName -ResourceGroupName $DestinationRG
        ```
        Compruebe que la instantánea se haya creado en el grupo de recursos de destino.

        ```powershell
        Get-AzSnapshot -ResourceGroupName $DestinationRG
        ```
        A continuación, se incluye una salida de ejemplo:

        ```output
        PS C:\Users\user> $DiskResourceGroup = "myaserg"
        PS C:\Users\user> $DiskName = "myazdisk1"
        PS C:\Users\user> $SnapshotName = "myasdisk1ss"
        PS C:\Users\user> $DestinationRG = "myaseazrg1"
        PS C:\Users\user> $Disk = Get-AzDisk -ResourceGroupName $DiskResourceGroup -DiskName $DiskName
        PS C:\Users\user> $SnapshotConfig = New-AzSnapshotConfig -SourceUri $Disk.Id -CreateOption Copy -Location 'dbelocal'
        PS C:\Users\user> $Snapshot=New-AzSnapshot -Snapshot $SnapshotConfig -SnapshotName $SnapshotName -ResourceGroupName $DestinationRG
        PS C:\Users\user> Get-AzSnapshot -ResourceGroupName $DestinationRG
                
        ResourceGroupName            : myaseazrg1
        ManagedBy                    :
        Sku                          : Microsoft.Azure.Management.Compute.Models.SnapshotSku
        TimeCreated                  : 6/28/2021 6:57:40 PM
        OsType                       :
        HyperVGeneration             :
        CreationData                 : Microsoft.Azure.Management.Compute.Models.CreationDat
                                       a
        DiskSizeGB                   : 10
        DiskSizeBytes                : 10737418240
        UniqueId                     : fbc1cfac-8bbb-44d8-8aa4-9e8811950fcc
        EncryptionSettingsCollection :
        ProvisioningState            : Succeeded
        Incremental                  : False
        Encryption                   : Microsoft.Azure.Management.Compute.Models.Encryption
        Id                           : /subscriptions/.../r
                                       esourceGroups/myaseazrg1/providers/Microsoft.Compute/
                                       snapshots/myasdisk1ss
        Name                         : myasdisk1ss
        Type                         : Microsoft.Compute/snapshots
        Location                     : DBELocal
        Tags                         : {}
                
        PS C:\Users\user>
        ```

### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

1. Obtenga una lista de las máquinas virtuales que se están ejecutando en el dispositivo. Identifique la máquina virtual que quiere detener.

    ```powershell
    Get-AzureRMVM
    ```

    A continuación, se incluye una salida de ejemplo:

    ```powershell
    PS C:\Users\user> Get-AzureRMVM

    ResourceGroupName    Name         Location  VmSize         OsType   NIC         ProvisioningState Zone
    -----------------    ----         --------  ------         ------   ---         ----------------- ----
    MYASERG           myasewindowsvm1 dbelocal Standard_D1_v2  Linux myasewindowsvm1nic  Succeeded
    MYASERG1            myaselinuxvm1 dbelocal Standard_D1_v2  Linux   myaselinuxvm1nic  Succeeded
    MYASERG2             myasetestvm1 dbelocal Standard_D1_v2  Linux    myasetestvm1nic  Succeeded

    PS C:\Users\user>
    ```
    
1. Pare la VM.

    ```powershell
    Stop-AzureRMVM –ResourceGroupName <Resource group name> -Name <VM name>
    ```

    A continuación, se incluye una salida de ejemplo:

    ```powershell
    PS C:\Users\user> Stop-AzureRMVM -ResourceGroupName myaserg2 -Name myasetestvm1

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    
    OperationId :
    Status      : Succeeded
    StartTime   : 4/9/2021 8:43:47 AM
    EndTime     : 4/9/2021 8:44:27 AM
    Error       :
    
    PS C:\Users\user>
    ```
    También puede detener la máquina virtual desde Azure Portal.
 

2. Tome una instantánea del disco de máquina virtual y guárdela en un grupo de recursos local. Puede usar este procedimiento tanto para los discos de datos como del sistema operativo.

   1. Obtenga la lista de los discos del dispositivo o de un grupo de recursos específico. Anote el nombre del disco del que se va a realizar la copia de seguridad.

        ```powershell
        Get-AzureRMDisk -ResourceGroupName <Resource group name>
        ```   
        A continuación, se incluye una salida de ejemplo:

        ```powershell
        PS C:\Users\user> $Disk = Get-AzureRMDisk -ResourceGroupName myaserg2
        PS C:\Users\user> $Disk.Name
        myasetestdisk1
        myasetestvm1_disk1_0ed91809927f4023b7aceb6eeca51c05
        PS C:\Users\user>
        ```
   1. Cree un grupo de recursos local para que sirva como destino de la instantánea de VM.

        ```powershell
        PS C:\Users\user> New-AzureRmResourceGroup -ResourceGroupName myaserg3 -Location dbelocal
    
        ResourceGroupName : myaserg3
        Location          : dbelocal
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/.../resourceGroups/myaserg3
        
        PS C:\Users\user>
        ```

   1. Configure algunos parámetros.

      ```powershell
      $DiskResourceGroup = <Disk resource group>
      $DiskName = <Disk name>
      $SnapshotName = <Snapshot name>
      $DestinationRG = <Snapshot destination resource group>
      ```

   3. Establezca la configuración de la instantánea y tómela.

        ```powershell
        $Disk = Get-AzureRmDisk -ResourceGroupName $DiskResourceGroup -DiskName $DiskName
        $SnapshotConfig = New-AzureRmSnapshotConfig -SourceUri $Disk.Id -CreateOption Copy -Location 'dbelocal'
        $Snapshot = New-AzureRmSnapshot -Snapshot $SnapshotConfig -SnapshotName $SnapshotName -ResourceGroupName $DestinationRG
        ```
        Compruebe que la instantánea se haya creado en el grupo de recursos de destino.

        ```powershell
        Get-AzureRMSnapshot -ResourceGroupName $DestinationRG
        ```
        A continuación, se incluye una salida de ejemplo:

        ```powershell
        PS C:\Users\user> $DiskResourceGroup = "myaserg2"
        PS C:\Users\user> $DiskName = "myasetestdisk1"
        PS C:\Users\user> $SnapshotName = "myasetestdisk1_ss"
        PS C:\Users\user> $DestinationRG = "myaserg3"
        PS C:\Users\user> $Disk = Get-AzureRmDisk -ResourceGroupName $DiskResourceGroup -DiskName $DiskName
        PS C:\Users\user> $SnapshotConfig = New-AzureRmSnapshotConfig -SourceUri $Disk.Id -CreateOption Copy -Location 'dbelocal'
        PS C:\Users\user> $Snapshot=New-AzureRmSnapshot -Snapshot $SnapshotConfig -SnapshotName $SnapshotName -ResourceGroupName $DestinationRG
        PS C:\Users\user> Get-AzureRMSnapshot -ResourceGroupName $DestinationRG
        
        ResourceGroupName  : myaserg3
        ManagedBy          :
        Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
        TimeCreated        : 4/9/2021 4:23:21 PM
        OsType             :
        CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
        DiskSizeGB         : 10
        EncryptionSettings :
        ProvisioningState  : Succeeded
        Id                 : /subscriptions/.../resourceGroups/myaserg3/providers/Microsoft.Compute/snapshots/myasetestdisk1_ss
        Name               : myasetestdisk1_ss
        Type               : Microsoft.Compute/snapshots
        Location           : DBELocal
        Tags               : {}
        
        PS C:\Users\user>
        ```
---

## <a name="copy-the-snapshot-into-a-local-storage-account"></a>Copie la instantánea en una cuenta de almacenamiento local.

Copie las instantáneas en una cuenta de almacenamiento local del dispositivo. 

### <a name="az"></a>[Az](#tab/az)

1. Configure algunos parámetros. 

    ```powershell
    $StorageAccountRG = <Local storage account resource group>
    $StorageAccountName = <Storage account name>
    $StorageEndpointSuffix = <Connection string in format: DeviceName.DnsDomain.com>
    $DestStorageContainer = <Destination storage container>
    $DestFileName = <Blob file name> 
    ```

1. Cree una cuenta de almacenamiento local en el dispositivo. 

    ```powershell
    New-AzStorageAccount -Name  $StorageAccountName -ResourceGroupName $StorageAccountRG -Location DBELocal -SkuName Standard_LRS
    ```

    A continuación, se incluye una salida de ejemplo: 

    ```output
    PS C:\Users\user> New-AzStorageAccount -Name  $StorageAccountName -ResourceGroupName $StorageAccountRG -Location DBELocal -SkuName Standard_LRS
    
    StorageAccountName ResourceGroupName PrimaryLocation SkuName      Kind    AccessTier
    ------------------ ----------------- --------------- -------      ----    ----------
    myaseazsa1         myaseazrg2        DBELocal        Standard_LRS Storage
    PS C:\Users\user>
    ```

1. Cree un contenedor en la cuenta de almacenamiento local que ha creado. 

    ```powershell
    $keys = Get-AzStorageAccountKey -ResourceGroupName $StorageAccountRG -Name $StorageAccountName
    $keyValue = $keys[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue -Protocol Http -Endpoint $StorageEndpointSuffix;
    $container = New-AzStorageContainer -Name $DestStorageContainer -Context $storageContext -Permission Container -ErrorAction Ignore;    
    ```
    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\Users\user> $StorageAccountRG = "myaseazrg2"
    PS C:\Users\user> $StorageAccountName = "myaseazsa1"
    PS C:\Users\user> $StorageEndpointSuffix = "myasegpu.wdshcsso.com"
    PS C:\Users\user> $DestStorageContainer = "testcont1"
    PS C:\Users\user> $DestFileName = "testfile1"

    PS C:\Users\user> $keys = Get-AzStorageAccountKey -ResourceGroupName $StorageAccountRG -Name $StorageAccountName
    PS C:\Users\user> $keyValue = $keys[0].Value
    PS C:\Users\user> $storageContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue -Protocol Http -Endpoint $StorageEndpointSuffix;                                                                                   
    PS C:\Users\user> $storagecontext
    
    StorageAccountName  : myaseazsa1
    BlobEndPoint        : http://myaseazsa1.blob.myasegpu.wdshcsso.com/
    TableEndPoint       : http://myaseazsa1.table.myasegpu.wdshcsso.com/
    QueueEndPoint       : http://myaseazsa1.queue.myasegpu.wdshcsso.com/
    FileEndPoint        : http://myaseazsa1.file.myasegpu.wdshcsso.com/
    Context             : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
    Name                :
    StorageAccount      : BlobEndpoint=http://myaseazsa1.blob.myasegpu.wdshcsso.com/;Que
                          ueEndpoint=http://myaseazsa1.queue.myasegpu.wdshcsso.com/;Tabl
                          eEndpoint=http://myaseazsa1.table.myasegpu.wdshcsso.com/;FileE
                          ndpoint=http://myaseazsa1.file.myasegpu.wdshcsso.com/;AccountN
                          ame=myaseazsa1;AccountKey=[key hidden]
    TableStorageAccount : BlobEndpoint=http://myaseazsa1.blob.myasegpu.wdshcsso.com/;Que
                          ueEndpoint=http://myaseazsa1.queue.myasegpu.wdshcsso.com/;Tabl
                          eEndpoint=http://myaseazsa1.table.myasegpu.wdshcsso.com/;FileE
                          ndpoint=http://myaseazsa1.file.myasegpu.wdshcsso.com/;DefaultE
                          ndpointsProtocol=https;AccountName=myaseazsa1;AccountKey=[key
                          hidden]
    Track2OauthToken    :
    EndPointSuffix      : myasegpu.wdshcsso.com/
    ConnectionString    : BlobEndpoint=http://myaseazsa1.blob.myasegpu.wdshcsso.com/;Que
                          ueEndpoint=http://myaseazsa1.queue.myasegpu.wdshcsso.com/;Tabl
                          eEndpoint=http://myaseazsa1.table.myasegpu.wdshcsso.com/;FileE
                          ndpoint=http://myaseazsa1.file.myasegpu.wdshcsso.com/;AccountN
                          ame=myaseazsa1;AccountKey=itOn5Awjh3hnoGKL7EDQ681zhIKG/szCt05Z
                          IWAxP/T22gwEXb5l0sKjI833Hqpc0MsBiSH2rM6NuuwnJyEO1Q==
    ExtendedProperties  : {}
    
    
    
    PS C:\Users\user> $container = New-AzStorageContainer -Name $DestStorageContainer -Context $storageContext -Permission Container -ErrorAction Ignore;
    PS C:\Users\user> $container
    Blob End Point: http://myaseazsa1.blob.myasegpu.wdshcsso.com/
    
    Name                 PublicAccess         LastModified
    ----                 ------------         ------------
    testcont1           Container            6/28/2021 2:46:03 PM +00:00
    
    PS C:\Users\user>
    ```    

    También puede usar el Explorador de Azure Storage para [crear una cuenta de almacenamiento local](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#create-a-storage-account) y, a continuación, [crear un contenedor en la cuenta de almacenamiento local](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload) en el dispositivo. 



1. Descargue la instantánea en la cuenta de almacenamiento local.

   ```powershell
   $sassnapshot = Grant-AzSnapshotAccess -ResourceGroupName $DestinationRG -SnapshotName $SnapshotName -Access 'Read' -DurationInSecond 3600
   $destContext = New-AzStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $keyValue 
   Start-AzStorageBlobCopy -AbsoluteUri $sassnapshot.AccessSAS -DestContainer $DestStorageContainer -DestContext $destContext -DestBlob $DestFileName
   ```

    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\Users\user> $sassnapshot
    
    AccessSAS : https://md-2.blob.myasegpu.wdshcsso.com/22615edc48654bb8b77e383d3a7649ac
    /abcd.vhd?sv=2017-04-17&sr=b&si=43ca8395-6942-496b-92d7-f0d6dc68ab63&sk=system-1&sig
    =K%2Bc34uq7%2BLcTetG%2Bj9loOH440e03vDkD24Ug0Gf%2Bex8%3D
       
    PS C:\Users\user> $destContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue
    PS C:\Users\user> $destContext
    
    
    StorageAccountName  : myaseazsa1
    BlobEndPoint        : https://myaseazsa1.blob.myasegpu.wdshcsso.com/
    TableEndPoint       : https://myaseazsa1.table.myasegpu.wdshcsso.com/
    QueueEndPoint       : https://myaseazsa1.queue.myasegpu.wdshcsso.com/
    FileEndPoint        : https://myaseazsa1.file.myasegpu.wdshcsso.com/
    Context             : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
    Name                :
    StorageAccount      : BlobEndpoint=https://myaseazsa1.blob.myasegpu.wdshcsso.com/;Qu
                          eueEndpoint=https://myaseazsa1.queue.myasegpu.wdshcsso.com/;Ta
                          bleEndpoint=https://myaseazsa1.table.myasegpu.wdshcsso.com/;Fi
                          leEndpoint=https://myaseazsa1.file.myasegpu.wdshcsso.com/;Acco
                          untName=myaseazsa1;AccountKey=[key hidden]
    TableStorageAccount : BlobEndpoint=https://myaseazsa1.blob.myasegpu.wdshcsso.com/;Qu
                          eueEndpoint=https://myaseazsa1.queue.myasegpu.wdshcsso.com/;Ta
                          bleEndpoint=https://myaseazsa1.table.myasegpu.wdshcsso.com/;Fi
                          leEndpoint=https://myaseazsa1.file.myasegpu.wdshcsso.com/;Defa
                          ultEndpointsProtocol=https;AccountName=myaseazsa1;AccountKey=[
                          key hidden]
    Track2OauthToken    :
    EndPointSuffix      : myasegpu.wdshcsso.com/
    ConnectionString    : BlobEndpoint=https://myaseazsa1.blob.myasegpu.wdshcsso.com/;Qu
                          eueEndpoint=https://myaseazsa1.queue.myasegpu.wdshcsso.com/;Ta
                          bleEndpoint=https://myaseazsa1.table.myasegpu.wdshcsso.com/;Fi
                          leEndpoint=https://myaseazsa1.file.myasegpu.wdshcsso.com/;Acco
                          untName=myaseazsa1;AccountKey=itOn5Awjh3hnoGKL7EDQ681zhIKG/szC
                          t05ZIWAxP/T22gwEXb5l0sKjI833Hqpc0MsBiSH2rM6NuuwnJyEO1Q==
    ExtendedProperties  : {}
            
    PS C:\Users\user> Start-AzStorageBlobCopy -AbsoluteUri $sassnapshot.AccessSAS -DestContainer $DestStorageContainer -DestContext $destContext -DestBlob $DestFileName    
    
       AccountName: myaseazsa1, ContainerName: testcont1
    
    Name                 BlobType  Length          ContentType                    LastMo
                                                                                  dified
    ----                 --------  ------          -----------                    ------
    testfile1            BlockBlob -1                                             202...
       
    PS C:\Users\user>
    ```

    También puede usar el Explorador de Storage para comprobar que la instantánea se haya copiado correctamente en la cuenta de almacenamiento.

    ![Explorador de Storage que muestra la copia de seguridad en el contenedor de la cuenta de almacenamiento local](media/azure-stack-edge-gpu-back-up-virtual-machine-disks/back-up-virtual-machine-disk-2.png)

### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

Copie las instantáneas en una cuenta de almacenamiento local del dispositivo. 

1. Configure algunos parámetros. 

    ```powershell
    $StorageAccountRG = <Local storage account resource group>
    $StorageAccountName = <Storage account name>
    $StorageEndpointSuffix = <Connection string in format: DeviceName.DnsDomain.com>
    $DestStorageContainer = <Destination storage container>
    $DestFileName = <Blob file name> 
    ```

1. Cree una cuenta de almacenamiento local en el dispositivo. 

    ```powershell
    New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Storage account resource group> -Location DBELocal -SkuName Standard_LRS
    ```

    A continuación, se incluye una salida de ejemplo: 

    ```output
    PS C:\Users\user> New-AzureRmStorageAccount -Name myasesa4 -ResourceGroupName myaserg4 -Location DBELocal -SkuName Standard_LRS
    StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime        ProvisioningState EnableHttpsTrafficOnly
    ------------------ ----------------- -------- -------     ----    ---------- ------------        ----------------- ---------------
    myasesa4           myaserg4          DBELocal StandardLRS Storage            4/9/2021 6:02:56 PM Succeeded         False
    
    PS C:\Users\user>
    ```

1. Cree un contenedor en la cuenta de almacenamiento local que ha creado. 

    ```powershell
    $keys = Get-AzureRmStorageAccountKey -ResourceGroupName $StorageAccountRG -Name $StorageAccountName
    $keyValue = $keys[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue -Protocol Http -Endpoint $StorageEndpointSuffix;
    $container = New-AzureStorageContainer -Name $DestStorageContainer -Context $storageContext -Permission Container -ErrorAction Ignore;    
    ```
    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\Users\user> $StorageAccountName = "myasesa4"                                                              
    PS C:\Users\user> $StorageAccountRG = "myaserg4"
    PS C:\Users\user> $DestStorageContainer = "myasecont2"
    PS C:\Users\user> $StorageEndpointSuffix = "myasegpudev.wdshcsso.com"
    PS C:\Users\user> $DestFileName = "testfile1"

    PS C:\Users\user> $keys = Get-AzureRmStorageAccountKey -ResourceGroupName $StorageAccountRG -Name $StorageAccountName
    PS C:\Users\user> $keyValue = $keys[0].Value
    PS C:\Users\user> $storageContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue -Protocol Http -Endpoint $StorageEndpointSuffix;                                                                                   
    PS C:\Users\user> $storagecontext 
    StorageAccountName : myasesa4                                                                                      
    BlobEndPoint       : http://myasesa4.blob.myasegpudev.wdshcsso.com/                                                
    TableEndPoint      : http://myasesa4.table.myasegpudev.wdshcsso.com/
    QueueEndPoint      : http://myasesa4.queue.myasegpudev.wdshcsso.com/
    FileEndPoint       : http://myasesa4.file.myasegpudev.wdshcsso.com/
    Context            : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
    Name               :
    StorageAccount     : BlobEndpoint=http://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=http://myasesa4.que
                         ue.myasegpudev.wdshcsso.com/;TableEndpoint=http://myasesa4.table.myasegpudev.wdshcsso.com/;Fi
                         leEndpoint=http://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey=[ke
                         y hidden]
    EndPointSuffix     : myasegpudev.wdshcsso.com/
    ConnectionString   : BlobEndpoint=http://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=http://myasesa4.que
                         ue.myasegpudev.wdshcsso.com/;TableEndpoint=http://myasesa4.table.myasegpudev.wdshcsso.com/;Fi
                         leEndpoint=http://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey=GSK
                         FuTCJi5Dby6A6C1F4jB4bYS/gBNslb7/FAdlh/0VWUg3Vxd1kHsbwN8sw85pMqdKG1AajoeiwzhievHPnlQ==
    ExtendedProperties : {}
    
    PS C:\Users\user> $container = New-AzureStorageContainer -Name $DestStorageContainer -Context $storageContext -Permission Container -ErrorAction Ignore;
    PS C:\Users\user> $container
    Blob End Point: http://myasesa4.blob.myasegpudev.wdshcsso.com/
    
    Name                 PublicAccess         LastModified
    ----                 ------------         ------------
    myasecont2           Container            4/12/2021 4:46:03 PM +00:00
    
    PS C:\Users\user>
    ```    

    También puede usar el Explorador de Azure Storage para [crear una cuenta de almacenamiento local](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#create-a-storage-account) y, a continuación, [crear un contenedor en la cuenta de almacenamiento local](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload) en el dispositivo. 



1. Descargue la instantánea en la cuenta de almacenamiento local.

   ```powershell
   $sassnapshot = Grant-AzureRmSnapshotAccess -ResourceGroupName $DestinationRG -SnapshotName $SnapshotName -Access 'Read' -DurationInSecond 3600
   $destContext = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $keyValue 
   Start-AzureStorageBlobCopy -AbsoluteUri $sassnapshot.AccessSAS -DestContainer $DestStorageContainer -DestContext $destContext -DestBlob $DestFileName
   ```

    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\Users\user> $sassnapshot= Grant-AzureRmSnapshotAccess -ResourceGroupName $DestinationRG -SnapshotName $SnapshotName -Access 'Read' -DurationInSecond 3600
    PS C:\Users\user> $sassnapshot
       
    AccessSAS : https://md-2.blob.myasegpudev.wdshcsso.com/3d95ae10d9924e6fb84de408d071f22d/abcd.vhd?sv=2017-04-17&sr=
    b&si=2535bf98-f87f-4738-9142-594e3c1150fc&sk=system-1&sig=4wrtYzWg6ePWBdrXlodrVgT76q7PIueCbw3bbShKCGs%3D   
    
    PS C:\Users\user> $destContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue
    PS C:\Users\user> $destContext
        
    StorageAccountName : myasesa4
    BlobEndPoint       : https://myasesa4.blob.myasegpudev.wdshcsso.com/
    TableEndPoint      : https://myasesa4.table.myasegpudev.wdshcsso.com/
    QueueEndPoint      : https://myasesa4.queue.myasegpudev.wdshcsso.com/
    FileEndPoint       : https://myasesa4.file.myasegpudev.wdshcsso.com/
    Context            : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
    Name               :
    StorageAccount     : BlobEndpoint=https://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=https://myasesa4.q
                         ueue.myasegpudev.wdshcsso.com/;TableEndpoint=https://myasesa4.table.myasegpudev.wdshcsso.com/;FileEndpoint=https://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey=[key hidden]                                  EndPointSuffix     : myasegpudev.wdshcsso.com/                                                                     ConnectionString   : BlobEndpoint=https://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=https://myasesa4.q
                         ueue.myasegpudev.wdshcsso.com/;TableEndpoint=https://myasesa4.table.myasegpudev.wdshcsso.com/
                         ;FileEndpoint=https://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey
                         =GSKFuTCJi5Dby6A6C1F4jB4bYS/gBNslb7/FAdlh/0VWUg3Vxd1kHsbwN8sw85pMqdKG1AajoeiwzhievHPnlQ==
    ExtendedProperties : {}

    PS C:\Users\user> Start-AzureStorageBlobCopy -AbsoluteUri $sassnapshot.AccessSAS -DestContainer $DestStorageContainer -DestContext $destContext -DestBlob $DestFileName
    
    
       Container Uri: https://myasesa4.blob.myasegpudev.wdshcsso.com/myasecont2
    
    Name                 BlobType  Length          ContentType                    LastModified         AccessTier Snapshot Time
    ----                 --------  ------          -----------                    ------------         ---------- ----
    testfile1            BlockBlob -1                                             2021-04-12 17:01:58Z
 
    PS C:\Users\user>
    ```

    También puede usar el Explorador de Storage para comprobar que la instantánea se haya copiado correctamente en la cuenta de almacenamiento.

    ![Explorador de Storage que muestra la copia de seguridad en el contenedor de la cuenta de almacenamiento local](media/azure-stack-edge-gpu-back-up-virtual-machine-disks/back-up-virtual-machine-disk-1.png)

---

## <a name="download-vhd-to-external-target"></a>Descarga de un disco duro virtual en un destino externo

Para mover las copias de seguridad a una ubicación externa, puede usar el Explorador de Azure Storage o AzCopy.

- Use el comando AzCopy siguiente para descargar el VHD en un destino externo.

    ```powershell
    azcopy copy "https://<local storage account name>.blob.<device name>.<DNS domain>/<container name>/<filename><SAS query string>" <destination target>
    ```

- Para configurar y usar el Explorador de Azure Storage con Azure Stack Edge, consulte las instrucciones de [Uso del Explorador de Storage para la carga](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload).

## <a name="next-steps"></a>Pasos siguientes

[Implementación de máquinas virtuales en el dispositivo Azure Stack Edge Pro con GPU mediante plantillas](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).