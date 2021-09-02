---
title: Configuración de escrituras en varias regiones en Azure Cosmos DB
description: Aprenda a configurar escrituras en varias regiones en las aplicaciones mediante diferentes SDK de Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, "seo-nov-2020"
ms.openlocfilehash: 66f2a8cee24c1e7e26a40faecabbaf2b3e0c78d6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114279"
---
# <a name="configure-multi-region-writes-in-your-applications-that-use-azure-cosmos-db"></a>Configuración de escrituras en varias regiones en las aplicaciones que usan Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Una vez creada una cuenta con varias regiones de escritura habilitadas, debe realizar dos cambios en la aplicación en ConnectionPolicy para que el cliente de Cosmos habilite las escrituras en varias regiones en Azure Cosmos DB. En ConnectionPolicy, establezca UseMultipleWriteLocations en true y pase el nombre de la región donde se implementa la aplicación a ApplicationRegion. Se rellena la propiedad PreferredLocations según la proximidad geográfica de la ubicación pasada. Si más adelante se agrega una nueva región a la cuenta, la aplicación no tiene que actualizarse ni volver a implementarse; se detecta automáticamente la región más cercana y se hospeda por sí sola en ella si se produce un evento regional.

> [!Note]
> Las cuentas de Cosmos configuradas inicialmente con una sola región de escritura pueden configurarse para varias regiones de escritura con cero tiempo de inactividad. Para más información, consulte, [Configuración de varias regiones de escritura](../how-to-manage-database-account.md#configure-multiple-write-regions).

## <a name="azure-portal"></a><a id="portal"></a> Azure Portal

Siga estos pasos para habilitar las escrituras en varias regiones desde Azure Portal:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

1. Vaya a la cuenta de Azure Cosmos y desde el menú, abra el panel **Replicar datos globalmente**.

1. En la opción **Escrituras en varias regiones**, seleccione **Habilitar**. Agrega automáticamente las regiones existentes a las regiones de lectura y escritura.

1. Puede agregar más regiones seleccionando los iconos en el mapa o el botón **Agregar región**. Todas las regiones que agregue tendrán habilitadas las operaciones de lectura y escritura.

1. Después de actualizar la lista de regiones, seleccione **Guardar** para aplicar los cambios.

   :::image type="content" source="./media/how-to-multi-master/enable-multi-region-writes.png" alt-text="Captura de pantalla para habilitar las escrituras en varias regiones mediante Azure Portal" lightbox="./media/how-to-multi-master/enable-multi-region-writes.png":::

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Para habilitar las escrituras en varias regiones en la aplicación, establezca `UseMultipleWriteLocations` en `true`. Además, establezca `SetCurrentLocation` en la región en que se va a implementar la aplicación y donde se va a replicar Azure Cosmos DB:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>SDK de .NET v3

Para habilitar escrituras en varias regiones en la aplicación, establezca `ApplicationRegion` en la región en la que se va a implementar la aplicación y donde se va a replicar Cosmos DB:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

También puede usar `CosmosClientBuilder` y `WithApplicationRegion` para lograr el mismo resultado:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-region-writes"></a> SDK de Java V4

Para habilitar escrituras en varias regiones en la aplicación, llame a `.multipleWriteRegionsEnabled(true)` y `.preferredRegions(preferredRegions)` en el generador de clientes, donde `preferredRegions` es un objeto `List` que contiene un elemento, que es la región en la que se va a implementar la aplicación y donde se va a replicar Cosmos DB:

# <a name="async"></a>[Asincrónico](#tab/api-async)

   [SDK para Java V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) API asincrónica

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

   [SDK para Java V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) API sincrónica

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-multi-region-writes"></a> Versión 2 del SDK de Java sincrónico

La versión 2 del SDK de Java usaba el Maven [com.microsoft.azure::azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb). Para habilitar escrituras en varias regiones en la aplicación, establezca `policy.setUsingMultipleWriteLocations(true)` y establezca `policy.setPreferredLocations` en la región en la que se va a implementar la aplicación y donde se va a replicar Cosmos DB:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>SDK de Node.js, JavaScript y TypeScript

Para habilitar las escrituras en varias regiones en la aplicación, establezca `connectionPolicy.UseMultipleWriteLocations` en `true`. Además, establezca `connectionPolicy.PreferredLocations` en la región en que se va a implementar la aplicación y donde se va a replicar Cosmos DB:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>SDK para Python

Para habilitar las escrituras en varias regiones en la aplicación, establezca `connection_policy.UseMultipleWriteLocations` en `true`. Además, establezca `connection_policy.PreferredLocations` en la región en que se va a implementar la aplicación y donde se va a replicar Cosmos DB.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Pasos siguientes

Lea los siguientes artículos:

* [Uso de tokens de sesión para administrar la coherencia en Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Tipos de conflicto y directivas de resolución de conflictos en Azure Cosmos DB](../conflict-resolution-policies.md)
* [Alta disponibilidad en Azure Cosmos DB](../high-availability.md)
* [Niveles de coherencia en Azure Cosmos DB](../consistency-levels.md)
* [Selección del nivel de coherencia adecuado en Azure Cosmos DB](../consistency-levels.md)
* [Inconvenientes de la coherencia, disponibilidad y rendimiento en Azure Cosmos DB](../consistency-levels.md)
* [Availability and performance tradeoffs for various consistency levels](../consistency-levels.md) (Compromisos entre rendimiento y disponibilidad en los distintos niveles de coherencia)
* [Escalado del rendimiento aprovisionado globalmente](../request-units.md)
* [Distribución global: En segundo plano](../global-dist-under-the-hood.md)