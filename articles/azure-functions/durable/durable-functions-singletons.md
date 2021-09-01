---
title: 'Singletons en Durable Functions: Azure'
description: Aprenda a utilizar singletons en la extensión Durable Functions para Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: azfuncdf
ms.openlocfilehash: 82b3db8d65093f6cbb4d4a3013c396e5b6caf795
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038160"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestadores singleton con Durable Functions (Azure Functions)

Para trabajos en segundo plano, a menudo es necesario asegurarse de que solo se ejecute una instancia de un orquestador determinado a la vez. Puede asegurar este tipo de comportamiento de singleton en [Durable Functions](durable-functions-overview.md) asignando un identificador de instancia determinado a un orquestador durante su creación.

## <a name="singleton-example"></a>Ejemplo de singleton

En el ejemplo siguiente se muestra una función de desencadenador de HTTP que crea una orquestación de trabajo en segundo plano de singleton. El código garantiza que solo existe una instancia de un identificador de instancia especificado.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists or an existing one stopped running(completed/failed/terminated).
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Completed 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Failed 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        // An instance with the specified ID doesn't exist or an existing one stopped running, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists or an existing one still running, don't create one.
        return new HttpResponseMessage(HttpStatusCode.Conflict)
        {
            Content = new StringContent($"An instance with ID '{instanceId}' already exists."),
        };
    }
}
```

> [!NOTE]
> El código de C# anterior corresponde a Durable Functions 2.x. En el caso de Durable Functions 1.x, debe usar el atributo `OrchestrationClient` en lugar del atributo `DurableClient`, además de usar el tipo de parámetro `DurableOrchestrationClient` en lugar de `IDurableOrchestrationClient`. Para obtener más información sobre las diferencias entre versiones, vea el artículo [Versiones de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists or an existing one stopped running(completed/failed/terminated).
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance 
        || existingInstance.runtimeStatus == "Completed" 
        || existingInstance.runtimeStatus == "Failed" 
        || existingInstance.runtimeStatus == "Terminated") {
        // An instance with the specified ID doesn't exist or an existing one stopped running, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists or an existing one still running, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

# <a name="python"></a>[Python](#tab/python)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**__init__.py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = req.route_params['instanceId']
    function_name = req.route_params['functionName']

    existing_instance = await client.get_status(instance_id)

    if existing_instance.runtime_status in [df.OrchestrationRuntimeStatus.Completed, df.OrchestrationRuntimeStatus.Failed, df.OrchestrationRuntimeStatus.Terminated, None]:
        event_data = req.get_body()
        instance_id = await client.start_new(function_name, instance_id, event_data)
        logging.info(f"Started orchestration with ID = '{instance_id}'.")
        return client.create_check_status_response(req, instance_id)
    else:
        return {
            'status': 409,
            'body': f"An instance with ID '${existing_instance.instance_id}' already exists"
        }

```

---

De forma predeterminada, los identificadores de instancia son identificadores únicos globales generados aleatoriamente. Aunque en el ejemplo anterior, el identificador de instancia se pasa en los datos de ruta desde la dirección URL. El código llama a `GetStatusAsync` (C#), `getStatus` (JavaScript) o `get_status` (Python) para comprobar si ya se está ejecutando una instancia con el identificador especificado. Si no se está ejecutando dicha instancia, se crea otra instancia con ese identificador.

> [!NOTE]
> En este ejemplo hay una posible condición de carrera. Si dos instancias de **HttpStartSingle** se ejecutan simultáneamente, ambas llamadas de función notificarán un resultado satisfactorio, pero realmente solo se iniciará una instancia de orquestación. Según los requisitos, esto puede tener efectos secundarios no deseados. Por este motivo, es importante asegurarse de que no hay dos solicitudes que puedan ejecutar esta función de desencadenador simultáneamente.

Los detalles de implementación de la función de orquestador no importan realmente. Podría tratarse de una función de orquestador normal, con un principio y un final, o de una que se ejecuta sin fin (es decir, una [orquestación infinita](durable-functions-eternal-orchestrations.md)). Lo importante es que haya solo una instancia en ejecución cada vez.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Obtener información acerca de las características HTTP nativas de las orquestaciones](durable-functions-http-features.md)
