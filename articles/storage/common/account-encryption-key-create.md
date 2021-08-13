---
title: Creación de una cuenta que admita las claves administradas por el cliente para tablas y colas
titleSuffix: Azure Storage
description: Obtenga información sobre cómo crear una cuenta de almacenamiento que admita la configuración de claves administradas por el cliente para tablas y colas. Use la CLI de Azure o una plantilla de Azure Resource Manager para crear una cuenta de almacenamiento que se base en la clave de cifrado de la cuenta para el cifrado de Azure Storage. Después, puede configurar las claves administradas por el cliente para la cuenta.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/09/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: cf646fe61e3fa00407cf2ff3f47f872167c00aa9
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111903905"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Creación de una cuenta que admita las claves administradas por el cliente para tablas y colas

Azure Storage cifra todos los datos de las cuentas de almacenamiento en reposo. De forma predeterminada, Queue Storage y Table Storage usan una clave cuyo ámbito es el servicio y que administra Microsoft. También puede optar por usar las claves administradas por el cliente para cifrar los datos de la cola o la tabla. Para usar claves administradas por el cliente con colas y tablas, primero debe crear una cuenta de almacenamiento que use una clave de cifrado cuyo ámbito sea la cuenta, en lugar del servicio. Después de crear una cuenta que use la clave de cifrado de la cuenta para los datos de la cola y la tabla, puede configurar las claves administradas por el cliente para esa cuenta de almacenamiento.

En este artículo se describe cómo crear una cuenta de almacenamiento que se basa en una clave cuyo ámbito es la cuenta. Cuando la cuenta se crea por primera vez, Microsoft usa la clave de cuenta para cifrar los datos de la cuenta y administra la clave. Posteriormente, puede configurar las claves administradas por el cliente para la cuenta con el fin de aprovechar estas ventajas, incluida la capacidad de proporcionar sus propias claves, actualizar la versión de la clave, rotar las claves y revocar los controles de acceso.

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Creación de una cuenta que use la clave de cifrado de la cuenta

Debe configurar una nueva cuenta de almacenamiento para usar la clave de cifrado de la cuenta para las colas y tablas en el momento de crear la cuenta de almacenamiento. El ámbito de la clave de cifrado no se puede cambiar una vez creada la cuenta.

La cuenta de almacenamiento debe ser de tipo de uso general v2. Puede crear la cuenta de almacenamiento y configurarla para que se base en la clave de cifrado de la cuenta mediante Azure Portal, PowerShell, la CLI de Azure o una plantilla de Azure Resource Manager.

Para obtener más información sobre la creación de una cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](storage-account-create.md).

> [!NOTE]
> Solo puede configurar Queue Storage y Table Storage para cifrar datos con la clave de cifrado de la cuenta cuando se crea la cuenta de almacenamiento. Blob Storage y Azure Files siempre usan la clave de cifrado de la cuenta para cifrar los datos.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Para crear una cuenta de almacenamiento que se base en la clave de cifrado de la cuenta con Azure Portal, siga estos pasos:

1. En el menú izquierdo del portal, seleccione **Cuentas de almacenamiento** para mostrar una lista de las cuentas de almacenamiento.
1. En la página **Cuentas de almacenamiento**, seleccione **Nuevo**.
1. Rellene los campos de la pestaña **Elementos básicos**.
1. En la pestaña Avanzadas, busque la sección **Tablas y colas** y seleccione **Enable support for customer-managed keys** (Habilitar la compatibilidad con claves administradas por el cliente).

    :::image type="content" source="media/account-encryption-key-create/enable-cmk-tables-queues.png" alt-text="Captura de pantalla que muestra cómo habilitar claves administradas por el cliente para colas y tablas al crear una cuenta nueva":::.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para usar PowerShell para crear una cuenta de almacenamiento que utilice la clave de cifrado de la cuenta, asegúrese de haber instalado la versión 3.4.0 del módulo de Azure PowerShell, o cualquier versión posterior. Para más información, consulte el artículo en el que se indica cómo [instalar el módulo de Azure PowerShell](/powershell/azure/install-az-ps).

A continuación, cree una cuenta de almacenamiento de uso general v2. Para ello, debe llamar al comando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount), con los parámetros adecuados:

- Incluya la opción `-EncryptionKeyTypeForQueue` y establezca su valor en `Account` a fin de usar la clave de cifrado de la cuenta para cifrar los datos en Queue Storage.
- Incluya la opción `-EncryptionKeyTypeForTable` y establezca su valor en `Account` a fin de usar la clave de cifrado de la cuenta para cifrar los datos en Table Storage.

En el ejemplo siguiente se muestra cómo crear una cuenta de almacenamiento de uso general v2 configurada para el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) y que usa la clave de cifrado de la cuenta para cifrar los datos tanto de Queue Storage como de Table Storage. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para usar la CLI de Azure para crear una cuenta de almacenamiento que se base en la clave de cifrado de la cuenta, asegúrese de haber instalado la CLI de Azure de la versión 2.0.80 o posterior. Para más información, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

