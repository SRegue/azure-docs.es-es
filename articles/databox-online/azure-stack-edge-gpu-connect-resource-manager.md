---
title: Conexión a Azure Resource Manager en el dispositivo Azure Stack Edge con GPU
description: Describe cómo conectarse a la instancia de Azure Resource Manager que se ejecuta en Azure Stack Edge Pro con GPU mediante Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/09/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5ac064d53f4d85d8f517ef7bba67e88abe625f16
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515674"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-device"></a>Conexión a Azure Resource Manager en el dispositivo Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Resource Manager proporciona una capa de administración que le permite crear, actualizar y eliminar recursos de su suscripción de Azure. El dispositivo Azure Stack Edge admite las mismas API de Azure Resource Manager para crear, actualizar y eliminar máquinas virtuales en una suscripción local. Esta compatibilidad le permite administrar el dispositivo de manera coherente con la nube. 

En este tutorial se describe cómo conectarse a las API locales en el dispositivo Azure Stack Edge mediante Azure Resource Manager con Azure PowerShell.


## <a name="endpoints-on-azure-stack-edge-device"></a>Puntos de conexión en el dispositivo Azure Stack Edge

En la tabla siguiente se resumen los distintos puntos de conexión expuestos en el dispositivo, los protocolos admitidos y los puertos para acceder a esos puntos de conexión. En el artículo encontrará referencias a estos puntos de conexión.

| # | Punto de conexión | Protocolos admitidos | Puerto usado | Se usa para |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | Para conectarse a Azure Resource Manager para la automatización |
| 2. | Servicio de token de seguridad | https | 443 | Para autenticarse mediante tokens de acceso y actualización |
| 3. | Blob* | https | 443 | Para conectar con Blob Storage mediante REST |

\* *La conexión al punto de conexión de Blob Storage no es necesaria para conectarse a Azure Resource Manager.*
 
## <a name="connecting-to-azure-resource-manager-workflow"></a>Conexión al flujo de trabajo de Azure Resource Manager

El proceso de conexión a las API locales del dispositivo mediante Azure Resource Manager requiere los pasos siguientes:

