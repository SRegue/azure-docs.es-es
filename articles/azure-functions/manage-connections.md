---
title: Administración de conexiones en Azure Functions
description: Aprenda a evitar problemas de rendimiento en Azure Functions mediante el uso de los clientes de conexión estáticos.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/23/2021
ms.openlocfilehash: 3a7f0f707957b4b3cfd7dc66efe9d2d011d58982
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105639"
---
# <a name="manage-connections-in-azure-functions"></a>Administración de conexiones en Azure Functions

Las funciones dentro de una aplicación de función comparten recursos. Entre esos recursos compartidos se encuentran las conexiones: Las conexiones HTTP, las conexiones de base de datos y las conexiones a servicios como Azure Storage. Cuando se ejecutan simultáneamente muchas funciones en un plan de consumo, es posible que se agoten las conexiones disponibles. Este artículo explica cómo codificar las funciones para evitar el uso de más conexiones que las que se necesitan.

> [!NOTE]
> Los límites de conexión descritos en este artículo solo se aplican cuando se ejecutan en un [plan de consumo](consumption-plan.md). Sin embargo, las técnicas que se describen aquí pueden ser beneficiosas cuando se ejecutan en cualquier plan.

## <a name="connection-limit"></a>Límite de conexiones

El número de conexiones disponibles de un plan de consumo está limitado en parte porque una aplicación de funciones de este plan se ejecuta en un [entorno de espacio aislado](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Una de las restricciones que impone el espacio aislado en el código es un límite en el número de conexiones salientes, que actualmente es 600 (de total de 1200) conexiones activas por instancia. Cuando se alcanza este límite, el entorno de ejecución de las funciones crea un registro con el siguiente mensaje: `Host thresholds exceeded: Connections`. Para obtener más información, consulte [Límites de servicio en Functions](functions-scale.md#service-limits).

Este límite es por instancia. Cuando el [controlador de escala agrega instancias de aplicación de función](event-driven-scaling.md) para controlar más solicitudes, cada instancia tendrá un límite de conexión independiente. Esto significa que no hay ningún límite de conexión global y puede tener mucho más de 600 conexiones activas de todas las instancias activas.

Para solucionar el problema, asegúrese de que ha habilitado Application Insights para su aplicación de funciones. Application Insights permite ver las métricas de las aplicaciones de funciones, como las ejecuciones. Para obtener más información, consulte [Visualización de la telemetría en Application Insights](analyze-telemetry-data.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Clientes estáticos

Para evitar mantener más conexiones de las necesarias, reutilice las instancias de cliente en lugar de crear nuevas con cada invocación de función. Se recomienda la reutilización de las conexiones de cliente para cualquier lenguaje en el que haya escrito su función. Por ejemplo, los cclientes de .NET, como [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true), [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) y los clientes de Azure Storage, pueden administrar las conexiones si usa un cliente único y estático.

Estas son algunas directrices que se deben seguir al utilizar a un cliente específico del servicio en una aplicación de Azure Functions:

- *No cree* un nuevo cliente con cada invocación de función.
- *Cree* un único cliente estático que pueda ser utilizado por cada invocación de función.
- *Considere* la posibilidad de crear un cliente único y estático en una clase auxiliar compartida si funciones diferentes utilizan el mismo servicio.

## <a name="client-code-examples"></a>Ejemplos de código de cliente

En esta sección se muestran los procedimientos recomendados para crear y utilizar clientes a partir del código de función.

### <a name="http-requests"></a>Solicitudes HTTP

# <a name="c"></a>[C#](#tab/csharp)
Este es un ejemplo de código de función de C# que crea una instancia de [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) estática:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Una pregunta común sobre [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) de .NET es "¿Debería desechar mi cliente?" En general, se desechan los objetos que implementan `IDisposable` cuando se ha terminado de utilizarlos. Pero no se desecha un cliente estático porque su uso no ha terminado cuando finaliza la función. Desea que el cliente estático viva en toda la duración de la aplicación.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Debido a que proporciona mejores opciones de administración de conexiones, debería utilizar la clase nativa [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) en lugar de los métodos no nativos, como el módulo `node-fetch`. Los parámetros de conexión se configuran mediante las opciones de la clase `http.agent`. Consulte [new Agent(\[options\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options) para ver las opciones detalladas disponibles con el agente HTTP.

La clase `http.globalAgent` global que se utiliza en `http.request()` tiene todos estos valores establecidos a sus respectivos valores predeterminados. La forma recomendada de configurar los límites de conexión en Functions es establecer un número máximo globalmente. El siguiente ejemplo establece el número máximo de sockets para la aplicación de función:

```js
http.globalAgent.maxSockets = 200;
```

 En el ejemplo siguiente se crea una nueva solicitud HTTP con un agente HTTP personalizado solo para esa solicitud:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
const options = { agent: httpAgent };
http.request(options, onResponseCallback);
```

---

### <a name="azure-cosmos-db-clients"></a>Clientes de Azure Cosmos DB 

# <a name="c"></a>[C#](#tab/csharp)

[DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) se conecta a una instancia de Azure Cosmos DB. La documentación de Azure Cosmos DB recomienda el [uso de un cliente de Azure Cosmos DB singleton para el ciclo de vida de la aplicación](../cosmos-db/performance-tips.md#sdk-usage). En el ejemplo siguiente se muestra un patrón para realizarlo en una función:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```
Si está trabajando con funciones v3.x, necesita una referencia a Microsoft.Azure.DocumentDB.Core. Agregar una referencia en el código:

```cs
#r "Microsoft.Azure.DocumentDB.Core"
```
Además, cree un archivo denominado "function.proj" para el desencadenador y agregue el contenido siguiente:

```cs

<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netcoreapp3.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.DocumentDB.Core" Version="2.12.0" />
    </ItemGroup>
</Project>

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) se conecta a una instancia de Azure Cosmos DB. La documentación de Azure Cosmos DB recomienda el [uso de un cliente de Azure Cosmos DB singleton para el ciclo de vida de la aplicación](../cosmos-db/performance-tips.md#sdk-usage). En el ejemplo siguiente se muestra un patrón para realizarlo en una función:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

---

## <a name="sqlclient-connections"></a>Conexiones de SqlClient

El código de la función puede usar el proveedor de datos .NET Framework para SQL Server ([SqlClient](/dotnet/api/system.data.sqlclient)) para establecer conexiones con una base de datos relacional de SQL. También es el proveedor subyacente para los marcos de datos que se basan en ADO.NET, como [Entity Framework](/ef/ef6/). A diferencia de las conexiones de [HttpClient](/dotnet/api/system.net.http.httpclient) y [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient), ADO.NET implementa la agrupación de conexiones de manera predeterminada. Sin embargo, como todavía puede quedarse sin conexiones, debería optimizar las conexiones con la base de datos. Para más información, consulte el artículo sobre la [agrupación de conexiones de SQL Server (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Algunos marcos de datos, como Entity Framework, habitualmente obtienen cadenas de conexión desde la sección **ConnectionStrings** de un archivo de configuración. En este caso, debe agregar explícitamente las cadenas de conexión de base de datos SQL a la colección **Cadenas de conexión** de la configuración de la aplicación de función y en el [archivo local.settings.json](functions-develop-local.md#local-settings-file) del proyecto local. Si está creando una instancia de [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) en el código de la función, debe almacenar el valor de la cadena de conexión en **Configuración de la aplicación** con las otras conexiones.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre por qué se recomiendan clientes estáticos, consulte [Antipatrón Improper Instantiation](/azure/architecture/antipatterns/improper-instantiation/).

Para más sugerencias de rendimiento de Azure Functions, consulte [Optimización del rendimiento y la confiabilidad de Azure Functions](functions-best-practices.md).
