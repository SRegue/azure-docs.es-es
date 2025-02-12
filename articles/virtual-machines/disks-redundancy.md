---
title: Opciones de redundancia para Azure Managed Disks
description: Obtenga información sobre el almacenamiento con redundancia de zona y el almacenamiento con redundancia local para Azure Managed Disks.
author: roygara
ms.author: rogarana
ms.date: 07/12/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 1875f43203735707a1bf49ac4e2d008abf898828
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113664486"
---
# <a name="redundancy-options-for-managed-disks"></a>Opciones de redundancia para Managed Disks

Azure Managed Disks ofrece dos opciones de redundancia de almacenamiento, el almacenamiento con redundancia de zona (ZRS) como versión preliminar y el almacenamiento con redundancia local. ZRS proporciona una mayor disponibilidad para los discos administrados que el almacenamiento con redundancia local (LRS). Sin embargo, la latencia de escritura de los discos LRS es mejor que la de los discos ZRS, ya que los discos LRS escriben datos de forma sincrónica en tres copias en un solo centro de datos.

## <a name="locally-redundant-storage-for-managed-disks"></a>Almacenamiento con redundancia local para Managed Disks

El almacenamiento con redundancia local (LRS) replica los datos tres veces dentro de un único centro de datos en la región seleccionada. LRS protege los datos frente a errores en la estantería de servidores y en la unidad. Para proteger un disco LRS de un error zonal, como un desastre natural u otros problemas, siga estos pasos:

- Use aplicaciones que puedan escribir datos de manera sincrónica en dos zonas y conmutar automáticamente por error a otra zona durante un desastre.
    - Un ejemplo sería SQL Server AlwaysOn.
- Realice copias de seguridad frecuentes de los discos LRS con instantáneas de ZRS.
- Habilite la recuperación ante desastres entre zonas para los discos LRS mediante [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). Sin embargo, la recuperación ante desastres entre zonas no proporciona un objetivo de punto de recuperación (RPO) de cero.

Si el flujo de trabajo no admite escrituras sincrónicas en el nivel de aplicación entre zonas, o si la aplicación debe cumplir un RPO de cero, los discos ZRS serían ideales.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Almacenamiento con redundancia de zona para Managed Disks

El almacenamiento con redundancia de zona (ZRS) replica los discos administrados de Azure de forma sincrónica en tres zonas de disponibilidad de Azure en la región seleccionada. Cada zona de disponibilidad es una ubicación física individual con alimentación, refrigeración y redes independientes. 

Un disco ZRS (versión preliminar) le permite recuperarse de errores en zonas de disponibilidad. Si una zona deja de funcionar, se puede conectar un disco ZRS a una máquina virtual (VM) de otra zona. Los discos ZRS también se pueden compartir entre máquinas virtuales para mejorar la disponibilidad con aplicaciones en clúster o distribuidas, como SQL FCI, ASCS/SCS de SAP o GFS2. Un disco ZRS compartido se puede conectar a las máquinas virtuales principal y secundarias de diferentes zonas para aprovechar las ventajas de ZRS y de las [zonas de disponibilidad](../availability-zones/az-overview.md). Si se produce un error en la zona principal, puede conmutar por error rápidamente a la máquina virtual secundaria mediante la [reserva persistente SCSI](disks-shared-enable.md#supported-scsi-pr-commands).

### <a name="billing-implications"></a>Implicaciones de facturación

Para obtener información detallada, consulte la [página de precios de Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="comparison-with-other-disk-types"></a>Comparación con otros tipos de disco

Excepto por la mayor latencia de escritura, los discos que usan ZRS son idénticos a los discos que usan LRS: tienen los mismos objetivos de escala. [Haga pruebas comparativas de los discos](disks-benchmarks.md) para simular la carga de trabajo de la aplicación y comparar la latencia entre los discos LRS y ZRS. 

### <a name="limitations"></a>Limitaciones

[!INCLUDE [disk-storage-zrs-limitations](../../includes/disk-storage-zrs-limitations.md)]

## <a name="next-steps"></a>Pasos siguientes

- Para aprender a crear un disco ZRS, consulte [Implementación de un disco administrado ZRS](disks-deploy-zrs.md).
