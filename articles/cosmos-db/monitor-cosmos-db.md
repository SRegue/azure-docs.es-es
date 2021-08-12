---
title: Supervisión de Azure Cosmos DB | Microsoft Docs
description: Obtenga información sobre cómo supervisar el rendimiento y la disponibilidad de Azure Cosmos DB.
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/01/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: 27dd64e510ffe00ce1c3c9f24f60c8c018401ab1
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110471064"
---
# <a name="monitor-azure-cosmos-db"></a>Supervisión de Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Si tiene aplicaciones y procesos empresariales críticos que dependen de recursos de Azure, querrá supervisar esos recursos para su disponibilidad, rendimiento y funcionamiento. En este artículo se describen los datos de supervisión generados por las bases de datos de Azure Cosmos y cómo puede usar las características de Azure Monitor para analizar y alertar sobre estos datos.

Puede supervisar los datos con métricas del lado cliente y del lado servidor. Al usar métricas del lado servidor, puede supervisar los datos almacenados en Azure Cosmos DB con las siguientes opciones:

* **Supervisión desde el portal de Azure Cosmos DB:** puede realizar la supervisión con las métricas disponibles en la pestaña **Métricas** de la cuenta de Azure Cosmos. Las métricas de esta pestaña incluyen datos de nivel de rendimiento, almacenamiento, disponibilidad, latencia, coherencia y sistema. De forma predeterminada, estas métricas tienen un período de retención de 7 días. Para más información, consulte la sección [Supervisión de los datos recopilados de Azure Cosmos DB](#monitoring-from-azure-cosmos-db) de este artículo.

* **Supervisión con métricas en Azure Monitor:** puede realizar la supervisión con las métricas de su cuenta de Azure Cosmos y crear paneles desde Azure Monitor. Azure Monitor recopila las métricas de Azure Cosmos DB de forma predeterminada, no tiene que configurar nada de forma explícita. Estas métricas se recopilan con una granularidad de un minuto; la granularidad puede variar en función de la métrica que elija. De forma predeterminada, estas métricas tienen un período de retención de 30 días. La mayoría de las métricas que están disponibles en las opciones anteriores también están disponibles en estas métricas. Los valores de la dimensión para las métricas, como el nombre del contenedor, no distinguen entre mayúsculas y minúsculas. Por lo tanto, debe usar una comparación sin distinción entre mayúsculas y minúsculas al realizar comparaciones de cadenas en estos valores de dimensión. Para más información, consulte la sección [Análisis de datos de métricas](#analyze-metric-data) de este artículo.

* **Supervisión con registros de diagnóstico en Azure Monitor:** puede supervisar los registros de su cuenta de Azure Cosmos y crear paneles desde Azure Monitor. La telemetría, como eventos y seguimientos que tienen lugar en un segundo nivel de detalle, se almacenan como registros. Por ejemplo, si cambia el rendimiento de un contenedor o cambian las propiedades de una cuenta de Cosmos, estos eventos se capturan en los registros. Estos registros se pueden analizar mediante la ejecución de consultas en los datos recopilados. Para más información, consulte la sección [Análisis de datos de registro](#analyze-log-data) de este artículo.

* **Supervisión mediante programación con los SDK:** puede supervisar su cuenta de Azure Cosmos mediante programación con los SDK de .NET, Java, Python y Node.js, y los encabezados de la API REST. Para más información, consulte la sección [Supervisión de Azure Cosmos DB mediante programación](#monitor-cosmosdb-programmatically) de este artículo.

La siguiente imagen muestra distintas opciones disponibles para supervisar la cuenta de Azure Cosmos DB mediante Azure Portal:

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="Opciones de supervisión disponibles en Azure Portal" border="false":::

Al usar Azure Cosmos DB, puede recopilar en el lado cliente los detalles de la carga de solicitudes, el identificador de actividad, la información de seguimiento de la pila y excepciones, el código de estado y subestado HTTP y la cadena de diagnóstico para depurar cualquier problema que pueda producirse. Esta información también es necesaria si necesita ponerse en contacto con el equipo de soporte técnico de Azure Cosmos DB. 

## <a name="monitor-overview"></a>Información general de supervisión

La página **Información general** de Azure Portal de cada cuenta de Azure Cosmos DB incluye una pequeña vista del uso de recursos, como el total de solicitudes, las solicitudes que dieron como resultado un código de estado HTTP específico y la facturación por hora. Esta información es útil, pero solo una pequeña cantidad de datos de supervisión está disponible en este panel. Algunos de estos datos se recopilan automáticamente y están disponibles para su análisis en cuanto se crea el recurso. Puede habilitar tipos adicionales de recopilación de datos con cierta configuración adicional.

## <a name="what-is-azure-monitor"></a>¿Qué es Azure Monitor?

Azure Cosmos DB crea datos de supervisión mediante [Azure Monitor](../azure-monitor/overview.md), que es un servicio de supervisión de pila completo de Azure, que proporciona un conjunto completo de características para supervisar los recursos de Azure, además de los recursos locales y en otras nubes.

Si no está familiarizado con la supervisión de los servicios de Azure, comience con el artículo [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md) que describe los siguientes conceptos:

* ¿Qué es Azure Monitor?
* Costos asociados con la supervisión
* Datos de supervisión recopilados en Azure
* Configuración de la recolección de datos
* Herramientas estándar en Azure para analizar datos de supervisión y alertar sobre ellos

Las secciones siguientes se basan en este artículo, donde se describen los datos específicos recopilados de Azure Cosmos DB y se proporcionan ejemplos para configurar la recopilación de datos y el análisis de estos datos con herramientas de Azure.

## <a name="cosmos-db-insights"></a>Información sobre Cosmos DB

La información de Cosmos DB se basa en la [característica de libros de Azure Monitor](../azure-monitor/visualize/workbooks-overview.md) y utiliza los mismos datos de supervisión recopilados para Azure Cosmos DB que se describen en las secciones siguientes. Use Azure Monitor para obtener una vista del rendimiento general, los errores, la capacidad y el estado operativo de todos los recursos de Azure Cosmos DB en una experiencia interactiva unificada y aproveche las otras características de Azure Monitor para el análisis detallado y las alertas. Para obtener más información, consulte el artículo [Exploración de la información de Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md).

> [!NOTE]
> Cuando cree contenedores, asegúrese de no utilizar el mismo nombre en dos de ellos con distintas mayúsculas y minúsculas. Algunos componentes de la plataforma de Azure no distinguen mayúsculas de minúsculas y esto puede producir confusión o problemas con los datos telemetría y las acciones que se realicen en los contenedores con estos nombres.

## <a name="monitoring-data"></a><a id="monitoring-from-azure-cosmos-db"></a> Supervisión de datos 

Azure Cosmos DB recopila los mismos tipos de datos de supervisión que otros recursos de Azure, que se describen en [Supervisión de datos de recursos de Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). Consulte [Referencia de datos de supervisión de Azure Cosmos DB](monitor-cosmos-db-reference.md) para una referencia detallada de los registros y las métricas creados por Azure Cosmos DB.

La página **Información general** de Azure Portal para cada base de datos de Azure Cosmos incluye una breve vista del uso de la base de datos, incluidos su solicitud y el uso de facturación por hora. Esta información es útil, pero solo una pequeña cantidad de datos de supervisión está disponible. Algunos de estos datos se recopilan automáticamente y están disponibles para su análisis en cuanto se crea la base de datos, mientras que se puede habilitar la recopilación de datos adicional con alguna configuración.

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="Página de información general":::

## <a name="collection-and-routing"></a>Recopilación y enrutamiento

Las métricas de la plataforma y el registro de actividad se recopilan y almacenan de forma automática, pero se pueden enrutar a otras ubicaciones mediante una configuración de diagnóstico.  

Los registros de recursos no se recopilan ni almacenan hasta que se crea una configuración de diagnóstico y se enrutan a una o varias ubicaciones.

Consulte [Creación de una configuración de diagnóstico para recopilar registros de plataforma y métricas en Azure](cosmosdb-monitor-resource-logs.md) para conocer el proceso detallado para crear una configuración de diagnóstico mediante Azure Portal, junto con algunos ejemplos de consulta de diagnóstico. Cuando se crea una configuración de diagnóstico, se especifican las categorías de registros que se van a recopilar.

En las secciones siguientes se describen las métricas y los registros que se pueden recopilar.

## <a name="analyzing-metrics"></a><a id="analyze-metric-data"></a> Análisis de métricas

Azure Cosmos DB proporciona una experiencia personalizada para trabajar con métricas. Puede analizar las métricas de Azure Cosmos DB con métricas de otros servicios de Azure mediante el explorador de métricas abriendo **Métricas** en el menú de **Azure Monitor**. Consulte [Introducción al explorador de métricas de Azure](../azure-monitor/essentials/metrics-getting-started.md) para más información sobre esta herramienta. También puede consultar cómo supervisar la [latencia del servidor](monitor-server-side-latency.md), el [uso de las unidades de solicitud](monitor-request-unit-usage.md) y el [uso normalizado de unidades de solicitud](monitor-normalized-request-units.md) para los recursos de Azure Cosmos DB.

Para ver una lista de las métricas de la plataforma recopiladas para Azure Cosmos DB, vea [Supervisión de métricas de referencia de datos de Azure Cosmos DB](monitor-cosmos-db-reference.md#metrics).

Todas las métricas de Azure Cosmos DB se encuentran en el espacio de nombres de las **métricas estándar de Cosmos DB**. Puede usar las siguientes dimensiones con estas métricas cuando agregue un filtro a un gráfico:

* CollectionName
* DatabaseName
* OperationType
* Region
* StatusCode

Como referencia, puede ver una lista de [todas las métricas de recursos que se admiten en Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Visualización de las métricas de nivel de operación para Azure Cosmos DB

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Seleccione **Monitor** en la barra de navegación izquierda y, a continuación, seleccione **Métricas**.

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Panel Métricas en Azure Monitor":::

1. En el panel **Métricas** panel > **Seleccionar un recurso** > elija la **suscripción**, y el **grupo de recursos** requeridos. En **Tipo de recurso**, seleccione **Cuentas de Azure Cosmos DB**, elija una de las cuentas de Azure Cosmos existentes y seleccione **Aplicar**.

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="Elección de una cuenta de Cosmos DB para ver las métricas":::

1. A continuación, puede seleccionar una métrica de la lista de métricas disponibles. Puede seleccionar métricas específicas de unidades de solicitud, almacenamiento, latencia, disponibilidad, Cassandra, etc. Para conocer los detalles de todas las métricas disponibles en esta lista, consulte el artículo [Métricas por categoría](monitor-cosmos-db-reference.md). En este ejemplo, vamos a seleccionar **Unidades de solicitud** y **Prom.** como valor de agregación.

   Además de estos detalles, también puede seleccionar los valores de **Intervalo de tiempo** y **Granularidad de tiempo** de las métricas. Como máximo, puede ver las métricas de los últimos 30 días.  Después de aplicar el filtro, se muestra un gráfico basado en dicho filtro. Puede ver el número medio de unidades de solicitud consumidas por minuto durante el período seleccionado.  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Elección de una métrica en Azure Portal":::

### <a name="add-filters-to-metrics"></a>Incorporación de filtros a métricas

También puede filtrar las métricas y el gráfico que se muestra por una propiedad **CollectionName**, **DatabaseName**, **OperationType**, **Region** y **StatusCode** concreta. Para filtrar las métricas, seleccione **Agregar filtro** y elija la propiedad necesaria, como por ejemplo **OperationType** y seleccione un valor como **Query**. A continuación, el gráfico muestra las unidades de solicitud consumidas por la operación de consulta durante el período seleccionado. Las operaciones ejecutadas a través del procedimiento almacenado no se registran, por lo que no están disponibles en la métrica OperationType.

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="Adición de un filtro para seleccionar la granularidad de la métrica":::

Para agrupar las métricas puede usar la opción **Apply splitting** (Aplicar división). Por ejemplo, puede agrupar las unidades de solicitud por tipo de operación y ver el gráfico de todas las operaciones a la vez, como se muestra en la siguiente imagen:

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="Adición del filtro para aplicar división":::

## <a name="analyzing-logs"></a><a id="analyze-log-data"></a> Análisis de registros

Los datos de los registros de Azure Monitor se almacenan en tablas, cada una con un conjunto propio de propiedades únicas.

Todos los registros de recursos de Azure Monitor tienen los mismos campos seguidos de campos específicos del servicio. El esquema común se describe en [Esquema de registros de recursos de Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). Para ver una lista de los tipos de registros de recursos recopiladas para Azure Cosmos DB, vea [Supervisión de la referencia de datos de Azure Cosmos DB](monitor-cosmos-db-reference.md#resource-logs).

El [registro de actividad](../azure-monitor/essentials/activity-log.md) es un registro de plataforma de Azure que proporciona conclusiones sobre los eventos del nivel de suscripción. Puede verlo de forma independiente o enrutarlo a registros de Azure Monitor, donde puede realizar consultas mucho más complejas mediante Log Analytics.  

Azure Cosmos DB almacena los datos en las tablas siguientes.

| Tabla | Descripción |
|:---|:---|
| AzureDiagnostics | Tabla común que utilizan varios servicios para almacenar registros de recursos. Los registros de recursos de Azure Cosmos DB se pueden identificar con `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Tabla común que almacena todos los registros del registro de actividad.

### <a name="sample-kusto-queries"></a>Ejemplos de consultas de Kusto

> [!IMPORTANT]
> Al seleccionar **Registros** en el menú de Azure Cosmos DB, se abre Log Analytics con el ámbito de la consulta establecido en la cuenta de Azure Cosmos DB. Esto significa que las consultas de registro solo incluirán datos de ese recurso. Si quiere ejecutar una consulta que incluya datos de otras cuentas o de otros servicios de Azure, seleccione **Registros** en el menú **Azure Monitor**. Consulte [Ámbito e intervalo de tiempo de una consulta de registro en Log Analytics de Azure Monitor](../azure-monitor/logs/scope.md) para obtener más información.

Estas son algunas consultas que puede escribir en la barra de búsqueda **Búsqueda de registros** para ayudarle a supervisar los recursos de Azure Cosmos. Estas consultas funcionan con el [nuevo lenguaje](../azure-monitor/logs/log-query-overview.md).

* Para consultar todos los registros de diagnóstico de Azure Cosmos DB durante el período de tiempo especificado:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* Para consultar todas las operaciones, agrupadas por recurso:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Para consultar toda la actividad de los usuarios, agrupada por recurso:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="alerts"></a>Alertas

Las alertas de Azure Monitor le informan de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Permiten identificar y solucionar las incidencias en el sistema antes de que los clientes puedan verlos. Puede establecer alertas en [métricas](../azure-monitor/alerts/alerts-metric-overview.md), [registros](../azure-monitor/alerts/alerts-unified-log.md) y el [registro de actividad](../azure-monitor/alerts/activity-log-alerts.md). Cada tipo de alerta tiene sus ventajas y desventajas.

Por ejemplo, en la tabla siguiente se enumeran algunas reglas de alerta de los recursos. En Azure Portal, puede encontrar una lista detallada de reglas de alerta. Para más información, consulte el artículo [Configuración de alertas](create-alerts.md).  

| Tipo de alerta | Condición | Descripción  |
|:---|:---|:---|
|Limitación de velocidad en unidades de solicitud (alerta de métrica) |Nombre de la dimensión: StatusCode, Operador: Es igual a, Valores de dimensión: 429  | Genera una alerta si el contenedor o una base de datos han superado el límite de rendimiento aprovisionado. |
|Región conmutada por error |Operador: Mayor que, Tipo de agregación: Recuento, Valor de umbral: 1 | Cuando se conmuta por error una sola región. Esta alerta es útil si no ha habilitado la conmutación automática por error. |
| Rotar claves (alerta de registro de actividad)| Nivel de evento: Informativo, Estado: iniciado| Genera una alerta cuando se rotan las claves de cuenta. Puede actualizar la aplicación con las nuevas claves. |

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Supervisión de Azure Cosmos DB mediante programación

Las métricas de nivel de cuenta disponibles en el portal, como el uso de almacenamiento de cuenta y el total de solicitudes, no están disponibles mediante las API de SQL. Sin embargo, puede recuperar datos de uso en el nivel de colección mediante las API de SQL. Para recuperar datos de nivel de colección, haga lo siguiente:

* Para usar la API de REST, [ejecute una operación GET en la colección](/rest/api/cosmos-db/get-a-collection). La información de cuota y uso de la colección se devuelve en los encabezados x-ms-resource-quota y x-ms-resource-usage de la respuesta.

* Para usar el SDK de .NET, use el método [DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync), que devuelve un objeto [ResourceResponse](/dotnet/api/microsoft.azure.documents.client.resourceresponse-1) que contiene varias propiedades de uso, como **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** y otras más.

Para acceder a métricas adicionales, use el [SDK de Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Se pueden recuperar definiciones de métricas mediante la llamada a:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

Para recuperar las métricas individuales, use el siguiente formato:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

Para más información, vea el artículo sobre la [API de REST de supervisión de Azure](../azure-monitor/essentials/rest-api-walkthrough.md).

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Referencia de datos de supervisión de Azure Cosmos DB](monitor-cosmos-db-reference.md) para una referencia de los registros y las métricas creados por Azure Cosmos DB.
* Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
