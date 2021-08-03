---
title: Montaje de Azure Blob Storage con el protocolo NFS 3.0 (versión preliminar) | Microsoft Docs
description: Aprenda a montar un contenedor en Blob Storage desde una máquina virtual (VM) de Azure o un cliente que se ejecuta de forma local mediante el protocolo NFS 3.0.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: d184569fd28e23b14ff3cb24a0c8e68477b9842d
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111888741"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>Montaje de Blob Storage con el protocolo Network File System (NFS) 3.0 (versión preliminar)

Puede montar un contenedor en Blob Storage desde una máquina virtual (VM) de Azure basada en Linux o un sistema Linux que se ejecuta de forma local mediante el protocolo NFS 3.0. En este artículo se proporcionan instrucciones paso a paso. Para más información sobre la compatibilidad con el protocolo NFS 3.0 en Blob Storage, vea [Compatibilidad del protocolo Network File System (NFS) 3.0 en Azure Blob Storage (versión preliminar)](network-file-system-protocol-support.md).

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>Paso 1: Registro de la característica de protocolo NFS 3.0 con la suscripción

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Abra una ventana de comando de PowerShell. 

2. Inicie sesión en la suscripción a Azure con el comando `Connect-AzAccount` y siga las instrucciones de la pantalla.

   ```powershell
   Connect-AzAccount
   ```

3. Si su identidad está asociada a más de una suscripción, establezca la suscripción activa.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Reemplace el valor de marcador de posición `<subscription-id>` por el identificador de la suscripción.

4. Registre la característica `AllowNFSV3` mediante el comando siguiente.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Registre el proveedor de recursos mediante el siguiente comando.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```
   
# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

1. Abra una ventana del terminal.

2. Inicie sesión en la suscripción a Azure con el comando `az login` y siga las instrucciones de la pantalla.

   ```azurecli-interactive
   az login
   ```
   
3. Registre la característica `AllowNFSV3` mediante el comando siguiente.

   ```azurecli-interactive
   az feature register --namespace Microsoft.Storage --name AllowNFSV3 --subscription <subscription-id>
   ```

   Reemplace el valor de marcador de posición `<subscription-id>` por el identificador de la suscripción.

4. Registre el proveedor de recursos mediante el siguiente comando.
    
   ```azurecli-interactive
   az provider register -n Microsoft.Storage --subscription <subscription-id>
   ```

   Reemplace el valor de marcador de posición `<subscription-id>` por el identificador de la suscripción.

---

## <a name="step-2-verify-that-the-feature-is-registered"></a>Paso 2: Comprobar si la característica está registrada 

La aprobación del registro puede tardar hasta una hora en completarse. Para comprobar si el registro se ha completado, ejecute los siguientes comandos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az feature show --namespace Microsoft.Storage --name AllowNFSV3 --subscription <subscription-id>
```

Reemplace el valor de marcador de posición `<subscription-id>` por el identificador de la suscripción.

---

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>Paso 3: Creación de una instancia de Azure Virtual Network (VNet)

La cuenta de almacenamiento debe incluirse en una red virtual. Una red virtual permite a los clientes conectarse de forma segura a su cuenta de almacenamiento. Para obtener más información sobre las redes virtuales y cómo crear una, consulte la [documentación de Virtual Network](../../virtual-network/index.yml).

> [!NOTE]
> Los clientes de la misma red virtual pueden montar contenedores en su cuenta. También puede montar un contenedor desde un cliente que se ejecuta en una red local, pero primero tendrá que conectar la red local a la red virtual. Consulte [Conexiones de red compatibles](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-4-configure-network-security"></a>Paso 4: Configuración de la seguridad de red

La única manera de proteger los datos en su cuenta es mediante una red virtual y otra configuración de seguridad de red. Las demás herramientas que se usan para proteger los datos, incluidas la autorización de claves de cuenta, la seguridad de Azure Active Directory (AD) y las listas de control de acceso (ACL), no se admiten todavía en cuentas que tienen habilitada la compatibilidad con el protocolo NFS 3.0.

Para proteger los datos de la cuenta, consulte estas recomendaciones: [Recomendaciones de seguridad de red para Blob Storage](security-recommendations.md#networking).

## <a name="step-5-create-and-configure-a-storage-account"></a>Paso 5: Creación y configuración de una cuenta de almacenamiento

Para montar un contenedor mediante NFS 3.0, debe crear una cuenta de almacenamiento **después** de registrar la característica con la suscripción. No puede habilitar cuentas que existían antes de registrar la característica.

En la versión preliminar de esta característica, el protocolo NFS 3.0 es compatible con las cuentas de almacenamiento estándar de uso general v2 y las cuentas de almacenamiento de blobs en bloques Premium. Para más información sobre estos tipos de cuentas de almacenamiento, consulte [Introducción a las cuentas de almacenamiento](../common/storage-account-overview.md).

A medida que configure la cuenta, elija estos valores:

|Configuración | Rendimiento Premium | Rendimiento Estándar  
|----|---|---|
|Location|Todas las regiones disponibles |Todas las regiones disponibles    
|Rendimiento|Premium| Estándar
|Tipo de cuenta|BlockBlobStorage| Uso general v2
|Replicación|Almacenamiento con redundancia local (LRS)| Almacenamiento con redundancia local (LRS) o almacenamiento con redundancia de zona (ZRS)
|Método de conectividad|Punto de conexión público (redes seleccionadas) o punto de conexión privado. |Punto de conexión público (redes seleccionadas) o punto de conexión privado.
|Se requiere transferencia segura|Disabled|Disabled
|Espacio de nombres jerárquico|habilitado|habilitado
|NFS V3|habilitado |habilitado 

Puede aceptar los valores predeterminados de las demás opciones de configuración. 

## <a name="step-6-create-a-container"></a>Paso 6: Crear un contenedor

Cree un contenedor en la cuenta de almacenamiento mediante alguna de estas herramientas o SDK:

|Herramientas|SDK|
|---|---|
|[Azure Portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[CLI de Azure](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>Paso 7: Montaje del contenedor

Cree un directorio en el sistema Linux y, a continuación, monte un contenedor en la cuenta de almacenamiento.

1. En un sistema Linux, cree un directorio.

   ```
   mkdir -p /mnt/test
   ```

2. Monte un contenedor mediante el comando siguiente.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Reemplace el marcador de posición `<storage-account-name>` que aparece en este comando por el nombre de la cuenta de almacenamiento.  

   - Reemplace el marcador de posición `<container-name>` por el nombre del contenedor.

---

## <a name="resolve-common-issues"></a>Resolución de problemas comunes

|Problema o error | Resolución|
|---|---|
|`Access denied by server while mounting`|Asegúrese de que el cliente se ejecute en una subred compatible. Consulte las [ubicaciones de red compatibles](network-file-system-protocol-support.md#supported-network-connections).|
|`No such file or directory`| Asegúrese de que el contenedor que está montando se haya creado después de comprobar que la característica se registró. Consulte [Paso 2: Compruebe que la característica esté registrada](#step-2-verify-that-the-feature-is-registered). Además, asegúrese de escribir el comando mount y sus parámetros directamente en el terminal. Si copia y pega cualquier parte de este comando en el terminal desde otra aplicación, es posible que aparezca este error en los caracteres ocultos de la información pegada.|

## <a name="see-also"></a>Consulte también

[Compatibilidad del protocolo Network File System (NFS) 3.0 en Azure Blob Storage (versión preliminar)](network-file-system-protocol-support.md)
