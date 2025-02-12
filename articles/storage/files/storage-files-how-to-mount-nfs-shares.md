---
title: 'Montaje de un recurso compartido de archivos NFS de Azure (versión preliminar): Azure Files'
description: Obtenga información acerca de cómo montar un recurso compartido de Network File System.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/01/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8f3565f05fc04a74e761b1070f0374677703d225
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113225269"
---
# <a name="how-to-mount-an-nfs-file-share-preview"></a>Montaje de un recurso compartido de archivos NFS (versión preliminar)

[Azure Files](storage-files-introduction.md) es el sencillo sistema de archivos en la nube de Microsoft. Los recursos compartidos de archivos de Azure se pueden montar en distribuciones de Linux mediante el protocolo Bloque de mensajes del servidor (SMB) o el protocolo Network File System (NFS) (versión preliminar). Este artículo se centra en el montaje con NFS. Para obtener más información sobre el montaje con SMB, consulte [Uso de Azure Files con Linux](storage-how-to-use-files-linux.md). Para obtener más información sobre los protocolos disponibles, consulte [Protocolos de recurso compartido de archivos de Azure](storage-files-planning.md#available-protocols).

## <a name="limitations"></a>Limitaciones

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilidad regional

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Requisitos previos

- [Creación de un recurso compartido de NFS](storage-files-how-to-create-nfs-shares.md)

    > [!IMPORTANT]
    > Solo se puede tener acceso a los recursos compartidos de NFS desde redes de confianza. Las conexiones a su recurso compartido de NFS deben originarse en uno de los orígenes siguientes:

- Use una de las siguientes soluciones de redes:
    - [Cree un punto de conexión privado](storage-files-networking-endpoints.md#create-a-private-endpoint) (recomendado) o [restrinja el acceso al punto de conexión público](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Configure una VPN de punto a sitio (P2S) en Linux para su uso con Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Configure una VPN de sitio a sitio para su uso con Azure Files](storage-files-configure-s2s-vpn.md).
    - Configure [ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="disable-secure-transfer"></a>Deshabilitación de la transferencia segura

1. Inicie sesión en Azure Portal y acceda a la cuenta de almacenamiento que contiene el recurso compartido de NFS que creó.
1. Seleccione **Configuración**.
1. Seleccione **Deshabilitado** para **Se requiere transferencia segura**.
1. Seleccione **Guardar**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Captura de la pantalla de configuración de la cuenta de almacenamiento con la transferencia segura deshabilitada.":::

## <a name="mount-an-nfs-share"></a>Montaje de un recurso compartido de NFS

1. Una vez creado el recurso compartido de archivos, selecciónelo y elija **Conectarse desde Linux**.
1. Escriba la ruta de acceso de montaje que quiere usar y, después, copie el script.
1. Conéctese al cliente y use el script de montaje proporcionado.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Captura de pantalla de la hoja de conexión del recurso compartido de archivos.":::

Ya ha montado el recurso compartido de NFS.

### <a name="validate-connectivity"></a>Validar conectividad

Si se produjo un error en el montaje, es posible que el punto de conexión privado no se haya configurado correctamente o sea inaccesible. Para obtener más información sobre cómo confirmar la conectividad, vea la sección [Comprobar la conectividad](storage-files-networking-endpoints.md#verify-connectivity) del artículo sobre puntos de conexión de red.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre Azure Files con nuestro artículo [Planeamiento de una implementación de Azure Files](storage-files-planning.md).
- Si tiene algún problema, consulte [Solución de problemas de los recursos compartidos de archivos NFS de Azure](storage-troubleshooting-files-nfs.md).