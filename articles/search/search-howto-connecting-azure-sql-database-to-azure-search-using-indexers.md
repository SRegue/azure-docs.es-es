---
title: Indexación de datos de Azure SQL
titleSuffix: Azure Cognitive Search
description: Configure un indexador de Azure SQL para automatizar la indexación de contenido y metadatos en la búsqueda de texto completo en Azure Cognitive Search.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/26/2021
ms.openlocfilehash: 27cebeb9eaff63d9acb7976cac5a8837cca5e702
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2021
ms.locfileid: "112983291"
---
# <a name="index-data-from-azure-sql"></a>Indexación de datos de Azure SQL

En este artículo se muestra cómo configurar un indexador de Azure SQL para extraer contenido y hacer que se pueda buscar en Azure Cognitive Search. En este flujo de trabajo se crea un índice de búsqueda en Azure Cognitive Search y se carga con contenido existente extraído de Azure SQL Database y las instancias administradas de Azure SQL.

En este artículo se describe la mecánica del uso de [indexadores](search-indexer-overview.md), pero también se describen las características que solo están disponibles con Azure SQL Database o SQL Managed Instance (por ejemplo, el seguimiento de cambios integrado).

Los indexadores de Azure SQL se pueden configurar mediante cualquiera de estos clientes:

* [Azure Portal](https://ms.portal.azure.com)
* [API REST](/rest/api/searchservice/Indexer-operations) de Azure Cognitive Search
* [SDK para .NET](/dotnet/api/azure.search.documents.indexes.models.searchindexer) de Azure Cognitive Search

En este artículo, se usan las API REST. 

## <a name="prerequisites"></a>Requisitos previos

* Los datos proceden de una sola tabla o vista. Si los datos están distribuidos entre varias tablas, puede crear una única vista de los datos. Un inconveniente del uso de vistas es que no podrá usar la detección de cambios integrada de SQL Server para actualizar un índice con cambios incrementales. Para más información, vea [Capturar filas cambiadas y eliminadas](#CaptureChangedRows) a continuación.

* Los tipos de datos deben ser compatibles. En los índices de búsqueda se admiten la mayoría de los tipos de SQL, pero no todos ellos. Para obtener una lista, vea [Asignación de tipos de datos](#TypeMapping).

* Las conexiones a una instancia de SQL Managed Instance deben realizarse a través de un punto de conexión público. Para más información, consulte [Conexiones de indexador a través de un punto de conexión público](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md).

* Las conexiones con SQL Server en una máquina virtual de Azure requieren la configuración manual de un certificado de seguridad. Para más información, consulte [Conexiones de indexador SQL Server en una máquina virtual de Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

La sincronización de datos en tiempo real no debe ser un requisito de la aplicación. Un indexador puede volver a indexar la tabla cada cinco minutos como máximo. Si los datos cambian con frecuencia y esos cambios deben reflejarse en el índice en cuestión de segundos o minutos, se recomienda usar la [API REST](/rest/api/searchservice/AddUpdate-or-Delete-Documents) o el [SDK de .NET](search-get-started-dotnet.md) para insertar directamente las filas actualizadas.

Se permite la indexación incremental. Si tiene un conjunto de datos grande y prevé ejecutar el indexador en una programación, Búsqueda cognitiva de Azure debe ser capaz de identificar de forma eficaz las filas nuevas, cambiadas o eliminadas. Solo se permite la indexación no incremental si se indexa a petición (no en una programación), o se indexan menos de 100 000 filas. Para más información, vea [Capturar filas cambiadas y eliminadas](#CaptureChangedRows) a continuación.

Azure Cognitive Search admite la autenticación de SQL Server, donde el nombre de usuario y la contraseña se proporcionan en la cadena de conexión. Como alternativa, puede configurar una identidad administrada y usar roles de Azure para omitir las credenciales en la conexión. Para más información, consulte [Configuración de una conexión de indexador mediante una identidad administrada](search-howto-managed-identities-sql.md).

## <a name="create-an-azure-sql-indexer"></a>Crear un indexador de Azure SQL

1. Cree el origen de datos:

   ```http
    POST https://myservice.search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   La cadena de conexión puede seguir cualquiera de los formatos siguientes:
    1. Puede obtener la cadena de conexión de [Azure Portal](https://portal.azure.com): use la opción `ADO.NET connection string`.
    1. Una cadena de conexión de identidad administrada que no incluye una clave de cuenta con el siguiente formato: `Initial Catalog|Database=<your database name>;ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Sql/servers/<your SQL Server name>/;Connection Timeout=connection timeout length;`. Para usar esta cadena de conexión, siga las instrucciones de [Configuración de una conexión de indexador a una base de datos de Azure SQL Database mediante una identidad administrada](search-howto-managed-identities-sql.md).

2. Si aún no tiene un índice de Búsqueda cognitiva de Azure de destino, créelo. Puede crear un índice mediante el [portal](https://portal.azure.com) o la [API de creación de índices](/rest/api/searchservice/Create-Index). Asegúrese de que el esquema del índice de destino sea compatible con el de la tabla de origen; consulte la [asignación entre tipos de datos de SQL y tipos de datos de Búsqueda cognitiva de Azure](#TypeMapping).

3. Cree el indexador asignándole un nombre y haciendo referencia al índice de origen y destino de datos:

   ```http
    POST https://myservice.search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
   ```

Un indizador creado de esta forma no tiene una programación. Se ejecuta automáticamente una vez en cuanto se crea. Puede volver a ejecutarlo en cualquier momento mediante una solicitud **ejecutar indizador** :

```http
    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2020-06-30
    api-key: admin-key
```

Puede personalizar varios aspectos del comportamiento del indexador, como el tamaño de lote y el número de documentos que se puede omitir antes de que la ejecución de un indexador produzca un error. Para más información, consulte [Create Indexer API](/rest/api/searchservice/Create-Indexer)(API para crear índices).

Puede que necesite permitir que los servicios de Azure se conecten a la base de datos. Consulte [Conectarse desde Azure](../azure-sql/database/firewall-configure.md) para obtener instrucciones sobre cómo hacerlo.

Para supervisar el estado del indizador y el historial de ejecución (número de elementos indizados, errores, etc.), use una solicitud **estado del indizador** :

```http
    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2020-06-30
    api-key: admin-key
```

La respuesta debe ser similar a la siguiente:

```json
    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }
```

El historial de ejecución contiene como máximo las 50 ejecuciones completadas más recientemente en orden cronológico inverso (la ejecución más reciente aparece en primer lugar).
Puede encontrar información adicional sobre la respuesta en [Obtener el estado del indizador](/rest/api/searchservice/get-indexer-status)

## <a name="run-indexers-on-a-schedule"></a>Ejecutar indexadores según una programación

También puede disponer que el indizador se ejecute periódicamente según una programación. Para ello, agregue la propiedad **schedule** al crear o actualizar el indexador. El ejemplo siguiente muestra una solicitud PUT para actualizar el indizador:

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

El parámetro **interval** es obligatorio. El intervalo se refiere al tiempo entre el inicio de dos ejecuciones consecutivas de indizador. El intervalo mínimo permitido es de 5 minutos y el máximo de un día. Debe tener el formato de un valor "dayTimeDuration" XSD (subconjunto restringido de un valor de [duración ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). El patrón de este es: `P(nD)(T(nH)(nM))`. Ejemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

Para más información sobre cómo definir las programaciones del indexador, consulte [Programación de indexadores para Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Capturar filas nuevas, cambiadas y eliminadas

Búsqueda cognitiva de Azure usa la **indexación incremental** para evitar tener que volver a indexar la tabla o vista completa cada vez que se ejecuta un indexador. Búsqueda cognitiva de Azure proporciona dos directivas de detección de cambios para admitir la indexación incremental. 

### <a name="sql-integrated-change-tracking-policy"></a>Directiva de seguimiento de cambios integrada de SQL

Si la base de datos SQL admite el [seguimiento de cambios](/sql/relational-databases/track-changes/about-change-tracking-sql-server), se recomienda usar la **directiva de seguimiento de cambios integrada de SQL**. Esta es la directiva más eficiente. Además, permite que Búsqueda cognitiva de Azure identifique las filas eliminadas sin tener que agregar a la tabla una columna de "eliminación temporal" explícita.

#### <a name="requirements"></a>Requisitos 

+ Requisitos de versión de la base de datos:
  * SQL Server 2012 SP3 y versiones posteriores, si usa SQL Server en máquinas virtuales de Azure.
  * Azure SQL Database o SQL Managed Instance.
+ Solo tablas (vistas no). 
+ En la base de datos, [habilite el seguimiento de los cambios](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) para la tabla. 
+ Ninguna clave principal compuesta (una clave principal que contiene más de una columna) en la tabla.  

#### <a name="usage"></a>Uso

Para usar esta directiva, cree o actualice el origen de datos de la siguiente manera:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }
```

Al usar la directiva de seguimiento de cambios integrada de SQL, no especifique una directiva de detección de eliminación de datos independiente, ya que esta directiva tiene compatibilidad integrada para identificar las filas eliminadas. Sin embargo, para que las eliminaciones se detecten "mágica y automáticamente", la clave de documento del índice de búsqueda debe ser la misma clave principal en la tabla SQL. 

> [!NOTE]  
> Cuando se usa [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql) para quitar un gran número de filas de una tabla SQL, debe [restablecer](/rest/api/searchservice/reset-indexer) el indexador para que se restablezca Seguimiento de cambios y recopile las eliminaciones de filas.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Directiva de detección de cambios de límite superior

Esta directiva de detección de cambios se basa en una columna de "marca de límite superior" que captura la versión o la hora en que se actualizó por última vez una fila. Si usa una vista, debe usar una directiva de marca de límite superior. La columna de marca de límite superior debe cumplir los siguientes requisitos.

#### <a name="requirements"></a>Requisitos 

* Todas las inserciones especifican un valor para la columna.
* Todas las actualizaciones de un elemento también cambian el valor de la columna.
* El valor de esta columna aumenta con cada inserción o actualización.
* Las consultas con las cláusulas WHERE y ORDER BY siguientes se pueden ejecutar de forma eficaz: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`.

> [!IMPORTANT] 
> Se recomienda encarecidamente usar el tipo de datos [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) para la columna de marca de límite superior. Si se utiliza cualquier otro tipo de datos, no se garantiza que el seguimiento de cambios capture todos los cambios en el caso de transacciones que se ejecutan simultáneamente con una consulta de indexador. Cuando se usa **rowversion** en una configuración con réplicas de solo lectura, debe señalar el indexador a la réplica principal. Solo se puede usar una réplica principal para escenarios de sincronización de datos.

#### <a name="usage"></a>Uso

Para usar una directiva de marca de límite superior, cree o actualice el origen de datos de la siguiente manera:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }
```

> [!WARNING]
> Si la tabla de origen no tiene un índice en la columna de límite superior, las consultas utilizadas por el indexador SQL pueden agotar el tiempo de espera. En concreto, la cláusula `ORDER BY [High Water Mark Column]` requiere un índice para ejecutarse de forma eficaz cuando la tabla contiene muchas filas.
>
>

<a name="convertHighWaterMarkToRowVersion"></a>

##### <a name="converthighwatermarktorowversion"></a>convertHighWaterMarkToRowVersion

Si usa un tipo de datos [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) para la columna de marca de límite superior, considere la posibilidad de usar la opción de configuración indexador `convertHighWaterMarkToRowVersion`. `convertHighWaterMarkToRowVersion` hace dos cosas:

* Utilice el tipo de datos rowversion para la columna de marca de límite superior en la consulta SQL del indexador. El uso del tipo de datos correcto mejora el rendimiento de las consultas de indexador.
* Reste 1 del valor rowversion antes de que se ejecute la consulta de indexador. Las vistas con una o varias combinaciones pueden tener filas con valores rowversion duplicados. Restar 1 garantiza que la consulta de indexador no pierde estas filas.

Para habilitar esta característica, cree o actualice el indexador con la siguiente configuración:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "convertHighWaterMarkToRowVersion" : true } }
    }
```

<a name="queryTimeout"></a>

##### <a name="querytimeout"></a>queryTimeout

Si se producen errores de tiempo de espera, puede utilizar la configuración de indexador `queryTimeout` para establecer el tiempo de espera de consulta en un valor mayor que el tiempo de espera predeterminado de 5 minutos. Por ejemplo, para establecer el tiempo de espera en 10 minutos, cree o actualice el indexador con la siguiente configuración:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

<a name="disableOrderByHighWaterMarkColumn"></a>

##### <a name="disableorderbyhighwatermarkcolumn"></a>disableOrderByHighWaterMarkColumn

También puede deshabilitar la cláusula `ORDER BY [High Water Mark Column]`. Sin embargo, esto no se recomienda porque si se interrumpe la ejecución del indexador por un error, dicho indexador tiene que volver a procesar todas las filas si se ejecuta más tarde, incluso si ya ha procesado casi todas las filas en el momento en el que se interrumpió. Para deshabilitar la cláusula `ORDER BY`, utilice la configuración `disableOrderByHighWaterMarkColumn` en la definición del indexador:  

```
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }
```

### <a name="soft-delete-column-deletion-detection-policy"></a>Directiva de detección de eliminación de columna de eliminación temporal
Cuando se eliminan filas de la tabla de origen, probablemente le interesará eliminar dichas filas del índice de búsqueda. Si usa la directiva de seguimiento de cambios integrada de SQL, esto se lleva a cabo automáticamente. Sin embargo, la directiva el seguimiento de cambios de marca de límite superior no le ayuda con las filas eliminadas. ¿Qué debe hacer?

Si las filas se quitaron físicamente de la tabla, Búsqueda cognitiva de Azure no tiene forma de deducir la presencia de registros que ya no existen.  Sin embargo, puede utilizar la técnica de "eliminación temporal" para eliminar filas lógicamente sin quitarlas de la tabla. Agregue una columna a la tabla o vista y marque filas como eliminadas mediante esa columna.

Cuando use la técnica de la eliminación temporal, puede especificar la directiva de eliminación temporal de la manera que se indica a continuación al crear o actualizar el origen de datos:

```
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

