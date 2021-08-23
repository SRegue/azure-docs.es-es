---
title: 'Azure Portal: Restricción del acceso para importar o exportar discos administrados con instancias de Private Link'
description: Habilite instancias de Private Link para los discos administrados con Azure Portal. Permite exportar e importar discos de forma segura dentro de una red virtual.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 07/15/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: af8b44cbd229fc2bdf28d2885356e219b3a8198a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114288311"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>Use Azure Portal para restringir el acceso de importación y exportación para discos administrados con Private Links

La compatibilidad de las instancias de Private Link con discos administrados permite restringir la exportación e importación de los discos administrados, con el fin de que solo se produzca dentro de una red virtual de Azure. Puede generar un identificador URI de firma de acceso compartido (SAS) con límite de tiempo para los discos administrados no conectados y las instantáneas para exportar los datos a otra región para la expansión regional, la recuperación ante desastres y para leer los datos para el análisis forense. También puede usar el identificador URI de SAS para cargar directamente un VHD en un disco vacío desde el entorno local. El tráfico de red entre los clientes de su red virtual y los discos administrados solo atraviesa la red virtual y un vínculo privado de la red troncal de Microsoft, lo que elimina la exposición a la red pública de Internet.

Puede crear un recurso de acceso a disco y vincularlo a la red virtual en la misma suscripción mediante la creación de un punto de conexión privado. Debe asociar un disco o una instantánea con un acceso al disco para exportar e importar los datos mediante vínculos privados. Además, debe establecer la propiedad NetworkAccessPolicy del disco o la instantánea en `AllowPrivate`. 

Puede establecer la propiedad NetworkAccessPolicy en `DenyAll` para evitar que alguien genere el identificador URI de SAS de un disco o una instantánea. El valor predeterminado de la propiedad NetworkAccessPolicy es `AllowAll`.

## <a name="limitations"></a>Limitaciones

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="create-a-disk-access-resource"></a>Creación de un recurso de acceso a disco

1. Inicie sesión en Azure Portal y vaya a **Acceso a disco** con [este vínculo](https://aka.ms/disksprivatelinks).

    > [!IMPORTANT]
    > Debe usar el [vínculo proporcionado](https://aka.ms/disksprivatelinks) para ir a la hoja Acceso a disco. No está visible actualmente en el portal público sin usar el vínculo.

1. Seleccione **+Agregar** para crear un nuevo recurso de acceso a disco.
1. En la hoja Crear, seleccione la suscripción, un grupo de recursos, escriba un nombre y seleccione una región.
1. Seleccione **Revisar + crear**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Captura de pantalla de la hoja de creación de un acceso a disco. Rellene el nombre deseado, seleccione una región, seleccione un grupo de recursos y continúe.":::

Cuando se haya creado el recurso, vaya directamente a él.

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Captura de pantalla del botón Ir al recurso en el portal":::

## <a name="create-a-private-endpoint"></a>Creación de un punto de conexión privado

Ahora que tiene un recurso de acceso a disco, puede usarlo para controlar el acceso a las importaciones y exportaciones del disco; esto se realiza mediante puntos de conexión privados. En consecuencia, tendrá que crear un punto de conexión privado y configurarlo para el acceso a disco.

1. En el recurso de acceso a disco, seleccione **Conexiones de punto de conexión privado**.
1. Seleccione **+ Punto de conexión privado**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Captura de pantalla de la hoja de información general del recurso de acceso a disco. Las conexiones de punto de conexión privado aparecen resaltadas.":::

1. Selección de un grupo de recursos
1. Rellene el nombre y seleccione la misma región en la que se creó el recurso de acceso a disco.
1. Seleccione **Siguiente: Recurso >**

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Captura de pantalla del flujo de trabajo de creación del punto de conexión privado, primera hoja. Si no selecciona la región adecuada, puede tener problemas más adelante.":::

1. En el panel **Recurso**, seleccione **Conectarse a un recurso de Azure en mi directorio**.
1. En **Tipo de recurso**, seleccione **Microsoft.Compute/diskAccesses**.
1. En **Recurso**, seleccione el recurso de acceso a disco que creó anteriormente.
1. Deje el campo **Subrecurso de destino** como **discos**.
1. Seleccione **Siguiente: Configuración >** .

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Captura de pantalla del flujo de trabajo de creación del punto de conexión privado, segundo panel. Con todos los valores resaltados (tipo de recurso, recurso, subrecurso de destino).":::

1. Seleccione la red virtual a la que desea limitar la exportación del disco; otras redes virtuales no podrán exportar el disco.

    > [!NOTE]
    > Si tiene un grupo de seguridad de red (NSG) habilitado para la subred seleccionada, se deshabilitará solo para los puntos de conexión privados de esta subred. En el resto de recursos de la subred seguirá vigente el grupo de seguridad de red.

1. Seleccione la subred adecuada.
1. Seleccione **Revisar + crear**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Captura de pantalla del flujo de trabajo de creación del punto de conexión privado, tercer panel. Red virtual y subred resaltada.":::

## <a name="enable-private-endpoint-on-your-disk"></a>Habilitación del punto de conexión privado en el disco

1. Vaya al disco que desea configurar.
1. Seleccione **Redes**
1. Seleccione **Punto de conexión privado (mediante acceso al disco)** y seleccione el acceso a disco que creó anteriormente.
1. Seleccione **Guardar**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Captura de pantalla del panel Redes del disco administrado. Resaltado de la selección del punto de conexión privado, así como el acceso a disco seleccionado. Al guardar, se configura el disco para este acceso.":::

Ya ha completado la configuración de los vínculos privados que puede usar al importar o exportar el disco administrado.

## <a name="next-steps"></a>Pasos siguientes

- Carga de un disco duro virtual en Azure o copia de un disco administrado en otra región: la [CLI de Azure](linux/disks-upload-vhd-to-managed-disk-cli.md) o el [módulo de Azure PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md)
- Descarga de un disco duro virtual [Windows](windows/download-vhd.md) o [Linux](linux/download-vhd.md)
- [Preguntas más frecuentes sobre vínculos privados](/azure/virtual-machines/faq-for-disks#private-links-for-securely-exporting-and-importing-managed-disks)
- [Exportación o copia de instantáneas administradas como VHD a una cuenta de almacenamiento en otra región con PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)