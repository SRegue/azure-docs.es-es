---
title: Empaquetado dinámico en Azure Media Services v3
description: En este artículo se proporciona información general sobre el empaquetado dinámico en Azure Media Services.
author: myoungerman
manager: femila
services: media-services
ms.service: media-services
ms.workload: media
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: acc993f5690fbc250659830b52099f8d3d966421
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322168"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Empaquetado dinámico en Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services proporciona funcionalidades integradas de empaquetado y de servidor de origen para entregar contenido en los formatos de protocolo de streaming HLS y MPEG DASH. En AMS, el [punto de conexión de streaming](stream-streaming-endpoint-concept.md) actúa como el servidor de "origen" que envía contenido con formato HLS y DASH a los reproductores cliente que admiten el streaming con velocidad de bits adaptable mediante estos formatos populares. El punto de conexión de streaming también admite muchas características, como el empaquetado dinámico Just-In-Time con o sin protección de contenido, para llegar a todos los dispositivos principales (como dispositivos iOS y Android). 

Actualmente, la mayoría de los exploradores y dispositivos móviles del mercado admiten y entienden los protocolos de streaming HLS o DASH. Por ejemplo, iOS requiere que los flujos se entreguen en formato HTTP Live Streaming (HLS), mientras que los dispositivos Android admiten HLS y MPEG DASH en determinados modelos (o bien mediante el uso del reproductor de nivel de aplicación [Exoplayer](https://exoplayer.dev/) para dispositivos Android).

En Media Services, un [punto de conexión de streaming](stream-streaming-endpoint-concept.md) (origen) representa un empaquetado dinámico (Just-In-Time) y el servicio de origen que puede entregar directamente el contenido en directo y a petición a una aplicación de reproducción de cliente. Usa uno de los protocolos de streaming de multimedia comunes que se mencionan en la sección siguiente. El *empaquetado dinámico* es una característica incluida en todos los puntos de conexión de streaming.

Las ventajas del empaquetado Just-In-Time son las siguientes:

* Permite almacenar todos los archivos en el formato de archivo MP4 estándar.
* No es necesario almacenar varias copias de los formatos HLS y DASH empaquetados estáticamente en el almacenamiento de blobs, lo que reduce la cantidad de contenido de vídeo almacenado y los costos generales de almacenamiento.
* Puede aprovechar de inmediato las nuevas actualizaciones de los protocolos y los cambios en las especificaciones a medida que evolucionan sin tener que volver a empaquetar el contenido estático en el catálogo.
* Puede entregar contenido con o sin cifrado y DRM usando los mismos archivos MP4 almacenados.
* Puede filtrar o modificar dinámicamente los manifiestos con filtros globales o filtros de nivel de recurso simples para quitar pistas, resoluciones e idiomas específicos, o bien para proporcionar clips más cortos de momentos destacados de los mismos archivos MP4 sin volver a codificar ni a representar el contenido. 

## <a name="to-prepare-your-source-files-for-delivery"></a>Para preparar los archivos de origen para su entrega

Para aprovechar el empaquetado dinámico, tiene que [codificar](encode-concept.md) el archivo intermedio (origen) en un conjunto de archivos MP4 de una o varias velocidades de bits (archivo multimedia base ISO 14496-12). Tiene que tener un [recurso](assets-concept.md) con los archivos MP4 y de configuración de streaming codificados que el empaquetado dinámico de Media Services necesita. A partir de este conjunto de archivos MP4, puede usar el empaquetado dinámico para proporcionar vídeo mediante los protocolos de streaming multimedia que se describen a continuación.

Normalmente, se usa el codificador estándar de Azure Media Services para generar este contenido mediante los valores preestablecidos de codificación en función del contenido o los valores preestablecidos de velocidad de bits adaptable.  Ambos generan un conjunto de archivos MP4 listos para el streaming y el empaquetado dinámico.  Como alternativa, puede codificar en un servicio externo, de forma local o en sus propias máquinas virtuales o aplicaciones de funciones sin servidor. El contenido codificado externamente se puede cargar en un recurso para streaming siempre que cumpla los requisitos de codificación para los formatos de streaming con velocidad de bits adaptable. Hay disponible un proyecto de ejemplo de la carga de un MP4 codificado previamente para streaming en los ejemplos del SDK de .NET. Consulte este ejemplo de [transmisión de archivos MP4 existentes](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamExistingMp4).


El empaquetado dinámico de Azure Media Services solo admite archivos de audio y vídeo en el formato del contenedor de MP4. Los archivos de audio deben estar codificados en un contenedor de MP4 también cuando se usen otros códecs, como Dolby.  

> [!TIP]
> Una manera de obtener los archivos de configuración de streaming y MP4 consiste en [codificar su archivo intermedio con Media Services](#encode-to-adaptive-bitrate-mp4s).  Se recomienda usar el [valor preestablecido de codificación en función del contenido](encode-content-aware-concept.md) para generar las mejores capas y configuraciones de streaming adaptable para el contenido. Vea códigos de ejemplo para codificar con el [SDK de .NET en la carpeta VideoEncoding](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding). 

Para que los vídeos del recurso codificado estén disponibles para que los clientes puedan reproducirlos, tiene que publicar el recurso mediante un [localizador de streaming](stream-streaming-locators-concept.md) y generar las direcciones URL de streaming HLS y DASH adecuadas. Al cambiar el protocolo usado en la consulta de formato de la dirección URL, el servicio entregará el manifiesto de streaming adecuado (HLS o MPEG DASH).

Como resultado, solo tendrá que almacenar los archivos y pagar por ellos en un único formato de almacenamiento (MP4), y Media Services generará y proporcionará los manifiestos HLS o DASH adecuados en función de las solicitudes de los reproductores de los clientes.

Si tiene previsto proteger el contenido mediante el cifrado dinámico de Media Services, consulte [Protocolos de streaming y tipos de cifrado](drm-content-protection-concept.md#streaming-protocols-and-encryption-types).

## <a name="deliver-hls"></a>Entrega de HLS
### <a name="hls-dynamic-packaging"></a>Empaquetado dinámico de HLS

El cliente de streaming puede especificar los siguientes formatos de HLS. Se recomienda usar el formato CMAF para garantizar la compatibilidad con los reproductores y dispositivos iOS más recientes.  En el caso de los dispositivos heredados, los formatos v4 y v3 están disponibles cambiando simplemente la cadena de consulta de formato.

|Protocolo| Cadena de formato| Ejemplo|
|---|---|---|
|HLS CMAF (recomendado)| format=m3u8-cmaf | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|HLS V4 |  format=m3u8-aapl | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 | format=m3u8-aapl-v3 | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|


> [!NOTE]
> Las instrucciones anteriores de Apple recomendaban que la reserva para las redes con un ancho de banda reducido sirviera para proporcionar un flujo de solo audio.  En la actualidad, el codificador de Media Services genera automáticamente una pista de solo audio.  Las instrucciones de Apple ahora indican que la pista de solo audio *no* debe incluirse, especialmente para la distribución de Apple TV.  Con el fin de evitar que el reproductor tenga como valor predeterminado una pista de solo audio, se recomienda usar la etiqueta "audio-only=false" en la URL que quita la copia de solo audio en HLS, o simplemente usar HLS-V3. Por ejemplo, `http://host/locator/asset.ism/manifest(format=m3u8-aapl,audio-only=false)`.


### <a name="hls-packing-ratio-for-vod"></a>Relación de empaquetado de HLS para VoD

Para controlar la relación de empaquetado del contenido de VoD para los formatos HLS más antiguos, puede configurar la etiqueta de metadatos **fragmentsPerHLSSegment** en el archivo .ism para controlar la relación de empaquetado predeterminada de 3:1 para los segmentos de TS entregados desde manifiestos de formato HLS más antiguos (v3 y v4). Este cambio en la configuración requiere modificar directamente el archivo .ism en el almacenamiento para ajustar la relación de empaquetado.

En el siguiente ejemplo de manifiesto de servidor .ism, **fragmentsPerHLSSegment** está establecido en 1. 
``` xml
   <?xml version="1.0" encoding="utf-8" standalone="yes"?>
   <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
         <meta name="formats" content="mp4" />
         <meta name="fragmentsPerHLSSegment" content="1"/>
      </head>
      <body>
         <switch>
         ...
         </switch>
      </body>
   </smil>
```

## <a name="deliver-dash"></a>Entrega de DASH
### <a name="dash-dynamic-packaging"></a>Empaquetado dinámico de DASH

El cliente de streaming puede especificar los siguientes formatos MPEG-DASH:

|Protocolo| Cadena de formato| Ejemplo|
|---|---|---|
|MPEG-DASH CMAF (recomendado)| format=mpd-time-cmaf | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|MPEG-DASH CSF (heredado)| format=mpd-time-csf | `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |

## <a name="deliver-smooth-streaming-manifests"></a>Entrega de manifiestos de Smooth Streaming
### <a name="smooth-streaming-dynamic-packaging"></a>Empaquetado dinámico de Smooth Streaming

El cliente de streaming puede especificar los siguientes formatos de Smooth Streaming:

|Protocolo|Notas y ejemplos| 
|---|---|
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|
|Smooth Streaming 2.0 (manifiesto heredado)|De forma predeterminada el formato de manifiesto Smooth Streaming contiene la etiqueta de repetición (r-tag). Sin embargo, algunos reproductores no admiten `r-tag`. Los clientes con estos reproductores pueden utilizar un formato que deshabilite la etiqueta r-tag:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> Smooth Streaming requiere que haya audio y vídeo en el streaming.

## <a name="on-demand-streaming-workflow"></a>Flujo de trabajo de streaming a petición

En los pasos siguientes se muestra un flujo de trabajo común de streaming de Media Services en el que se usa el empaquetado dinámico junto con el codificador estándar en Azure Media Services.

1. [Cargue un archivo de entrada](job-input-from-http-how-to.md), como por ejemplo MP4, QuickTime/MOV, o cualquier otro formato de archivo compatible. Este archivo también se conoce como archivo de origen o intermedio. Para la lista de formatos admitidos, consulte [Códecs y formatos de Standard Encoder](encode-media-encoder-standard-formats-reference.md).
1. [Codifique](#encode-to-adaptive-bitrate-mp4s) el archivo intermedio en un conjunto de archivos MP4 de velocidad de bits adaptable H.264/AAC.

    Si ya ha codificado los archivos y solo quiere copiar y transmitir los archivos, use: [CopyVideo API](/rest/api/media/transforms/createorupdate#copyvideo) y [CopyAudio API](/rest/api/media/transforms/createorupdate#copyaudio). Como resultado, se creará un archivo MP4 con un manifiesto de streaming (archivo .ism).

    Además, puede generar los archivos .ism y .ismc en un archivo precodificado, siempre que esté codificado con la configuración adecuada para el streaming con velocidad de bits adaptable (esta suele consistir en grupos de imágenes [GOP] de dos segundos, distancias de fotogramas clave de dos segundos como mínimo y máximo, y codificación en modo de velocidad de bits constante [CBR]).  

    Consulte el [ejemplo de transmisión de archivos MP4 existentes del SDK de .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamExistingMp4) para obtener más información sobre cómo generar los archivos .ism (manifiesto de servidor) y .ismc (manifiestos de cliente) para el streaming desde archivos MP4 existentes y precodificados.

1. Publicar el recurso de salida que contiene el conjunto de MP4 de velocidad de bits adaptable. Publicar mediante la creación de un [localizador de streaming](stream-streaming-locators-concept.md).
1. Generar direcciones URL que tienen como destino diferentes formatos (HLS, MPEG-DASH y Smooth Streaming). El *punto de conexión de streaming* se encarga de atender el manifiesto correcto y las solicitudes de todos estos formatos.
    
En el siguiente diagrama se muestra el flujo de trabajo para streaming a petición con empaquetado dinámico.

![Diagrama de un flujo de trabajo para streaming a petición con empaquetado dinámico](./media/encode-dynamic-packaging-concept/media-services-dynamic-packaging.svg)

La ruta de acceso de descarga aparece en la imagen anterior solo para mostrarle que puede descargar un archivo MP4 directamente a través del *punto de conexión de streaming* (origen) (debe especificar la [directiva de streaming](stream-streaming-policy-concept.md) descargable en el localizador de streaming).<br/>El empaquetador dinámico no está modificando el archivo. Opcionalmente puede usar las API de Azure Blob Storage para acceder a un MP4 directamente para la descarga progresiva si desea omitir las características del *punto de conexión de streaming* (origen). 

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Codificación en archivos MP4s de velocidad de bits adaptable

Los artículos siguientes muestran ejemplos de [cómo codificar un vídeo con Media Services](encode-concept.md):

* [Codificación en función del contenido](encode-content-aware-concept.md).
* [Codificación desde una dirección URL de HTTPS con valores preestablecidos integrados](job-input-from-http-how-to.md).
* [Codificación de un archivo local con valores preestablecidos integrados](job-input-from-local-file-how-to.md).
* [Compilación de un valor preestablecido personalizado para sus requisitos específicos de escenario o dispositivo](transform-custom-presets-how-to.md).
* [Ejemplos de código para codificar con el codificador estándar mediante .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding).

Consulte la lista de [códecs y formatos](encode-media-encoder-standard-formats-reference.md) de entrada compatibles con el codificador estándar.

## <a name="live-streaming-workflow"></a>Flujo de trabajo de streaming en vivo

Un evento en directo se puede establecer en una codificación de *paso a través* (un codificador en directo local envía una secuencia de velocidad de bits múltiple) o en una *codificación en directo* (un codificador en directo local envía una secuencia de velocidad de bits única). 

Este es un flujo de trabajo común para el streaming en vivo con el *empaquetado dinámico*:

1. Cree un [evento en directo](live-event-outputs-concept.md).
1. Obtenga la dirección URL de ingesta y configure el codificador local a fin de usar la dirección URL para enviar la fuente de contribución.
1. Obtenga la dirección URL de versión preliminar y úsela para verificar que la entrada del codificador se está recibiendo.
1. Cree un recurso nuevo.
1. Cree una salida en directo y use el nombre del recurso que ha creado.<br />La salida en directo archivará la secuencia en el recurso.
1. Cree un localizador de streaming con los tipos de directiva de streaming integrados.<br />Si va a cifrar el contenido, consulte [Introducción a la protección de contenido](drm-content-protection-concept.md).
1. Enumere las rutas de acceso en el localizador de streaming para recuperar las direcciones URL que se van a usar.
1. Obtenga el nombre de host del punto de conexión de streaming desde el que quiere realizar el streaming.
1. Generar direcciones URL que tienen como destino diferentes formatos (HLS, MPEG-DASH y Smooth Streaming). El *punto de conexión de streaming* se encarga de atender el manifiesto correcto y las solicitudes de los diferentes formatos.

Este diagrama muestra el flujo de trabajo para el streaming en vivo con *empaquetado dinámico*:

![Diagrama de un flujo de trabajo para codificación de paso a través con empaquetado dinámico](./media/live-streaming/pass-through.svg)

Para más información acerca del streaming en vivo en Media Services v3, consulte [Introducción al streaming en vivo](stream-live-streaming-concept.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Códecs de vídeo compatibles con el empaquetado dinámico

El empaquetado dinámico admite archivos de vídeo que estén en el formato de archivo del contenedor de MP4 y que contengan vídeo que esté codificado con [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC o AVC1) o [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 o hvc1).

> [!NOTE]
> Se han probado resoluciones de hasta 4K y velocidades de fotogramas de hasta 60 fotogramas por segundo con el *empaquetado dinámico*.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Códecs de audio compatibles con el empaquetado dinámico

El empaquetado dinámico también admite archivos de audio que estén almacenado en el formato del contenedor de archivos MP4 que contengan flujo de audio codificado con uno de los siguientes códecs:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 o HE-AAC v2). 
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 o E-AC3).  El audio codificado se debe almacenar en el formato del contenedor de MP4 para que funcione con el empaquetado dinámico.
* Dolby Atmos

   El streaming de contenido Dolby Atmos es compatible con estándares como el protocolo MPEG-DASH con formato de streaming común (CSF) o formato de aplicación multimedia común (CMAF) MP4 fragmentado, y mediante HTTP Live Streaming (HLS) con CMAF.
* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Los códecs de DTS admitidos por los formatos de empaquetado DASH-CSF, DASH-CMAF, HLS-M2TS y HLS-CMAF son:  

    * DTS Digital Surround (dtsc)
    * DTS-HD alta resolución y DTS HD Master Audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD Lossless (sin núcleo) (dtsl)

El empaquetado dinámico admite varias pistas de audio con DASH o HLS (versión 4 o posterior) para recursos de streaming que tienen varias pistas de audio con varios códecs y lenguajes.

En todos los códecs de audio anteriores, el audio codificado se debe almacenar en el formato del contenedor de MP4 para que funcione con el empaquetado dinámico. El servicio no admite formatos de archivo de flujo elemental sin procesar en Blob Storage (por ejemplo, los formatos .dts y .ac3 no se aceptarían) 

Para el empaquetado de audio solo se admiten los archivos con las extensiones .mp4 o .mp4a. 

### <a name="limitations"></a>Limitaciones

#### <a name="ios-limitation-on-aac-51-audio"></a>Limitación de iOS en el audio AAC 5.1

Los dispositivos Apple iOS no admiten el códec de audio versión 5.1 de AAC. Se debe codificar el audio multicanal mediante los códecs Dolby Digital o Dolby Digital Plus.

Para obtener información detallada, consulte la [especificación de creación de HLS para dispositivos Apple](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices).

> [!NOTE]
> Media Services no admite la codificación de Dolby digital, Dolby Digital Plus ni Dolby Digital Plus con formatos de audio multicanal de Dolby Atmos.

#### <a name="dolby-digital-audio"></a>Dolby Digital audio

El empaquetado dinámico de Media Services no admite actualmente archivos que contienen audio de [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (ya que se considera un códec heredado por Dolby).

## <a name="manifests"></a>Manifiestos

En el *empaquetado dinámico* de Media Services, los manifiestos de cliente de streaming para HLS, MPEG-DASH y Smooth Streaming se generan dinámicamente basándose en la consulta de **formato** de la dirección URL.  

Un archivo de manifiesto incluye metadatos de streaming como: el tipo de pista (audio, vídeo o texto), el nombre de la pista, la hora inicial y final, la velocidad de bits (calidades), los idiomas de pista, la ventana de presentación (ventana deslizante de duración fija), el códec de vídeo (FourCC). También indica al reproductor que recupere el siguiente fragmento ofreciendo información acerca de los próximos fragmentos de vídeo reproducibles que están disponibles y su ubicación. Los fragmentos (o segmentos) son "fragmentos" reales de un contenido de vídeo.

### <a name="examples"></a>Ejemplos

#### <a name="hls"></a>HLS

Este es un ejemplo de un archivo de manifiesto HLS, también llamado lista de reproducción maestra HLS: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

Este es un ejemplo de un archivo de manifiesto MPEG-DASH, también llamado descripción de presentación multimedia (MPD) MPEG-DASH:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>Smooth Streaming

Este es un ejemplo de un archivo de manifiesto de Smooth Streaming:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>Asignación de nombres a pistas del manifiesto

Si se especifica un nombre de pista de audio en el archivo .ism, Media Services agrega un elemento `Label` dentro de un objeto `AdaptationSet` para especificar la información de textura de la pista de audio específica. Ejemplo del manifiesto DASH de salida:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

El reproductor puede usar el elemento `Label` para mostrar en su interfaz de usuario.

### <a name="signaling-audio-description-tracks"></a>Señalización de pistas de descripción de audio

Puede agregar una pista de narración al vídeo que ayude a los clientes con problemas visuales a seguir la grabación de vídeo escuchando la narración. Es preciso anotar las pistas de audio como descripciones de audio en el manifiesto. Para ello, agregue los parámetros "accessibility" y "role" al archivo .ism. Debe establecer estos parámetros correctamente para señalizar las pistas de audio como descripciones de audio. Por ejemplo, agregue `<param name="accessibility" value="description" />` y `<param name="role" value="alternate"` al archivo .ism para una pista de audio concreta. 

Para más información, consulte el ejemplo de [Señalización de una pista de audio descriptiva ](signal-descriptive-audio-howto.md).

#### <a name="smooth-streaming-manifest"></a>Manifiesto de Smooth Streaming

Si está reproduciendo un flujo de Smooth Streaming, el manifiesto llevaría valores en los atributos `Accessibility` y `Role` para esa pista de audio. Por ejemplo, `Role="alternate" Accessibility="description"` se agregaría en el elemento `StreamIndex` para indicar que es una descripción de audio.

#### <a name="dash-manifest"></a>Manifiesto DASH

En el caso del manifiesto DASH, se agregarían los dos elementos siguientes para indicar la descripción del audio:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>Lista de reproducción de HLS

En el caso de HLS v7, y las versiones superiores`(format=m3u8-cmaf)`, su lista de reproducción llevaría `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` cuando se señale la pista de la descripción de audio.



#### <a name="example"></a>Ejemplo

Para más información, consulte [Señalización de pistas de descripción de audio](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest-filtering"></a>Filtrado de manifiestos dinámicos

Para controlar el número de pistas, formatos, velocidades de bits y ventanas de tiempo de presentación que se envían a los reproductores, puede usar el filtro dinámico con el empaquetador dinámico de Media Services. Para más información, consulte [Manifiestos de un filtrado previo con el empaquetador dinámico](filters-dynamic-manifest-concept.md).

## <a name="dynamic-encryption-for-drm"></a>Cifrado dinámico para DRM

Puede usar el *cifrado dinámico* para cifrar de forma dinámica el contenido en directo o a petición con AES-128 o cualquiera de los tres principales sistemas de administración de derechos digitales (DRM): Microsoft PlayReady, Google Widevine y Apple FairPlay. Media Services también proporciona un servicio para entregar claves AES y licencias de DRM a clientes autorizados. Para más información, consulte [Cifrado dinámico](drm-content-protection-concept.md).

> [!NOTE]
> Widevine es un servicio que ofrece Google Inc. y que está sujeto a los términos del servicio y la directiva de privacidad de Google, Inc.

## <a name="more-information"></a>Más información

Consulte [Comunidad de Azure Media Services](media-services-community.md) para ver diferentes formas de formular preguntas, enviar comentarios y obtener actualizaciones de Media Services.

## <a name="need-help"></a>¿Necesita ayuda?

Puede abrir una incidencia de soporte técnico si se desplaza a la [nueva solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Pasos siguientes

[Carga, codificación y streaming de vídeos](stream-files-tutorial-with-api.md)
