---
title: Procedimientos recomendados de desarrollo para SQL de Synapse
description: Recomendaciones y procedimientos recomendados que debe conocer para el desarrollo en SQL de Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: eb107ecfa24de98fc9fab4dd52c8daa568eb852f
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862501"
---
# <a name="development-best-practices-for-synapse-sql"></a>Procedimientos recomendados de desarrollo para SQL de Synapse

En este artículo se describen la guía y los procedimientos recomendados a medida que desarrolla una solución de almacenamiento de datos. 

## <a name="dedicated-sql-pool-development-best-practices"></a>Procedimientos recomendados para el desarrollo de grupos de SQL dedicados

### <a name="reduce-cost-with-pause-and-scale"></a>Menos costos gracias a las características de pausa y escalado

Para más información acerca de cómo reducir los costos mediante la pausa y el escalado, consulte [Administración de procesos](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?context=/azure/synapse-analytics/context/context).

### <a name="maintain-statistics"></a>Mantenimiento de estadísticas

Asegúrese de que actualiza las estadísticas diariamente o después de cada carga.  Existen inconvenientes entre el rendimiento y el costo de crear y actualizar las estadísticas. Si cree que tarda demasiado en realizar el mantenimiento de todas las estadísticas, sea más selectivo sobre qué columnas tienen estadísticas o cuáles necesitan actualizarse con frecuencia.  

Por ejemplo, debería actualizar las columnas de fecha en las que se agregan valores todos los días. 

> [!NOTE]
> Sacará el máximo provecho con las estadísticas en las columnas relacionadas con combinaciones, las columnas que se usan en la cláusula WHERE y las columnas de GROUP BY.

