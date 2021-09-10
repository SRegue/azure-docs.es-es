---
title: Recopilar y analizar registros de recursos
description: Obtenga información sobre cómo enviar registros de recursos y datos de eventos de grupos de contenedores en Azure Container Instances a los registros de Azure Monitor.
ms.topic: article
ms.date: 07/13/2020
ms.openlocfilehash: 4c43d16c7df7ef54e401966e0c114de4d79cbdac
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112071"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Registro de instancias y grupos de contenedores con registros de Azure Monitor

Las áreas de trabajo de Log Analytics proporcionan una ubicación centralizada para almacenar y consultar datos de registro no solo de los recursos de Azure, sino también de los recursos locales y de los recursos de otras nubes. Azure Container Instances incluye compatibilidad integrada para el envío de registros y datos de evento a los registros de Azure Monitor.

Para enviar un registro de grupo de contenedores y datos de eventos a los registros de Azure Monitor, especifique una clave y un identificador de un área de trabajo de Log Analytics existente al configurar un grupo de contenedores. 

En las secciones siguientes se describe cómo crear un grupo de contenedores con el registro habilitado y cómo consultar registros. También puede [actualizar un grupo de contenedores](container-instances-update.md) con un identificador de área de trabajo y una clave de área de trabajo para habilitar el registro.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Actualmente, solo puede enviar datos de eventos desde instancias de contenedor de Linux a Log Analytics.

## <a name="prerequisites"></a>Prerrequisitos

Para habilitar el registro en las instancias de los contenedores, necesita lo siguiente:

* [Área de trabajo de Log Analytics](../azure-monitor/logs/quick-create-workspace.md)
* [CLI de Azure](/cli/azure/install-azure-cli) (o [Cloud Shell](../cloud-shell/overview.md))

## <a name="get-log-analytics-credentials"></a>Obtención de las credenciales de Log Analytics

Azure Container Instances necesita permiso para enviar datos a su área de trabajo de Log Analytics. Para conceder este permiso y habilitar el registro, es preciso proporcionar el identificador del área de trabajo de Log Analytics y una de sus claves (la principal o la secundaria) al crear el grupo de contenedores.

Para obtener el identificador y la clave principal del área de trabajo de Log Analytics:

1. Vaya al área de trabajo de Log Analytics en Azure Portal
1. En **Configuración**, seleccione **Agents management** (Administración de agentes).
1. Anote el valor de:
   * **Id. de área de trabajo**
   * **Clave principal**

## <a name="create-container-group"></a>Creación de un grupo de contenedores

Ahora que tiene el identificador y la clave principal del área de trabajo de Log Analytics, ya puede crear un grupo de contenedores con el registro habilitado.

En los ejemplos siguientes se muestran dos maneras de crear un grupo de contenedores formado por un solo contenedor [fluentd][fluentd]: CLI de Azure y la CLI de Azure con una plantilla de YAML. El contenedor fluentd genera varias líneas de salida en su configuración predeterminada. Dado que esta salida se envía a su área de trabajo de Log Analytics, sirve para mostrar la visualización y consulta de registros.

### <a name="deploy-with-azure-cli"></a>Implementación con la CLI de Azure

Para implementar con la CLI de Azure, especifique los parámetros `--log-analytics-workspace` y `--log-analytics-workspace-key` en el comando [az container create][az-container-create]. Antes de ejecutar el siguiente comando, reemplace los dos valores de área de trabajo por los valores obtenidos en el paso anterior (y actualice el nombre del grupo de recursos).

> [!NOTE]
> En el ejemplo siguiente se extrae una imagen de contenedor público de Docker Hub. Se recomienda configurar un secreto de extracción para autenticarse mediante una cuenta de Docker Hub en lugar de realizar una solicitud de extracción anónima. Para mejorar la confiabilidad al trabajar con contenido público, importe y administre la imagen en un registro de contenedor privado de Azure. [Más información sobre cómo trabajar con imágenes públicas.](../container-registry/buffer-gate-public-content.md)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Implementación con YAML

