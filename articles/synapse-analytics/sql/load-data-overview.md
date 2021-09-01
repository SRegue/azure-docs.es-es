---
title: Diseño de una estrategia de carga de datos de PolyBase para un grupo de SQL dedicado
description: En lugar de un proceso de extracción, transformación y carga (ETL), diseñe uno de extracción, carga y transformación (ETL) para cargar datos con SQL dedicado.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 79be8f9d98816690171476d9c5764884e1b4e0f7
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861204"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Diseño de una estrategia de carga de datos de PolyBase para un grupo de SQL dedicado en Azure Synapse Analytics

Los almacenes de datos SMP tradicionales usan un proceso de extracción, transformación y carga (ETL) para cargar los datos. El grupo de SQL de Azure es una arquitectura de diseño de procesamiento paralelo masivo (MPP) que aprovecha la escalabilidad y la flexibilidad de los recursos de proceso y almacenamiento. El uso de un proceso de extracción, carga y transformación (ELT) puede aprovechar las ventajas de las funcionalidades integradas de procesamiento de consultas distribuidas, y eliminar los recursos necesarios para transformar los datos antes de cargarlos.

Aunque el grupo de SQL admite muchos métodos de carga, incluidas opciones que no son Polybase como BCP y SQL BulkCopy API, la manera más rápida y más escalable para cargar datos es mediante PolyBase.  PolyBase es una tecnología que tiene acceso a datos externos almacenados en Azure Blob Storage o Azure Data Lake Store mediante el lenguaje T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Extracción, carga y transformación (ELT)

Extracción, carga y transformación (ELT) es un proceso mediante el que se extraen datos se extraen de un sistema de origen, se cargan en un almacén de datos y, después, se transforman.

Los pasos básicos para implementar un proceso ELT de PolyBase para un grupo de SQL dedicado son:

1. Extraer los datos de origen en archivos de texto.
2. Llevar los datos a Azure Blob Storage o Azure Data Lake Store.
3. Preparar los datos para la carga.
4. Cargar los datos en las tablas de almacenamiento provisional del grupo de SQL dedicado mediante PolyBase.
5. Transformar los datos.
6. Insertar los datos en tablas de producción.

Si desea un tutorial sobre la carga, consulte [Use PolyBase to load data from Azure blob storage to Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) (Usar PolyBase para cargar datos de Azure Blob Storage en Azure Synapse Analytics).

Para obtener más información, consulte [el blog de patrones de carga](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extraer los datos de origen en archivos de texto

La obtención de datos del sistema de origen depende de la ubicación del almacenamiento.  El objetivo consiste en mover los datos a archivos de texto delimitados compatibles con PolyBase.

### <a name="polybase-external-file-formats"></a>Formatos de archivo externos de PolyBase

PolyBase carga los datos de los archivos de texto delimitados que están codificados mediante UTF-8 y UTF-16. Además de los archivos de texto delimitados, también carga datos desde formatos de archivos Hadoop como RC File, ORC y Parquet. Asimismo, PolyBase puede cargar datos desde Gzip y archivos comprimidos de Snappy. Hay que tener en cuenta que actualmente PolyBase no admite el formato ASCII extendido, el formato de ancho fijo y formatos anidados como WinZip, JSON y XML.

Si está exportando desde SQL Server, puede usar la [herramienta de línea de comandos bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) para exportar los datos en archivos de texto delimitados. La asignación de tipo de datos de Parquet a Azure Synapse Analytics es la siguiente:

| **Tipo de datos de Parquet** |                      **Tipo de datos de SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        TINYINT        |                           TINYINT                            |
|       SMALLINT        |                           SMALLINT                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            FLOAT                             |
|         FLOAT         |                             real                             |
|        double         |                            money                             |
|        double         |                          SMALLMONEY                          |
|        string         |                            NCHAR                             |
|        string         |                           NVARCHAR                           |
|        string         |                             char                             |
|        string         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       timestamp       |                             date                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           datetime                           |
|       timestamp       |                             time                             |
|       date            |                             date                             |
|        Decimal        |                            Decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Llevar los datos a Azure Blob Storage o Azure Data Lake Store

Para llevar los datos a Azure Storage, puede moverlos a [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) o [Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md). Los datos deben almacenarse en archivos de texto en ambas ubicaciones. PolyBase puede cargar esos datos desde cualquiera de estas ubicaciones.

Herramientas y servicios que puede usar para mover datos a Azure Storage:

- El servicio [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) mejora el rendimiento de la red, el rendimiento en general y la capacidad de predicción. ExpressRoute es un servicio que enruta los datos a Azure a través de una conexión privada dedicada. Las conexiones ExpressRoute no enrutan datos a través de la red pública de Internet. Estas conexiones son más fiables y ofrecen velocidades más altas, menores latencias y mayor seguridad que las conexiones habituales a través de Internet.
- La [utilidad AzCopy](../../storage/common/storage-use-azcopy-v10.md) mueve los datos a Azure Storage a través de la red pública de Internet. Esto funciona si el tamaño de los datos es de menos de 10 TB. Para realizar cargas de forma regular con AzCopy, pruebe la velocidad de la red para ver si es aceptable.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md) tiene una puerta de enlace que se puede instalar en el servidor local. A continuación, puede crear una canalización para llevar los datos desde el servidor local hasta Azure Storage. Para usar Data Factory con un grupo de SQL dedicado, consulte [Carga de datos en un grupo de SQL dedicado](../../data-factory/load-azure-sql-data-warehouse.md).

