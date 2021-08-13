---
title: Admisión y compatibilidad de Gremlin de Azure Cosmos DB con las características de TinkerPop
description: Más información acerca del lenguaje de Gremlin de Apache TinkerPop. Aprenda sobre las características y los pasos disponibles en Azure Cosmos DB y las diferencias de compatibilidad del motor de grafos de TinkerPop.
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/06/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: a2e868fa6054681f37d699bdc469db9ded0b8ee9
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113356372"
---
# <a name="azure-cosmos-db-gremlin-graph-support-and-compatibility-with-tinkerpop-features"></a>Compatibilidad de los grafos de Gremlin de Azure Cosmos DB con las características de TinkerPop
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB admite el lenguaje de recorrido de grafo de [Apache Tinkerpop](https://tinkerpop.apache.org) conocido como [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps). Puede usar el lenguaje Gremlin para crear entidades de grafo (vértices y aristas), modificar las propiedades de las entidades, realizar consultas y recorridos, y eliminar entidades.

El motor de Azure Cosmos DB Graph sigue estrechamente la especificación de los pasos de recorrido de [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), pero hay diferencias en la implementación específicas de Azure Cosmos DB. En este artículo se proporciona un tutorial rápido de Gremlin y se enumeran las características de Gremlin que se admiten en Gremlin API.

## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatibles

En la siguiente tabla se muestran los controladores Gremlin populares que puede usar en Azure Cosmos DB:

| Descargar | Source | Introducción | Versión del conector admitida |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.4.6/reference/#gremlin-DotNet) | [Gremlin.NET en GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Crear gráfico con .NET](create-graph-dotnet.md) | 3.4.6 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Crear gráfico con Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript en GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Crear gráfico con Node.js](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python en GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Crear gráfico con Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP en GitHub](https://github.com/PommeVerte/gremlin-php) | [Crear gráfico con PHP](create-graph-php.md) | 3.1.0 |
| [Go Lang](https://github.com/supplyon/gremcos/) | [Go Lang](https://github.com/supplyon/gremcos/) | | Colaboradores externos han creado esta biblioteca. El equipo de Azure Cosmos DB no ofrece ningún soporte técnico ni mantiene la biblioteca. |
| [Gremlin Console](https://tinkerpop.apache.org/downloads.html) | [Documentación de TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Crear gráfico con la consola de Gremlin](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Objetos de grafos permitidos

TinkerPop es una norma que abarca una amplia gama de tecnologías de grafos. Por lo tanto, usa una terminología estándar para describir qué características proporciona un proveedor de grafos. Azure Cosmos DB proporciona una base de datos de grafos de escritura, persistente y de alta simultaneidad que se puede dividir entre varios servidores o clústeres. 

En la tabla siguiente se enumeran las características de TinkerPop implementadas por Azure Cosmos DB: 

| Category | Implementación de Azure Cosmos DB |  Notas | 
| --- | --- | --- |
| Características de grafos | Proporciona persistencia y acceso simultáneo. Diseñado para admitir transacciones | Los métodos de proceso se pueden implementar mediante el conector de Spark. |
| Características de variables | Admite valores Boolean, Integer, Byte, Double, Float, Integer, Long, String. | Admite tipos primitivos; es compatible con tipos complejos mediante el modelo de datos. |
| Características de vértices | Admite RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty.  | Permite crear, modificar y eliminar vértices. |
| Características de propiedades de vértices | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Permite crear, modificar y eliminar propiedades de vértices. |
| Características de aristas | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Permite crear, modificar y eliminar aristas. |
| Características de propiedades de aristas | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Permite crear, modificar y eliminar propiedades de aristas. |

## <a name="gremlin-wire-format"></a>Formato de alambre de Gremlin

Azure Cosmos DB usa el formato JSON al devolver los resultados de las operaciones de Gremlin. Actualmente, Azure Cosmos DB admite el formato JSON. Por ejemplo, el fragmento de código siguiente muestra una representación de JSON de un vértice *devuelto al cliente* desde Azure Cosmos DB:

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

A continuación, se describen las propiedades que usa el formato JSON para los vértices:

| Propiedad | Descripción | 
| --- | --- | --- |
| `id` | Identificador del vértice. Debe ser único (en combinación con el valor de `_partition`, si corresponde). Si no se especifica ningún valor se suministrarán automáticamente con un GUID | 
| `label` | Etiqueta del vértice. Esta propiedad se utiliza para describir el tipo de entidad. |
| `type` | Se usa para distinguir los vértices de los documentos que no son grafos. |
| `properties` | Contenedor de propiedades definidas por el usuario asociadas con el vértice. Cada propiedad puede tener varios valores. |
| `_partition` | Clave de partición del vértice. Se una para la [creación de particiones de grafos](graph-partitioning.md). |
| `outE` | Esta propiedad contiene una lista de las aristas de un vértice. Almacenar la información de proximidad con los vértices permite ejecutar recorridos rápidamente. Las aristas se agrupan en función de sus etiquetas. |

Cada propiedad puede almacenar varios valores dentro de una matriz.

| Propiedad | Descripción |
| --- | --- |
| `value` | El valor de la propiedad. |

La arista contiene la siguiente información para ayudar a la navegación a otras partes del grafo.

| Propiedad | Descripción |
| --- | --- |
| `id` | Identificador de la arista. Debe ser único (en combinación con el valor de `_partition`, si corresponde). |
| `label` | Etiqueta de la arista. Esta propiedad es opcional y se usa para describir el tipo de relación. |
| `inV` | Esta propiedad contiene una lista de vértices para una línea. Almacenar la información de proximidad con la línea permite ejecutar recorridos rápidamente. Los vértices se agrupan en función de sus etiquetas. |
| `properties` | Contenedor de propiedades definidas por el usuario asociadas con la arista. |

## <a name="gremlin-steps"></a>Pasos de Gremlin

Ahora, echemos un vistazo a los pasos de Gremlin que Azure Cosmos DB admite. Para una referencia completa de Gremlin, consulte la [referencia de TinkerPop](https://tinkerpop.apache.org/docs/3.3.2/reference).

| paso | Descripción | Documentación de TinkerPop 3.2. |
| --- | --- | --- |
| `addE` | Agrega una arista entre dos vértices. | [Paso addE](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Agrega un vértice al grafo. | [Paso addV](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Garantiza que todos los recorridos devuelven un valor. | [y un paso](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step). |
| `as` | Modulador de pasos para asignar una variable a la salida de un paso. | [paso as](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | Modulador de pasos que se usa con `group` y `order`. | [paso by](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Devuelve el primer recorrido que devuelve un resultado. | [paso coalesce](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Devuelve un valor constante. Se usa con `coalesce`.| [paso constant](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Devuelve el número del recorrido. | [paso count](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Devuelve los valores sin duplicados. | [paso dedup](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Quita los valores (vértice/arista). | [paso drop](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Crea una descripción de todas las operaciones generadas por el paso de Gremlin ejecutado | [paso executionProfile](graph-execution-profile.md) |
| `fold` | Actúa como una barrera que calcula el agregado de los resultados.| [paso fold](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Agrupa los valores en función de las etiquetas especificadas.| [paso group](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Se utiliza para filtrar las propiedades, los vértices y las aristas. Admite las variantes `hasLabel`, `hasId`, `hasNot` y `has`. | [paso has](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Inserta valores en un flujo.| [paso inject](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Se usa para filtrar mediante una expresión booleana. | [paso is](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Se usa para limitar el número de elementos en el recorrido.| [paso limit](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Encapsula localmente una sección de un recorrido, de forma similar a una subconsulta. | [paso local](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Se usa para generar la negación de un filtro. | [paso not](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Devuelve el resultado del recorrido especificado si produce un resultado; en caso contrario, devuelve el elemento que realiza la llamada. | [paso optional](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Garantiza que al menos uno de los recorridos devuelve un valor. | [paso or](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Muestra los resultados con el criterio de ordenación especificado. | [paso order](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Devuelve la ruta de acceso completa del recorrido. | [paso path](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Proyecta las propiedades como un mapa. | [paso project](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Devuelve las propiedades de las etiquetas especificadas. | [paso properties](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Filtra el intervalo de valores especificado.| [paso range](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Repite el paso el número de veces especificado. Se usa para crear bucles. | [paso repeat](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Se usa para tomar muestras de datos del recorrido. | [paso sample](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Se usa para proyectar los resultados del recorrido. |  [paso select](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Se utiliza para realizar agregados sin bloqueo del recorrido. | [paso store](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Función de filtrado de cadena. Esta función se usa como predicado en el paso `has()` para hacer coincidir una propiedad con el inicio de una cadena determinada | [TextP predicates](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Función de filtrado de cadena. Esta función se usa como predicado en el paso `has()` para hacer coincidir una propiedad con el final de una cadena determinada | [TextP predicates](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Función de filtrado de cadena. Esta función se usa como predicado en el paso `has()` para hacer coincidir una propiedad con el contenido de una cadena determinada | [TextP predicates](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Función de filtrado de cadena. Esta función se usa como predicado en el paso `has()` para hacer coincidir una propiedad que no comienza con una cadena determinada | [TextP predicates](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Función de filtrado de cadena. Esta función se usa como predicado en el paso `has()` para hacer coincidir una propiedad que no finaliza con una cadena determinada | [TextP predicates](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Función de filtrado de cadena. Esta función se usa como predicado en el paso `has()` para hacer coincidir una propiedad que no contiene una cadena determinada | [TextP predicates](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Agrega las rutas de acceso desde un vértice en un árbol. | [paso tree](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Extrae un iterador como un paso.| [paso unfold](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Combina los resultados de varios recorridos.| [paso union](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Incluye los pasos necesarios para los recorridos entre los vértices y las aristas `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` y `otherV` | [pasos vertex](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Se usa para filtrar los resultados del recorrido. Admite los operadores `eq`, `neq`, `lt`, `lte`, `gt`, `gte` y `between`  | [paso where](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

De manera predeterminada, el motor optimizado para escritura de Azure Cosmos DB admite la indexación automática de todas las propiedades dentro de los vértices y los bordes. Por lo tanto, las consultas con filtros, las consultas de intervalo, la ordenación o los agregados de cualquier propiedad se procesan a partir del índice y se sirven de forma eficiente. Para más información sobre cómo funciona la indexación en Azure Cosmos DB, consulte nuestro artículo sobre la [indexación independiente del esquema](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="behavior-differences"></a>Diferencias de comportamiento

* El motor Graph de Azure Cosmos DB ejecuta el recorrido centrando la ***prioridad en la amplitud***, mientras que Gremlin de TinkerPop lo hace centrando la prioridad en la profundidad. Este comportamiento logra un mejor rendimiento en un sistema escalable horizontalmente como Cosmos DB.

## <a name="unsupported-features"></a>Características no admitidas

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** es una especificación independiente del lenguaje de programación para recorridos de grafos. Cosmos DB Graph todavía no lo admite. Use `GremlinClient.SubmitAsync()` y pase el recorrido como una cadena de texto.

* Actualmente, no se admite el establecimiento de la cardinalidad ***`property(set, 'xyz', 1)`***. En su lugar, use `property(list, 'xyz', 1)`. Para más información, consulte [Propiedades Vertex con TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* El ***paso `match()`*** no se encuentra disponible actualmente. Este paso proporciona funcionalidades de consulta declarativa.

* No se admiten los ***objetos como propiedades*** en bordes ni vértices. Las propiedades solo pueden ser tipos primitivos o matrices.

* No se admite la ***ordenación por propiedades de la matriz*** `order().by(<array property>)`. Solo se admite la ordenación por tipos primitivos.

* No se admiten los ***tipos JSON no primitivos***. Use los tipos `string`, `number` o `true`/`false`. No se admiten los valores `null`. 

* El serializador ***GraphSONv3*** no se admite actualmente. Use las clases Serializer, Reader y Writer de `GraphSONv2` en la configuración de conexión. Los resultados devueltos por la API de Azure Cosmos DB Gremlin no tienen el mismo formato que el formato GraphSON. 

* Actualmente no se admiten las **expresiones y funciones lambda**. Esto incluye las funciones `.map{<expression>}`, `.by{<expression>}` y `.filter{<expression>}`. Para obtener más información y saber cómo volver a escribirlas con los pasos de Gremlin, vea [Una nota sobre las expresiones lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* No se admiten las ***transacciones*** debido a la naturaleza distribuida del sistema.  Configure el modelo de coherencia adecuado en la cuenta de Gremlin para "leer sus propias escrituras", y use la simultaneidad optimista para resolver las escrituras conflictivas.

## <a name="known-limitations"></a>Restricciones conocidas

* **Uso de índices para las consultas de Gremlin con pasos `.V()` de recorrido intermedio**: actualmente, solo la primera llamada a `.V()` de un recorrido usará el índice para resolver los filtros o predicados asociados a él. Las llamadas subsiguientes no consultarán el índice, lo que podría aumentar la latencia y el costo de la consulta.
    
Suponiendo que la indexación predeterminada, una consulta de Gremlin de lectura típica que se inicia con el paso `.V()` usaría parámetros en sus pasos de filtrado asociados, como `.has()` o `.where()`, para optimizar el costo y el rendimiento de la consulta. Por ejemplo:

```java
g.V().has('category', 'A')
```

Sin embargo, cuando se incluye más de un paso `.V()` en la consulta de Gremlin, la resolución de los datos para la consulta podría no ser la óptima. Tome la siguiente consulta como ejemplo:

```java
g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
```

Esta consulta devolverá dos grupos de vértices basados en su propiedad llamada `category`. En este caso, solo la primera llamada, `g.V().has('category', 'A')`, usará el índice para resolver los vértices en función de los valores de sus propiedades.

Una solución alternativa para esta consulta es usar pasos subtransversales como `.map()` y `union()`. Esto se muestra a continuación:

```java
// Query workaround using .map()
g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

// Query workaround using .union()
g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
```

Puede revisar el rendimiento de las consultas mediante el [paso de Gremlin `executionProfile()`](graph-execution-profile.md).

## <a name="next-steps"></a>Pasos siguientes

* Empezar a compilar una aplicación de grafos [con nuestros SDK](create-graph-dotnet.md) 
* Más información acerca de la [compatibilidad con grafos](graph-introduction.md) en Azure Cosmos DB