**softDeleteMarkerValue** debe ser una cadena. Use la representación de cadena del valor real. Por ejemplo, si tiene una columna de enteros donde las filas eliminadas se marcan con el valor 1, use `"1"`. Si tiene una columna BIT donde las filas eliminadas se marcan con el valor booleano true, use el literal de cadena `True` o `true`, no importan las mayúsculas y minúsculas.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Asignación entre tipos de datos de SQL y de Búsqueda cognitiva de Azure
| Tipo de datos de SQL | Tipos de campos de índice de destino permitidos | Notas |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, numérico decimal de dinero |Edm.String |Búsqueda cognitiva de Azure no admite la conversión de tipos decimales en Edm.Double porque esto podría provocar la pérdida de precisión |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Una cadena SQL se puede usar para rellenar un campo Collection (EDM.String) si la cadena representa una matriz JSON de cadenas: `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| geography |Edm.GeographyPoint |Solo se admiten instancias de geography de tipo POINT con SRID 4326 (que es el valor predeterminado) |
| rowversion |N/D |Las columnas de versión de la fila no se pueden almacenar en el índice de búsqueda, pero pueden usarse para el seguimiento de cambios |
| time, timespan, binary, varbinary, image, xml, geometry, tipos CLR |N/D |No compatible |

## <a name="configuration-settings"></a>Valores de configuración
El indexador de SQL expone varios valores de configuración:

| Configuración | Tipo de datos | Propósito | Valor predeterminado |
| --- | --- | --- | --- |
| queryTimeout |string |Establece el tiempo de espera para la ejecución de consultas SQL |5 minutos ("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |Hace que la consulta SQL utilizada por la directiva de límite superior omita la cláusula ORDER BY. Consulte [Directiva de detección de cambios de límite superior](#HighWaterMarkPolicy). |false |

Esta configuración se utiliza en el objeto `parameters.configuration` en la definición del indexador. Por ejemplo, para establecer el tiempo de espera de consulta en 10 minutos, cree o actualice el indexador con la siguiente configuración:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

## <a name="faq"></a>Preguntas más frecuentes

**P: ¿Puedo usar un indizador de SQL Azure con Bases de datos SQL que se ejecutan en máquinas virtuales de IaaS en Azure?**

Sí. Sin embargo, debe permitir que el servicio de búsqueda se conecte a su base de datos. Consulte el artículo [Configuración de una conexión desde un indexador de Búsqueda cognitiva de Azure a SQL Server en una máquina virtual de Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) para más información.

**P: ¿Puedo usar un indizador de SQL Azure con Bases de datos SQL que se ejecutan localmente?**

No directamente. No se recomienda ni se admite una conexión directa, ya que requeriría abrir las bases de datos al tráfico de Internet. Los clientes han realizado correctamente este escenario mediante tecnologías de puente como Azure Data Factory. Para más información, vea [Inserción de datos en un índice de Búsqueda cognitiva de Azure mediante el uso de Azure Data Factory](../data-factory/v1/data-factory-azure-search-connector.md).

**P: P: ¿Puedo usar un indexador de Azure SQL con bases de datos que no son de SQL Server que se ejecutan en IaaS en Azure?**

No. No admitimos esta posibilidad, ya que no hemos probado el indexador con bases de datos que no son de SQL Server.  

**P: ¿Puedo crear varios indizadores que se ejecuten según una programación?**

Sí. Sin embargo, solo puede ejecutarse un indizador en un nodo de cada vez. Si necesita varios indizadores que se ejecuten simultáneamente, considere la posibilidad de escalar verticalmente el servicio de búsqueda a más de una unidad de búsqueda.

**P: ¿Afecta la ejecución de un indexador a la carga de trabajo de la consulta?**

Sí. El indizador se ejecuta en uno de los nodos del servicio de búsqueda, y los recursos de dicho nodo se reparten entre la indización y la prestación de servicios al tráfico de consultas y otras solicitudes de API. Si al ejecutar cargas de trabajo intensivas de indexación y consulta detecta una frecuencia alta de errores 503 o el aumento de los tiempos de respuesta, considere la posibilidad de [escalar verticalmente el servicio de búsqueda](search-capacity-planning.md).

**P: ¿Puedo usar una réplica secundaria en un [clúster de conmutación por error](../azure-sql/database/auto-failover-group-overview.md) como un origen de datos?**

Depende. Para la indexación completa de una tabla o vista, puede usar una réplica secundaria. 

Para la indexación incremental, Búsqueda cognitiva de Azure admite dos directivas de detección de cambios: Directiva de seguimiento de cambios integrada de SQL y directiva de detección de cambios de límite superior.

En las réplicas de solo lectura, la SQL Database no admite el seguimiento de cambios integrado. Por tanto, debe usar la directiva Marca de límite superior. 

La recomendación estándar es usar el tipo de datos rowversion para la columna de marca de límite superior. Pero el uso de rowversion se basa en la función `MIN_ACTIVE_ROWVERSION`, que no se admite en las réplicas de solo lectura. Por tanto, si usa rowversion debe apuntar el indexador a una réplica principal.

Si intenta usar rowversion en una réplica de solo lectura, verá el siguiente error: 

"No se admite el uso de una columna rowversion para el seguimiento de cambios en réplicas de disponibilidad secundarias (de solo lectura). Actualice el origen de datos y especifique una conexión con la réplica de disponibilidad principal. La propiedad actual de "Updateability" de la base de datos es "READ_ONLY"".

**P: ¿Puedo usar una columna alternativa, que no sea rowversion, para el seguimiento de los cambios de marca de límite superior?**

No se recomienda. Solo **rowversion** permite la sincronización de datos confiable. Pero según la lógica de aplicación, puede ser seguro si:

+ Se puede asegurar de que, cuando se ejecuta el indexador, no hay ninguna transacción pendiente en la tabla que se indexa (por ejemplo, todas las actualizaciones de tabla tienen lugar como un lote según una programación y la programación del indexador de Búsqueda cognitiva de Azure está establecida para evitar la superposición con la programación de las actualizaciones de tabla).  

+ Vuelve a indexar periódicamente para seleccionar las filas que faltan.