---
title: Descripción del lenguaje de consultas
description: Describe las tablas de Resource Graph y los tipos de datos, los operadores y las funciones de Kusto disponibles que se pueden usar con Azure Resource Graph.
ms.date: 06/11/2021
ms.topic: conceptual
ms.openlocfilehash: f9a9d6b937256787d0457f150d5f3dfaca81d9cd
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112020248"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Información del lenguaje de consulta de Azure Resource Graph

El lenguaje de consulta de Azure Resource Graph admite numerosos operadores y funciones. Cada uno funciona en función del [lenguaje de consulta de Kusto (KQL)](/azure/kusto/query/index). Para información sobre el lenguaje de consulta usado por Resource Graph, comience con el [tutorial para KQL](/azure/kusto/query/tutorial).

En este artículo se tratan los componentes de idioma admitidos por Resource Graph:

- [Tablas de Resource Graph](#resource-graph-tables)
- [Elementos del lenguaje personalizado de Resource Graph](#resource-graph-custom-language-elements)
- [Elementos del lenguaje KQL admitidos](#supported-kql-language-elements)
- [Ámbito de la consulta](#query-scope)
- [Caracteres de escape](#escape-characters)

## <a name="resource-graph-tables"></a>Tablas de Resource Graph

Resource Graph proporciona varias tablas para los datos que almacena sobre los tipos de recursos de Azure Resource Manager y sus propiedades. Algunas tablas se pueden utilizar con los operadores `join` o `union` para obtener propiedades de tipos de recursos relacionados. Esta es la lista de tablas disponibles en Resource Graph:

|Tabla de Resource Graph |¿Se puede usar `join` con otras tablas? |Descripción |
|---|---|---|
|Recursos |Sí |La tabla predeterminada, si no se ha definido ninguna en la consulta. La mayoría de los tipos de recursos y propiedades de Resource Manager están aquí. |
|ResourceContainers |Sí |Incluye los datos y los tipos de recursos de suscripción (en versión preliminar: `Microsoft.Resources/subscriptions`) y grupo de recursos (`Microsoft.Resources/subscriptions/resourcegroups`). |
|AdvisorResources |Sí (versión preliminar) |Incluye recursos _relacionados_ con `Microsoft.Advisor`. |
|AlertsManagementResources |Sí (versión preliminar) |Incluye recursos _relacionados_ con `Microsoft.AlertsManagement`. |
|ExtendedLocationResources |No |Incluye recursos _relacionados_ con `Microsoft.ExtendedLocation`. |
|GuestConfigurationResources |No |Incluye recursos _relacionados_ con `Microsoft.GuestConfiguration`. |
|KubernetesConfigurationResources |No |Incluye recursos _relacionados_ con `Microsoft.KubernetesConfiguration`. |
|MaintenanceResources |Parcial, solo conectar _con_. (versión preliminar) |Incluye recursos _relacionados_ con `Microsoft.Maintenance`. |
|PatchAssessmentResources|No |Incluye los recursos _relacionados_ con la evaluación de revisiones de Azure Virtual Machines. |
|PatchInstallationResources|No |Incluye los recursos _relacionados_ con la instalación de revisiones de Azure Virtual Machines. |
|PolicyResources |No |Incluye recursos _relacionados_ con `Microsoft.PolicyInsights`. (**Versión preliminar**) |
|RecoveryServicesResources |Parcial, solo unirse _con_. (versión preliminar) |Incluye recursos _relacionados_ con `Microsoft.DataProtection` y `Microsoft.RecoveryServices`. |
|SecurityResources |Sí (versión preliminar) |Incluye recursos _relacionados_ con `Microsoft.Security`. |
|ServiceHealthResources |No (vista previa) |Incluye recursos _relacionados_ con `Microsoft.ResourceHealth`. |
|WorkloadMonitorResources |No |Incluye recursos _relacionados_ con `Microsoft.WorkloadMonitor`. |

Para ver una lista completa, incluidos los tipos de recursos, consulte [Referencia: tablas y tipos de recursos admitidos](../reference/supported-tables-resources.md).

> [!NOTE]
> _Recursos_ es la tabla predeterminada. Al consultar la tabla _Recursos_, no es necesario proporcionar el nombre de la tabla a menos que se utilicen `join` o `union`. Sin embargo, el procedimiento recomendado es incluir siempre la tabla inicial en la consulta.

Use Azure Resource Graph en el portal para detectar qué tipos de recursos están disponibles en cada tabla. Como alternativa, utilice una consulta como `<tableName> | distinct type` para obtener una lista de los tipos de recursos que admite la tabla de Resource Graph que existe en su entorno.

En la consulta siguiente se muestra un uso sencillo de `join`. El resultado de la consulta combina las columnas y los nombres de columna duplicados de la tabla combinada, _ResourceContainers_ en este ejemplo, se anexan con **1**. Como la tabla _ResourceContainers_ tiene tipos para suscripciones y grupos de recursos, se puede usar cualquier tipo para unirse al recurso desde la tabla _Resources_.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

En la consulta siguiente se muestra un uso más complejo de `join`. En primer lugar, la consulta usa `project` para obtener los campos de _Resources_ para el tipo de recurso de los almacenes de Azure Key Vault. En el paso siguiente se usa `join` para combinar los resultados con _ResourceContainers_, donde el tipo es una suscripción _en_ una propiedad que se encuentra en el elemento `project` de la primera tabla y en el elemento `project` de la tabla combinada. El cambio de nombre del campo evita que `join` lo agregue como _name1_, ya que el campo ya se encuentra en el elemento project de _Resources_. El resultado de la consulta es un solo almacén de claves que muestra su tipo, nombre, ubicación y el grupo de recursos del almacén de claves, junto con el nombre de la suscripción en que se encuentra.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| project name, type, location, subscriptionId, resourceGroup
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, location, resourceGroup, SubName
| limit 1
```

> [!NOTE]
> Al limitar los resultados de `join` con `project`, la propiedad utilizada por `join` para relacionar las dos tablas, _subscriptionId_ en el ejemplo anterior, debe incluirse en `project`.

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>Propiedades extendidas (versión preliminar)

Como característica _en vista previa_, algunos de los tipos de recursos de Resource Graph tienen propiedades adicionales relacionadas con el tipo que se pueden consultar de manera adicional a las propiedades proporcionadas por Azure Resource Manager. Este conjunto de valores, conocido como _propiedades extendidas_, existe en un tipo de recurso compatible con `properties.extended`. Para ver qué tipos de recursos tienen _propiedades extendidas_, use la siguiente consulta:

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

Ejemplo: Obtenga el recuento de máquinas virtuales mediante `instanceView.powerState.code`:

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Elementos del lenguaje personalizado de Resource Graph

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>Sintaxis de consulta compartida (versión preliminar)

Como característica en vista previa, se puede acceder directamente a una [consulta compartida](../tutorials/create-share-query.md) en una consulta de Resource Graph. Este escenario permite crear consultas estándar como consultas compartidas, así como reutilizarlas. Para llamar a una consulta compartida dentro de una consulta de Resource Graph, use la sintaxis `{{shared-query-uri}}`. El URI de la consulta compartida es el _identificador de recurso_ de la consulta compartida en la página **Configuración** de dicha consulta. En este ejemplo, el URI de la consulta compartida es `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS`.
Este URI apunta a la suscripción, al grupo de recursos y al nombre completo de la consulta compartida a la que se desea hacer referencia en otra consulta. Esta consulta es la misma que la creada en [Tutorial: Creación y uso compartido de una consulta](../tutorials/create-share-query.md).

> [!NOTE]
> No se puede guardar como consulta compartida una consulta que haga referencia a una consulta compartida.

Ejemplo 1: Solo de la consulta compartida únicamente

Los resultados de esta consulta de Resource Graph son los mismos que los de la consulta almacenada en la consulta compartida.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

Ejemplo 2: Inclusión de la consulta compartida como parte de una mayor

En primer lugar, esta consulta usa la consulta compartida y, a continuación, usa `limit` para restringir aún más los resultados.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>Elementos del lenguaje KQL admitidos

Resource Graph admite un subconjunto de todos los [tipos de datos](/azure/kusto/query/scalar-data-types/), [funciones escalares](/azure/kusto/query/scalarfunctions), [operadores escalares](/azure/kusto/query/binoperators) y [funciones de agregado](/azure/kusto/query/any-aggfunction) de KQL. Resource Graph admite [operadores tabulares](/azure/kusto/query/queries) específicos, algunos de los cuales tienen comportamientos distintos.

### <a name="supported-tabulartop-level-operators"></a>Operadores de nivel superior o tabulares compatibles

Esta es la lista de operadores tabulares de KQL admitidos por Resource Graph con ejemplos específicos:

|KQL |Consulta de ejemplo de Resource Graph |Notas |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Contador de almacenes de claves](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Mostrar los recursos que contienen almacenamiento](../samples/starter.md#show-storage) | |
|[extend](/azure/kusto/query/extendoperator) |[Count virtual machines by OS type](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Almacén de claves con el nombre de la suscripción](../samples/advanced.md#join) |Tipos de combinación admitidos: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Límite de 3 `join` en una sola consulta, 1 de los cuales puede ser un elemento `join` entre tablas. Si todos los usos de `join` entre tablas se realiza entre _Resources_ y _ResourceContainers_, se permiten tres `join` entre tablas. No se permiten las estrategias de combinación personalizadas, como la combinación de difusión. Para conocer las tablas que pueden usar `join`, consulte [Tablas de Resource Graph](#resource-graph-tables). |
|[limit](/azure/kusto/query/limitoperator) |[List all public IP addresses](../samples/starter.md#list-publicip) |Sinónimo de `take`. No funciona con [Skip](./work-with-data.md#skipping-records). |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Operador heredado, use `mv-expand` en su lugar. Máximo de _RowLimit_ de 400. El valor predeterminado es 128. |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[Enumeración de Cosmos DB con ubicaciones de escritura concretas](../samples/advanced.md#mvexpand-cosmosdb) |Máximo de _RowLimit_ de 400. El valor predeterminado es 128. Límite de 2 `mv-expand` en una sola consulta.|
|[order](/azure/kusto/query/orderoperator) |[List resources sorted by name](../samples/starter.md#list-resources) |Sinónimo de `sort` |
|[project](/azure/kusto/query/projectoperator) |[List resources sorted by name](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Eliminación de columnas de los resultados](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[List resources sorted by name](../samples/starter.md#list-resources) |Sinónimo de `order` |
|[summarize](/azure/kusto/query/summarizeoperator) |[Count Azure resources](../samples/starter.md#count-resources) |Solo la primera página simplificada |
|[take](/azure/kusto/query/takeoperator) |[List all public IP addresses](../samples/starter.md#list-publicip) |Sinónimo de `limit`. No funciona con [Skip](./work-with-data.md#skipping-records). |
|[top](/azure/kusto/query/topoperator) |[Show first five virtual machines by name and their OS type](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[Combinación de los resultados de dos consultas para formar un solo resultado](../samples/advanced.md#unionresults) |Tabla única permitida: _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_nombreDeColumna_\] _tabla_. Límite de 3 partes de `union` en una sola consulta. No se permite la resolución aproximada de tablas de segmento `union`. Se puede usar en una sola tabla o entre las tablas _Resources_ y _ResourceContainers_. |
|[where](/azure/kusto/query/whereoperator) |[Show resources that contain storage](../samples/starter.md#show-storage) | |

Hay un límite predeterminado de 3 operadores `join` y 3 operadores `mv-expand` en una única consulta del SDK de Resource Graph. Puede solicitar un aumento de estos límites para el inquilino en **Ayuda y soporte técnico**.

Para admitir la experiencia del portal "Abrir consulta", el explorador de Azure Resource Graph tiene un límite global más alto que el SDK de Resource Graph.

## <a name="query-scope"></a>Ámbito de la consulta

El ámbito de las suscripciones desde las que una consulta devuelve los recursos depende del método de acceso a Resource Graph. La CLI de Azure y Azure PowerShell rellenan la lista de suscripciones que se van a incluir en la solicitud, en función del contexto del usuario autorizado. Cada lista de suscripciones se puede definir manualmente con las **suscripciones** y los parámetros de **suscripción**, respectivamente.
En la API de REST y en todos los demás SDK, la lista de suscripciones en las que se incluyen los recursos debe definirse explícitamente como parte de la solicitud.

Como **versión preliminar**, la versión de API de REST `2020-04-01-preview` agrega una propiedad para limitar el ámbito de la consulta a un [grupo de administración](../../management-groups/overview.md). Esta API de versión preliminar también hace que la propiedad de suscripción sea opcional. Si no se define un grupo de administración o una lista de suscripciones, el ámbito de la consulta comprenderá todos los recursos, incluidos los recursos delegados de [Azure Lighthouse](../../../lighthouse/overview.md), a los que puede acceder el usuario autenticado. La nueva propiedad `managementGroupId` toma el id. del grupo de administración, que es diferente del nombre del grupo de administración. Cuando se especifica `managementGroupId`, se incluyen los recursos de las primeras 5000 suscripciones de la jerarquía de grupos de administración especificada. `managementGroupId` no se puede usar al mismo tiempo que `subscriptions`.

Ejemplo: Consulte todos los recursos de la jerarquía del grupo de administración denominado "Mi grupo de administración" que cuenta con el id. "myMG".

- URI DE LA API REST

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2020-04-01-preview
  ```

- Cuerpo de la solicitud

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroupId": "myMG"
  }
  ```

## <a name="escape-characters"></a>Carácter de escape

Algunos nombres de propiedad, como los que incluyen `.` o `$`, debe encapsularse o escapar en la consulta para que se interprete correctamente; de lo contrario, no proporcionan los resultados previstos.

- `.` -Encapsule el nombre de propiedad como en: `['propertyname.withaperiod']`

  Consulta de ejemplo que encapsula la propiedad _odata.type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` -Incluya un carácter de escape en el nombre de propiedad. El carácter de escape utilizado depende el shell desde donde se ejecute Resource Graph.

  - **bash** - `\`

    Consulta de ejemplo con escape de la propiedad _\$type_ en Bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -no escapa el carácter `$`.

  - **PowerShell** - ``` ` ```

    Consulta de ejemplo con escape de la propiedad _\$type_ en PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Pasos siguientes

- Vea el lenguaje en uso en[Consultas básicas](../samples/starter.md).
- Vea los usos avanzados en [Consultas avanzadas](../samples/advanced.md).
- Obtenga más información sobre cómo [explorar recursos](explore-resources.md).
