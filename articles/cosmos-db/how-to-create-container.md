---
title: Creación de un contenedor en la API de SQL de Azure Cosmos DB
description: Aprenda a crear un contenedor en la API de SQL de Azure Cosmos DB mediante Azure Portal, .NET, Java, Python, Node.js y otros SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: e9fc377acf528d564411a5c65c5fce9d6282118a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749933"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Creación de un contenedor en la API de SQL de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

En este artículo se explican las distintas formas de crear un contenedor en la API de SQL de Azure Cosmos DB. Muestra cómo crear un contenedor mediante Azure Portal, la CLI de Azure, PowerShell o los SDK admitidos. En este artículo se muestra cómo crear un contenedor, especificar la clave de partición y aprovisionar el rendimiento.

En este artículo se explican las distintas formas de crear un contenedor en la API de SQL de Azure Cosmos DB. Si usa una API diferente, consulte los artículos [API para MongoDB](how-to-create-container-mongodb.md), [Cassandra API](cassandra/how-to-create-container-cassandra.md), [Gremlin API](how-to-create-container-gremlin.md) y [Table API](table/how-to-create-container.md) para crear el contenedor.

> [!NOTE]
> Cuando cree contenedores, asegúrese de no utilizar el mismo nombre en dos de ellos con distintas mayúsculas y minúsculas. Algunos componentes de la plataforma de Azure no distinguen mayúsculas de minúsculas y esto puede producir confusión o problemas con los datos telemetría y las acciones que se realicen en los contenedores con estos nombres.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Creación de un contenedor mediante Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. [Cree una cuenta de Azure Cosmos](create-sql-api-dotnet.md#create-account) o seleccione una ya existente.

1. Abra el panel **Data Explorer** y seleccione **Nuevo contenedor**. Después, proporcione los detalles siguientes:

   * Indique si va a crear una nueva base de datos o a usar una existente.
   * Escriba un valor en **Container Id** (Id. de contenedor).
   * Escriba un valor en **Partition key** (Clave de partición) (por ejemplo, `/ItemID`).
   * Seleccione un rendimiento de **Autoscale** (Escalabilidad automática) o **Manual** y especifique el valor de **Container throughput** (Rendimiento de contenedor) (por ejemplo, 1000 RU/s). Escriba un rendimiento que quiera aprovisionar (por ejemplo, 1000 RU).
   * Seleccione **Aceptar**.

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="Captura de pantalla de Data Explorer, con la Nueva colección resaltada":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a> Creación de un contenedor mediante la CLI de Azure

[Cree un contenedor con la CLI de Azure](manage-with-cli.md#create-a-container). Para obtener una lista de todos los ejemplos de la CLI de Azure en todas las API de Azure Cosmos DB, vea [Ejemplos de la CLI de Azure para Azure Cosmos DB](cli-samples.md).

## <a name="create-a-container-using-powershell"></a>Creación de un contenedor mediante PowerShell

[Cree un contenedor con PowerShell](manage-with-powershell.md#create-container). Para obtener una lista de todos los ejemplos de PowerShell en todas las API de Azure Cosmos DB, vea [Ejemplos de PowerShell](powershell-samples.md).

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>Creación de un contenedor mediante el SDK para .NET

Si se produce una excepción de tiempo de espera al crear una colección, realice una operación de lectura para validar si la colección se ha creado correctamente. La operación de lectura emite una excepción hasta que la operación de creación de la colección se realiza correctamente. Para la lista de códigos de estado admitidos por la operación de creación, consulte el artículo [Códigos de estado HTTP para Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="next-steps"></a>Pasos siguientes

* [Creación de particiones en Azure Cosmos DB](partitioning-overview.md)
* [Unidades de solicitud en Azure Cosmos DB](request-units.md)
* [Aprovisionamiento del rendimiento en contenedores y bases de datos](set-throughput.md)
* [Uso de la cuenta de Azure Cosmos](./account-databases-containers-items.md)