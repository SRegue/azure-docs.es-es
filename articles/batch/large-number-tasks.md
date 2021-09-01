---
title: Envío de un gran número de tareas a un trabajo de Batch
description: Obtenga información sobre cómo enviar de forma eficaz un gran número de tareas en un único trabajo de Azure Batch.
ms.topic: how-to
ms.date: 12/30/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 825bee374ec006708b4b0b38e7d101554b3a9c25
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014938"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Envío de un gran número de tareas a un trabajo de Batch

Al ejecutar cargas de trabajo de Azure Batch a gran escala, quizá quiera enviar decenas de miles, cientos de miles o incluso más tareas a un único trabajo.

En este artículo se explica cómo enviar grandes cantidades de tareas con un rendimiento considerablemente mayor a un único trabajo de Batch. Una vez enviadas las tareas, pasan a la cola de Batch para el procesamiento en el grupo que especificara para el trabajo.

## <a name="use-task-collections"></a>Uso de colecciones de tareas

Al agregar un gran número de tareas, use los métodos apropiados o las sobrecargas proporcionadas por las API de Batch para agregar tareas como una *colección*, en lugar de una a una. Por lo general, se crea una colección de tareas al definir las que se repiten en un conjunto de archivos de entrada o de parámetros para el trabajo.

El tamaño máximo de la colección de tareas que se puede agregar en una sola llamada depende de la API de Batch que se utilice.

### <a name="apis-allowing-collections-of-up-to-100-tasks"></a>API que admiten colecciones de hasta 100 tareas

Estas API de Batch limitan la colección a 100 tareas. El límite podría ser menor en función del tamaño de las tareas (por ejemplo, si tienen mayor número de archivos de recursos o variables de entorno).

- [REST API](/rest/api/batchservice/task/addcollection)
- [API de Python](/python/api/azure-batch/azure.batch.operations.TaskOperations)
- [API de Node.js](/javascript/api/@azure/batch/task)

Al usar estas API, deberá proporcionar la lógica para dividir el número de tareas y que se cumpla el límite de la colección, y para controlar los errores y los reintentos en caso de errores al incorporar tareas. Si la colección es demasiado grande para agregar tareas, la solicitud genera un error y será necesario intentarlo después de nuevo con menos tareas.

### <a name="apis-allowing-collections-of-larger-numbers-of-tasks"></a>API que permiten colecciones de un mayor número de tareas

Otras API de Batch admiten colecciones de tareas mucho más grandes; se limitan únicamente por la disponibilidad de RAM en el cliente remitente. Estas API realizan un control transparente al dividir la colección de tareas en "fragmentos" para las API de menor nivel y vuelven a intentar la incorporación de la tarea en caso de error.

