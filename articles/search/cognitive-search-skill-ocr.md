---
title: Aptitud cognitiva de OCR
titleSuffix: Azure Cognitive Search
description: Extraiga texto de archivos de imágenes mediante el reconocimiento óptico de caracteres (OCR) en una canalización de enriquecimiento de Azure Cognitive Search.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: b0ed1860abe3d8a85e87e2bf2746812e1ca67d2e
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860303"
---
# <a name="ocr-cognitive-skill"></a>Aptitud cognitiva de OCR

La aptitud de **reconocimiento óptico de caracteres (OCR)** reconoce texto impreso y manuscrito en archivos de imágenes. Esta aptitud utiliza los modelos de aprendizaje automático proporcionados por [Computer Vision](../cognitive-services/computer-vision/overview.md) API [v3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) de Cognitive Services. La aptitud de **reconocimiento óptico de caracteres** se asocia a la siguiente funcionalidad:

+ En inglés, español, alemán, francés, italiano, portugués y holandés, se usa ["Read"](../cognitive-services/computer-vision/overview-ocr.md#read-api) API.
+ En todos los demás idiomas se usa la API [OCR heredado](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20d).

La aptitud de **OCR** extrae texto de los archivos de imagen. Entre otros, estos son los formatos de archivos admitidos:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ .TIFF

> [!NOTE]
> Esta aptitud está enlazada a Cognitive Services y necesita [un recurso facturable](cognitive-search-attach-cognitive-services.md) para las transacciones que superan los 20 documentos por indizador al día. La ejecución de aptitudes integradas se cobra según los [precios de pago por uso de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes.
> 
> Además, la extracción de imágenes [se puede facturar mediante Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).
>

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas.

| Nombre de parámetro     | Descripción |
|--------------------|-------------|
| `detectOrientation`    | Permite la detección automática de la orientación de la imagen. <br/> Valores válidos: true / false.|
| `defaultLanguageCode` | <p>    Código de idioma del texto de entrada. Los lenguajes compatibles incluyen: <br/> zh-Hans (chino simplificado) <br/> zh-Hant (chino tradicional) <br/>cs (checo) <br/>da (danés) <br/>nl (neerlandés) <br/>en (inglés) <br/>fi (finés)  <br/>fr (francés) <br/>  de (alemán) <br/>el (griego) <br/> hu (húngaro) <br/> it (italiano) <br/>  ja (japonés) <br/> ko (coreano) <br/> nb (noruego) <br/>   pl (polaco) <br/> pt (portugués) <br/>  ru (ruso) <br/>  es (español) <br/>  sv (sueco) <br/>  tr (turco) <br/> ar (árabe) <br/> ro (rumano) <br/> sr-Cyrl (serbio cirílico) <br/> sr-Latn (serbio latino) <br/>  sk (eslovaco) <br/>  unk (desconocido) <br/><br/> Si el código de idioma no está especificado o es nulo, el idioma que se establezca será "inglés". Si el idioma se establece explícitamente en "unk", el idioma se detectará automáticamente. </p> |
| `lineEnding` | Valor que se va a usar entre cada línea detectada. Valores posibles: "Space", "CarriageReturn", "LineFeed".  El valor predeterminado es "Space". |

Anteriormente, había un parámetro denominado "textExtractionAlgorithm" para especificar si la aptitud debe extraer texto "impreso" o "escrito a mano".  Este parámetro está en desuso y ya no es necesario, porque el algoritmo de Read API más reciente es capaz de extraer ambos tipos de texto a la vez.  Si la definición de la aptitud ya incluye este parámetro, no necesita quitarlo, pero ya no se usará y ambos tipos de texto se extraerán independientemente de su configuración.

## <a name="skill-inputs"></a>Entradas de la aptitud

| Nombre de entrada      | Descripción                                          |
|---------------|------------------------------------------------------|
| `image`         | Tipo complejo. Actualmente, solo funciona con el campo "/document/normalized_images", que crea el indexador de Azure Blob cuando ```imageAction``` está establecido en un valor diferente a ```none```. Para obtener más información, consulte este [ejemplo](#sample-output).|


## <a name="skill-outputs"></a>Salidas de la aptitud
| Nombre de salida      | Descripción                   |
|---------------|-------------------------------|
| `text`             | Texto sin formato extraído de la imagen.   |
| `layoutText`    | Tipo complejo que describe el texto extraído, así como la ubicación donde se encontró.|


## <a name="sample-definition"></a>Definición de ejemplo

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Texto y salida de layoutText de ejemplo

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Sample: combinar el texto extraído de imágenes insertadas con el contenido del documento.

Un caso de uso común a la hora de combinar texto, es la capacidad de combinar la representación textual de imágenes (el texto de una aptitud de OCR o la leyenda de una imagen) en el campo de contenido de un documento.

El siguiente conjunto de aptitudes de ejemplo crea un campo *merged_text*. Este campo contiene el contenido textual del documento y el texto procesado con OCR de cada una de las imágenes insertadas en ese documento.

#### <a name="request-body-syntax"></a>Sintaxis del cuerpo de la solicitud
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text",
          "source": "/document/content"
        },
        {
          "name": "itemsToInsert", 
          "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", 
          "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", 
          "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
En el ejemplo del conjunto de aptitudes anterior, se asume que existe un campo de imágenes normalizadas. Para crear este campo, establezca la configuración *imageAction* en la definición del indexador como *generateNormalizedImages*, tal como se muestra a continuación:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Consulte también

+ [¿Qué es el reconocimiento óptico de caracteres?](../cognitive-services/computer-vision/overview-ocr.md)
+ [Aptitudes integradas](cognitive-search-predefined-skills.md)
+ [Aptitud TextMerger](cognitive-search-skill-textmerger.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Create Indexer (REST)](/rest/api/searchservice/create-indexer)
