---
title: 'Administración de instancias con Durable Functions: Azure'
description: Aprenda a administrar instancias en la extensión Durable Functions para Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 05/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 04c3e9f1a5c5a1a23a618f3274057a5e03a9f0e1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752320"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Administración de instancias con Durable Functions en Azure

Las orquestaciones de Durable Functions son funciones con estado de larga duración que se pueden iniciar, consultar y finalizar mediante las API de administración integradas. En el [enlace del cliente de orquestación](durable-functions-bindings.md#orchestration-client) de Durable Functions se exponen también otras API de administración de instancias, como el envío de eventos externos a instancias, la purga del historial de instancias, etc. En este artículo se detallan todas las operaciones de administración de instancias admitidas.

## <a name="start-instances"></a>Inicio de instancias

Los métodos `StartNewAsync` (.NET), `startNew` (JavaScript) o `start_new` (Python) en el [enlace del cliente de orquestación](durable-functions-bindings.md#orchestration-client) inician una nueva instancia de orquestación. Internamente, este método escribe un mensaje a través del [proveedor de almacenamiento de Durable Functions](durable-functions-storage-providers.md) y, a continuación, se devuelve. Este mensaje desencadena de forma asincrónica el inicio de una [función de orquestación](durable-functions-types-features-overview.md#orchestrator-functions) con el nombre especificado.

Los parámetros para iniciar una nueva instancia de orquestación son los siguientes:

* **Name**: el nombre de la función de orquestador que programar.
* **Entrada**: todos los datos serializables con JSON que deben pasarse como entrada a la función de orquestador.
* **InstanceId**: (Opcional) El identificador único de la instancia. Si no se especifica este parámetro, el método utiliza un identificador aleatorio.

> [!TIP]
> Use un identificador aleatorio para el identificador de la instancia siempre que sea posible. Los identificadores de instancia aleatorios ayudan a garantizar una distribución equitativa de la carga al escalar las funciones de orquestador entre varias máquinas virtuales. El momento adecuado para usar identificadores de instancia no aleatorios es cuando el identificador debe proceder de un origen externo o cuando implementa el patrón de [orquestador singleton](durable-functions-singletons.md).

El código siguiente es una función de ejemplo que inicia una nueva instancia de orquestación:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> El código de C# anterior corresponde a Durable Functions 2.x. En el caso de Durable Functions 1.x, debe usar el atributo `OrchestrationClient` en lugar del atributo `DurableClient`, además de usar el tipo de parámetro `DurableOrchestrationClient` en lugar de `IDurableOrchestrationClient`. Para obtener más información sobre las diferencias entre versiones, vea el artículo [Versiones de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>A menos que se especifique lo contrario, en los ejemplos de esta página se usa el desencadenador HTTP con el siguiente archivo function.json.

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Este ejemplo corresponde a Durable Functions versión 2.x. En la versión 1.x, use `orchestrationClient` en lugar de `durableClient`.

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

# <a name="python"></a>[Python](#tab/python)

<a name="javascript-function-json"></a>A menos que se especifique lo contrario, en los ejemplos de esta página se usa el desencadenador HTTP con el siguiente archivo function.json.

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [    
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Este ejemplo corresponde a Durable Functions versión 2.x. En la versión 1.x, use `orchestrationClient` en lugar de `durableClient`.

**__init__.py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    
    instance_id = await client.start_new('HelloWorld', None, None)
    logging.log(f"Started orchestration with ID = ${instance_id}.")

```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

También puede iniciar una instancia directamente si usa el [comando `func durable start-new`](../functions-core-tools-reference.md#func-durable-start-new) de Core Tools, que acepta los parámetros siguientes:

* **`function-name` (obligatorio)** : nombre de la función que se va a iniciar.
* **`input` (opcional)** : entrada a la función, ya sea en línea o a través de un archivo JSON. En el caso de los archivos, agregue un prefijo a la ruta de acceso al archivo con `@`, como `@path/to/file.json`.
* **`id` (opcional)** : identificador de la instancia de orquestación. Si no se especifica este parámetro, el método utiliza un GUID aleatorio.
* **`connection-string-setting` (opcional)** : nombre de la configuración de la aplicación que contiene la cadena de conexión de almacenamiento que se va a usar. El valor predeterminado es AzureWebJobsStorage.
* **`task-hub-name` (opcional)** : nombre de la central de tareas de Durable Functions que se va a usar. El valor predeterminado es DurableFunctionsHub. También puede establecerlo en [host.json](durable-functions-bindings.md#host-json) utilizando durableTask:HubName.

> [!NOTE]
> Los comandos de Core Tools suponen que los ejecuta desde el directorio raíz de una aplicación de función. Si proporciona explícitamente los parámetros `connection-string-setting` y `task-hub-name`, puede ejecutar los comandos desde cualquier directorio. Aunque puede ejecutar estos comandos sin la ejecución de un host de la aplicación de función, es posible que no pueda observar algunos efectos propios de una situación en la que el host se está ejecutando. Por ejemplo, el comando `start-new` pone en cola un mensaje de inicio en el centro de tareas de destino, pero la orquestación no se ejecuta realmente a menos que haya un proceso de host de la aplicación de función en ejecución que pueda procesar el mensaje.

> [!NOTE]
> Actualmente, los comandos de Core Tools solo se admiten cuando se usa el [proveedor predeterminado de Azure Storage](durable-functions-storage-providers.md) para conservar el estado del entorno de ejecución.

El siguiente comando inicia la función denominada HelloWorld y le pasa el contenido del archivo `counter-data.json`:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Consulta de instancias

Después de iniciar nuevas instancias de orquestación, lo más probable es que tenga que consultar su estado del entorno de ejecución para saber si se están ejecutando, si se han completado o si se ha producido un error.

Los métodos `GetStatusAsync` (.NET), `getStatus` (JavaScript) o `get_status` (Python) en el [enlace del cliente de orquestación](durable-functions-bindings.md#orchestration-client) consultan el estado de una instancia de orquestación.

Toma los valores `instanceId` (obligatorio), `showHistory` (opcional), `showHistoryOutput` (opcional) y `showInput` (opcional) como parámetros.

* **`showHistory`** : si se establece en `true`, la respuesta contiene el historial de ejecución.
* **`showHistoryOutput`** : si se establece en `true`, el historial de ejecución contiene salidas de actividad.
* **`showInput`** : si se establece en `false`, la respuesta no contendrá la entrada de la función. El valor predeterminado es `true`.

El método devuelve un objeto con las siguientes propiedades:

* **Name**: el nombre de la función de orquestador.
* **InstanceId**: el identificador de instancia de la orquestación (debe ser el mismo que la entrada `instanceId`).
* **CreatedTime**: la hora en que la función de orquestador empezó a ejecutarse.
* **LastUpdatedTime**: la hora a la que la orquestación estableció el último punto de control.
* **Entrada**: la entrada de la función como un valor JSON. Este campo no se rellena si `showInput` es false.
* **CustomStatus**: estado de orquestación personalizada en formato JSON.
* **Salida**: la salida de la función como un valor JSON (si se ha completado la función). Si se produce un error en la función de orquestador, esta propiedad incluye los detalles del error. Si se finaliza la función de orquestador, esta propiedad incluye el motivo de la finalización (si lo hubiera).
* **RuntimeStatus**: Uno de los valores siguientes:
  * **Pending**: la instancia se ha programado, pero aún no ha empezado a ejecutarse.
  * **En ejecución**: la instancia ha empezado a ejecutarse.
  * **Completed**: la instancia se ha completado con normalidad.
  * **ContinuedAsNew**: la instancia se ha reiniciado con un nuevo historial. Este estado es transitorio.
  * **Error**: se ha producido un error en la instancia.
  * **Finalizada**: la instancia se ha detenido abruptamente.
* **History**: el historial de ejecución de la orquestación. Este campo solo se rellena si `showHistory` está establecido en `true`.

> [!NOTE]
> Los orquestadores no se marcan como `Completed` hasta que todas las tareas que tienen programadas han finalizado _y_ el orquestador ha vuelto. En otras palabras, no basta con que un orquestador alcance su instrucción `return` para que se marque como `Completed`. Esto es especialmente importante en aquellos casos en los que se usa `WhenAny`; estos orquestadores a menudo usan `return` antes de que se hayan ejecutado todas las tareas programadas.

Este método devuelve `null` (.NET), `undefined` (JavaScript) o `None` (Python) si la instancia no existe.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> El código de C# anterior corresponde a Durable Functions 2.x. En el caso de Durable Functions 1.x, debe usar el atributo `OrchestrationClient` en lugar del atributo `DurableClient`, además de usar el tipo de parámetro `DurableOrchestrationClient` en lugar de `IDurableOrchestrationClient`. Para obtener más información sobre las diferencias entre versiones, vea el artículo [Versiones de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
    // example: if status.runtimeStatus === df.OrchestrationRuntimeStatus.Running: ...
}
```

Consulte [Instancias de inicio](#javascript-function-json) para la configuración del archivo function.json.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    status = await client.get_status(instance_id)
    # do something based on the current status
    # example: if (existing_instance.runtime_status is df.OrchestrationRuntimeStatus.Running) { ...
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

También es posible obtener el estado de una instancia de orquestación directamente con el [comando `func durable get-runtime-status`](../functions-core-tools-reference.md#func-durable-get-runtime-status) de Core Tools.

> [!NOTE]
> Actualmente, los comandos de Core Tools solo se admiten cuando se usa el [proveedor predeterminado de Azure Storage](durable-functions-storage-providers.md) para conservar el estado del entorno de ejecución.

El comando `durable get-runtime-status` toma los parámetros siguientes:

* **`id` (obligatorio)** : identificador de la instancia de orquestación.
* **`show-input` (opcional)** : si se establece en `true`, la respuesta contiene la entrada de la función. El valor predeterminado es `false`.
* **`show-output` (opcional)** : si se establece en `true`, la respuesta contiene la salida de la función. El valor predeterminado es `false`.
* **`connection-string-setting` (opcional)** : nombre de la configuración de la aplicación que contiene la cadena de conexión de almacenamiento que se va a usar. El valor predeterminado es `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nombre de la central de tareas de Durable Functions que se va a usar. El valor predeterminado es `DurableFunctionsHub`. También puede establecerse en [host.json](durable-functions-bindings.md#host-json) mediante durableTask:HubName.

El siguiente comando recupera el estado (incluidas la entrada y salida) de una instancia con un identificador de instancia de orquestación de 0ab8c55a66644d68a3a8b220b12d209c. Se supone que está ejecutando el comando `func` desde el directorio raíz de la aplicación de función:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Puede utilizar el comando `durable get-history` para recuperar el historial de una instancia de orquestación. Toma los parámetros siguientes:

* **`id` (obligatorio)** : identificador de la instancia de orquestación.
* **`connection-string-setting` (opcional)** : nombre de la configuración de la aplicación que contiene la cadena de conexión de almacenamiento que se va a usar. El valor predeterminado es `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nombre de la central de tareas de Durable Functions que se va a usar. El valor predeterminado es `DurableFunctionsHub`. También puede establecerse en host.json mediante durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Consulta de todas las instancias

Puede usar el método [ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync) (.NET), [getStatusAll](/javascript/api/durable-functions/durableorchestrationclient#getstatusall--) (JavaScript) o `get_status_all` (Python) para consultar los estados de todas las instancias de orquestación en la [central de tareas](durable-functions-task-hubs.md). Este método devuelve una lista de objetos que representan las instancias de orquestación que coinciden con los parámetros de consulta.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var noFilter = new OrchestrationStatusQueryCondition();
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        noFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
    
    // Note: ListInstancesAsync only returns the first page of results.
    // To request additional pages provide the result.ContinuationToken
    // to the OrchestrationStatusQueryCondition's ContinuationToken property.
}
```

> [!NOTE]
> El código de C# anterior corresponde a Durable Functions 2.x. En el caso de Durable Functions 1.x, debe usar el atributo `OrchestrationClient` en lugar del atributo `DurableClient`, además de usar el tipo de parámetro `DurableOrchestrationClient` en lugar de `IDurableOrchestrationClient`. Para obtener más información sobre las diferencias entre versiones, vea el artículo [Versiones de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instances = await client.get_status_all()

    for instance in instances:
        logging.log(json.dumps(instance))
```

Consulte [Instancias de inicio](#javascript-function-json) para la configuración del archivo function.json.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

También es posible consultar instancias directamente con el [comando `func durable get-instances`](../functions-core-tools-reference.md#func-durable-get-instances) de Core Tools.

> [!NOTE]
> Actualmente, los comandos de Core Tools solo se admiten cuando se usa el [proveedor predeterminado de Azure Storage](durable-functions-storage-providers.md) para conservar el estado del entorno de ejecución.

El comando `durable get-instances` toma los parámetros siguientes:

* **`top` (opcional)** : este comando admite la paginación. Este parámetro corresponde al número de instancias recuperadas por solicitud. El valor predeterminado es 10.
* **`continuation-token` (opcional)** : un token para indicar qué página o sección de instancias se va a recuperar. Cada ejecución `get-instances` devuelve un token para el siguiente conjunto de instancias.
* **`connection-string-setting` (opcional)** : nombre de la configuración de la aplicación que contiene la cadena de conexión de almacenamiento que se va a usar. El valor predeterminado es `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nombre de la central de tareas de Durable Functions que se va a usar. El valor predeterminado es `DurableFunctionsHub`. También puede establecerse en [host.json](durable-functions-bindings.md#host-json) mediante durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Consulta de instancias con filtros

¿Qué ocurre si no necesita realmente toda la información que puede proporcionar una consulta de instancias estándar? Por ejemplo, ¿qué sucede si solo desea obtener la hora de creación de la orquestación o el estado del entorno de ejecución de la orquestación? Puede restringir la consulta aplicando filtros.

Use el método [ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.NET) o [getStatusBy](/javascript/api/durable-functions/durableorchestrationclient#getstatusby-date---undefined--date---undefined--orchestrationruntimestatus---) (JavaScript) para obtener una lista de instancias de orquestación que coincidan con un conjunto de filtros predefinidos.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    // Get the first 100 running or pending instances that were created between 7 and 1 day(s) ago
    var queryFilter = new OrchestrationStatusQueryCondition
    {
        RuntimeStatus = new[]
        {
            OrchestrationRuntimeStatus.Pending,
            OrchestrationRuntimeStatus.Running,
        },
        CreatedTimeFrom = DateTime.UtcNow.Subtract(TimeSpan.FromDays(7)),
        CreatedTimeTo = DateTime.UtcNow.Subtract(TimeSpan.FromDays(1)),
        PageSize = 100,
    };
    
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        queryFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
}
```

> [!NOTE]
> El código de C# anterior corresponde a Durable Functions 2.x. En el caso de Durable Functions 1.x, debe usar el atributo `OrchestrationClient` en lugar del atributo `DurableClient`, además de usar el tipo de parámetro `DurableOrchestrationClient` en lugar de `IDurableOrchestrationClient`. Para obtener más información sobre las diferencias entre versiones, vea el artículo [Versiones de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2021, 3, 10, 10, 1, 0),
        new Date(2021, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

Consulte [Instancias de inicio](#javascript-function-json) para la configuración del archivo function.json.

# <a name="python"></a>[Python](#tab/python)

```python
import logging
from datetime import datetime
import json
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.OrchestrationRuntimeStatus import OrchestrationRuntimeStatus

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    runtime_status = [OrchestrationRuntimeStatus.Completed, OrchestrationRuntimeStatus.Running]

    instances = await client.get_status_by(
        datetime(2021, 3, 10, 10, 1, 0),
        datetime(2021, 3, 10, 10, 23, 59),
        runtime_status
    )

    for instance in instances:
        logging.log(json.dumps(instance))
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

En Azure Functions Core Tools, también se puede usar el comando `durable get-instances` con filtros. Además de los parámetros `top`, `continuation-token`, `connection-string-setting` y `task-hub-name` mencionados anteriormente, puede usar tres parámetros de filtro (`created-after`, `created-before` y `runtime-status`).

> [!NOTE]
> Actualmente, los comandos de Core Tools solo se admiten cuando se usa el [proveedor predeterminado de Azure Storage](durable-functions-storage-providers.md) para conservar el estado del entorno de ejecución.

Estos son los parámetros del comando `durable get-instances`.

* **`created-after` (opcional)** : recuperación de las instancias creadas después de esta fecha y hora (UTC). Se aceptan valores de datetime con formato ISO 8601.
* **`created-before` (opcional)** : recuperación de las instancias creadas antes de esta fecha y hora (UTC). Se aceptan valores de datetime con formato ISO 8601.
* **`runtime-status` (opcional)** : Recupere las instancias con un estado determinado (por ejemplo, en ejecución o completado). Puede proporcionar varios estados (separados por espacios).
* **`top` (opcional)** : número de instancias recuperadas por solicitud. El valor predeterminado es 10.
* **`continuation-token` (opcional)** : un token para indicar qué página o sección de instancias se va a recuperar. Cada ejecución `get-instances` devuelve un token para el siguiente conjunto de instancias.
* **`connection-string-setting` (opcional)** : nombre de la configuración de la aplicación que contiene la cadena de conexión de almacenamiento que se va a usar. El valor predeterminado es `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nombre de la central de tareas de Durable Functions que se va a usar. El valor predeterminado es `DurableFunctionsHub`. También puede establecerse en [host.json](durable-functions-bindings.md#host-json) mediante durableTask:HubName.

Si no proporciona ningún filtro (`created-after`, `created-before`, o `runtime-status`), el comando simplemente recupera instancias `top`, sin tener en cuenta la hora de creación o el estado del entorno de ejecución.

```bash
func durable get-instances --created-after 2021-03-10T13:57:31Z --created-before  2021-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Finalización de instancias

Si tiene una instancia de orquestación que está tardando demasiado tiempo en ejecutarse, o si simplemente tiene que detenerla antes por cualquier motivo, puede finalizarla.

Puede usar los métodos `TerminateAsync` (.NET), `terminate` (JavaScript) o `terminate` (Python) del [enlace del cliente de orquestación](durable-functions-bindings.md#orchestration-client) para terminar las instancias. Los dos parámetros son `instanceId` y una cadena `reason`, que se escriben en los registros y en el estado de la instancia.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "Found a bug";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> El código de C# anterior corresponde a Durable Functions 2.x. En el caso de Durable Functions 1.x, debe usar el atributo `OrchestrationClient` en lugar del atributo `DurableClient`, además de usar el tipo de parámetro `DurableOrchestrationClient` en lugar de `IDurableOrchestrationClient`. Para obtener más información sobre las diferencias entre versiones, vea el artículo [Versiones de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Found a bug";
    return client.terminate(instanceId, reason);
};
```

Consulte [Instancias de inicio](#javascript-function-json) para la configuración del archivo function.json.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Found a bug"
    return client.terminate(instance_id, reason)
```

---

Una instancia finalizada, con el tiempo, realizará una transición al estado `Terminated`. Esta transición no se realizará de forma inmediata. Más bien, la operación de finalización se pondrá en cola en el centro de tareas junto con otras operaciones para esa instancia. Puede usar la [consulta de instancia ](#query-instances)de las API para saber cuándo una instancia finalizada ha alcanzado el estado `Terminated`.

> [!NOTE]
> La finalización de la instancia no se propaga actualmente. Las funciones de actividad y las suborquestaciones se ejecutan hasta completarse, independientemente de si ha finalizado la instancia de orquestación que las llamó.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

También es posible finalizar una instancia de orquestación directamente con el [comando `func durable terminate`](../functions-core-tools-reference.md#func-durable-terminate) de Core Tools.

> [!NOTE]
> Actualmente, los comandos de Core Tools solo se admiten cuando se usa el [proveedor predeterminado de Azure Storage](durable-functions-storage-providers.md) para conservar el estado del entorno de ejecución.

El comando `durable terminate` toma los parámetros siguientes:

* **`id` (obligatorio)** : identificador de la instancia de orquestación que se va a finalizar.
* **`reason` (opcional)** : motivo de la finalización.
* **`connection-string-setting` (opcional)** : nombre de la configuración de la aplicación que contiene la cadena de conexión de almacenamiento que se va a usar. El valor predeterminado es `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nombre de la central de tareas de Durable Functions que se va a usar. El valor predeterminado es `DurableFunctionsHub`. También puede establecerse en [host.json](durable-functions-bindings.md#host-json) mediante durableTask:HubName.

El siguiente comando finaliza una instancia de orquestación con un identificador de 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Found a bug"
```

## <a name="send-events-to-instances"></a>Envío de eventos a instancias

En algunos escenarios, las funciones de orquestador deben esperar y escuchar eventos externos. Entre los ejemplos de escenarios en los que esto es útil se incluyen los escenarios de [supervisión ](durable-functions-overview.md#monitoring) e [interacción humana](durable-functions-overview.md#human).

Puede enviar notificaciones de eventos a instancias en ejecución mediante los métodos `RaiseEventAsync` (.NET), `raiseEvent` (JavaScript) o `raise_event` (Python) del [cliente de orquestación](durable-functions-bindings.md#orchestration-client). Las instancias que pueden controlar estos eventos son aquellas que están en espera de una llamada a `WaitForExternalEvent` (.NET) o que producen una tarea `waitForExternalEvent` (JavaScript) o una tarea `wait_for_external_event` (Python).

Los parámetros de `RaiseEventAsync` (.NET) y `raiseEvent` (JavaScript) son los siguientes:

* **InstanceId**: el identificador único de la instancia.
* **EventName**: el nombre del evento que se va a enviar.
* **EventData**: una carga que se puede serializar con JSON que enviar a la instancia.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> El código de C# anterior corresponde a Durable Functions 2.x. En el caso de Durable Functions 1.x, debe usar el atributo `OrchestrationClient` en lugar del atributo `DurableClient`, además de usar el tipo de parámetro `DurableOrchestrationClient` en lugar de `IDurableOrchestrationClient`. Para obtener más información sobre las diferencias entre versiones, vea el artículo [Versiones de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Consulte [Instancias de inicio](#javascript-function-json) para la configuración del archivo function.json.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    event_data = [1, 2 ,3]
    return client.raise_event(instance_id, 'MyEvent', event_data)
```

---

> [!NOTE]
> Si no hay ninguna instancia de orquestación con el identificador de instancia especificado, se descartará el mensaje del evento. Si existe una instancia pero aún no está esperando el evento, el evento se almacenará en el estado de la instancia hasta que esté listo para ser recibido y procesado.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

También es posible generar un evento para una instancia de orquestación directamente con el [comando `func durable raise-event`](../functions-core-tools-reference.md#func-durable-raise-event) de Core Tools.

> [!NOTE]
> Actualmente, los comandos de Core Tools solo se admiten cuando se usa el [proveedor predeterminado de Azure Storage](durable-functions-storage-providers.md) para conservar el estado del entorno de ejecución.

El comando `durable raise-event` toma los parámetros siguientes:

* **`id` (obligatorio)** : identificador de la instancia de orquestación.
* **`event-name`** : nombre del evento que se va a generar.
* **`event-data` (opcional)** : datos que se van a enviar a la instancia de orquestación. Puede ser la ruta de acceso a un archivo JSON, o puede proporcionar los datos directamente en la línea de comandos.
* **`connection-string-setting` (opcional)** : nombre de la configuración de la aplicación que contiene la cadena de conexión de almacenamiento que se va a usar. El valor predeterminado es `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nombre de la central de tareas de Durable Functions que se va a usar. El valor predeterminado es `DurableFunctionsHub`. También puede establecerse en [host.json](durable-functions-bindings.md#host-json) mediante durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Esperar a que finalice la orquestación

En las orquestaciones de larga ejecución, quizá prefiera esperar y obtener los resultados de una orquestación. En estos casos, también resulta útil poder definir un período de tiempo de espera en la orquestación. Si se agota el tiempo de espera, se debe devolver el estado de la orquestación en lugar de los resultados.

Se pueden usar los métodos `WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET), `waitForCompletionOrCreateCheckStatusResponse` (JavaScript) o `wait_for_completion_or_create_check_status_response` (Python) para obtener la salida real de una instancia de orquestación de forma sincrónica. De manera predeterminada, estos métodos usan un valor predeterminado de 10 segundos para `timeout` y de 1 segundo para `retryInterval`.  

Este ejemplo de función que se desencadena mediante HTTP muestra cómo utilizar la API:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Consulte [Instancias de inicio](#javascript-function-json) para la configuración del archivo function.json.

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

timeout = "timeout"
retry_interval = "retryInterval"

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instance_id = await client.start_new(req.route_params['functionName'], None, req.get_body())
    logging.log(f"Started orchestration with ID = '${instance_id}'.")

    timeout_in_milliseconds = get_time_in_seconds(req, timeout)
    timeout_in_milliseconds = timeout_in_milliseconds if timeout_in_milliseconds != None else 30000
    retry_interval_in_milliseconds = get_time_in_seconds(req, retry_interval)
    retry_interval_in_milliseconds = retry_interval_in_milliseconds if retry_interval_in_milliseconds != None else 1000

    return client.wait_for_completion_or_create_check_status_response(
        req,
        instance_id,
        timeout_in_milliseconds,
        retry_interval_in_milliseconds
    )

def get_time_in_seconds(req: func.HttpRequest, query_parameter_name: str):
    query_value = req.params.get(query_parameter_name)
    return query_value if query_value != None else 1000
```

---

Llame a la función con la siguiente línea. Utilice 2 segundos para el tiempo de espera y 0,5 segundos para el intervalo de reintento:

```bash
curl -X POST "http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5"
```

Dependiendo del tiempo necesario para obtener la respuesta de la instancia de orquestación, se dan dos casos:

* Las instancias de orquestación se completan dentro del tiempo de espera definido (en este caso 2 segundos); y la respuesta es la salida de la instancia de orquestación real entregada de manera sincrónica:

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Date: Thu, 14 Dec 2021 06:14:29 GMT
Transfer-Encoding: chunked

[
    "Hello Tokyo!",
    "Hello Seattle!",
    "Hello London!"
]
```

* Las instancias de orquestación no se pueden completar dentro del tiempo de espera definido, y la respuesta es la predeterminada descrita en [Detección de la dirección URL de la API de HTTP](durable-functions-http-api.md):

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Date: Thu, 14 Dec 2021 06:13:51 GMT
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
Retry-After: 10
Transfer-Encoding: chunked

{
    "id": "d3b72dddefce4e758d92f4d411567177",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
}
```

> [!NOTE]
> El formato de las direcciones URL del webhook puede diferir en función de la versión del host de Azure Functions en ejecución. El ejemplo anterior es para el host de Azure Functions 3.0.

## <a name="retrieve-http-management-webhook-urls"></a>Recuperación de las direcciones URL del webhook de administración de HTTP

Puede usar un sistema externo para supervisar o generar eventos en una orquestación. Los sistemas externos se pueden comunicar con Durable Functions a través de direcciones URL de webhook que forman parte de la respuesta predeterminada descrita en [Detección de la dirección URL de la API de HTTP](durable-functions-http-features.md#http-api-url-discovery). También se puede obtener acceso mediante programación a las direcciones URL de webhook con el [enlace del cliente de orquestación](durable-functions-bindings.md#orchestration-client). Se pueden usar los métodos `CreateHttpManagementPayload` (.NET) o `createHttpManagementPayload` (JavaScript) para obtener un objeto serializable que contenga estas direcciones URL de webhook.

Los métodos `CreateHttpManagementPayload` (.NET) y `createHttpManagementPayload` (JavaScript) tienen un solo parámetro:

* **instanceId**: el identificador único de la instancia.

Los métodos devuelven un objeto con las siguientes propiedades de cadena:

* **Id**: el identificador de instancia de la orquestación (debe ser el mismo que la entrada `InstanceId`).
* **StatusQueryGetUri**: Dirección URL del estado de la instancia de orquestación.
* **SendEventPostUri**: Dirección URL de generación del evento de la instancia de orquestación.
* **TerminatePostUri**: Dirección URL de finalización de la instancia de orquestación.
* **PurgeHistoryDeleteUri**: La dirección URL del "historial de purga" de la instancia de orquestación.

Las funciones pueden enviar instancias de estos objetos a sistemas externos para supervisar o provocar eventos en las orquestaciones correspondientes, tal y como se muestra en los ejemplos siguientes:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> El código de C# anterior corresponde a Durable Functions 2.x. En el caso de Durable Functions 1.x, debe usar `DurableActivityContext` en lugar de `IDurableActivityContext`, el atributo `OrchestrationClient` en lugar del atributo `DurableClient` además de usar el tipo de parámetro `DurableOrchestrationClient` en lugar de `IDurableOrchestrationClient`. Para obtener más información sobre las diferencias entre versiones, vea el artículo [Versiones de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

Consulte [Instancias de inicio](#javascript-function-json) para la configuración del archivo function.json.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.cosmosdb.cdb.Document:
    client = df.DurableOrchestrationClient(starter)

    payload = client.create_check_status_response(req, instance_id).get_body().decode()

    return func.cosmosdb.CosmosDBConverter.encode({
        id: instance_id,
        payload: payload
    })
```
---

## <a name="rewind-instances-preview"></a>Rebobinado de instancias (versión preliminar)

Si tiene un error de la orquestación por un motivo inesperado, puede *rebobinar* la instancia a un estado correcto anterior mediante una API creada con ese propósito.

> [!NOTE]
> Esta API no pretende ser un sustituto para el control de errores y las directivas de reintentos pertinentes. En su lugar, el objetivo es que se use solo en casos donde las instancias de orquestación producen un error por razones inesperadas. Para obtener más detalles sobre el control de errores y las directivas de reintento, consulte el artículo [Control de errores](durable-functions-error-handling.md).

Use los métodos `RewindAsync` (.NET) o `rewind` (JavaScript) del [enlace del cliente de orquestación](durable-functions-bindings.md#orchestration-client) para volver a poner la orquestación en el estado *En ejecución*. Estos métodos volverán a ejecutar la actividad o los errores de ejecución de suborquestación que generaron el error en la orquestación.

Por ejemplo, supongamos que tiene un flujo de trabajo que requiere una serie de [aprobaciones realizadas por humanos](durable-functions-overview.md#human). Suponga que hay una serie de funciones de actividad que notifican a alguien cuando se requiere su aprobación y que esperan la respuesta en tiempo real. Después de que todas las actividades de aprobación han recibido respuestas o se ha agotado el tiempo de espera, suponga que otra actividad devuelve un error debido a un error de configuración en la aplicación (por ejemplo, una cadena de conexión de base de datos no válida). El resultado es un error de orquestación en el flujo de trabajo. Con la API `RewindAsync` (.NET) o `rewind` (JavaScript), un administrador de aplicaciones puede corregir el error de configuración y devolver la orquestación con error de vuelta al estado inmediatamente anterior al error. No es necesario volver a aprobar ninguno de los pasos de interacción humana y, ahora, la orquestación se puede completar correctamente.

> [!NOTE]
> La característica para *rebobinar* no admite el rebobinado de instancias de orquestación que utilizan temporizadores durables.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> El código de C# anterior corresponde a Durable Functions 2.x. En el caso de Durable Functions 1.x, debe usar el atributo `OrchestrationClient` en lugar del atributo `DurableClient`, además de usar el tipo de parámetro `DurableOrchestrationClient` en lugar de `IDurableOrchestrationClient`. Para obtener más información sobre las diferencias entre versiones, vea el artículo [Versiones de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Consulte [Instancias de inicio](#javascript-function-json) para la configuración del archivo function.json.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Actualmente, esta característica no se admite en Python.

<!-- ```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Orchestrator failed and needs to be revived."
    return client.rewind(instance_id, reason)
``` -->

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

También es posible rebobinar una instancia de orquestación directamente con el [comando `func durable rewind`](../functions-core-tools-reference.md#func-durable-rewind) de Core Tools.

> [!NOTE]
> Actualmente, los comandos de Core Tools solo se admiten cuando se usa el [proveedor predeterminado de Azure Storage](durable-functions-storage-providers.md) para conservar el estado del entorno de ejecución.

El comando `durable rewind` toma los parámetros siguientes:

* **`id` (obligatorio)** : identificador de la instancia de orquestación.
* **`reason` (opcional)** : motivo para devolver a un estado anterior a la instancia de orquestación.
* **`connection-string-setting` (opcional)** : nombre de la configuración de la aplicación que contiene la cadena de conexión de almacenamiento que se va a usar. El valor predeterminado es `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nombre de la central de tareas de Durable Functions que se va a usar. De forma predeterminada, se usa el nombre de la central de tareas en el archivo [host.json](durable-functions-bindings.md#host-json).

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Purga del historial de instancias

Para quitar todos los datos asociados con una orquestación, puede purgar el historial de instancias. Por ejemplo, puede que quiera eliminar las filas de una tabla de Azure y los blobs de mensajes grandes asociados a una instancia completada. Para ello, use los métodos `PurgeInstanceHistoryAsync` (.NET), `purgeInstanceHistory` (JavaScript) o `purge_instance_history` (Python) del objeto del [cliente de orquestación](durable-functions-bindings.md#orchestration-client).

Estos métodos tienen dos sobrecargas: La primera sobrecarga purga el historial por el identificador de la instancia de orquestación:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Consulte [Instancias de inicio](#javascript-function-json) para la configuración del archivo function.json.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    return client.purge_instance_history(instance_id)
```

---

En el siguiente ejemplo se muestra una función de desencadenador por temporizador que purga el historial de todas las instancias de orquestación que se completan después del intervalo de tiempo especificado. En este caso, se quitan los datos de todas las instancias que se completaron hace 30 días o más. Esta función de ejemplo está programada para ejecutarse una vez al día, a las 12:00 p. m. UTC:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")] TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> El código de C# anterior corresponde a Durable Functions 2.x. En el caso de Durable Functions 1.x, debe usar el atributo `OrchestrationClient` en lugar del atributo `DurableClient`, además de usar el tipo de parámetro `DurableOrchestrationClient` en lugar de `IDurableOrchestrationClient`. Para obtener más información sobre las diferencias entre versiones, vea el artículo [Versiones de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

El método `purgeInstanceHistoryBy` se puede usar para purgar condicionalmente el historial de instancias para varias instancias.

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Este ejemplo corresponde a Durable Functions versión 2.x. En la versión 1.x, use `orchestrationClient` en lugar de `durableClient`.

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.DurableOrchestrationStatus import OrchestrationRuntimeStatus
from datetime import datetime, timedelta

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    created_time_from = datetime.min
    created_time_to = datetime.today() + timedelta(days = -30)
    runtime_statuses = [OrchestrationRuntimeStatus.Completed]

    return client.purge_instance_history_by(created_time_from, created_time_to, runtime_statuses)
```
---

> [!NOTE]
> Para que la operación de historial de purga se realice correctamente, el estado del runtime de la instancia de destino debe ser **Completado**, **Finalizado** o **Con errores**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Puede purgar el historial de una instancia de orquestación con el [comando `func durable purge-history`](../functions-core-tools-reference.md#func-durable-purge-history) de Core Tools. De forma similar al segundo ejemplo de C# de la sección anterior, se purga el historial de todas las instancias de orquestación creadas durante un intervalo de tiempo especificado. Puede filtrar aún más las instancias purgadas por estado del entorno de ejecución.

> [!NOTE]
> Actualmente, los comandos de Core Tools solo se admiten cuando se usa el [proveedor predeterminado de Azure Storage](durable-functions-storage-providers.md) para conservar el estado del entorno de ejecución.

El comando `durable purge-history` tiene varios parámetros:

* **`created-after` (opcional)** : purga del historial de instancias creado después de esta fecha y hora (UTC). Se aceptan valores de datetime con formato ISO 8601.
* **`created-before` (opcional)** : purga del historial de instancias creado antes de esta fecha y hora (UTC). Se aceptan valores de datetime con formato ISO 8601.
* **`runtime-status` (opcional)** : Purgue el historial de las instancias con un estado determinado (por ejemplo, en ejecución o completado). Puede proporcionar varios estados (separados por espacios).
* **`connection-string-setting` (opcional)** : nombre de la configuración de la aplicación que contiene la cadena de conexión de almacenamiento que se va a usar. El valor predeterminado es `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nombre de la central de tareas de Durable Functions que se va a usar. De forma predeterminada, se usa el nombre de la central de tareas en el archivo [host.json](durable-functions-bindings.md#host-json).

El comando siguiente elimina el historial de todas las instancias con error creadas antes del 14 de noviembre de 2021 a las 7:35 p. m. (UTC).

```bash
func durable purge-history --created-before 2021-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Eliminación de una central de tareas

Con el [comando `func durable delete-task-hub`](../functions-core-tools-reference.md#func-durable-delete-task-hub) de Core Tools, puede eliminar todos los artefactos de almacenamiento asociados a una central de tareas determinada, incluidas las tablas, las colas y los blobs de Azure Storage. 

> [!NOTE]
> Actualmente, los comandos de Core Tools solo se admiten cuando se usa el [proveedor predeterminado de Azure Storage](durable-functions-storage-providers.md) para conservar el estado del entorno de ejecución.

El comando `durable delete-task-hub` tiene dos parámetros:

* **`connection-string-setting` (opcional)** : nombre de la configuración de la aplicación que contiene la cadena de conexión de almacenamiento que se va a usar. El valor predeterminado es `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)** : nombre de la central de tareas de Durable Functions que se va a usar. De forma predeterminada, se usa el nombre de la central de tareas en el archivo [host.json](durable-functions-bindings.md#host-json).

El siguiente comando elimina todos los datos de Azure Storage asociados a la central de tareas `UserTest`.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Aprenda a controlar las versiones](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Referencia de la API HTTP integrada para la administración de instancias](durable-functions-http-api.md)
