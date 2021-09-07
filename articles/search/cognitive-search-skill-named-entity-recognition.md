---
title: Aptitud cognitiva Reconocimiento de entidades con nombre
titleSuffix: Azure Cognitive Search
description: Extraiga entidades con nombre de persona, ubicación y organización del texto en una canalización de enriquecimiento con inteligencia artificial en Búsqueda cognitiva de Azure.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 0b9de4c1abc492a24dee26dd05e7a164239c8f9b
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860312"
---
#   <a name="named-entity-recognition-cognitive-skill"></a>Aptitud cognitiva Reconocimiento de entidades con nombre

La habilidad **Reconocimiento de entidades con nombre** extrae entidades con nombre del texto. Las entidades disponibles incluyen los tipos `person`, `location` y `organization`.

> [!IMPORTANT]
> La aptitud de reconocimiento de entidades con nombre ya no está disponible y se ha reemplazado por [Microsoft.Skills.Text.v3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md). Siga las recomendaciones de las [Aptitudes de Cognitive Search en desuso](cognitive-search-skill-deprecated.md) para migrar a una aptitud admitida.

 > [!NOTE]
> A medida que expanda el ámbito aumentando la frecuencia de procesamiento, agregando más documentos o agregando más algoritmos de IA, tendrá que [asociar un recurso facturable de Cognitive Services](cognitive-search-attach-cognitive-services.md). Los cargos se acumulan cuando se llama a las API de Cognitive Services y por la extracción de imágenes como parte de la fase de descifrado de documentos de Azure Cognitive Search. No hay ningún cargo por la extracción de texto de documentos. La ejecución de aptitudes integradas se cobra según los [precios de pago por uso de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes.
> 
> La extracción de imágenes es un cargo adicional que mide Azure Cognitive Search, como se describe en la [página de precios](https://azure.microsoft.com/pricing/details/search/). La extracción de texto es gratuita.
>


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Límites de datos
El tamaño máximo de un registro debe tener menos de 50 000 caracteres según la medición de [`String.Length`](/dotnet/api/system.string.length). Si tiene que dividir los datos antes de enviarlos al extractor de frases clave, puede usar la [aptitud de división de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas.

| Nombre de parámetro     | Descripción |
|--------------------|-------------|
| categories    | Matriz de categorías que se deben extraer.  Tipos de categorías posibles: `"Person"`, `"Location"` y `"Organization"`. Si no se proporciona ninguna categoría, se devuelven todos los tipos.|
|defaultLanguageCode |  Código de idioma del texto de entrada. Se admiten los siguientes idiomas: `de, en, es, fr, it`|
| minimumPrecision  | Número comprendido entre 0 y 1. Si la precisión es inferior a este valor, no se devuelve la entidad. El valor predeterminado es 0.|

## <a name="skill-inputs"></a>Entradas de la aptitud

| Nombre de entrada      | Descripción                   |
|---------------|-------------------------------|
| languageCode  | Opcional. El valor predeterminado es `"en"`.  |
| text          | Texto que se analizará.          |

## <a name="skill-outputs"></a>Salidas de la aptitud

| Nombre de salida     | Descripción                   |
|---------------|-------------------------------|
| persons      | Una matriz de cadenas donde cada cadena representa el nombre de una persona. |
| locations  | Una matriz de cadenas donde cada cadena representa una ubicación. |
| organizations  | Una matriz de cadenas donde cada cadena representa una organización. |
| entities | Una matriz de tipos complejos. Cada tipo complejo incluye los siguientes campos: <ul><li>categoría (`"person"`, `"organization"` o `"location"`)</li> <li>valor (el nombre de entidad real)</li><li>desplazamiento (la ubicación donde se encontró en el texto)</li><li>confianza (un valor entre 0 y 1 que representa esa confianza de que el valor es una entidad real)</li></ul> |

##  <a name="sample-definition"></a>Definición de ejemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```
##  <a name="sample-input"></a>Entrada de ejemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Salida de ejemplo

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="warning-cases"></a>Casos de advertencia
Si el código de idioma del documento no se admite, se devuelve una advertencia y no se extrae ninguna entidad.

## <a name="see-also"></a>Consulte también

+ [Aptitudes integradas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Aptitud Reconocimiento de entidades (V3)](cognitive-search-skill-entity-recognition-v3.md)