---
title: Salida de Azure Functions desde Azure Stream Analytics
description: En este artículo se describe Azure Functions como salida para Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/28/2021
ms.openlocfilehash: ccedab6284fd5dac5a3d9f8d221a22803a3571f8
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2021
ms.locfileid: "110666677"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Salida de Azure Functions desde Azure Stream Analytics

Azure Functions es un servicio de proceso sin servidor que puede usar para ejecutar código a petición sin necesidad de aprovisionar ni administrar explícitamente la infraestructura. Permite implementar el código desencadenado por los eventos que se producen en servicios de Azure o de sus socios. Esta capacidad que tiene Azure Functions para responder a los desencadenadores hace que sea una salida natural para Azure Stream Analytics. Este adaptador de salida permite que los usuarios conecten Stream Analytics con Azure Functions y ejecuten un script o parte de un código como respuesta a una variedad de eventos.

La salida de Azure Functions desde Stream Analytics no está disponible en las regiones de Azure China (21Vianet) y Azure Alemania (T-Systems International). Tampoco se admite la conexión a Azure Functions dentro de una red virtual (VNet) desde un trabajo de Stream Analytics que se ejecute en un clúster de varios inquilinos.

Azure Stream Analytics invoca a Azure Functions a través de desencadenadores HTTP. El adaptador de salida de Azure Functions está disponible con las siguientes propiedades configurables:

| Nombre de propiedad | Descripción |
| --- | --- |
| Aplicación de función |Nombre de la aplicación de Azure Functions. |
| Función |Nombre de la función en la aplicación de Azure Functions. |
| Clave |Si quiere usar una instancia de Azure Functions desde otra suscripción, debe proporcionar la clave para acceder a la función. |
| Tamaño máximo de lote |Propiedad que permite establecer el tamaño máximo de cada lote de salida que se envía a la instancia de Azure Functions. La unidad de entrada se muestra en bytes. De manera predeterminada, este valor es 262 144 bytes (256 KB). |
| Número máximo de lotes  |Propiedad que permite especificar el número máximo de eventos en cada lote que se envía a Azure Functions. El valor predeterminado es 100. |

Azure Stream Analytics espera el estado de HTTP 200 de la aplicación de Functions para los lotes que se procesaron correctamente.

Cuando Azure Stream Analytics recibe una excepción 413 ("La entidad de solicitud HTTP es demasiado grande") de una función de Azure, disminuye el tamaño de los lotes que envía a Azure Functions. En el código de función de Azure, use esta excepción para asegurarse de que Azure Stream Analytics no envíe lotes demasiado grandes. También, asegúrese de que los valores de tamaño y número máximo de lotes que se usan en la función sean coherentes con los valores que se especifican en el portal de Stream Analytics.

> [!NOTE]
> Durante la conexión de prueba, Stream Analytics envía un lote vacío a Azure Functions para probar si la conexión entre los dos funciona. Asegúrese de que la aplicación de Functions controla las solicitudes por lotes vacías para asegurarse de que la conexión de prueba es correcta.

También tenga en cuenta que no se genera ninguna salida en las situaciones en las que no se producen eventos en un período de tiempo. Como resultado, no se llama a la función **computeResult**. Este comportamiento es coherente con las funciones integradas de agregado en ventanas.

## <a name="partitioning"></a>Creación de particiones

La clave de partición se basa en la cláusula PARTITION BY de la consulta. El número de escritores de salida sigue las particiones de entrada para [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Tamaño de lote de salida

El tamaño predeterminado de lote es de 262 144 bytes (256 KB). El número predeterminado de eventos por lote es 100. El tamaño del lote es configurable y puede aumentar o disminuir en las opciones de salida de Stream Analytics.

## <a name="limitation"></a>Limitación

Azure Functions debería completar su solicitud en menos de 100 segundos dado que, transcurrido este tiempo, el cliente HTTP agota el tiempo de espera. Si Azure Functions tarda más de 100 segundos en procesar un lote de datos, hay un tiempo de espera que desencadenará un reintento. Este reintento puede dar lugar a que se dupliquen los datos, ya que Azure Functions los procesará de nuevo y podría generar la misma salida, dado que es posible que se haya generado parcialmente en la solicitud anterior.


## <a name="next-steps"></a>Pasos siguientes

* [Inicio rápido: Creación de un trabajo de Stream Analytics mediante Azure Portal](stream-analytics-quick-create-portal.md)
* [Inicio rápido: Creación de un trabajo de Azure Stream Analytics mediante la CLI de Azure](quick-create-azure-cli.md)
* [Inicio rápido: Creación de un trabajo de Azure Stream Analytics mediante una plantilla de ARM](quick-create-azure-resource-manager.md)
* [Inicio rápido: Creación de un trabajo de Stream Analytics mediante Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Inicio rápido: Creación de un trabajo de Azure Stream Analytics con Visual Studio](stream-analytics-quick-create-vs.md)
* [Inicio rápido: Creación de un trabajo de Azure Stream Analytics en Visual Studio Code](quick-create-visual-studio-code.md)
