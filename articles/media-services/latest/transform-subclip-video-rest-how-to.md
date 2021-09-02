---
title: Creación de un subclip de vídeo al codificar mediante REST de Media Services
description: En este tema se describe cómo crear un subclip de vídeo al codificar mediante Azure Media Services con REST
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/10/2019
ms.author: inhenkel
ms.openlocfilehash: 3f8712709f1160150275ee55d84522746bbe8a3a
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "122651954"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Creación de un subclip de vídeo al codificar mediante Azure Media Services: REST

Puede recortar un vídeo, o crear un subclip de este al codificarlo mediante la opción [Trabajo](/rest/api/media/jobs). Esta funcionalidad se puede usar con cualquier elemento [Transformación](/rest/api/media/transforms) compilado mediante los valores preestablecidos [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) o [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). 

El ejemplo de REST de este tema crea un trabajo que recorta un vídeo cuando envía un trabajo de codificación. 

[!INCLUDE [warning-rest-api-retry-policy.md](./includes/warning-rest-api-retry-policy.md)]


## <a name="prerequisites"></a>Prerrequisitos

Para completar los pasos descritos en este tema, ha de:

- [Crear una cuenta de Azure Media Services](./account-create-how-to.md).
- [Configuración de Postman para llamadas API REST de Azure Media Services](setup-postman-rest-how-to.md).
    
    Asegúrese de seguir el último paso en el tema [Obtención del token de Azure AD](setup-postman-rest-how-to.md#get-azure-ad-token). 
- Crear una transformación y un recurso de salida. Puede ver cómo crear una transformación y un recurso de salida en el tutorial [Codificación de un archivo remoto según una dirección URL y transmisión del vídeo: REST](stream-files-tutorial-with-rest.md).
- Revisar el tema [Concepto de codificación](encode-concept.md).

## <a name="create-a-subclipping-job"></a>Creación de un trabajo de creación de subclips

1. En la colección de Postman que descargó, seleccione **Transforms and jobs (Transformaciones y trabajos)**  -> **Create Job with Sub Clipping** (Crear un trabajo con creación de subclips).
    
    La solicitud **PUT** tiene este aspecto:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Actualice el valor de la variable de entorno "transformName" con el nombre de la transformación. 
1. Seleccione la pestaña **Cuerpo** y actualice el objeto "myOutputAsset" con el nombre del recurso de salida.

    ```json
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
            "Ignite-short.mp4"
          ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Presione **Enviar**.

    En la sección **Respuesta**, verá la información sobre el trabajo que se creó y envía, así como el estado del trabajo. 

## <a name="next-steps"></a>Pasos siguientes

[Procedimiento de codificación con una transformación personalizada](transform-custom-preset-rest-how-to.md) 