Consulte también [Administración de estadísticas en tablas](develop-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) y [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

### <a name="hash-distribute-large-tables"></a>Distribución Hash para tablas grandes

De forma predeterminada, las tablas se distribuyen según el patrón Round Robin. Esta funcionalidad facilita a los usuarios la tarea de comenzar a crear tablas sin tener que decidir sobre la distribución.  El rendimiento de las tablas round robin puede ser suficiente para algunas cargas de trabajo. Sin embargo, en la mayoría de los casos, seleccionar una columna de distribución funcionará mucho mejor.  

El ejemplo más común de tabla distribuida por una columna que supera con creces a una Round Robin es al combinarse dos tablas grandes de hechos.  

Por ejemplo, si tiene una tabla de pedidos que se distribuye por order_id, y una tabla de transacciones que también se distribuye por order_id, cuando se una la tabla de pedidos a la de transacciones en order_id, esta consulta se convertirá en una consulta de paso a través. 

Esto significa que se eliminan las operaciones de movimiento de datos.  Menos pasos suponen consultas más rápidas.  Menos movimiento de datos también se traduce en consultas más rápidas.

> [!TIP]
> Al cargar una tabla con distribución, asegúrese de que no se ordenan los datos entrantes en la clave de distribución, ya que esto ralentizará la carga.  

Consulte los siguientes vínculos para conocer detalles adicionales sobre cómo la selección de una columna de distribución mejora el rendimiento y cómo definir una tabla distribuida en la cláusula WITH de la instrucción CREATE TABLES.

Consulte también [Información general de tablas](develop-tables-overview.md), [Distribución de tablas](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?context=/azure/synapse-analytics/context/context), [Selección de distribución de tablas](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) y [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

### <a name="do-not-over-partition"></a>Sin particiones excesivas
Crear particiones de datos puede resultar eficaz para el mantenimiento de los datos mediante la modificación de particiones o exámenes de optimización, pero el exceso de particiones puede ralentizar las consultas.  A menudo, una estrategia de creación de particiones con granularidad alta que puede funcionar bien en SQL Server no funciona correctamente en el grupo de SQL dedicado.  

> [!NOTE]
> A menudo, una estrategia de creación de particiones con granularidad alta que puede funcionar bien en SQL Server no funciona correctamente en el grupo de SQL dedicado.  

El exceso de particiones también puede reducir la eficacia de los índices de almacén de columnas agrupadas si cada partición tiene menos de 1 millón de filas. El grupo de SQL dedicado crea particiones de datos en su nombre en 60 bases de datos. 

Por lo tanto, si crea una tabla con 100 particiones, el resultado serán 6000 particiones.  Cada carga de trabajo es diferente, por lo mejor es probar con las particiones para ver qué funciona mejor para la suya.  

Una opción que debe tener en cuenta consiste en usar una granularidad inferior a la que le funcionaba en SQL Server.  Por ejemplo, puede usar particiones semanales o mensuales, en lugar de diarias.

Consulte también [Creación de particiones de tablas](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context).

### <a name="minimize-transaction-sizes"></a>Reducción del tamaño de las transacciones

Las instrucciones INSERT, UPDATE Y DELETE se ejecutan en las transacciones y, cuando fallan, deben deshacerse.  Para que no se tarde tanto en deshacer, reduzca el tamaño de las transacciones siempre que pueda.  Puede hacerlo si divide las instrucciones INSERT, UPDATE y DELETE en partes.  

Por ejemplo, si tiene una instrucción INSERT que se suele tardar 1 hora, puede dividirla en cuatro partes, por lo que cada ejecución duraría 15 minutos.

> [!TIP]
> Aproveche los casos de registro mínimo, como CTAS, TRUNCATE, DROP TABLE o INSERT para vaciar las tablas y así reducir el riesgo de reversión.  

Otra manera de eliminar reversiones es usar funciones de solo metadatos, como la modificación de particiones para la administración de datos.  

Por ejemplo, en lugar de ejecutar una instrucción DELETE para eliminar todas las filas de una tabla cuyo order_date fuera octubre de 2001, podría dividir los datos mensualmente y desactivar la división con los datos de una partición vacía de otra tabla (consulte los ejemplos de ALTER TABLE).  

Para tablas sin particiones, puede usar CTAS en lugar de DELETE para escribir los datos que quiera mantener en una tabla.  Si CTAS tarda lo mismo, es una operación mucho más segura, ya que su registro de transacciones es mínimo y se puede cancelar rápidamente si es necesario.

Consulte también [Introducción a las transacciones](develop-transactions.md), [Optimización de transacciones](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?context=/azure/synapse-analytics/context/context), [Creación de particiones de tablas](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) y [CREATE TABLE AS SELECT (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context).

### <a name="use-the-smallest-possible-column-size"></a>Uso del tamaño de columna mínimo

Al definir el DDL, usar el tipo de datos mínimo compatible con los datos mejorará el rendimiento de la consulta. Esta acción tiene especial importancia para las columnas CHAR y VARCHAR.  

Si el valor mayor máximo de una columna es 25 caracteres, defina la columna como VARCHAR(25).  Evite definir todas las columnas de caracteres con una longitud predeterminada de gran tamaño.  También, defina las columnas como VARCHAR en lugar de NVARCHAR cuando no se necesite nada más.

Consulte también [Información general de tablas](develop-tables-overview.md), [Tipos de datos de las tablas](develop-tables-data-types.md) y [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

### <a name="optimize-clustered-columnstore-tables"></a>Optimización de tablas de almacén de columnas agrupadas

Los índices de almacén de columnas en clúster son una de las maneras más eficaces de almacenar los datos en el grupo de SQL dedicado.  De forma predeterminada, las tablas del grupo de SQL dedicado se crean como almacén de columnas en clúster.  

Para conseguir el máximo rendimiento de las consultas en las tablas de almacén de columnas, es importante la calidad de los segmentos.  Escriben filas en las tablas de almacén de columnas bajo presión de memoria afecta a la calidad de segmento.  

La calidad de segmento se puede medir por el número de filas de un grupo de filas comprimido.  Puede encontrar instrucciones detalladas sobre la detección y mejora de la calidad de los segmentos en las tablas de almacén de columnas agrupadas en la sección [Causas de una calidad deficiente del índice de almacén de columnas](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context#causes-of-poor-columnstore-index-quality) del artículo [Indexación de tablas](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context).  

Como es importante que los segmentos de almacén de columnas sean de una buena calidad, es conveniente usar identificadores de usuario que se encuentren en la clase de recursos grande o mediana para cargar los datos. El uso de [unidades de almacenamiento de datos](resource-consumption-models.md) inferiores significa que desea asignar una clase de recurso mayor para el usuario que realiza la carga.

Dado que las tablas de almacén de columnas, generalmente, no insertan datos en un segmento del almacén de columnas comprimido hasta que hay más de 1 millón de filas por tabla y cada tabla del grupo de SQL dedicado se divide en 60 partes, las tablas de almacén de columnas no serán útiles para las consultas a menos que la tabla tenga más de 60 millones de filas.  

> [!TIP]
> En el caso de las tablas con menos de 60 millones de filas, un índice de almacén de columnas puede no ser la solución óptima.  

Además, si realiza particiones de los datos, recuerde que cada partición deberá tener 1 millón de filas para beneficiarse de un índice de almacén de columnas agrupadas.  Si una tabla tiene 100 particiones, tendrá que tener al menos 6 mil millones de filas para beneficiarse de un almacén de columnas agrupadas (60 distribuciones *100 particiones* 1 millón de filas).  

Si la tabla no tiene 6 mil millones de filas, reduzca el número de particiones o considere la posibilidad de usar una tabla de montón en su lugar.  También puede experimentar para ver si consigue un mejor rendimiento mediante una tabla de montón con índices secundarios, en lugar de con una tabla de almacén de columnas.

Al consultar una tabla de almacén de columnas, las consultas se ejecutarán más rápido si selecciona solo las que necesita.  

Consulte también [Índices de tablas](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context), [Guía de índices de almacén de columnas](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true) y [Recompilación de índices de almacén de columnas](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context#rebuilding-indexes-to-improve-segment-quality).

## <a name="serverless-sql-pool-development-best-practices"></a>Procedimientos recomendados para el desarrollo de grupos de SQL sin servidor

### <a name="general-considerations"></a>Consideraciones generales

El grupo de SQL sin servidor permite consultar archivos de las cuentas de almacenamiento de Azure. No tiene funcionalidades de almacenamiento local o ingesta, lo que significa que todos los archivos que la consulta tiene como destino son externos a SQL sin servidor. Por lo tanto, todo lo relacionado con la lectura de archivos desde el almacenamiento puede afectar al rendimiento de las consultas.

### <a name="colocate-azure-storage-account-and-serverless-sql-pool"></a>Ubicación conjunta de la cuenta de Azure Storage y el grupo de SQL sin servidor

Para minimizar la latencia, ubique conjuntamente la cuenta de Azure Storage y el punto de conexión del grupo de SQL sin servidor. Las cuentas de almacenamiento y los puntos de conexión aprovisionados durante la creación del área de trabajo se encuentran en la misma región.

Para un rendimiento óptimo, si accede a otras cuentas de almacenamiento con el grupo de SQL sin servidor, asegúrese de que se encuentren en la misma región. De lo contrario, aumentará la latencia de la transferencia de red de los datos de la región remota a la región del punto de conexión.

### <a name="azure-storage-throttling"></a>Limitaciones de Azure Storage

Varias aplicaciones y servicios pueden acceder a la cuenta de almacenamiento. Cuando el número de IOPS o el rendimiento combinados que generan las aplicaciones, los servicios y la carga de trabajo del grupo de SQL sin servidor superan los límites de la cuenta de almacenamiento, se producen limitaciones de almacenamiento. Cuando se produce la limitación de almacenamiento, hay un efecto negativo significativo en el rendimiento de las consultas.

Cuando se detecta la limitación, el grupo de SQL sin servidor dispone de controles integrados para este escenario. El grupo de SQL sin servidor realizará solicitudes al almacenamiento a un ritmo más lento hasta que la limitación se resuelva. 

Sin embargo, para que la ejecución de las consultas sea óptima, se recomienda que no fuerce la cuenta de almacenamiento con otras cargas de trabajo durante la ejecución de consultas.

### <a name="prepare-files-for-querying"></a>Preparación de los archivos para la consulta

Si es posible, puede preparar los archivos para mejorar el rendimiento:

- Convierta los archivos CSV a Parquet: Parquet es un formato de columnas. Dado que se trata de un formato comprimido, los tamaños de archivo son menores que los de los archivos CSV con los mismos datos, y un grupo de SQL sin servidor necesita menos tiempo y solicitudes de almacenamiento para leerlos.
- Si una consulta tiene como destino un solo archivo de gran tamaño, se beneficiará de dividirlo en varios archivos más pequeños.
- Pruebe a mantener el tamaño del archivo CSV por debajo de los 10 GB.
- Es preferible tener archivos de igual tamaño para una sola ruta de acceso OPENROWSET o una ubicación de tabla externa.
- Particione los datos al almacenar las particiones en diferentes carpetas o con nombres de archivo distintos: consulte [Uso de las funciones filename y filepath para seleccionar particiones específicas](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Uso de las funciones fileinfo y filepath para seleccionar particiones específicas

A menudo, los datos se organizan en particiones. Puede indicar a un grupo de SQL sin servidor que consulte carpetas y archivos concretos. De este modo se reducirá el número de archivos y la cantidad de datos que la consulta tiene que leer y procesar. 

Por lo tanto, conseguirá un mejor rendimiento. Para obtener más información, consulte las funciones [filename](query-data-storage.md#filename-function) y [filepath](query-data-storage.md#filepath-function), así como ejemplos sobre cómo [consultar archivos específicos](query-specific-files.md).

Si los datos en el almacenamiento no tienen particiones, considere la posibilidad de crear particiones para usar estas funciones en la optimización de las consultas que están dirigidas a esos archivos.

Cuando [consulte las tablas externas de Apache Spark para Azure Synapse con particiones](develop-storage-files-spark-tables.md) desde el grupo de SQL sin servidor, automáticamente la consulta tendrá como destino solo los archivos necesarios.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Uso de CETAS para mejorar el rendimiento de las consultas y las combinaciones

[CETAS](develop-tables-cetas.md) es una de las características más importantes que están disponibles en el grupo de SQL sin servidor. CETAS es una operación en paralelo que crea metadatos de tabla externa y exporta el resultado de la consulta SELECT a un conjunto de archivos de la cuenta de almacenamiento.

Puede usar CETAS para almacenar en un nuevo conjunto de archivos las partes de las consultas que se usan frecuentemente, como las tablas de referencia combinadas. Más adelante, podrá realizar combinaciones con esta tabla externa única, en lugar de repetir combinaciones comunes en varias consultas. 

Cuando CETAS genera archivos Parquet, las estadísticas se crean automáticamente cuando la primera consulta selecciona como destino a esta tabla externa y se mejora el rendimiento.

### <a name="next-steps"></a>Pasos siguientes

Si necesita información que no esté incluida en este artículo, use la función de **búsqueda de documentación** en el lado izquierdo de esta página para buscar en todos los documentos del grupo de SQL.  En la [página de preguntas y respuestas de Microsoft para Azure Synapse Analytics](/answers/topics/azure-synapse-analytics.html) puede publicar preguntas para otros usuarios y para el grupo del producto de Azure Synapse Analytics. Supervisamos continuamente este foro para garantizar que sus preguntas las responde otro usuario o alguno de nosotros.  

Si prefiere formular sus preguntas en Stack Overflow, también tenemos un [foro de Stack Overflow acerca de Azure Synapse Analytics](https://stackoverflow.com/questions/tagged/azure-sqldw).