| Núm. de paso | Realizará este paso... | .. en esta ubicación. |
| --- | --- | --- |
| 1. | [Configuración del dispositivo Azure Stack Edge](#step-1-configure-azure-stack-edge-device) | Interfaz de usuario web local. |
| 2. | [Creación e instalación de certificados](#step-2-create-and-install-certificates) | Cliente de Windows/interfaz de usuario web local |
| 3. | [Revisión y configuración de requisitos previos](#step-3-install-powershell-on-the-client) | Cliente Windows |
| 4. | [Configuración de Azure PowerShell en el cliente](#step-4-set-up-azure-powershell-on-the-client) | Cliente Windows |
| 5. | [Modificación del archivo de host para la resolución de nombres de punto de conexión](#step-5-modify-host-file-for-endpoint-name-resolution) | Cliente de Windows o servidor DNS |
| 6. | [Comprobación de que se ha resuelto el nombre del punto de conexión](#step-6-verify-endpoint-name-resolution-on-the-client) | Cliente Windows |
| 7. | [Uso de cmdlets de Azure PowerShell para comprobar la conexión a Azure Resource Manager](#step-7-set-azure-resource-manager-environment) | Cliente Windows |

En las secciones siguientes se ofrece información detallada sobre cada uno de los pasos anteriores para conectarse a Azure Resource Manager.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, asegúrese de que el cliente que usa para conectarse al dispositivo mediante Azure Resource Manager usa TLS 1.2. Para más información, vaya a [Configuración TLS 1.2 en el cliente de Windows para acceder al dispositivo Azure Stack Edge](azure-stack-edge-gpu-configure-tls-settings.md).

## <a name="step-1-configure-azure-stack-edge-device"></a>Paso 1: Configuración del dispositivo Azure Stack Edge 

Siga los pasos a continuación en la interfaz de usuario web local del dispositivo Azure Stack Edge.

1. Complete la configuración de red para el dispositivo Azure Stack Edge. 

    ![Página "Configuración de red" de la interfaz de usuario web local](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    Tome nota de la dirección IP del dispositivo. Más adelante usará esta dirección IP.

2. Configure el nombre del dispositivo y el dominio DNS en la página **Dispositivo**. Anote el nombre del dispositivo y el dominio DNS, ya que los necesitará más adelante.

    ![Página "Dispositivo" de la interfaz de usuario web local](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > El nombre del dispositivo, el dominio DNS se usará para crear los puntos de conexión que se exponen.
    > Use los puntos de conexión de Azure Resource Manager y Blob de la página **Dispositivo** en la interfaz de usuario web local.


## <a name="step-2-create-and-install-certificates"></a>Paso 2: Creación e instalación de certificados

Los certificados aseguran que la comunicación sea de confianza. En el dispositivo Azure Stack Edge, se generan automáticamente los certificados de dispositivo, blob y Azure Resource Manager autofirmados. Opcionalmente, puede traer sus propios certificados blob y Azure Resource Manager firmado.

Cuando incorpore un certificado firmado de su propiedad, necesitará también la cadena de firma correspondiente. En el caso de los certificados de cadena de firma, Azure Resource Manager y blob en el dispositivo, necesitará también los certificados correspondientes en la máquina cliente para autenticarse y comunicarse con el dispositivo.

Para conectarse a Azure Resource Manager, tendrá que crear u obtener los certificados de cadena y punto de conexión de firma, importar estos certificados en el cliente de Windows y, por último, cargar estos certificados en el dispositivo.

### <a name="create-certificates"></a>Creación de certificados

Solo para uso de prueba y desarrollo, puede usar Windows PowerShell para crear certificados en el sistema local. Al crear los certificados para el cliente, siga estas instrucciones:

1. En primer lugar, tiene que crear un certificado raíz para la cadena de firma. Para más información, consulte los pasos para [crear certificados de cadena de firma](azure-stack-edge-gpu-create-certificates-powershell.md#create-signing-chain-certificate).

2. A continuación, puede crear los certificados de punto de conexión para el blob y Azure Resource Manager (opcional). Puede obtener estos puntos de conexión de la página **Dispositivo** en la interfaz de usuario web local. Consulte los pasos para [crear certificados de punto de conexión](azure-stack-edge-gpu-create-certificates-powershell.md#create-signed-endpoint-certificates).

3. Para todos estos certificados, asegúrese de que el nombre de sujeto y el nombre alternativo de sujeto cumplen las siguientes directrices:

    |Tipo |Nombre del firmante (SN)  |Nombre alternativo del firmante (SAN)  |Ejemplo de nombre de firmante |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage*|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Certificado único de varios SAN para ambos puntos de conexión|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

\* Blob Storage no es necesario para conectarse a Azure Resource Manager. Se muestra aquí por si crea cuentas de almacenamiento locales en el dispositivo.

Para más información sobre los certificados, vaya a [Carga de certificados en el dispositivo e importación de certificados en los clientes que acceden al dispositivo](azure-stack-edge-gpu-manage-certificates.md).

### <a name="upload-certificates-on-the-device"></a>Carga de certificados en el dispositivo

Los certificados creados en el paso anterior se encontrarán en el almacén Personal de su cliente. Estos certificados tienen que exportarse en el cliente en archivos de formato adecuado que se puedan cargar en el dispositivo.

1. El certificado raíz tiene que exportarse como un archivo de formato DER con la extensión de archivo *.cer*. Para obtener pasos detallados, consulte [Exportación de certificados como un archivo de formato.cer](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-der-format).

2. Los certificados de punto de conexión se tienen que exportar como archivos *.pfx* con claves privadas. Para obtener pasos detallados, consulte [Exportación de certificados como un archivo .pfx con claves privadas](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key).

3. Los certificados raíz y de punto de conexión se cargan en el dispositivo con la opción **+ Agregar certificado** de la página **Certificados** en la interfaz de usuario web local. Para cargar los certificados, siga los pasos descritos en [Carga de certificados](azure-stack-edge-gpu-manage-certificates.md#upload-certificates).


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Importación de certificados en el cliente que ejecuta Azure PowerShell

El cliente de Windows en el que se invocará las API de Azure Resource Manager tiene que establecer confianza con el dispositivo. Para ello, los certificados creados en el paso anterior tienen que importarse en el cliente de Windows en el almacén de certificados adecuado.

1. El certificado raíz que exportó como formato DER con extensión *.cer* debe importarse ahora en las entidades de certificación raíz de confianza en el sistema cliente. Para obtener pasos detallados, consulte [Importación de certificados en el almacén de entidades de certificación raíz de confianza.](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format)

2. Los certificados de punto de conexión que exportó como *.pfx* tienen que exportarse como *.cer*. Este *.cer* se importa entonces en el almacén de certificados **Personal** en el sistema. Para obtener pasos detallados, consulte [Importación de certificados en un almacén personal](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format).

## <a name="step-3-install-powershell-on-the-client"></a>Paso 3: Instalación de PowerShell en el cliente 

### <a name="az"></a>[Az](#tab/Az)

El cliente de Windows tiene que cumplir los siguientes requisitos previos:


1. Ejecutar PowerShell versión 5.0. Es necesario tener PowerShell versión 5.0. Para comprobar la versión de PowerShell en el sistema, ejecute el siguiente cmdlet:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Compare la versión **principal** y asegúrese de que sea 5.0 o posterior.

    Si su versión no está actualizada, consulte [Actualización de Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell).

    Si no tiene PowerShell 5.0, siga las instrucciones de [Instalación de Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true).

    A continuación, se muestra un ejemplo de salida:

    ```output
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      19041  906
    ```
    
1. Puede tener acceso a la Galería de PowerShell.

    Ejecute PowerShell como administrador. Compruebe que la versión de PowerShellGet es anterior a la 2.2.3. Además, confirme que `PSGallery` está registrado como repositorio.

    ```powershell
    Install-Module PowerShellGet –MinimumVersion 2.2.3
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    A continuación, se muestra un ejemplo de salida:
    
    ```output
    PS C:\windows\system32> Install-Module PowerShellGet –MinimumVersion 2.2.3
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```    

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

El cliente de Windows tiene que cumplir los siguientes requisitos previos:


1. Ejecutar PowerShell versión 5.0. Es necesario tener PowerShell versión 5.0. No se admite PowerShell Core. Para comprobar la versión de PowerShell en el sistema, ejecute el siguiente cmdlet:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Compare la versión **principal** y asegúrese de que sea 5.0 o posterior.

    Si su versión no está actualizada, consulte [Actualización de Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell).

    Si no tiene PowerShell 5.0, siga las instrucciones de [Instalación de Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true).

    A continuación, se muestra un ejemplo de salida:

    ```output
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. Puede tener acceso a la Galería de PowerShell.

    Ejecute PowerShell como administrador. Confirme si `PSGallery` está registrada como repositorio.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    A continuación, se muestra un ejemplo de salida:
    
    ```output
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
---    
Si el repositorio no es de confianza o necesita más información, consulte [Confirmación de la accesibilidad de la Galería de PowerShell](/azure-stack/operator/azure-stack-powershell-install?view=azs-1908&preserve-view=true&preserve-view=true#2-validate-the-powershell-gallery-accessibility).

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>Paso 4: Configuración de Azure PowerShell en el cliente 

### <a name="az"></a>[Az](#tab/Az)

Instalará los módulos de Azure PowerShell en el cliente que funcionará con el dispositivo.

1. Ejecute PowerShell como administrador. Necesita acceso a la Galería de PowerShell. 


1. En primer lugar, compruebe que no hay ninguna versión existente de los módulos `AzureRM` y `Az` en el cliente. Para comprobarlo, ejecute los comandos siguientes:

    ```powershell
    # Check existing versions of AzureRM modules
    Get-InstalledModule -Name AzureRM -AllVersions

    # Check existing versions of Az modules
    Get-InstalledModule -Name Az -AllVersions
    ```

    Si hay versiones existentes, use el cmdlet `Uninstall-Module` para desinstalarlas. Para obtener más información, vea 
    - [Desinstalación de módulos AzureRM](/powershell/azure/uninstall-az-ps?view=azps-6.0.0&preserve-view=true#uninstall-the-az-module)
    - [Desinstalación de módulos Az](/powershell/azure/uninstall-az-ps?view=azps-6.0.0&preserve-view=true#uninstall-the-azurerm-module)

1. Para instalar los módulos Azure PowerShell necesarios desde el Galería de PowerShell, ejecute el siguiente comando:

    - Si el cliente usa PowerShell Core, versión 7.0 y posteriores:

        ```powershell
        # Install the Az.BootStrapper module. Select Yes when prompted to install NuGet.
        
        Install-Module -Name Az.BootStrapper
        
        # Install and import the API Version Profile into the current PowerShell session.
        
        Use-AzProfile -Profile 2020-09-01-hybrid -Force
        
        # Confirm the installation of PowerShell
        Get-Module -Name "Az*" -ListAvailable
        ```
    
    - Si el cliente usa PowerShell 5.1 y versiones posteriores:
        
        ```powershell
        #Install the Az module version 1.10.0
        
        Install-Module –Name Az –RequiredVersion 1.10.0    
        ```

3.  Asegúrese de que la versión 1.10.0 del módulo Az se ejecuta al final de la instalación. 
   

    Si usó PowerShell Core 7.0 y versiones posteriores, la salida de ejemplo siguiente indica que los módulos Az, versión 1.10.0 se instalaron correctamente.
    
    ```output
   
    PS C:\windows\system32> Install-Module -Name Az.BootStrapper
    PS C:\windows\system32> Use-AzProfile -Profile 2020-09-01-hybrid -Force
    Loading Profile 2020-09-01-hybrid
    PS C:\windows\system32> Get-Module -Name "Az*" -ListAvailable
    ```

    Si usó PowerShell 5.1 y versiones posteriores, la salida de ejemplo siguiente indica que los módulos Az, versión 1.10.0 se instalaron correctamente.
     
    ```powershell
    PS C:\WINDOWS\system32> Get-InstalledModule -Name Az -AllVersions
    Version              Name                                Repository           Description
    -------              ----                                ----------           ------
    1.10.0               Az                                  PSGallery            Mic...  
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Instalará los módulos de Azure PowerShell en el cliente que funcionará con el dispositivo.

1. Ejecute PowerShell como administrador. Necesita acceso a la Galería de PowerShell. 


2. Para instalar los módulos Azure PowerShell necesarios desde el Galería de PowerShell, ejecute el siguiente comando:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
    # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    Asegúrese de que la versión 2.5.0 del módulo Azure-RM se ejecuta al final de la instalación. 
    Si tiene una versión existente del módulo Azure-RM que no coincide con la versión requerida, desinstálela con el siguiente comando:

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    Tendrá que volver a instalar la versión requerida.
   

    A continuación, se muestra una salida de ejemplo que indica que los módulos AzureRM, versión 2.5.0 se instalaron correctamente.
    
    ```powershell
    PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
    PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Loading Profile 2019-03-01-hybrid
    PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
     
        Directory: C:\Program Files\WindowsPowerShell\Modules
     
    ModuleType Version    Name                          ExportedCommands
    ---------- -------    ----                          ----------------
    Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
    Script     2.5.0      AzureRM
    Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
    Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
    Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
    Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
    Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
    Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
    Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
    Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
    Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
    Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
    Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
    Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...
    
     
        Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
     
    ModuleType Version    Name                            ExportedCommands
    ---------- -------    ----                           ----------------
    Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
    ```
---   

## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>Paso 5: Modificación del archivo de host para la resolución de nombres de punto de conexión 

Ahora agregará la dirección IP del dispositivo a:

- El archivo de host en el cliente, o
- La configuración del servidor DNS

> [!IMPORTANT]
> Se recomienda modificar la configuración del servidor DNS para la resolución de nombres del punto de conexión.

En el cliente de Windows que usa para conectarse al dispositivo, realice los pasos siguientes:

1. Inicie el **Bloc de notas** como administrador y, a continuación, abra el archivo de **hosts** que se encuentra en C:\Windows\System32\Drivers\etc.

    ![Archivo de hosts del Explorador de Windows](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)

2. Agregue las entradas siguientes al archivo de **hosts** reemplazando los valores por los adecuados para el dispositivo: 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > La entrada en el archivo de hosts debe coincidir exactamente con la proporcionada para conectarse a Azure Resource Manager en un paso posterior. Asegúrese de que la entrada de dominio DNS está en minúsculas. Para obtener los valores de `<appliance name>` y `<DNS domain>`, vaya a la página **Dispositivo** en la interfaz de usuario local del dispositivo.

    En un paso anterior, guardó la dirección IP del dispositivo desde la interfaz de usuario web local.

    La entrada `login.<appliance name>.<DNS domain>` es el punto de conexión del servicio de token de seguridad (STS). STS es el responsable de la creación, validación, renovación y cancelación de tokens de seguridad. El servicio de token de seguridad se usa para crear el token de acceso y el token de actualización que se usan para la comunicación continua entre el dispositivo y el cliente.

    El punto de conexión de Blob Storage es opcional al conectarse a Azure Resource Manager. Este punto de conexión es necesario al transferir datos a Azure a través de cuentas de almacenamiento.

3. Como referencia, utilice la siguiente imagen. Guarde el archivo **hosts**.

    ![archivo hosts en el Bloc de notas](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>Paso 6: Comprobación de la resolución de nombres del punto de conexión en el cliente

Compruebe si el nombre del punto de conexión se resuelve en el cliente que está usando para conectarse al dispositivo.

1. Puede usar la utilidad de línea de comandos `ping.exe` para comprobar que el nombre del punto de conexión está resuelto. Dada una dirección IP, el comando `ping` devolverá el nombre de host TCP/IP del equipo en el que se realiza el seguimiento.

    Agregue el modificador `-a` a la línea de comandos como se muestra en el ejemplo siguiente. Si se devuelve el nombre de host, también se devolverá esta información potencialmente valiosa en la respuesta.

    ![Entrada de ping en el símbolo del sistema](media/azure-stack-edge-gpu-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>Paso 7: Establecimiento del entorno de Azure Resource Manager

### <a name="az"></a>[Az](#tab/Az)

Establezca el entorno de Azure Resource Manager y compruebe que la comunicación de dispositivo a cliente mediante Azure Resource Manager funciona correctamente. Siga estos pasos para realizar esta comprobación:


1. Use el cmdlet `Add-AzEnvironment` para asegurarse de que la comunicación a través de Azure Resource Manager funciona correctamente y de que las llamadas a la API pasan por el puerto dedicado para Azure Resource Manager: 443.

    El cmdlet `Add-AzEnvironment` agrega puntos de conexión y metadatos para permitir que los cmdlets de Azure Resource Manager se conecten con una nueva instancia de Azure Resource Manager. 


    > [!IMPORTANT]
    > La dirección URL del punto de conexión de Azure Resource Manager que proporcione en el siguiente cmdlet distingue mayúsculas de minúsculas. Asegúrese de que la dirección URL del punto de conexión está en minúsculas y coincide con lo que proporcionó en el archivo de hosts. Si el tipo mayúsculas y minúsculas no coincide, verá un error.

    ```powershell
    Add-AzEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    A continuación se muestra una salida de ejemplo:
    
    ```output
    PS C:\WINDOWS\system32> Add-AzEnvironment -Name AzASE -ARMEndpoint "https://management.myasegpu.wdshcsso.com/"
    
    Name  Resource Manager Url                      ActiveDirectory Authority
    ----  --------------------                      -------------------------
    AzASE https://management.myasegpu.wdshcsso.com/ https://login.myasegpu.wdshcsso.c...
    ```

2. Establezca el entorno como Azure Stack Edge y el puerto que se usará para las llamadas de Azure Resource Manager como 443. El entorno se define de dos maneras:

    - Configure el entorno. Escriba el siguiente comando:

        ```powershell
        Set-AzEnvironment -Name <Environment Name>
        ```
        
        Esta es una salida de ejemplo.

        ```output
        PS C:\WINDOWS\system32> Set-AzEnvironment -Name AzASE

        Name  Resource Manager Url                      ActiveDirectory Authority
        ----  --------------------                      -------------------------
        AzASE https://management.myasegpu.wdshcsso.com/ https://login.myasegpu.wdshcsso.c...     
        ```
        Para más información, vaya a [Set-AzEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true).    

    - Defina el entorno en línea para cada cmdlet que ejecute. Esto garantiza que todas las llamadas a la API pasan por el entorno correcto. De forma predeterminada, las llamadas pasarán por Azure público, pero quiere que pasen por el entorno que estableció para el dispositivo Azure Stack Edge.

    - Consulte más información sobre cómo [cambiar los entornos Az](#switch-environments).

2. Llame a las API del dispositivo local para autenticar las conexiones a Azure Resource Manager. 

    1. Estas credenciales son para una cuenta de máquina local y se usan únicamente para el acceso a la API.

    2. Puede conectarse mediante `login-AzAccount` o mediante el comando `Connect-AzAccount`. 

        1. Para iniciar sesión, escriba el siguiente comando. 
        
            ```powershell
            $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            Connect-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            ```

            Use el identificador de inquilino c0257de7-538f-415c-993a-1b87a031879d, ya que en esta instancia está codificado de forma fija.
            Use el siguiente nombre de usuario y contraseña.

            - **Nombre de usuario** - *EdgeArmUser*

            - **Contraseña** - [establezca la contraseña para Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md) y use esta contraseña para iniciar sesión. 
       


            Esta es una salida de ejemplo de `Connect-AzAccount`.

            ```output
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzASE
            
            PS C:\windows\system32>
            ```                   
        
            Una manera alternativa de iniciar sesión es usar el cmdlet `login-AzAccount`. 
            
            `login-AzAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d` 

            Esta es una salida de ejemplo. 
         
            ```output
            PS C:\WINDOWS\system32> login-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account               SubscriptionName              TenantId
            -------               ----------------              --------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a...            
            
            PS C:\WINDOWS\system32>
            ```
3. Para comprobar que la conexión al dispositivo funciona, use el comando `Get-AzResource`. Este comando debe devolver todos los recursos que existen localmente en el dispositivo.

    Esta es una salida de ejemplo.

    ```output
    PS C:\WINDOWS\system32> Get-AzResource
    
    Name              : aseimagestorageaccount
    ResourceGroupName : ase-image-resourcegroup
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ase-image-resourcegroup/providers/Microsoft.Storage/storageac
                        counts/aseimagestorageaccount
    Tags              :
    
    Name              : myaselinuxvmimage1
    ResourceGroupName : ASERG
    ResourceType      : Microsoft.Compute/images
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Compute/images/myaselinuxvmimage1
    Tags              :
    
    Name              : ASEVNET
    ResourceGroupName : ASERG
    ResourceType      : Microsoft.Network/virtualNetworks
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Network/virtualNetworks/ASEVNET
    Tags              :
    
    PS C:\WINDOWS\system32>  
    ```

   

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Establezca el entorno de Azure Resource Manager y compruebe que la comunicación de dispositivo a cliente mediante Azure Resource Manager funciona correctamente. Siga estos pasos para realizar esta comprobación:


1. Use el cmdlet `Add-AzureRmEnvironment` para asegurarse de que la comunicación a través de Azure Resource Manager funciona correctamente y de que las llamadas a la API pasan por el puerto dedicado para Azure Resource Manager: 443.

    El cmdlet `Add-AzureRmEnvironment` agrega puntos de conexión y metadatos para permitir que los cmdlets de Azure Resource Manager se conecten con una nueva instancia de Azure Resource Manager. 


    > [!IMPORTANT]
    > La dirección URL del punto de conexión de Azure Resource Manager que proporcione en el siguiente cmdlet distingue mayúsculas de minúsculas. Asegúrese de que la dirección URL del punto de conexión está en minúsculas y coincide con lo que proporcionó en el archivo de hosts. Si el tipo mayúsculas y minúsculas no coincide, verá un error.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    A continuación se muestra una salida de ejemplo:
    
    ```output
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. Establezca el entorno como Azure Stack Edge y el puerto que se usará para las llamadas de Azure Resource Manager como 443. El entorno se define de dos maneras:

    - Configure el entorno. Escriba el siguiente comando:

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    Para más información, vaya a [Set-AzureRMEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true).

    - Defina el entorno en línea para cada cmdlet que ejecute. Esto garantiza que todas las llamadas a la API pasan por el entorno correcto. De forma predeterminada, las llamadas pasarán por Azure público, pero quiere que pasen por el entorno que estableció para el dispositivo Azure Stack Edge.

    - Consulte más información sobre [cómo cambiar los entornos AzureRM](#switch-environments).

2. Llame a las API del dispositivo local para autenticar las conexiones a Azure Resource Manager. 

    1. Estas credenciales son para una cuenta de máquina local y se usan únicamente para el acceso a la API.

    2. Puede conectarse mediante `login-AzureRMAccount` o mediante el comando `Connect-AzureRMAccount`. 

        1. Para iniciar sesión, escriba el siguiente comando. El identificador de inquilino en esta instancia está codificado de forma rígida: c0257de7-538f-415c-993a-1b87a031879d. Use el siguiente nombre de usuario y contraseña.

            - **Nombre de usuario** - *EdgeArmUser*

            - **Contraseña** - [establezca la contraseña para Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md) y use esta contraseña para iniciar sesión. 

            ```output
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            Una manera alternativa de iniciar sesión es usar el cmdlet `login-AzureRmAccount`. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d` 

            Este es un ejemplo de resultado del comando. 
         
            ```output
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```
---

Si le han surgido problemas con las conexiones de Azure Resource Manager, consulte [Solución de problemas de Azure Resource Manager](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md) para obtener instrucciones. 

> [!IMPORTANT]
> La conexión a Azure Resource Manager expira cada 1,5 horas o si se reinicia el dispositivo Azure Stack Edge. Si esto ocurre, los cmdlets que ejecute, devolverán mensajes de error porque ya no está conectado a Azure. Tiene que volver a iniciar sesión.

## <a name="switch-environments"></a>Cambio de entornos

Es posible que tenga que cambiar entre dos entornos.

### <a name="az"></a>[Az](#tab/Az)

Ejecute el comando `Disconnect-AzAccount` para cambiar a otro `AzEnvironment`. Si usa `Set-AzEnvironment` y `Login-AzAccount`, sin usar `Disconnect-AzAccount`, el entorno no se cambia.  

En los siguientes ejemplos se muestra cómo cambiar entre dos entornos, `AzASE1` y `AzASE2`.

En primer lugar, enumere todos los entornos existentes en el cliente.


```output
PS C:\WINDOWS\system32> Get-AzEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

A continuación, obtenga el entorno al que está conectado actualmente mediante Azure Resource Manager.

```output
PS C:\WINDOWS\system32> Get-AzContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

Ahora debe desconectarse del entorno actual antes de cambiar al otro entorno.


```output
PS C:\WINDOWS\system32> Disconnect-AzAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, ...], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Inicie sesión en el otro entorno. A continuación se muestra la salida de ejemplo.

```output
PS C:\WINDOWS\system32> Login-AzAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Ejecute este cmdlet para confirmar el entorno al que está conectado.

```output
PS C:\WINDOWS\system32> Get-AzContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
Ahora ha cambiado al entorno deseado.

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Ejecute el comando `Disconnect-AzureRmAccount` para cambiar a otro `AzureRmEnvironment`. Si usa `Set-AzureRmEnvironment` y `Login-AzureRmAccount`, sin usar `Disconnect-AzureRmAccount`, el entorno no se cambia.  

En los siguientes ejemplos se muestra cómo cambiar entre dos entornos, `AzDBE1` y `AzDBE2`.

En primer lugar, enumere todos los entornos existentes en el cliente.


```output
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

A continuación, obtenga el entorno al que está conectado actualmente mediante Azure Resource Manager.

```output
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

Ahora debe desconectarse del entorno actual antes de cambiar al otro entorno.


```output
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, ...], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Inicie sesión en el otro entorno. A continuación se muestra la salida de ejemplo.

```output
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Ejecute este cmdlet para confirmar el entorno al que está conectado.

```output
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
---

Ahora ha cambiado al entorno deseado.

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de Azure Resource Manager](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md).
- [Implementación de máquinas virtuales en un dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).
