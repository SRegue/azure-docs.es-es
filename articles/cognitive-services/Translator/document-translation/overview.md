---
title: ¿Qué es la traducción de documentos?
description: Información general sobre el proceso y el servicio de traducción de documentos por lotes basados en la nube.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 06/20/2021
ms.openlocfilehash: 267109bcacd8fcb9ff7d44ca6f9a74ca4dc39fda
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112582949"
---
# <a name="what-is-document-translation"></a>¿Qué es la traducción de documentos?

La traducción de documentos es una característica basada en la nube del servicio [Azure Translator](../translator-info-overview.md) y forma parte de la familia de Azure Cognitive Services de las API de REST. La API de traducción de documentos traduce documentos entre todos los [idiomas y dialectos admitidos](../../language-support.md) manteniendo la estructura del documento y el formato de los datos. 

Esta documentación contiene los siguientes tipos de artículos:  

* Los [**inicios rápidos**](get-started-with-document-translation.md) son instrucciones de inicio que le guiarán a la hora de hacer solicitudes al servicio.
* Las [**guías de procedimientos**](create-sas-tokens.md) contienen instrucciones para usar la característica de una manera más específica o personalizada. 
* [**La referencia**](reference/rest-api-guide.md) proporciona la configuración, los valores, las palabras clave y la configuración de la API REST.

## <a name="document-translation-key-features"></a>Características clave de la traducción de documentos

| Característica | Descripción |
| ---------| -------------|
| **Traducir archivos grandes**| Traducir documentos completos de forma asincrónica.|
|**Traducir numerosos archivos**|Traduzca varios archivos entre todos los idiomas y dialectos admitidos manteniendo la estructura del documento y el formato de los datos.|
|**Conservar la presentación del archivo de origen**| Traducir archivos conservando el diseño y el formato originales.|
|**Aplicar traducción personalizada**| Traducir documentos con modelos de [traducción personalizada](../customization.md#custom-translator) y general.|
|**Aplicar glosarios personalizados**|Traducir documentos mediante glosarios personalizados.|
|**Detectar automáticamente el idioma del documento**|Permita que el servicio de traducción de documentos determine el idioma del documento.|
|**Traducir documentos con contenido en varios idiomas**|Use la característica de detección automática para traducir documentos con contenido en varios idiomas al idioma de destino.|

> [!NOTE]
> Al traducir documentos con contenido en varios idiomas, la característica está pensada para oraciones completas en un solo idioma. Si las oraciones incluyen más de un idioma, es posible que el contenido no se traduzca al idioma de destino.
> 
## <a name="how-to-get-started"></a>Cómo empezar

En nuestra guía de paso a paso, aprenderá a empezar a trabajar rápidamente con el traductor de documentos. Para empezar, necesitará una [cuenta de Azure](https://azure.microsoft.com/free/cognitive-services/) activa.  En caso de no tener ninguna, puede [crear una gratis](https://azure.microsoft.com/free).

> [!div class="nextstepaction"]
> [Introducción](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>Formatos de documento admitidos

Los siguientes tipos de archivo de documento son compatibles con la traducción de documentos:

| Tipo de archivo| Extensión de archivo|Descripción|
|---|---|--|
|PDF de Adobe|.pdf|Portable Document Format de Adobe Acrobat|
|Valores separados por comas |.csv| Archivo de datos sin formato delimitados por comas que usan los programas de hoja de cálculo.|
|HTML|.html, .htm|Lenguaje de marcado de hipertexto|
|Formato de archivo de intercambio de localización|.xlf. , xliff| Formato de documento paralelo que se exporta desde los sistemas de memoria de traducción. Los idiomas utilizados se definen dentro del archivo.|
|Markdown| .markdown, .mdown, .mkdn, .md, .mkd, .mdwn, .mdtxt, .mdtext, .rmd| Lenguaje de incremento ligero para crear texto con formato.|
|MHTML|.mthml, .mht| Formato de archivo de página web que se usa para combinar código HTML y sus recursos complementarios.|
|Microsoft Excel|.xls, .xlsx|Archivo de hoja de cálculo para el análisis de datos y la documentación|
|Microsoft Outlook|.msg|Mensaje de correo electrónico creado o guardado en Microsoft Outlook|
|Microsoft PowerPoint|.ppt, .pptx| Archivo de presentación utilizado para mostrar contenido en formato de presentación|
|Microsoft Word|.doc, .docx| Archivo de documento de texto|
|Texto de OpenDocument|.odt|Archivo de documento de texto de código abierto.|
|Presentación de OpenDocument|.odp|Archivo de presentación de código abierto.|
|Hoja de cálculo de OpenDocument|.ods|Archivo de hoja de cálculo de código abierto.|
|Formato de texto enriquecido|.rtf|Documento de texto que incluye formato.|
|Valores separados por tabulaciones/TAB|.tsv/.tab| Archivo de datos sin formato delimitado por tabulaciones que usan los programas de hoja de cálculo.|
|Texto|.txt| Documento de texto sin formato|

## <a name="supported-glossary-formats"></a>Formatos de glosario compatibles

Los siguientes tipos de archivo de glosario son compatibles con la traducción de documentos:

| Tipo de archivo| Extensión de archivo|Descripción|
|---|---|--|
|Valores separados por comas| .csv |Archivo de datos sin formato delimitados por comas que usan los programas de hoja de cálculo.|
|Formato de archivo de intercambio de localización|.xlf. , xliff| Formato de documento paralelo que se exporta desde los sistemas de memoria de traducción. Los idiomas utilizados se definen dentro del archivo.|
|Valores separados por tabulaciones/TAB|.tsv, .tab| Archivo de datos sin formato delimitado por tabulaciones que usan los programas de hoja de cálculo.|

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción a la traducción de documentos](get-started-with-document-translation.md)
