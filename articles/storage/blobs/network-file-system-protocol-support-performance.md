---
title: Consideraciones de rendimiento de NFS 3.0 en Azure Blob Storage (versión preliminar) | Microsoft Docs
description: Optimice el rendimiento de las solicitudes de almacenamiento de Network File System (NFS) 3.0 con las recomendaciones de este artículo.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 6a5ebed9f6b8bf5ed40829e13bbbcc43b7ebbc8a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110069551"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage-preview"></a>Consideraciones de rendimiento de Network File System (NFS) 3.0 en Azure Blob Storage (versión preliminar)

Blob Storage ya es compatible con el protocolo Network File System (NFS) 3.0. En este artículo se incluyen recomendaciones que le ayudarán a optimizar el rendimiento de las solicitudes de almacenamiento. Para más información sobre la compatibilidad con NFS 3.0 en Azure Blob Storage, consulte [Compatibilidad del protocolo Network File System (NFS) 3.0 en Azure Blob Storage (versión preliminar)](network-file-system-protocol-support.md).

> [!NOTE]
> La compatibilidad con el protocolo NFS 3.0 en Azure Blob Storage se encuentra en versión preliminar pública. Admite cuentas de almacenamiento GPV2 con rendimiento de nivel Estándar y cuentas de almacenamiento de blobs en bloques con nivel de rendimiento Premium en todas las regiones públicas.

## <a name="add-clients-to-increase-throughput"></a>Adición de clientes para aumentar el rendimiento 

Azure Blob Storage escala linealmente hasta alcanzar el límite máximo de entrada y salida de la cuenta de almacenamiento. Por lo tanto, las aplicaciones pueden lograr un mayor rendimiento mediante el uso de más clientes.  Para ver los límites de entrada y salida de la cuenta de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento para cuentas de almacenamiento estándar](../common/scalability-targets-standard-account.md).

En el gráfico siguiente se muestra cómo aumenta el ancho de banda a medida que se agregan más clientes. En este gráfico, un cliente es una máquina virtual y la cuenta utiliza el nivel de rendimiento estándar. 

> [!div class="mx-imgBorder"]
> ![Rendimiento Estándar](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

En el gráfico siguiente se muestra el mismo efecto cuando se aplica a una cuenta que usa el nivel de rendimiento prémium.

> [!div class="mx-imgBorder"]
> ![Rendimiento Premium](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>Uso del nivel de rendimiento prémium para aplicaciones a pequeña escala

No todas las aplicaciones se pueden escalar verticalmente agregando más clientes. Para esas aplicaciones, la [cuenta de almacenamiento de blobs en bloques prémium de Azure](../common/storage-account-create.md) ofrece una latencia baja coherente y elevadas tasas de transacciones. La cuenta de almacenamiento de blobs en bloques prémium puede alcanzar el ancho de banda máximo con menos subprocesos y clientes. Por ejemplo, con un solo cliente, una cuenta de almacenamiento de blobs en bloques prémium puede alcanzar **2,3** veces más ancho de banda en comparación con la misma configuración que se usa con una cuenta de almacenamiento v2 de uso general y rendimiento estándar. 

Cada barra del gráfico siguiente muestra la diferencia en el ancho de banda logrado entre las cuentas de almacenamiento de rendimiento estándar y prémium. A medida que aumenta el número de clientes, se reduce la diferencia.  

> [!div class="mx-imgBorder"]
> ![Rendimiento relativo](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="avoid-frequent-overwrites-on-data"></a>Evitación de sobrescrituras frecuentes en los datos

Se tarda más tiempo en completar una operación de sobrescritura que una nueva operación de escritura. Esto se debe a que una operación de sobrescritura de NFS, especialmente una edición parcial de archivos en contexto, es una combinación de varias operaciones de blobs subyacentes: una operación de lectura, una modificación y una operación de escritura. Por lo tanto, una aplicación que requiere ediciones en contexto frecuentes no es adecuada para las cuentas de almacenamiento de blobs habilitadas para NFS. 

## <a name="deploy-azure-hpc-cache-for-latency-senstive-applications"></a>Implementación Azure HPC Cache para aplicaciones sensibles a la latencia

Algunas aplicaciones pueden requerir baja latencia, además de alto rendimiento. Puede implementar [Azure HPC Cache](../../hpc-cache/nfs-blob-considerations.md) para mejorar significativamente la latencia. Obtenga más información sobre la [Latencia en Blob Storage](storage-blobs-latency.md). 

## <a name="other-best-practice-recommendations"></a>Otros procedimientos recomendados 

- Use máquinas virtuales con más ancho de banda de red.

- Use varios puntos de montaje cuando las cargas de trabajo lo permitan.

- Use tantos subprocesos como sea posible.

- Use tamaños de bloques grandes.

- Realice las solicitudes de almacenamiento desde un cliente que se encuentre en la misma región que la cuenta de almacenamiento. Esto puede mejorar la latencia de red.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre la compatibilidad con NFS 3.0 en Azure Blob Storage, consulte [Compatibilidad del protocolo Network File System (NFS) 3.0 en Azure Blob Storage (versión preliminar)](network-file-system-protocol-support.md).

- Para comenzar, consulte [Montaje de Blob Storage con el protocolo Network File System (NFS) 3.0 (versión preliminar)](network-file-system-protocol-support-how-to.md).
