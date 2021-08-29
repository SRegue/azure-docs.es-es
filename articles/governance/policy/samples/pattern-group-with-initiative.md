---
title: 'Patrón: agrupación de definiciones de directiva con iniciativas'
description: Este patrón de Azure Policy proporciona un ejemplo de cómo agrupar definiciones de directiva en una iniciativa.
ms.date: 08/17/2021
ms.topic: sample
ms.openlocfilehash: 0311da9da70186cf8a244b9bcb8b4b68d3e6ccdc
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324178"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Patrón de Azure Policy: agrupación de definiciones de directiva

Una iniciativa es un grupo de definiciones de directiva. Al agrupar definiciones de directiva relacionadas en un único objeto, puede crear una única asignación que habría representado varias asignaciones.

## <a name="sample-initiative-definition"></a>Definición de iniciativa de ejemplo

Esta iniciativa implementa dos definiciones de directiva, cada una de las cuales toma los parámetros **tagName** y **tagValue**. La propia iniciativa tiene dos parámetros: **costCenterValue** y **productNameValue**.
Se proporciona cada uno de estos parámetros de iniciativa a cada una de las definiciones de directiva agrupadas. Este diseño maximiza la reutilización de las definiciones de directiva existentes, a la vez que limita el número de asignaciones necesarias creadas para implementarlas.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Explicación

#### <a name="initiative-parameters"></a>Parámetros de iniciativa

Una iniciativa puede definir sus propios parámetros que luego se pasan a las definiciones de directiva agrupadas.
En este ejemplo, **costCenterValue** y **productNameValue** se definen como parámetros de iniciativa. Los valores se proporcionan cuando se asigna la iniciativa.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Definiciones de directiva incluidas

Cada definición de directiva incluida debe proporcionar el valor de **policyDefinitionId** y una matriz **parameters** si la definición de directiva acepta parámetros. En el siguiente fragmento de código, la definición de directiva incluida usa dos parámetros: **tagName** y **tagValue**. **tagName** se define con un literal, pero **tagValue** usa el parámetro **costCenterValue** definido por la iniciativa. Este paso de los valores mejora la reutilización.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Pasos siguientes

- Consulte otros [patrones y definiciones integradas](./index.md).
- Revise la [estructura de definición de Azure Policy](../concepts/definition-structure.md).
- Vea la [Descripción de los efectos de directivas](../concepts/effects.md).
