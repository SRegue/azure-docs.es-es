---
title: Aprovisionamiento del rendimiento de escalabilidad automática en SQL API de Azure Cosmos DB
description: Aprenda a aprovisionar el rendimiento de escalabilidad automática en el nivel de contenedor y base de datos en SQL API de Azure Cosmos DB mediante Azure Portal, la CLI, PowerShell y otros SDK.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/18/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: ce17b54905861759c437c2df735dd5515991b507
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110456623"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db---sql-api"></a>Aprovisionamiento del rendimiento de escalabilidad automática en una base de datos o un contenedor de Azure Cosmos DB: SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

En este artículo se explica cómo aprovisionar el rendimiento de escalabilidad automática en una base de datos o un contenedor (colección, grafo o tabla) en SQL API de Azure Cosmos DB. Puede habilitar la escalabilidad automática en un solo contenedor o aprovisionar el rendimiento de escalabilidad automática en una base de datos y compartirlo entre todos los contenedores de la base de datos.

Si va a usar otra API, vea los artículos [MongoDB API](how-to-provision-throughput-mongodb.md), [Cassandra API](how-to-provision-throughput-cassandra.md) y [Gremlin API](how-to-provision-throughput-gremlin.md) para aprovisionar el rendimiento.

## <a name="azure-portal"></a>Azure portal

### <a name="create-new-database-or-container-with-autoscale"></a>Creación de una base de datos o un contenedor con escalabilidad automática

1. Inicie sesión en [Azure Portal](https://portal.azure.com) o en el [explorador de Azure Cosmos DB](https://cosmos.azure.com/).

1. Vaya a la cuenta de Azure Cosmos DB y abra la pestaña **Explorador de datos**.

1. Seleccione **Nuevo contenedor**. Escriba un nombre para la base de datos, el contenedor y una clave de partición. En el rendimiento de la base de datos o del contenedor, seleccione la opción **Escalabilidad automática** y establezca el [rendimiento máximo (RU/s)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works) al que quiere escalar la base de datos o el contenedor.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="Creación de un contenedor y configuración del rendimiento aprovisionado de escalabilidad automática":::

1. Seleccione **Aceptar**.

Para aprovisionar la escalabilidad automática en la base de datos de rendimiento compartido, seleccione la opción **Aprovisionar rendimiento de base de datos** al crear una nueva base de datos. 

### <a name="enable-autoscale-on-existing-database-or-container"></a>Habilitación de la escalabilidad automática en la base de datos o el contenedor existente

1. Inicie sesión en [Azure Portal](https://portal.azure.com) o en el [explorador de Azure Cosmos DB](https://cosmos.azure.com/).

1. Vaya a la cuenta de Azure Cosmos DB y abra la pestaña **Explorador de datos**.

1. Seleccione **Escala y configuración** para su contenedor o **Escala** para la base de datos.

1. En **Escala**, seleccione la opción **Escalabilidad automática** y **Guardar**.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="Habilitación de la escalabilidad automática en un contenedor existente":::

> [!NOTE]
> Cuando se habilita la escalabilidad automática en una base de datos o un contenedor existente, el sistema determina el valor inicial para el número máximo de RU/s, en función de la configuración de rendimiento y el almacenamiento aprovisionados manualmente. Una vez completada la operación, puede cambiar el número máximo de RU/s si es necesario. [Más información.](autoscale-faq.yml#how-does-the-migration-between-autoscale-and-standard--manual--provisioned-throughput-work-) 

## <a name="azure-cosmos-db-net-v3-sdk"></a>SDK de Azure Cosmos DB para .NET V3

Use la [versión 3.9 o posterior](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) de la API del SDK de Azure Cosmos DB para .NET para SQL API para administrar los recursos de escalabilidad automática. 

> [!IMPORTANT]
> Puede usar el SDK para .NET para crear nuevos recursos de escalabilidad automática. El SDK no admite la migración entre el rendimiento estándar (manual) y la escalabilidad automática. Actualmente, el escenario de migración solo se admite en [Azure Portal](#enable-autoscale-on-existing-database-or-container), la [CLI](#azure-cli) y [PowerShell](#azure-powershell).

### <a name="create-database-with-shared-throughput"></a>Creación de una base de datos con rendimiento compartido

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>Creación de un contenedor con rendimiento dedicado

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>Lectura del rendimiento actual (RU/s)

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>Cambio del rendimiento máximo de escalabilidad automática (RU/s)

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk"></a>SDK de Azure Cosmos DB para Java V4

Puede usar la [versión 4.0 o posterior](https://mvnrepository.com/artifact/com.azure/azure-cosmos) de Azure Cosmos DB Java SDK para SQL API para administrar los recursos de escalabilidad automática.

> [!IMPORTANT]
> Puede usar el SDK para Java para crear nuevos recursos de escalabilidad automática. El SDK no admite la migración entre el rendimiento estándar (manual) y la escalabilidad automática. Actualmente, el escenario de migración solo se admite en [Azure Portal](#enable-autoscale-on-existing-database-or-container), la [CLI](#azure-cli) y [PowerShell](#azure-powershell).
### <a name="create-database-with-shared-throughput"></a>Creación de una base de datos con rendimiento compartido

# <a name="async"></a>[Asincrónico](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>Creación de un contenedor con rendimiento dedicado

# <a name="async"></a>[Asincrónico](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>Lectura del rendimiento actual (RU/s)

# <a name="async"></a>[Asincrónico](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>Cambio del rendimiento máximo de escalabilidad automática (RU/s)

# <a name="async"></a>[Asincrónico](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Las plantillas de Azure Resource Manager se pueden usar para aprovisionar el rendimiento de escalabilidad automática en una nueva base de datos o en recursos de nivel de contenedor para todas las API de Azure Cosmos DB. Consulte [Plantillas de Azure Resource Manager para Azure Cosmos DB](./templates-samples-sql.md) para ejemplos. Por diseño, las plantillas de Azure Resource Manager no se pueden usar para migrar entre el rendimiento aprovisionado y el de escalabilidad automática en un recurso existente. 

## <a name="azure-cli"></a>CLI de Azure

La CLI de Azure se puede usar para aprovisionar el rendimiento de escalabilidad automática en una nueva base de datos o en recursos de nivel de contenedor para todas las API de Azure Cosmos DB, o para habilitar la escalabilidad automática en un recurso existente. Para ejemplos, consulte [Ejemplos de la CLI de Azure para Azure Cosmos DB](cli-samples.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell se puede usar para aprovisionar el rendimiento de escalabilidad automática en una nueva base de datos o en recursos de nivel de contenedor para todas las API de Azure Cosmos DB, o para habilitar la escalabilidad automática en un recurso existente. Para ejemplos, consulte [Ejemplos de Azure PowerShell para Azure Cosmos DB](powershell-samples.md).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre las [ventajas del rendimiento aprovisionado con escalabilidad automática](provision-throughput-autoscale.md#benefits-of-autoscale).
* Obtenga información acerca de cómo [elegir entre el rendimiento manual y de escalabilidad automática](how-to-choose-offer.md).
* Revise las [preguntas más frecuentes sobre Escalabilidad automática](autoscale-faq.yml).