A continuación, cree una cuenta de almacenamiento de uso general v2. Para ello, llame al comando [az storage account create](/cli/azure/storage/account#az_storage_account_create), con los parámetros adecuados:

- Incluya la opción `--encryption-key-type-for-queue` y establezca su valor en `Account` a fin de usar la clave de cifrado de la cuenta para cifrar los datos en Queue Storage.
- Incluya la opción `--encryption-key-type-for-table` y establezca su valor en `Account` a fin de usar la clave de cifrado de la cuenta para cifrar los datos en Table Storage.

En el ejemplo siguiente se muestra cómo crear una cuenta de almacenamiento de uso general v2 configurada para el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) y que usa la clave de cifrado de la cuenta para cifrar los datos tanto de Queue Storage como de Table Storage. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Plantilla](#tab/template)

En el ejemplo de JSON siguiente se crea una cuenta de almacenamiento de uso general v2 configurada para el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) y que usa la clave de cifrado de la cuenta para cifrar los datos tanto de Queue Storage como de Table Storage. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Después de crear una cuenta que se base en la clave de cifrado de cuenta, puede configurar las claves administradas por el cliente que se almacenan en Azure Key Vault o en el modelo de seguridad de hardware administrado de Key Vault (HSM). Para obtener información sobre cómo almacenar las claves administradas por el cliente en un almacén de claves, consulte [Configuración del cifrado con claves que administra el cliente en Azure Key Vault](customer-managed-keys-configure-key-vault.md). Para obtener información sobre cómo almacenar las claves administradas por el cliente en un HSM administrado, consulte [Configuración del cifrado con claves que administra el cliente en HSM administrado de Azure Key Vault](customer-managed-keys-configure-key-vault-hsm.md).

## <a name="verify-the-account-encryption-key"></a>Verificación de la clave de cifrado de la cuenta

Después de crear la cuenta, puede comprobar que la cuenta de almacenamiento use una clave de cifrado que tenga como ámbito la cuenta; para ello, use Azure Portal, PowerShell o la CLI de Azure.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Para comprobar que un servicio de una cuenta de almacenamiento usa una clave de cifrado que está en el ámbito de la cuenta con Azure Portal, siga estos pasos:

1. Vaya a la nueva cuenta de almacenamiento en Azure Portal.
1. En la sección **Seguridad y redes**, seleccione **Cifrado.**
1. Si la cuenta de almacenamiento se creó para tener como base la clave de cifrado de la cuenta, verá en la pestaña **Cifrado** que las claves administradas por el cliente se pueden habilitar para los cuatro servicios de Azure Storage: blobs, archivos, tablas y colas.

    :::image type="content" source="media/account-encryption-key-create/verify-cmk-tables-queues.png" alt-text="Captura de pantalla que muestra cómo comprobar que la cuenta de almacenamiento se basa en la clave de cifrado de la cuenta":::.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para comprobar que un servicio de una cuenta de almacenamiento usa la clave de cifrado de la cuenta con PowerShell, llame al comando [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount). Este comando devuelve un conjunto de propiedades de la cuenta de almacenamiento y sus valores. Busque el campo `KeyType` en cada servicio dentro de la propiedad `Encryption` y compruebe que esté establecida en `Account`.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para comprobar que un servicio de una cuenta de almacenamiento usa la clave de cifrado de la cuenta con la CLI de Azure, llame al comando [az storage account show](/cli/azure/storage/account#az_storage_account_show). Este comando devuelve un conjunto de propiedades de la cuenta de almacenamiento y sus valores. Busque el campo `keyType` de cada servicio dentro de la propiedad de cifrado y verifique que esté establecido en `Account`.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Plantilla](#tab/template)

N/D

---

Después de comprobar que la cuenta de almacenamiento usa una clave de cifrado que está en el ámbito de la cuenta, puede habilitar las claves administradas por el cliente para esta. Los cuatro servicios de Azure Storage&mdash;blobs, archivos, tablas y colas&mdash;usarán la clave administrada por el cliente para el cifrado.

## <a name="pricing-and-billing"></a>Precios y facturación

Una cuenta de almacenamiento que se crea para usar una clave de cifrado en el ámbito de la cuenta se factura por la capacidad de almacenamiento de tablas y las transacciones a una tarifa distinta a la de una cuenta que usa la clave de ámbito de servicio predeterminada. Para más información, consulte [Precios de Azure Table Storage](https://azure.microsoft.com/pricing/details/storage/tables/).

## <a name="next-steps"></a>Pasos siguientes

- [Cifrado de Azure Storage para datos en reposo](storage-service-encryption.md)
- [Claves administradas por el cliente para el cifrado de Azure Storage](customer-managed-keys-overview.md)
- [Configuración del cifrado con claves administradas por el cliente almacenadas en Azure Key Vault](customer-managed-keys-configure-key-vault.md)
- [Configuración del cifrado con claves administradas por el cliente almacenadas en HSM administrado de Azure Key Vault](customer-managed-keys-configure-key-vault-hsm.md).