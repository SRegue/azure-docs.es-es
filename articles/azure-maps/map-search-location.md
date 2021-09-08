---
title: Presentación de los resultados de la búsqueda en un mapa | Microsoft Azure Maps
description: En este artículo, obtendrá información sobre cómo realizar una solicitud de búsqueda mediante el SDK web de Microsoft Azure Maps y mostrar los resultados en el mapa.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen
ms.openlocfilehash: be6b1a133af6ee93aa67d4fb4571dbde1c659278
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439585"
---
# <a name="show-search-results-on-the-map"></a>Presentación de los resultados de la búsqueda en el mapa

En este artículo se describe cómo buscar ubicaciones de interés y mostrar los resultados de búsqueda en el mapa.

Hay dos maneras de buscar una ubicación de interés. Una consiste en usar un módulo de servicio para realizar una solicitud de búsqueda. La otra consiste en realizar una solicitud de búsqueda en la [Azure Maps Fuzzy Search API](/rest/api/maps/search/getsearchfuzzy) a través de la [API de captura](https://fetch.spec.whatwg.org/). Ambos métodos se describen a continuación.

## <a name="make-a-search-request-via-service-module"></a>Realización de una solicitud de búsqueda a través del módulo de servicio

<iframe height='500' scrolling='no' title='Presentación de los resultados de búsqueda en un mapa (módulo de servicio)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>Consulte el Pen <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show search results on a map (Service Module)</a> (Presentación de los resultados de búsqueda en un mapa [módulo de servicio]) de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

En el código anterior, el primer bloque construye un objeto de mapa y establece el mecanismo de autenticación para usar el token de acceso. Puede consultar [Creación de un mapa](./map-create.md) para obtener instrucciones.

El segundo bloque de código crea un elemento `TokenCredential` para autenticar las solicitudes HTTP en Azure Maps con el token de acceso. A continuación, pasa `TokenCredential` a `atlas.service.MapsURL.newPipeline()` y crea una instancia de [canalización](/javascript/api/azure-maps-rest/atlas.service.pipeline). `searchURL` representa una dirección URL para las operaciones [Search](/rest/api/maps/search) de Azure Maps.

El tercer bloque de código crea un objeto de origen de datos con la clase [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) y le agrega los resultados de la búsqueda. Una [capa de símbolos](/javascript/api/azure-maps-control/atlas.layer.symbollayer) usa texto o iconos para representar los datos basados en puntos encapsulados en [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) como símbolos en el mapa.  A continuación, se crea una capa de símbolos. El origen de datos se agrega a la capa de símbolos y, después, esta se agrega al mapa.

El cuarto bloque de código usa el método [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) en el [módulo de servicio](how-to-use-services-module.md). Permite realizar una búsqueda de texto de forma libre a través de la [Get Search Fuzzy rest API](/rest/api/maps/search/getsearchfuzzy) para buscar el punto de interés. Las solicitudes Get Search Fuzzy API puede administrar cualquier combinación de entradas aproximadas. Después se extrae una colección de características GeoJSON de la respuesta con el método `geojson.getFeatures()` y se agregan al origen de datos, lo cual provoca que los datos se representen automáticamente en el mapa mediante la capa de símbolos.

El último bloque de código ajusta los límites de cámara para el mapa mediante la propiedad [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) del mapa.

La solicitud de búsqueda, el origen de datos, la capa de símbolos y los límites de cámara están dentro del [agente de escucha de eventos](/javascript/api/azure-maps-control/atlas.map#events) del mapa. Queremos asegurarnos de que los resultados se muestran una vez que el mapa se carga por completo.


## <a name="make-a-search-request-via-fetch-api"></a>Realización de una solicitud de búsqueda a través de la API de captura

<iframe height='500' scrolling='no' title='Presentación de los resultados de la búsqueda en un mapa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>Consulte el Pen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Presentación de los resultados de la búsqueda en un mapa</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

En el código anterior, el primer bloque de código construye un objeto mapa. Establece el mecanismo de autenticación para usar el token de acceso. Puede consultar [Creación de un mapa](./map-create.md) para obtener instrucciones.

El segundo bloque de código crea una dirección URL para realizar una solicitud de búsqueda. También crea dos matrices para almacenar límites y chinchetas para los resultados de búsqueda.

El tercer bloque de código usa la [API de captura](https://fetch.spec.whatwg.org/). La [API de captura](https://fetch.spec.whatwg.org/) se usa para hacer una solicitud a [Azure Maps Fuzzy search API](/rest/api/maps/search/getsearchfuzzy) para buscar los puntos de interés. Fuzzy Search API puede administrar cualquier combinación de entradas aproximadas. A continuación, administra y analiza la respuesta de búsqueda y agrega las chinchetas de resultados a la matriz searchPins.

El cuarto bloque de código crea un objeto de origen de datos con la clase [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource). En el código, se agregan los resultados de la búsqueda al objeto de origen. Una [capa de símbolos](/javascript/api/azure-maps-control/atlas.layer.symbollayer) usa texto o iconos para representar los datos basados en puntos encapsulados en [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) como símbolos en el mapa. A continuación, se crea una capa de símbolos. El origen de datos se agrega a la capa de símbolos y, después, esta se agrega al mapa.

El último bloque de código crea un objeto [BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox). Usa la matriz de resultados y, a continuación, ajusta los límites de cámara para el mapa mediante la configuración [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) del mapa. A continuación, muestra las chinchetas de resultados.

La solicitud de búsqueda, el origen de datos, la capa de símbolos y los límites de cámara se establecen en el [agente de escucha de eventos](/javascript/api/azure-maps-control/atlas.map#events) del mapa para asegurar que los resultados se muestran una vez que el mapa se carga completamente.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Procedimientos recomendados de uso del servicio de búsqueda](how-to-use-best-practices-for-search.md)

Más información sobre la **búsqueda aproximada**:

> [!div class="nextstepaction"]
> [Fuzzy Search API de Azure Maps](/rest/api/maps/search/getsearchfuzzy)

Más información sobre las clases y los métodos utilizados en este artículo:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

Consulte los siguientes artículos para obtener ejemplos de código completo:

> [!div class="nextstepaction"]
> [Obtención de información de una coordenada](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Presentación de indicaciones de ruta de A a B](map-route.md)