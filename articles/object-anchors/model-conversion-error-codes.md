---
title: Código de error de la conversión de modelos
description: Código de error de la conversión de modelos para el servicio de Azure Object Anchors.
author: jastenze
manager: vriveras
ms.author: jastenze
ms.date: 04/20/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: e47e4e9f784e51c646da22063ee954d9b60a4e11
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202678"
---
# <a name="model-conversion-error-codes"></a>Código de error de la conversión de modelos

Para los modos comunes de error de conversión de modelo, el objeto `Azure.MixedReality.ObjectAnchors.Conversion.AssetConversionProperties` obtenido del campo `Value` en el `Azure.MixedReality.ObjectAnchors.Conversion.AssetConversionOperation` contiene un campo ErrorCode del tipo `ConversionErrorCode`. Este tipo enumera estos modos comunes de error para la localización de mensajes de error, la recuperación de errores y sugerencias al usuario sobre cómo se puede corregir el error.

| Código de error                    | Descripción                       |  Mitigación                       |
| ---                      | ---                               | ---                               |
| INVALID_ASSET_URI | No se encontró el recurso en el URI proporcionado al iniciar el trabajo de conversión. | Al desencadenar un trabajo de conversión de recursos, proporcione un URI de carga obtenido del servicio donde se ha cargado el recurso que se va a convertir. |
| INVALID_JOB_ID | El identificador proporcionado para el trabajo de conversión de recursos que se va a crear se estableció en el GUID con solo ceros predeterminado. | Si se especifica un GUID al crear un trabajo de conversión de recursos, asegúrese de que no sea el GUID con solo ceros predeterminado. |
| INVALID_GRAVITY | El vector de gravedad proporcionado al crear el trabajo de conversión de recursos era un vector con solo ceros. | Al iniciar una conversión de recursos, proporcione el vector de gravedad que corresponde al recurso cargado. |
| INVALID_SCALE | El factor de escala proporcionado no era un valor positivo distinto de cero. | Al iniciar una conversión de recursos, proporcione el valor escalar que corresponde a la escala de la unidad de medida (con respecto a los medidores) del recurso cargado. |
| ASSET_SIZE_TOO_LARGE | El archivo intermedio .PLY generado a partir del recurso o su equivalente serializado era demasiado grande. | Consulte las directrices sobre el tamaño de los recursos antes de enviar un recurso para su conversión, para garantizar la conformidad: aka.ms/aoa/faq |
| ASSET_DIMENSIONS_OUT_OF_BOUNDS | Las dimensiones del recurso superaron el límite de dimensión física. Esto puede ser el signo de que al crear un trabajo, se estableció una escala de forma incorrecta para el recurso. | Consulte las directrices sobre el tamaño de los recursos antes de enviar un recurso para su conversión para garantizar la conformidad y asegúrese de que la escala proporcionada corresponde al recurso cargado: aka.ms/aoa/faq |
| ZERO_FACES | El archivo intermedio .PLY generado a partir del recurso no tenía caras, cosa que lo convierte en inválido para la conversión. | Asegúrese de que el recurso es una malla válida. |
| INVALID_FACE_VERTICES | El archivo intermedio .PLY generado a partir del recurso contenía caras que hacían referencia a vértices inexistentes. | Asegúrese de que el archivo de recursos está construido de forma válida. |
| ZERO_TRAJECTORIES_GENERATED | Las trayectorias de las cámaras generadas a partir del recurso cargado estaban vacías. | Consulte las directrices sobre recursos antes de enviar un recurso para su conversión, para garantizar la conformidad: aka.ms/aoa/faq |
| TOO_MANY_RIG_POSES | El número de posiciones de plataforma en el archivo intermedio .PLY excedió los límites del servicio. | Consulte las directrices sobre el tamaño de los recursos antes de enviar un recurso para su conversión, para garantizar la conformidad: aka.ms/aoa/faq |
| SERVICE_ERROR | Se ha producido un error de servicio desconocido. | Póngase en contacto con un miembro del equipo de servicio de Object Anchors si el problema persiste: https://github.com/Azure/azure-object-anchors/issues |
| ASSET_CANNOT_BE_CONVERTED | El recurso proporcionado estaba dañado, tenía un formato incorrecto o, de lo contrario, no se podía convertir en el formato proporcionado. | Asegúrese de que el recurso es un archivo construido de forma válida del tipo especificado y consulte las directrices de tamaño de los recursos antes de enviar un recurso para su conversión para garantizar la conformidad: aka.ms/aoa/faq |

Los errores que se produzcan fuera de los trabajos de conversión de recursos reales aparecerán como excepciones. En particular, el `Azure.RequestFailedException` se puede realizar para llamadas de servicio que reciben un código de respuesta HTTP incorrecto (4xx o 5xx) o inesperado. Para más información sobre estas excepciones, analice los campos `Status`, `ErrorCode` o `Message` de la excepción.

| Excepción                  | Causa                       |
| ---                      | ---                               |
| ArgumentException |  <ul><li>Se produce cuando se usa un id. de cuenta con solo ceros o construido de forma no válida para construir una solicitud con ObjectAnchorsConversionClient.</li><li>Se produce al intentar inicializar ObjectAnchorsConversionClient mediante un dominio de cuenta de espacio en blanco no válido.</li><li>Se produce cuando se proporciona una versión de servicio no compatible a ObjectAnchorsConversionClient a través de ObjectAnchorsConversionClientOptions.</li></ul> |
| ArgumentNullException | <ul><li>Se produce al intentar inicializar ObjectAnchorsConversionClient mediante un dominio de cuenta nulo no válido.</li><li>Se produce al intentar inicializar ObjectAnchorsConversionClient mediante un dominio de cuenta nulo no válido.</li></ul> |
| RequestFailedException | <ul><li>Se produce para todos los demás problemas resultantes de un código de estado HTTP no válido (no relacionado con el estado de un trabajo que se va a ejecutar), como una cuenta que no se encuentra, un URI de carga no válido detectado por el servicio front-end, un error de servicio front-end, etc.</li></ul> |
| UnsupportedAssetFileTypeException | <ul><li>Se produce al intentar enviar un trabajo con un recurso con una extensión o un tipo de archivo especificado que no es compatible con el servicio de conversión de Azure Object Anchors.</li></ul> |