- [API de .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)
- [API de Java](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync)
- [Extensión de la CLI de Azure Batch](batch-cli-templates.md) con plantillas de la CLI de Batch
- [Extensión del SDK de Python](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Aumento de rendimiento del envío de tareas

La adición de una colección grande de tareas a un trabajo puede tardar un tiempo. Por ejemplo, la adición de 20 000 tareas mediante la API de .NET puede tardar hasta un minuto. Dependiendo de la API de Batch y la carga de trabajo, puede mejorar el rendimiento de la tarea al modificar uno o varios de estos aspectos.

### <a name="task-size"></a>Tamaño de la tarea

La adición de tareas grandes tarda más que las pequeñas. Para reducir el tamaño de las tareas de una colección puede simplificar la línea de comandos de la tarea, reducir el número de variables de entorno o administrar más eficazmente los requisitos de ejecución de la tarea.

Por ejemplo, en lugar de usar un gran número de archivos de recursos, instale las dependencias de las tareas mediante una [Tarea de inicio](jobs-and-tasks.md#start-task) en el grupo, un [paquete de aplicación](batch-application-packages.md) o un [contenedor de Docker](batch-docker-container-workloads.md).

### <a name="number-of-parallel-operations"></a>Número de operaciones paralelas

En función de la API de Batch, puede aumentar el rendimiento al aumentar el número máximo de operaciones simultáneas por cliente de Batch. Configure esta opción mediante la propiedad [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) de la API de .NET o el parámetro `threads` de métodos como [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations) de la extensión del SDK de Python de Batch. (Esta propiedad no está disponible en el SDK de Python de Batch nativo).

De forma predeterminada, esta propiedad está establecida en 1, pero puede elegir un valor mayor para mejorar el rendimiento de las operaciones. El aumento de rendimiento se compensa al consumirse ancho de banda de red y rendimiento de la CPU. El rendimiento de la tarea aumenta hasta 100 veces `MaxDegreeOfParallelism` o `threads`. En la práctica, debe establecer el número de operaciones simultáneas por debajo de 100.

 La extensión de la CLI de Azure Batch con plantillas de Batch aumenta el número de operaciones simultáneas automáticamente en función del número de núcleos disponibles, pero esta propiedad no se configura en la CLI.

### <a name="http-connection-limits"></a>Límites de conexiones HTTP

Tener muchas conexiones HTTP simultáneas puede limitar el rendimiento del cliente de Batch al agregar grandes cantidades de tareas. Algunas API limitan el número de conexiones HTTP. Al desarrollar con la API de .NET, por ejemplo, la propiedad [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) se establece en 2 de forma predeterminada. Se recomienda aumentar el valor a uno próximo al número de operaciones en paralelo o mayor.

## <a name="example-batch-net"></a>Ejemplo: Batch .NET

Los siguientes fragmentos de código de C# muestran opciones que deben configurarse al agregar un gran número de tareas mediante la API de .NET de Batch.

Para aumentar el rendimiento de la tarea, aumente el valor de la propiedad [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) de [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient). Por ejemplo:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```

Agregue una colección de tareas al trabajo mediante la sobrecarga adecuada de los métodos [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync) o [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask
). Por ejemplo:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```

## <a name="example-batch-cli-extension"></a>Ejemplo: extensión de la CLI de Batch

Mediante las extensiones de la CLI de Azure Batch con [plantillas de la CLI de Batch](batch-cli-templates.md), cree un archivo JSON de plantilla de trabajo que incluya un [generador de tareas](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). El generador de tareas configura una colección de tareas relacionadas para un trabajo a partir de una única definición de tarea.  

La siguiente es una plantilla de trabajo de ejemplo para un trabajo de barrido paramétrico unidimensional con un gran número de tareas (en este caso, 250 000). La línea de comandos de la tarea es un comando `echo` sencillo.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Para ejecutar un trabajo con la plantilla, consulte [Uso de plantillas y transferencia de archivos de la CLI de Azure Batch](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Ejemplo: extensión del SDK de Python de Batch

Para usar la extensión del SDK de Python de Azure Batch, instale el SDK de Python y la extensión:

```
pip install azure-batch
pip install azure-batch-extensions
```

Después de importar el paquete mediante `import azext.batch as batch`, configure un `BatchExtensionsClient` que use la extensión del SDK:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Cree una colección de tareas para agregarla a un trabajo. Por ejemplo:

```python
tasks = list()
# Populate the list with your tasks
...
```

Agregue la colección de tareas mediante [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations). Establezca el parámetro `threads` para aumentar el número de operaciones simultáneas:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

La extensión del SDK de Python de Batch también admite la incorporación de parámetros de tarea a un trabajo mediante una especificación de JSON para un generador de tareas. Por ejemplo, configure los parámetros del trabajo para un barrido paramétrico similar al del ejemplo de plantilla de la CLI de Batch anterior:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Agregue los parámetros al trabajo. Establezca el parámetro `threads` para aumentar el número de operaciones simultáneas:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el uso de la extensión de la CLI de Azure Batch con [plantillas de la CLI de Batch](batch-cli-templates.md).
- Más información sobre la [extensión del SDK de Python de Batch](https://pypi.org/project/azure-batch-extensions/).
- Lea los [procedimientos recomendados de Azure Batch](best-practices.md).
