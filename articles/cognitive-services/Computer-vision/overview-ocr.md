---
title: ¿Qué es el reconocimiento óptico de caracteres?
titleSuffix: Azure Cognitive Services
description: El servicio de reconocimiento óptico de caracteres (OCR) extrae texto visible de una imagen y lo devuelve como cadenas estructuradas.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 06/21/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3c644a62dfd48f86fd21a39af57211ff7c20ae36
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112520627"
---
# <a name="what-is-optical-character-recognition"></a>¿Qué es el reconocimiento óptico de caracteres?

El servicio de reconocimiento óptico de caracteres (OCR) permite extraer el texto impreso o manuscrito de imágenes, como fotos de señales de tráfico y productos, así como de documentos tales como facturas, informes financieros, artículos, etc. Las tecnologías OCR de Microsoft admiten la extracción de texto impreso en [varios idiomas](./language-support.md). Siga un [inicio rápido](./quickstarts-sdk/client-library.md) para comenzar.

![Demostraciones de OCR](./Images/ocr-demo.gif)

Esta documentación contiene los siguientes tipos de artículos:
* Los [inicios rápidos](./quickstarts-sdk/client-library.md) son instrucciones paso a paso que permiten realizar llamadas al servicio y obtener los resultados en un breve período de tiempo. 
* Las [guías paso a paso](./Vision-API-How-to-Topics/call-read-api.md) contienen instrucciones para usar el servicio de maneras más específicas o personalizadas.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="read-api"></a>Read API 

Computer Vision [Read API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) es la tecnología de OCR más reciente de Azure ([información sobre las novedades](./whats-new.md)) que permite extraer texto impreso (en varios idiomas), texto manuscrito (solo en inglés), dígitos y símbolos de divisa de imágenes, y documentos PDF con varias páginas. Esta tecnología está optimizada para extraer texto de imágenes con mucho texto y de documentos PDF con varias páginas y una mezcla de idiomas. Es compatible con la detección de texto impreso y manuscrito en un mismo documento o una misma imagen.

![Cómo OCR convierte imágenes y documentos en una salida estructurada con texto extraído](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Requisitos de entrada

La llamada a **Read** usa las imágenes y los documentos como entrada. Tienen los siguientes requisitos:

* Formatos de archivos admitidos: JPEG, PNG, BMP, PDF y TIFF.
* En el caso de los archivos PDF y TIFF, se procesan hasta 2000 páginas (solo las primeras dos páginas en el nivel Gratis).
* El tamaño de archivo debe ser inferior a 50 MB (6 MB para el nivel Gratis); y sus dimensiones, de al menos 50 x 50 píxeles y, como máximo, de 10 000 x 10 000 píxeles. 

## <a name="supported-languages"></a>Idiomas compatibles
Read API admite un total de 73 idiomas en texto de estilo impreso. Consulte la lista completa de [idiomas admitidos por OCR](./language-support.md#optical-character-recognition-ocr). El OCR de estilo manuscrito se admite exclusivamente en inglés.

## <a name="key-features"></a>Características principales

Read API incluye las características siguientes. 

* Extracción de texto de impresión en 73 idiomas
* Extracción de texto manuscrito en inglés
* Líneas de texto y palabras con puntuaciones de ubicación y confianza
* No se requiere identificación de idioma
* Compatibilidad con idiomas mixtos, modo mixto (impresión y escritura a mano)
* Selección de páginas e intervalos de páginas de documentos grandes de varias páginas
* Orden de lectura natural para las líneas de texto
* Clasificación de escritura a mano para líneas de texto
* Disponible como contenedor sin distribución de Docker para la implementación local

Aprenda a [usar las características de OCR](./vision-api-how-to-topics/call-read-api.md).

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Uso de Cloud API o implementación local
Las instancias de Read Cloud API 3.x son la opción preferida para la mayoría de los clientes debido a su facilidad de integración y su inmediata productividad. Azure y el servicio Computer Vision controlan las necesidades de escalado, rendimiento, seguridad de los datos y cumplimiento, lo que le permite centrarse en satisfacer las necesidades de los clientes.

En las implementaciones locales, el [contenedor de Docker de Read (versión preliminar)](./computer-vision-how-to-install-containers.md) le permite implementar las nuevas funcionalidades de OCR en su entorno local. Los contenedores son excelentes para requisitos específicos de control de datos y seguridad.

> [!WARNING]
> Las operaciones de reconocimiento de texto de Computer Vision 2.0 pronto estarán en desuso en favor de la nueva [Read API](#read-api) de la que se habla en este artículo. Los clientes existentes deben [realizar la transición a operaciones de lectura](upgrade-api-versions.md).

## <a name="data-privacy-and-security"></a>Seguridad y privacidad de datos

Al igual que sucede con todas las instancias de Cognitive Services, los desarrolladores que usan el servicio Computer Vision deben estar al tanto de las directivas de Microsoft sobre los datos de clientes. Para más información, consulte la [página de Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) en Microsoft Trust Center.

## <a name="next-steps"></a>Pasos siguientes

- Comience a trabajar con los [inicios rápidos de la biblioteca cliente o la API REST de OCR (Read)](./quickstarts-sdk/client-library.md).
- Obtenga información sobre la [API REST de Read 3.2](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
