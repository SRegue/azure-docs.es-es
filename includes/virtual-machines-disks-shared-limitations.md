---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/11/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 19b9cfc6ad6467b2779abb3561899fd3bd8d037e
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112040408"
---
Los discos compartidos solo se pueden habilitar para un subconjunto de tipos de disco. Actualmente, solo los discos Ultra Disks y SSD Premium pueden habilitar discos compartidos. Cada disco administrado que tiene discos compartidos habilitados está sujeto a las siguientes limitaciones, organizadas según el tipo de disco:

### <a name="ultra-disks"></a>Discos Ultra

Los discos Ultra Disks tienen su propia lista independiente de limitaciones, que no están relacionadas con los discos compartidos. Para conocer las limitaciones de Ultra Disks, vea [Uso de Azure Ultra Disks](../articles/virtual-machines/disks-enable-ultra-ssd.md).

Al compartir discos Ultra Disks, estos tienen las siguientes limitaciones adicionales:

- Actualmente, se limita a la compatibilidad con Azure Resource Manager o SDK. 
- Solo se pueden usar discos básicos con algunas versiones del clúster de conmutación por error de Windows Server. Para más información, consulte [Requisitos de hardware y opciones de almacenamiento de clústeres de conmutación por error](/windows-server/failover-clustering/clustering-requirements).
- Solo se admite el [cifrado del lado servidor](../articles/virtual-machines/disk-encryption.md); [Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md) no se admite actualmente.

Los discos Ultra Disks compartidos están disponibles en todas las regiones que admiten este tipo de disco de forma predeterminada y no requieren que se registren para poder usarlos.

### <a name="premium-ssds"></a>SSD Premium

- Actualmente, se limita a la compatibilidad con Azure Resource Manager o SDK. 
- Solo se puede habilitar en discos de datos, no en discos de sistema operativo.
- El almacenamiento en caché del host de **solo lectura** no está disponible para los discos SSD premium con `maxShares>1`.
- La expansión de disco no está disponible para los discos SSD premium con `maxShares>1`.
- Al usar conjuntos de disponibilidad y conjuntos de escalado de máquinas virtuales con discos compartidos de Azure, la [alineación del dominio de error del almacenamiento](../articles/virtual-machines/availability.md) con el dominio de error de la máquina virtual no se exige para el disco de datos compartido.
- Al usar [grupos con ubicación por proximidad (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md), todas las máquinas virtuales que comparten un disco deben ser parte del mismo grupo con ubicación por proximidad.
- Solo se pueden usar discos básicos con algunas versiones del clúster de conmutación por error de Windows Server. Para más información, consulte [Requisitos de hardware y opciones de almacenamiento de clústeres de conmutación por error](/windows-server/failover-clustering/clustering-requirements).
- La compatibilidad con Azure Site Recovery todavía no está disponible.
- Azure Backup está disponible a través de [Azure Disk Backup](../articles/backup/disk-backup-overview.md).
- Solo se admite el [cifrado del lado servidor](../articles/virtual-machines/disk-encryption.md); [Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md) no se admite actualmente.

#### <a name="regional-availability"></a>Disponibilidad regional

Las unidades SSD premium compartidas están disponibles en todas las regiones en las que estén disponibles los discos administrados.
