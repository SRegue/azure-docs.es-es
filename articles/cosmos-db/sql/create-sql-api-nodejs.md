---
title: 'Inicio rápido: Uso de Node.js para realizar consultas desde una cuenta de SQL API de Azure Cosmos DB'
description: Uso de Node.js para crear una aplicación que se conecte a una cuenta de Azure Cosmos DB SQL API y consulte datos.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 08/26/2021
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: 635e85223dc7a3fb2fd579fa33baa338ebf9cc50
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123118126"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Inicio rápido: Uso de Node.js para conectarse y consultar datos en una cuenta de Azure Cosmos DB SQL API
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [SDK para Java v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Conector de Spark v3](create-sql-api-spark.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

En este inicio rápido se crea y administra una cuenta de Azure Cosmos DB para SQL API desde Azure Portal, y se usa una aplicación de Node.js clonada desde GitHub. Azure Cosmos DB es un servicio de base de datos multimodelo que permite crear y consultar rápidamente bases de datos de documentos, tablas, claves-valores y grafos con funcionalidades de distribución global y escala horizontal.

## <a name="walkthrough-video"></a>Tutorial de vídeo

Este vídeo incluye un tutorial completo sobre el contenido de este artículo.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). O bien, [pruebe gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sin una suscripción de Azure. También puede usar el [emulador de Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) con el identificador URI `https://localhost:8081` y la clave `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Node.js 6.0.0+](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Creación de una cuenta de Azure Cosmos

Para los fines de este inicio rápido, puede usar la opción [Pruebe gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) para crear una cuenta de Azure Cosmos.

1. Vaya a la página [Pruebe gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/).

1. Elija la cuenta de API **SQL** y seleccione **Crear**. Inicie sesión con su cuenta de Microsoft.

1. Una vez que el inicio de sesión se haya realizado correctamente, la cuenta de Azure Cosmos debe estar preparada. Seleccione **Abrir** en Azure Portal para abrir la cuenta recién creada.

La opción "Pruebe gratis Azure Cosmos DB" no requiere una suscripción de Azure y le ofrece una cuenta de Azure Cosmos durante un período limitado de 30 días. Si desea usar la cuenta de Azure Cosmos durante un período de tiempo más largo, debe [crear la cuenta](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) en la suscripción de Azure.

## <a name="add-a-container"></a>Agregar un contenedor

[!INCLUDE [cosmos-db-create-collection](../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Adición de datos de ejemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consulta de los datos

[!INCLUDE [cosmos-db-create-sql-api-query-data](../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación en Node.js desde GitHub, establecer la cadena de conexión y ejecutarla.

1. Ejecute el comando siguiente para clonar el repositorio de ejemplo. Este comando crea una copia de la aplicación de ejemplo en el equipo.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>Revisión del código

Este paso es opcional. Si está interesado en aprender cómo se crean los recursos de base de datos de Azure Cosmos en el código, puede revisar los siguientes fragmentos de código. En caso contrario, puede ir directamente a [Actualización de la cadena de conexión](#update-your-connection-string).

Si conoce la versión anterior del SDK de SQL para JavaScript, es posible que esté acostumbrado a ver los términos _colección_ y _documento_. Dado que Azure Cosmos DB admite [varios modelos de API](../introduction.md), la [versión 2.0+ del SDK de JavaScript](https://www.npmjs.com/package/@azure/cosmos) usa los términos genéricos _container_ (contenedor), que puede ser una colección, un grafo o una tabla, e _item_ (elemento) para describir el contenido del contenedor.

El SDK de JavaScript de Cosmos DB se denomina "@azure/cosmos" y se puede instalar desde npm...

```bash
npm install @azure/cosmos
```

Los fragmentos de código siguientes se han tomado del archivo _app.js_.

- `CosmosClient` se importa desde el paquete npm `@azure/cosmos`.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Se inicializa un nuevo objeto `CosmosClient`.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Seleccione la base de datos "Tasks".

  ```javascript
  const database = client.database(databaseId);
  ```

- Seleccione el contenedor o la colección "Items".

  ```javascript
  const container = database.container(containerId);
  ```

- Seleccione todos los elementos del contenedor "Items".

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- Creación de un elemento

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Actualización de un elemento

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- Eliminación de un elemento

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> En los métodos "update" y "delete", el elemento debe seleccionarse de la base de datos mediante una llamada a `container.item()`. Los dos parámetros pasados son el identificador del elemento y la clave de partición del elemento. En este caso, la clave de partición es el valor del campo "category".

## <a name="update-your-connection-string"></a>Actualización de la cadena de conexión

Ahora vuelva a Azure Portal para obtener los detalles de la cadena de conexión de su cuenta de Azure Cosmos. Copie la cadena de conexión en la aplicación para que pueda conectarse a su base de datos.

1. En la cuenta de Azure Cosmos DB, en [Azure Portal](https://portal.azure.com/), seleccione **Claves** en el panel de navegación izquierdo y, después, **Claves de lectura y escritura**. Use los botones de copia del lado derecho de la pantalla para copiar el URI y la clave principal en el archivo _app.js_ en el paso siguiente.

   :::image type="content" source="./media/create-sql-api-dotnet/keys.png" alt-text="Visualizar y copiar una clave de acceso en Azure Portal, hoja Claves":::

2. Abra el archivo _config.js_.

3. Copie el valor del identificador URI del portal (mediante el botón de copia) y establézcalo como valor de la clave de punto de conexión en _config.js_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Después, copie el valor de CLAVE PRINCIPAL del portal y establézcalo como el valor de `config.key` en _config.js_. Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Ejecución la aplicación

1. Ejecute `npm install` en un terminal para instalar el paquete npm "@azure/cosmos".

2. Ejecute `node app.js` en un terminal para iniciar la aplicación de nodo.

3. Se enumeran los dos elementos que creó anteriormente en este inicio rápido. Se crea un elemento. La marca "isComplete" de ese elemento se actualiza a "true" y, por último, se elimina el elemento.

Puede continuar experimentando con esta aplicación de ejemplo o volver al Explorador de datos, modificar y trabajar con los datos.

## <a name="review-slas-in-the-azure-portal"></a>Revisión de los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a crear una cuenta de Azure Cosmos DB, a crear un contenedor mediante el Explorador de datos y a ejecutar una aplicación de Node.js. Ahora puede importar datos adicionales en la cuenta de Azure Cosmos DB.

¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
* Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
* Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).

> [!div class="nextstepaction"]
> [Importación de datos a Azure Cosmos DB](../import-data.md)
