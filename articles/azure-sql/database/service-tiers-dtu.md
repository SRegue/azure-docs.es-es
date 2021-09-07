---
title: 'Niveles de servicio: modelo de compra basado en DTU'
description: Obtenga información acerca de los niveles de servicio en el modelo de compra basado en DTU para Azure SQL Database para proporcionar los tamaños de proceso y de almacenamiento.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 8/12/2021
ms.openlocfilehash: 56ed469843c78b299d0cec426eb490cedce1defe
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015473"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Niveles de servicio en el modelo de compra basado en DTU
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Los niveles de servicio en el modelo de compra basado en DTU se diferencian por una variedad de tamaños de proceso con una cantidad fija de almacenamiento incluido, un período de retención fijo para copias de seguridad y un precio fijo. Todos los niveles de servicio en el modelo de compra basado en DTU proporcionan flexibilidad de cambiar los tamaños de proceso con el mínimo [tiempo de inactividad](https://azure.microsoft.com/support/legal/sla/azure-sql-database); sin embargo, hay un breve período de cambio donde se pierde la conectividad a la base de datos, lo que se puede mitigar con la lógica de reintento. Las bases de datos únicas y los grupos elásticos se facturan por horas en función del nivel de servicio y el tamaño de proceso.

> [!IMPORTANT]
> [Instancia administrada de Azure SQL Database](../managed-instance/sql-managed-instance-paas-overview.md) no admite un modelo de compra basado en DTU. 


> [!NOTE]
> Para obtener información sobre los niveles de servicio basados en núcleos virtuales, consulte el artículo sobre [niveles de servicio basados en núcleos virtuales](service-tiers-vcore.md). Para más información acerca de cómo distinguir los niveles de servicio basados en DTU y los niveles de servicio basados en núcleo virtual, consulte [Modelos de compra](purchasing-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Comparación de los niveles de servicio basados en DTU

La selección de un nivel de servicio depende sobre todo de los requisitos de continuidad del negocio, de almacenamiento y de rendimiento.

||Básico|Estándar|Premium|
| :-- | --: |--:| --:|
|**Carga de trabajo de destino**|Desarrollo y producción|Desarrollo y producción|Desarrollo y producción|
|**SLA de tiempo de actividad**|99,99%|99,99%|99,99%|
|**Retención de copia de seguridad máxima**|7 días|35 días|35 días|
|**CPU**|Bajo|Bajo, medio, alto|Medio, alto|
|**IOPS (aproximado)** \* |1-4 IOPS por DTU| 1-4 IOPS por DTU | >25 IOPS por DTU|
|**Latencia de E/S (aproximada)**|5 ms (lectura), 10 ms (escritura)|5 ms (lectura), 10 ms (escritura)|2 ms (lectura/escritura)|
|**Índice de almacén de columnas** |N/D|S3 y versiones posteriores|Compatible|
|**OLTP en memoria**|N/D|N/D|Compatible|

\* Todas las IOPS de lectura y escritura en archivos de datos, incluida la E/S en segundo plano (punto de comprobación y escritura diferida).

> [!IMPORTANT]
> Los objetivos del servicio Basic, S0, S1 y S2 proporcionan menos de un núcleo virtual (CPU).  En el caso de las cargas de trabajo con un uso intensivo de CPU, se recomienda tener un objetivo de servicio S3 o superior. 
>
> En los objetivos de servicio de tipo Basic, S0 y S1, los archivos de base de datos se almacenan en Azure Standard Storage, que usa medios de almacenamiento basados en unidades de disco duro (HDD). Estos objetivos de servicio son más adecuados para el desarrollo, las pruebas y otras cargas de trabajo de acceso poco frecuente que no dan tanta importancia a la variabilidad del rendimiento.
>

> [!TIP]
> Para ver los límites reales de la [gobernanza de recursos](resource-limits-logical-server.md#resource-governance) de una base de datos o un grupo elástico, consulte la vista [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database).

> [!NOTE]
> Puede obtener una base de datos de Azure SQL Database gratuita en el nivel de servicio Básico junto con una cuenta gratuita de Azure para explorar Azure. Para obtener información, consulte [Cree una base de datos administrada en la nube con su cuenta gratuita de Azure](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Límites de DTU de una sola base de datos y almacenamiento

Los tamaños de proceso se expresan como unidades de transacción de base de datos (DTU) para las bases de datos únicas y como unidades de transacción de base de datos elásticas (eDTU) para los grupos elásticos. Para obtener más información sobre DTU y eDTU, consulte [Modelo de compra basado en DTU](purchasing-models.md#dtu-based-purchasing-model).

||Básico|Estándar|Premium|
| :-- | --: | --: | --: |
| **Tamaño máximo de almacenamiento** | 2 GB | 1 TB | 4 TB  |
| **Cantidad máxima de DTU** | 5 | 3000 | 4000 |

> [!IMPORTANT]
> En algunas circunstancias, puede que deba reducir una base de datos para reclamar el espacio no utilizado. Para obtener más información, consulte [Administración del espacio de archivo en Azure SQL Database](file-space-manage.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Límites de eDTU de grupo elástico, almacenamiento y base de datos agrupada

|| **Basic** | **Estándar** | **Premium** |
| :-- | --: | --: | --: |
| **Tamaño máximo de almacenamiento por base de datos**  | 2 GB | 1 TB | 1 TB |
| **Tamaño máximo de almacenamiento por grupo** | 156 GB | 4 TB | 4 TB |
| **Cantidad máxima de eDTU por base de datos** | 5 | 3000 | 4000 |
| **Cantidad máxima de eDTU por grupo** | 1600 | 3000 | 4000 |
| **Cantidad máxima de bases de datos por grupo** | 500  | 500 | 100 |

> [!IMPORTANT]
> Existe más de 1 TB de almacenamiento en el nivel Premium actualmente disponible en todas las regiones excepto: Este de China, Norte de China, Centro de Alemania y Nordeste de Alemania. En estas regiones, el almacenamiento máximo en el nivel Prémium está limitado a 1 TB.  Para más información, consulte las [limitaciones actuales de P11 y P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> En algunas circunstancias, puede que deba reducir una base de datos para reclamar el espacio no utilizado. Para más información, consulte [Administración del espacio de archivo en Azure SQL Database](file-space-manage.md).

## <a name="dtu-benchmark"></a>Prueba comparativa de DTU

Las características físicas (CPU, memoria, E/S) asociadas a cada medida de DTU se calibran con una prueba comparativa que simula la carga de trabajo de base de datos real.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlación de los resultados de la prueba comparativa con el rendimiento real de la base de datos

Es importante comprender que todas las pruebas comparativas solo son representativas e indicativas. Las velocidades de transacción logradas con la aplicación de la prueba comparativa no serán iguales que las que se podrían lograr con otras aplicaciones. La prueba comparativa comprende un conjunto de diferentes tipos de transacción ejecutados en un esquema que contiene una variedad de tipos de datos y tablas. Si bien la prueba comparativa ejerce las mismas operaciones básicas que son comunes para todas las cargas de trabajo OLTP, no representa ninguna clase específica de base de datos o aplicación. El objetivo de la prueba comparativa es proporcionar una orientación razonable del rendimiento relativo de una base de datos que se puede esperar al aumentar o reducir el tamaño de proceso. En realidad, las bases de datos son de distintos tamaños y complejidad, tienen distintas combinaciones de cargas de trabajo y responden de maneras diferentes. Por ejemplo, una aplicación que haga un uso intensivo de ES podría alcanzar antes el umbral de ES, o una que haga un uso intensivo de la CPU podría alcanzar antes los límites de CPU. No se garantiza que una base de datos concreta se escale de la misma manera que la prueba comparativa bajo una carga creciente.

La prueba comparativa y su metodología se describen a continuación de forma más detallada.

### <a name="benchmark-summary"></a>Resumen de la prueba comparativa

La prueba comparativa mide el rendimiento de una mezcla de operaciones de bases de datos básicas que se producen con mayor frecuencia en las cargas de trabajo de procesamiento de transacciones en línea (OLTP). Aunque la prueba comparativa está diseñada teniendo en cuenta la computación en la nube, el esquema de la base de datos, el rellenado de datos y las transacciones se diseñaron para representar ampliamente los elementos básicos usados con mayor frecuencia en las cargas de trabajo OLTP.

### <a name="schema"></a>Schema

El esquema se ha diseñado para que presente una variedad y complejidad suficientes como para permitir una amplia gama de operaciones. La prueba comparativa se ejecuta en una base de datos formada por seis tablas. Las tablas pertenecen a tres categorías: de tamaño fijo, de escalado y de crecimiento. Existen dos tablas de tamaño fijo, tres tablas de escalado y una tabla de crecimiento. Las tablas de tamaño fijo tienen un número de filas constante. Las tablas de escalado presentan una cardinalidad proporcional al rendimiento de la base de datos, pero no cambian durante la prueba comparativa. La tabla de crecimiento tiene un tamaño igual que la tabla de escalado en la carga inicial, pero después la cardinalidad cambia durante el transcurso de la prueba comparativa según se van insertando y eliminando filas.

El esquema incluye una combinación de tipos de datos que incluyen valores enteros, numéricos, caracteres y fecha/hora. El esquema incluye claves principales y secundarias, pero no claves externas; es decir, no hay restricciones de integridad referenciales entre las tablas.

Un programa de generación de datos genera los datos para la base de datos inicial. Los datos enteros y numéricos se generan con diversas estrategias. En algunos casos, los valores se distribuyen al azar a lo largo de un intervalo. En otros casos, se permuta al azar un conjunto de valores para asegurarse de que se mantiene una distribución específica. Los campos de texto se generan a partir de una lista ponderada de palabras para producir datos con aspecto real.

La base de datos se dimensiona basándose en un “factor de escala”. El factor de escala (abreviado SF) determina la cardinalidad de las tablas de escalado y de crecimiento. Como se describe a continuación en la sección Usuarios y velocidad, el tamaño de la base de datos, el número de usuarios y el rendimiento máximo se escalan de modo proporcional entre sí.

### <a name="transactions"></a>Transacciones

La carga de trabajo consta de nueve tipos de transacciones, como se muestra en la tabla siguiente. Cada transacción se diseño para destacar un conjunto determinado de características del sistema en el motor de la base de datos y en el hardware del sistema, con un elevado contraste con respecto a las otras transacciones. Este enfoque facilita la evaluación del impacto de diferentes componentes sobre el rendimiento global. Por ejemplo, la transacción “Lectura intensa” produce un número significativo de operaciones de lectura de disco.

| Tipo de transacción | Descripción |
| --- | --- |
| Lectura ligera |SELECT; en memoria; solo lectura |
| Lectura mediana |SELECT; principalmente en memoria; solo lectura |
| Lectura intensa |SELECT; principalmente no en memoria; solo lectura |
| Actualización ligera |UPDATE; en memoria; solo escritura |
| Actualización intensa |UPDATE; principalmente no en memoria; solo escritura |
| Inserción ligera |INSERT; en memoria; solo escritura |
| Inserción intensa |INSERT; principalmente no en memoria; solo escritura |
| Eliminar |DELETE; combinación de en memoria y no en memoria; solo lectura |
| CPU intensa |SELECT; en memoria; carga en CPU relativamente intensa; solo lectura |

### <a name="workload-mix"></a>Combinación de cargas de trabajo

Las transacciones se seleccionan aleatoriamente de una distribución ponderada con la siguiente combinación global. La combinación global presenta una relación de lectura/escritura aproximadamente de 2:1.

| Tipo de transacción | % de combinación |
| --- | --- |
| Lectura ligera |35 |
| Lectura mediana |20 |
| Lectura intensa |5 |
| Actualización ligera |20 |
| Actualización intensa |3 |
| Inserción ligera |3 |
| Inserción intensa |2 |
| Eliminar |2 |
| CPU intensa |10 |

### <a name="users-and-pacing"></a>Usuarios y velocidad

La carga de trabajo de la prueba comparativa está dirigida a partir de una herramienta que envía transacciones a través de un conjunto de conexiones para simular el comportamiento de numerosos usuarios simultáneos. Aunque todas las conexiones y transacciones son generadas a máquina, para simplificar nos referiremos a estas conexiones como “usuarios”. Aunque cada usuario opera independientemente de todos los demás usuarios, todos los usuarios realizan el mismo ciclo de pasos mostrado a continuación:

1. Establecer una conexión de base de datos.
2. Repetir hasta que se señale la salida:
   - Seleccionar una transacción aleatoriamente (a partir de una distribución ponderada).
   - Realizar la transacción seleccionada y medir el tiempo de respuesta.
   - Esperar un retraso de velocidad.
3. Cerrar la conexión de la base de datos.
4. Salir.

El retraso de velocidad (en el paso 2c) se selecciona aleatoriamente, pero con una distribución que tenga un promedio de 1,0 segundos. De este modo, cada usuario puede, en promedio, generar como máximo una transacción por segundo.

### <a name="scaling-rules"></a>Reglas de escalado

El número de usuarios viene determinado por el tamaño de la base de datos (en unidades de factor de escala). Hay un usuario por cada cinco unidades de factor de escala. Debido al retraso de velocidad, un usuario puede, en promedio, generar como máximo una transacción por segundo.

Por ejemplo, una base de datos que tenga un factor de escala 500 (SF=500) tendrá 100 usuarios y podrá alcanzar una velocidad máxima de 100 TPS. Para generar una velocidad de TPS mayor, son necesarios más usuarios y una base de datos mayor.

### <a name="measurement-duration"></a>Duración de la medición

Una ejecución válida de la prueba comparativa precisa una duración de medición en estado fijo de al menos una hora.

### <a name="metrics"></a>Métricas

Las métricas clave de la prueba comparativa son rendimiento y tiempo de respuesta.

- El rendimiento es la medición de rendimiento esencial en la prueba comparativa. El rendimiento se indica en transacciones por unidad de tiempo, contando todos los tipos de transacciones.
- El tiempo de respuesta es una medición de la previsibilidad del rendimiento. La restricción del tiempo de respuesta varía con la clase de servicio, presentando las clases de servicio mayores un requisito de tiempo de respuesta más estricto, como se muestra a continuación.

| Clase de servicio | Medición del rendimiento | Requisito del tiempo de respuesta |
| --- | --- | --- |
| Premium |Transacciones por segundo |Percentil 95 en 0,5 segundos |
| Estándar |Transacciones por minuto |Percentil 90 en 1,0 segundo |
| Básico |Transacciones por hora |Percentil 80 en 2,0 segundos |

> [!NOTE]
> Las métricas de tiempo de respuesta son específicas de la [prueba comparativa de DTU](#dtu-benchmark). Los tiempos de respuesta de otras cargas de trabajo dependen de la carga de trabajo y variarán.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre las opciones de tamaños de proceso y de tamaños de almacenamiento específicas que hay disponibles para las bases de datos únicas, consulte [SQL Database DTU-based resource limits for single databases](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes) (Límites de recursos basados en DTU de SQL Database para bases de datos únicas).
- Para más información sobre las opciones de tamaño de almacenamiento y tamaños de proceso específicas que hay disponibles para los grupos elásticos, consulte [SQL Database DTU-based resource limits](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes) (Límites de recursos basados en DTU de SQL Database).
