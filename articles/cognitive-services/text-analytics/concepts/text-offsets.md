---
title: Desplazamientos de texto en Text Analytics API
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre los desplazamientos provocados por codificaciones de varios idiomas y Emojis.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 05/18/2021
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 89091f8914acea62370290e25a2f98f5bcb220a7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745545"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Desplazamientos de texto en la salida de Text Analytics API

La compatibilidad con varios idiomas y con Emojis ha dado lugar a codificaciones Unicode que usan más de un [punto de código](https://wikipedia.org/wiki/Code_point) para representar un solo carácter mostrado, llamado grafema. Por ejemplo, los Emojis como 🌷 y 👍 pueden utilizar varios caracteres para crear la forma con caracteres adicionales para los atributos visuales, como el tono de máscara. Del mismo modo, la palabra Hindi `अनुच्छेद` está codificada como cinco letras y tres marcas de combinación.

Debido a las distintas longitudes de posibles codificaciones de varios idiomas y Emojis, Text Analytics API puede devolver desplazamientos en la respuesta.

## <a name="offsets-in-the-api-response"></a>Desplazamientos en la respuesta de la API 

Siempre que se devuelvan desplazamientos en la respuesta de la API, como [Reconocimiento de entidades con nombre](../how-tos/text-analytics-how-to-entity-linking.md) o [Análisis de sentimiento](../how-tos/text-analytics-how-to-sentiment-analysis.md), recuerde lo siguiente:

* Los elementos de la respuesta pueden ser específicos del punto de conexión al que se llamó. 
* Las cargas de HTTP POST/GET están codificadas en [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), que puede ser o no la codificación de caracteres predeterminada del compilador del lado cliente o sistema operativo.
* Los desplazamientos hacen referencia a recuentos de grafemas basados en el estándar [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0), no recuentos de caracteres.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extracción de subcadenas de texto con desplazamientos

Los desplazamientos pueden causar problemas al usar métodos substring basados en caracteres, por ejemplo el método [substring()](/dotnet/api/system.string.substring) de .NET. Un problema es que un desplazamiento puede hacer que un método substring finalice en mitad de una codificación de grafemas de carácter múltiple en lugar de al final.

En .NET, tenga en cuenta el uso de la clase [StringInfo](/dotnet/api/system.globalization.stringinfo), que le permite trabajar con una cadena como una serie de elementos textuales, en lugar de objetos de caracteres individuales. También puede buscar bibliotecas del separador de grafemas en su entorno de software preferido. 

Text Analytics API devuelve estos elementos textuales también, por motivos de comodidad.

## <a name="offsets-in-api-version-31"></a>Desplazamientos de la API versión 3.1

En la versión 3.1 de la API, todos los puntos de conexión de Text Analytics API que devuelven un desplazamiento serán compatibles con el parámetro `stringIndexType`. Este parámetro ajusta los atributos `offset` y `length` en la salida de la API para que coincidan con el esquema solicitado de iteración de cadena. Actualmente, se admiten tres tipos:

1. `textElement_v8` (valor predeterminado): itera por los grafemas, tal y como se define en [Unicode Standard 8.0.0](https://unicode.org/versions/Unicode8.0.0).
2. `unicodeCodePoint`: itera por los [puntos de código Unicode](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564), el esquema predeterminado para Python 3.
3. `utf16CodeUnit`: itera por las [unidades de código UTF-16](https://unicode.org/faq/utf_bom.html#UTF16), el esquema predeterminado para JavaScript, Java y .NET.

Si el elemento `stringIndexType` solicitado coincide con el entorno de programación de su elección, la extracción de subcadenas se puede realizar mediante los métodos estándar substring o Slice. 

## <a name="see-also"></a>Consulte también

* [Información general de Text Analytics](../overview.md)
* [Análisis de opiniones](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconocimiento de entidades](../how-tos/text-analytics-how-to-entity-linking.md)
* [Detección de idioma](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconocimiento de idioma](../how-tos/text-analytics-how-to-language-detection.md)
