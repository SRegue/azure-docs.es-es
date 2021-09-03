---
title: Regiones en las que está disponible Azure Video Analyzer for Media (anteriormente, Video Indexer)
titleSuffix: Azure Video Analyzer for Media
description: En este artículo tratan las regiones de Azure en las que Azure Video Analyzer for Media (anteriormente, Video Indexer) está disponible.
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 1db75ea3ba67d4e8f0551495274ec3fff38e29b4
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119175"
---
# <a name="azure-regions-in-which-video-analyzer-for-media-exists"></a>Regiones de Azure en las que existe Video Analyzer for Media

Las API de Azure Video Analyzer for Media (anteriormente, Video Indexer) incluyen un parámetro **location** que se debe establecer en la región de Azure a la que se debe enrutar la llamada. Este parámetro debe ser una [región de Azure en la que esté disponible Video Analyzer for Media](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Ubicaciones

El parámetro `location` debe tener como valor el nombre en código de la región de Azure. Si usa la versión preliminar de Video Analyzer for Media, debe poner `"trial"` como valor. `trial` es el valor predeterminado para el parámetro `location`. Si no, para obtener el nombre en código de la versión de Azure en la que está su cuenta y a la que se debe enrutar la llamada, puede usar Azure Portal o ejecutar el siguiente comando de la [CLI de Azure](/cli/azure).

### <a name="azure-portal"></a>Azure portal

1. Inicie sesión en el sitio web de [Video Analyzer for Media](https://www.videoindexer.ai/).
1. Seleccione **Cuentas de usuario** en la esquina superior derecha de la página.
1. Busque la ubicación de la cuenta en la esquina superior derecha.  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="Ubicación":::
    
###  <a name="cli-command"></a>Comando de la CLI

```azurecli-interactive
az account list-locations
```

Una vez que ejecuta la línea mostrada anteriormente, obtiene una lista de todas las regiones de Azure. Vaya a la región de Azure que tenga el elemento *displayName* que busca y use su valor *name* para el parámetro **location**.

Por ejemplo, para la región Oeste de EE. UU. 2 de Azure (se muestra a continuación), usará "westus2" para el parámetro **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Pasos siguientes

- [Personalización del modelo de lenguaje mediante las API](customize-language-model-with-api.md)
- [Personalización del modelo de marcas mediante las API](customize-brands-model-with-api.md)
- [Personalización del modelo de persona mediante las API](customize-person-model-with-api.md)