Utilice este método si prefiere implementar grupos de contenedores con YAML. El siguiente fragmento de código YAML define un grupo de contenedores con un solo contenedor. Copie el código YAML en un nuevo archivo y sustituya `LOG_ANALYTICS_WORKSPACE_ID` y `LOG_ANALYTICS_WORKSPACE_KEY` por los valores que obtuvo en el paso anterior. Guarde el archivo con el nombre **deploy-aci.yaml**.

> [!NOTE]
> En el ejemplo siguiente se extrae una imagen de contenedor público de Docker Hub. Se recomienda configurar un secreto de extracción para autenticarse mediante una cuenta de Docker Hub en lugar de realizar una solicitud de extracción anónima. Para mejorar la confiabilidad al trabajar con contenido público, importe y administre la imagen en un registro de contenedor privado de Azure. [Más información sobre cómo trabajar con imágenes públicas.](../container-registry/buffer-gate-public-content.md)

```yaml
apiVersion: 2019-12-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

A continuación, ejecute el siguiente comando para implementar el grupo de contenedores. Reemplace `myResourceGroup` por un grupo de recursos de su suscripción (o bien cree antes un grupo de recursos denominado "myResourceGroup"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Debería recibir una respuesta de Azure con detalles de implementación poco después de emitir el comando.

## <a name="view-logs"></a>Ver registros

Una vez que haya implementado el grupo de contenedores, las primeras entradas de registro pueden tardar varios minutos (hasta 10) en aparecer en Azure Portal. 

Para ver los registros del grupo de contenedores en la tabla `ContainerInstanceLog_CL`:

1. Vaya al área de trabajo de Log Analytics en Azure Portal
1. En **General**, seleccione **Registros**.  
1. Escriba la consulta siguiente: `ContainerInstanceLog_CL | limit 50`
1. Seleccione **Ejecutar**.

Debería ver que la consulta muestra varios resultados. Si no ve ninguno, espere unos minutos y seleccione el botón **Ejecutar** para volver a ejecutar la consulta. De forma predeterminada, las entradas del registro aparecen en formato de **tabla**. Luego puede expandir una fila para ver el contenido de una entrada de registro individual.

![Resultados de Búsqueda de registros en Azure Portal][log-search-01]

## <a name="view-events"></a>Ver eventos

También puede ver los eventos de las instancias de contenedor en Azure Portal. Los eventos incluyen la hora a la que se crea la instancia y el momento en que se inicia. Para ver los datos de evento en la tabla `ContainerEvent_CL`:

1. Vaya al área de trabajo de Log Analytics en Azure Portal
1. En **General**, seleccione **Registros**.  
1. Escriba la consulta siguiente: `ContainerEvent_CL | limit 50`
1. Seleccione **Ejecutar**.

Debería ver que la consulta muestra varios resultados. Si no ve ninguno, espere unos minutos y seleccione el botón **Ejecutar** para volver a ejecutar la consulta. De forma predeterminada, las entradas aparecen en formato de **tabla**. Luego puede expandir una fila para ver el contenido de una entrada individual.

![Resultados de la búsqueda de registros en Azure Portal][log-search-02]

## <a name="query-container-logs"></a>Consulta de registros de contenedores

Los registros de Azure Monitor incluyen un amplio [lenguaje de consulta][query_lang] para poder extraer información de miles de líneas de la salida del registro.

La estructura básica de una consulta consiste en la tabla de origen (en este artículo, `ContainerInstanceLog_CL` o `ContainerEvent_CL`) seguida de una serie de operadores separados por el carácter de barra vertical (`|`). Puede encadenar varios operadores para refinar los resultados y realizar funciones avanzadas.

Para ver los resultados de una consulta de ejemplo, pegue la siguiente consulta en el cuadro de texto de consulta y seleccione el botón **Ejecutar** para ejecutar la consulta. Esta consulta muestra todas las entradas de registro cuyo campo "Message" contiene la palabra "warn":

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

También se admiten consultas más complejas. Por ejemplo, esta consulta muestra solo las entradas de registro del grupo de contenedores "mycontainergroup001" generadas en la última hora:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="log-schema"></a>Esquema de registro

> [!NOTE]
> Algunas de las columnas enumeradas a continuación solo existen como parte del esquema y no tendrán ningún dato emitido en los registros. Estas columnas se indican a continuación con una descripción de "Vacío".

### <a name="containerinstancelog_cl"></a>ContainerInstanceLog_CL

|Columna|Tipo|Descripción|
|-|-|-|
|Computer|string|Vacío|
|ContainerGroup_s|string|Nombre del grupo de contenedores asociado al registro.|
|ContainerID_s|string|Identificador único para el contenedor asociado al registro.|
|ContainerImage_s|string|Nombre de la imagen de contenedor asociada al registro.|
|Location_s|string|Ubicación del recurso asociado al registro.|
|Message|string|Si procede, el mensaje del contenedor.|
|OSType_s|string|Nombre del sistema operativo en el que se basa el contenedor.|
|RawData|string|Vacío|
|ResourceGroup|string|Nombre del grupo de recursos al que está asociado el registro.|
|Source_s|string|Nombre del componente de registro, "LoggingAgent".|
|SubscriptionId|string|Identificador único de la suscripción a la que está asociado el registro.|
|TimeGenerated|datetime|Marca de tiempo de cuando el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento.|
|Tipo|string|Nombre de la tabla.|
|_ResourceId|string|Identificador único del recurso al que está asociado el registro.|
|_SubscriptionId|string|Identificador único de la suscripción a la que está asociado el registro.|

### <a name="containerevent_cl"></a>ContainerEvent_CL

|Columna|Tipo|Descripción|
|-|-|-|
|Computer|string|Vacío|
|ContainerGroupInstanceId_g|string|Identificador único del grupo de contenedores asociado al registro.|
|ContainerGroup_s|string|Nombre del grupo de contenedores asociado al registro.|
|ContainerName_s|string|Nombre del contenedor asociado al registro.|
|Count_d|real|Número de veces que se ha producido el evento desde el último sondeo.|
|FirstTimestamp_t|datetime|Marca de tiempo de la primera vez que se produjo el evento.|
|Location_s|string|Ubicación del recurso asociado al registro.|
|Message|string|Si procede, el mensaje del contenedor.|
|OSType_s|string|Nombre del sistema operativo en el que se basa el contenedor.|
|RawData|string|Vacío|
|Reason_s|string|Vacío|
|ResourceGroup|string|Nombre del grupo de recursos al que está asociado el registro.|
|SubscriptionId|string|Identificador único de la suscripción a la que está asociado el registro.|
|TimeGenerated|datetime|Marca de tiempo de cuando el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento.|
|Tipo|string|Nombre de la tabla.|
|_ResourceId|string|Identificador único del recurso al que está asociado el registro.|
|_SubscriptionId|string|Identificador único de la suscripción a la que está asociado el registro.|

## <a name="next-steps"></a>Pasos siguientes

### <a name="azure-monitor-logs"></a>Registros de Azure Monitor

Para más información acerca de cómo realizar consultas en registros y configurar alertas en registros de Azure Monitor, consulte:

* [Descripción de las búsquedas de registros en los registros de Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Alertas unificadas en Azure Monitor](../azure-monitor/alerts/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Supervisión de la CPU y la memoria de los contenedores

Para obtener información acerca de la supervisión de los recursos de CPU y de memoria de un instancia de contenedor, consulte:

* [Supervisión de los recursos de los contenedores en Azure Container Instances](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: /azure/data-explorer/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create