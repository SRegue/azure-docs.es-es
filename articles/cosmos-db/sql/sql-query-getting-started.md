---
title: Introducción a las consultas SQL en Azure Cosmos DB
description: Obtenga información sobre cómo usar consultas SQL para consultar datos de Azure Cosmos DB. Puede cargar datos de ejemplo en un contenedor en Azure Cosmos DB y consultarlos.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: tisande
ms.openlocfilehash: af71ff52ead86c0cdeea307e736c36ac27536bfa
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029946"
---
# <a name="getting-started-with-sql-queries"></a>Introducción a las consultas SQL
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

En las cuentas de la API de SQL de Azure Cosmos DB, hay dos maneras de leer los datos:

**Lecturas puntuales**: puede realizar una búsqueda de clave y valor en un único *id. de elemento* y clave de partición. La combinación de *id. de elemento* y clave de partición es la clave y el propio elemento es el valor. En el caso de un documento de 1 KB, las lecturas puntuales normalmente cuestan 1 [unidad de solicitud](../request-units.md) con una latencia inferior a 10 ms. Las lecturas puntuales devuelven un único elemento.

Estos son algunos ejemplos de cómo realizar **lecturas puntuales** con cada SDK:

- [SDK de .NET](/dotnet/api/microsoft.azure.cosmos.container.readitemasync)
- [SDK de Java](/java/api/com.azure.cosmos.cosmoscontainer.readitem#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [SDK de Node.js](/javascript/api/@azure/cosmos/item#read-requestoptions-)
- [SDK de Python](/python/api/azure-cosmos/azure.cosmos.containerproxy#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

**Consultas SQL**: puede consultar datos escribiendo consultas mediante el Lenguaje de consulta estructurado (SQL) como lenguaje de consulta JSON. Las consultas siempre cuestan al menos 2,3 unidades de solicitud y, en general, tendrán una latencia mayor y más variable que las lecturas puntuales. Las consultas pueden devolver muchos elementos.

La mayoría de las cargas de trabajo con mucha actividad de lectura en Azure Cosmos DB utilizan una combinación de lecturas puntuales y consultas SQL. Si solo necesita leer un único elemento, las lecturas puntuales son más baratas y rápidas que las consultas. Las lecturas puntuales no necesitan usar el motor de consultas para tener acceso a los datos y pueden leer los datos directamente. Por supuesto, no es posible que todas las cargas de trabajo lean los datos exclusivamente mediante lecturas puntuales, por lo que la compatibilidad con SQL como lenguaje de consulta y la [indexación independiente del esquema](../index-overview.md) proporcionan una manera más flexible de acceder a los datos.

Estos son algunos ejemplos de cómo realizar **consultas SQL** con cada SDK:

- [SDK de .NET](../sql-api-dotnet-v3sdk-samples.md#query-examples)
- [SDK de Java](../sql-api-java-sdk-samples.md#query-examples)
- [SDK de Node.js](../sql-api-nodejs-samples.md#item-examples)
- [SDK de Python](../sql-api-python-samples.md#item-examples)

En el resto de este documento se muestra cómo empezar a escribir consultas SQL en Azure Cosmos DB. Las consultas SQL se pueden ejecutar mediante el SDK o Azure Portal.

## <a name="upload-sample-data"></a>Cargar datos de ejemplo

En la cuenta de la API de SQL para Cosmos DB, abra el [Explorador de datos](../data-explorer.md) para crear un contenedor denominado `Families`. Después de crear el contenedor, use el explorador de estructuras de datos para buscarlo y abrirlo. En el contenedor `Families` verá la opción `Items` justo debajo del nombre del contenedor. Abra dicha opción y se mostrará un botón, en la barra de menús en el centro de la pantalla, para crear un "Nuevo elemento". Deberá usar esta característica para crear los siguientes elementos JSON.

### <a name="create-json-items"></a>Creación de elementos JSON

Los dos elementos JSON siguientes son documentos sobre las familias Andersen y Wakefield. Incluyen información sobre sus padres, hijos y mascotas, así como su dirección y la información de registro. 

El primer elemento tiene cadenas, números, valores booleanos, matrices y propiedades anidadas:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

El segundo elemento usa `givenName` y `familyName`, en lugar de `firstName` y `lastName`:

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Consulta de los elementos JSON

Pruebe algunas consultas con los datos JSON para entender algunos aspectos clave del lenguaje de consulta SQL de Azure Cosmos DB.

La consulta siguiente devuelve los elementos en los que el campo `id` coincide con `AndersenFamily`. Puesto que es una consulta `SELECT *`, la salida de la consulta es el elemento JSON completo. Para más información sobre la sintaxis de SELECT, consulte [Instrucción SELECT](sql-query-select.md).

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Los resultados de la consulta son:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

La siguiente consulta vuelve a formatear la salida JSON en una forma diferente. La consulta proyecta un nuevo objeto JSON `Family` con dos campos seleccionados, `Name` y `City`, cuando la ciudad de la dirección es la misma que el estado. "NY, NY" coincide con esto.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Los resultados de la consulta son:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

La consulta siguiente devuelve todos los nombres proporcionados de los hijos de la familia cuyo `id` coincide con `WakefieldFamily`, ordenados por ciudad.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Los resultados son:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Observaciones

Los ejemplos anteriores muestran varios aspectos del lenguaje de consulta de Cosmos DB:  

* Como la API de SQL trabaja en valores JSON, trata con entidades en forma de árbol en lugar de con filas y columnas. Puede hacer referencia a los nodos de árbol de cualquier profundidad arbitraria, como `Node1.Node2.Node3…..Nodem`, de forma similar a la referencia de dos partes de `<table>.<column>` en SQL ANSI.

* Dado que el lenguaje de consulta funciona con datos sin esquema, el sistema de tipos debe enlazarse dinámicamente. La misma expresión podría generar diversos tipos en distintos elementos. El resultado de una consulta es un valor JSON válido, pero no se garantiza que sea de un esquema fijo.  

* Azure Cosmos DB solo admite elementos JSON estrictos. El sistema de tipos y las expresiones se restringen para tratar únicamente con tipos JSON. Para más información, consulte la [especificación de JSON](https://www.json.org/).  

* Un contenedor de Cosmos es una colección sin esquemas de elementos JSON. Las relaciones tanto dentro de los elementos de un contenedor como entre ellos se capturan de manera implícita por independencia, no por las relaciones entre la clave principal y la clave externa. Esta característica es importante para las combinaciones entre los elementos que se describen en [Combinaciones en Azure Cosmos DB](sql-query-join.md).

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Azure Cosmos DB](../introduction.md)
- [Ejemplos de .NET de Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula SELECT](sql-query-select.md)
- ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
    - Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
    - Si conoce las velocidades de solicitud típicas de la carga de trabajo de la base de datos actual, lea sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](../estimate-ru-with-capacity-planner.md).
