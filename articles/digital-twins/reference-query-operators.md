---
title: 'Referencia del lenguaje de consulta de Azure Digital Twins: operadores'
titleSuffix: Azure Digital Twins
description: Documentación de referencia para los operadores de lenguaje de consulta de Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 03/22/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 06945efc6be8700955b1a475d00d21951102c214
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492800"
---
# <a name="azure-digital-twins-query-language-reference-operators"></a>Referencia del lenguaje de consulta de Azure Digital Twins: operadores

Este documento contiene información de referencia sobre los **operadores** del [lenguaje de consulta de Azure Digital Twins](concepts-query-language.md).

## <a name="comparison-operators"></a>Operadores de comparación

Se admiten los siguientes operadores de la familia de operadores de comparación.

* `=`, `!=`: se usa para comparar la igualdad de las expresiones.
* `<`, `>` : se usa para comparar expresiones de manera ordenada.
* `<=`, `>=` : se usa para comparar expresiones de manera ordenada e incluye la igualdad.

### <a name="example"></a>Ejemplo

A continuación, se proporciona un ejemplo del uso de `=`. La siguiente consulta devuelve gemelos cuyo valor de Temperature es igual a 80.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="EqualityExample":::

A continuación, se proporciona un ejemplo del uso de `<`. La siguiente consulta devuelve gemelos cuyo valor de Temperature es menor que 80.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="ComparisonExample":::

A continuación, se proporciona un ejemplo del uso de `<=`. La siguiente consulta devuelve gemelos cuyo valor de Temperature es menor o igual que 80.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="OrderedComparisonExample":::

## <a name="contains-operators"></a>Operadores de contenido

Se admiten los siguientes operadores de la familia de operadores de contenido.

* `IN`: se evalúa como true si un valor determinado se encuentra en un conjunto de valores.
* `NIN`: se evalúa como true si un valor determinado no se encuentra en un conjunto de valores.

### <a name="example"></a>Ejemplo

A continuación, se proporciona un ejemplo del uso de `IN`. La consulta siguiente devuelve gemelos cuya propiedad `owner` es una de varias opciones de una lista.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="InExample":::

## <a name="logical-operators"></a>Operadores lógicos

Se admiten los siguientes operadores de la familia de operadores lógicos:
* `AND`: se usa para conectar dos expresiones y se evalúa como true si ambas son verdaderas.
* `OR`: se usa para conectar dos expresiones y se evalúa como true si al menos una de ellas es true.
* `NOT`: se usa para negar una expresión y se evalúa como true si no se cumple la condición de la expresión.

### <a name="example"></a>Ejemplo

A continuación, se proporciona un ejemplo del uso de `AND`. La consulta siguiente devuelve gemelos que cumplen ambas condiciones: un valor de Temperature inferior a 80 y un valor de Humidity inferior a 50.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="AndExample":::

A continuación, se proporciona un ejemplo del uso de `OR`. La consulta siguiente devuelve gemelos que cumplen al menos una de las condiciones: un valor de Temperature inferior a 80 y un valor de Humidity inferior a 50.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="OrExample":::

A continuación, se proporciona un ejemplo del uso de `NOT`. La consulta siguiente devuelve gemelos que no cumplen las condiciones de un valor de Temperature inferior a 80.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="NotExample":::

## <a name="limitations"></a>Limitaciones

Los límites siguientes se aplican a las consultas que usan operadores.
* [Límite de IN/NIN](#limit-for-innin)

Vea la sección siguiente para obtener más detalles.

### <a name="limit-for-innin"></a>Límite de IN/NIN

El límite para el número de valores que se pueden incluir en un conjunto `IN` o `NIN` es de 100 valores.