## <a name="3-prepare-the-data-for-loading"></a>3. Preparar los datos para la carga

Puede que tenga que preparar y limpiar los datos de la cuenta de almacenamiento antes de cargarlos en el grupo de SQL dedicado. Puede preparar los datos mientras están en el origen y a medida que los exporta a archivos de texto o después de que estén en Azure Storage.  Si trabaja con los datos en la parte inicial el proceso, será más fácil manejarlos.  

### <a name="define-external-tables"></a>Definir tablas externas

Antes de poder cargar los datos, debe definir tablas externas en el almacén de datos. PolyBase emplea tablas externas para obtener acceso y definir los datos en Azure Storage. Una tabla externa es similar a una vista de base de datos. La tabla externa contiene el esquema de tabla y apunta a los datos que se almacenan fuera del almacén de datos.

Si define tablas externas debe especificar el origen de datos, el formato de los archivos de texto y las definiciones de tabla. A continuación se muestran los temas de sintaxis de T-SQL que necesitará:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>Formato de los archivos de texto

Una vez que se definen los objetos externos, es necesario alinear las filas de los archivos de texto con la tabla externa y la definición de formato de archivo. Los datos de cada fila del archivo de texto deben alinearse con la definición de tabla.
Para dar formato a los archivos de texto:

- Si los datos provienen de un origen no relacional, debe transformarlos en filas y columnas. Si los datos son de un origen relacional o no relacional, se deben transformar para alinearlos con las definiciones de columna de la tabla en la que va a cargar los datos.
- Debe dar formato a datos en el archivo de texto que se alineará con los tipos de datos y columnas en la tabla de destino del grupo de SQL. Si se desalinean los tipos de datos en los archivos de texto externos y la tabla de almacenamiento de datos, las filas se rechazarán durante la carga.
- Debe separar los campos en el archivo de texto con un terminador.  Asegúrese de usar un carácter o una secuencia de caracteres que no se encuentre en los datos de origen. Use el terminador que especificó con la instrucción [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. Carga de los datos en las tablas de almacenamiento provisional del grupo de SQL dedicado mediante PolyBase.

Es una práctica recomendada para cargar datos en una tabla de almacenamiento provisional. Las tablas de almacenamiento provisional le permiten controlar los errores sin interferir con las tablas de producción. Asimismo, una tabla de almacenamiento provisional también ofrece la posibilidad de usar las funcionalidades integradas de procesamiento de consultas distribuidas del grupo de SQL para transformaciones de datos antes de insertar estos datos en tablas de producción.

### <a name="options-for-loading-with-polybase"></a>Opciones para cargar datos con PolyBase

Para cargar datos con PolyBase, puede usar cualquiera de estas opciones de carga:

- [PolyBase con T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) funciona bien cuando los datos están en Azure Blob Storage o Azure Data Lake Store. Le proporciona el máximo control sobre el proceso de carga, pero también es necesario definir objetos de datos externos. Los otros métodos definen estos objetos en segundo plano mientras asigna tablas de origen a las tablas de destino.  Para coordinar la carga de T-SQL, puede utilizar Azure Data Factory, SSIS o Azure Functions.
- [Polybase con SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) funciona bien cuando los datos de origen están en SQL Server. SSIS define las asignaciones de la tabla de origen a la de destino y también organiza la carga. Si ya dispone de paquetes SSIS, puede modificar los paquetes con los que vaya a trabajar con el nuevo destino del almacenamiento de datos.
- [PolyBase con Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md) es otra herramienta de orquestación.  Define una canalización y programa trabajos.
- [PolyBase con Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) transfiere los datos de una tabla de Azure Synapse Analytics para una trama de datos de Databricks o escribe datos de una trama de datos de Databricks en una tabla de Azure Synapse Analytics con PolyBase.

### <a name="non-polybase-loading-options"></a>Opciones de carga que no pertenecen a PolyBase

Si los datos no son compatibles con PolyBase, puede usar [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) o [SQLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy). El formato bcp carga los datos directamente en el grupo de SQL dedicado sin tener que pasar por Azure Blob Storage, y está diseñado únicamente para cargas pequeñas. Tenga en cuenta que el rendimiento de la carga de estas opciones es mucho más lento que PolyBase.

## <a name="5-transform-the-data"></a>5. Transformar los datos

Mientras los datos estén en una tabla de almacenamiento provisional, podrá realizar las transformaciones que sean necesarias para la carga de trabajo. A continuación, lleve los datos a una tabla de producción.

## <a name="6-insert-the-data-into-production-tables"></a>6. Insertar los datos en tablas de producción

La instrucción INSERT INTO ... SELECT lleva los datos de la tabla de almacenamiento provisional a la tabla permanente.

Cuando diseñe un proceso ETL, intente ejecutar el proceso con una pequeña muestra de prueba. Intente extraer 1000 filas de la tabla a un archivo, muévalo a Azure y, a continuación, intente cargarlo en una tabla de almacenamiento provisional.

## <a name="partner-loading-solutions"></a>Soluciones de carga de asociados

Muchos de nuestros asociados tienen soluciones de carga. Para obtener más información, consulte una lista de nuestros [asociados de soluciones](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?context=/azure/synapse-analytics/context/context).

## <a name="next-steps"></a>Pasos siguientes

Para obtener una guía de carga, consulte la [guía para cargar datos](data-loading-best-practices.md).
