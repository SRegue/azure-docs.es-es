---
title: Conversión de plantillas entre JSON y Bicep
description: Describe los comandos que se pueden usar para convertir plantillas de Azure Resource Manager de Bicep a JSON, y viceversa.
ms.topic: conceptual
ms.date: 05/17/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed273e9cf9af6f29ecc495536fd5d504d2b168f2
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353591"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>Conversión de plantillas de Resource Manager entre JSON y Bicep

En este artículo se describe cómo convertir plantillas de Azure Resource Manager de JSON a Bicep, y viceversa.

Para ejecutar los comandos de conversión, debe tener [instalada la CLI de Bicep](bicep-install.md).

Los comandos de conversión generan plantillas que son funcionalmente equivalentes. Sin embargo, es posible que no sean exactamente iguales en la implementación. Si se convierte una plantilla de JSON a Bicep y, después, se realiza el proceso inverso, es probable que se genere una plantilla con una sintaxis diferente a la de la plantilla original. Cuando se implementan, las plantillas convertidas generan los mismos resultados.

## <a name="convert-from-json-to-bicep"></a>Conversión de JSON a Bicep

La CLI de Bicep incluye un comando para descompilar cualquier plantilla de JSON existente en un archivo Bicep.

Si tiene la CLI de Azure 2.20.0 o posterior, la CLI de Bicep se instala automáticamente. Para descompilar un archivo JSON, use: .

```azurecli
az bicep decompile mainTemplate.json
```

Si no tiene una versión reciente de la CLI de Azure y, en su lugar, ha instalado manualmente la CLI de Bicep, use:

```bash
bicep decompile mainTemplate.json
```

Este comando proporciona un punto de partida para la creación de Bicep, pero el comando no sirve para todas las plantillas. Actualmente, las plantillas anidadas solo se pueden descompilar si usan el ámbito de evaluación de expresión "inner" (interno). Las plantillas que usan bucles de copia no se pueden descompilar.

## <a name="convert-from-bicep-to-json"></a>Conversión de Bicep a JSON

La CLI de Bicep también incluye un comando para convertir Bicep en JSON. 

Para CLI de Azure 2.20.0 o posterior, use el siguiente comando para compilar un archivo JSON:

```azurecli
az bicep build mainTemplate.bicep
```

Si ha instalado manualmente la CLI de Bicep, use:

```bash
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>Exportación de la plantilla y conversión

Puede exportar la plantilla para un grupo de recursos y, después, pasarla directamente al comando de descompilación. En el ejemplo siguiente se muestra cómo descompilar una plantilla exportada.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
az bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Exporte la plantilla](export-template-portal.md) a través del portal.

Use `bicep decompile <filename>` en el archivo descargado.

---

## <a name="side-by-side-view"></a>Vista en paralelo

[Bicep Playground](https://aka.ms/bicepdemo) le permite ver los archivos JSON y Bicep equivalentes en paralelo. Puede seleccionar una plantilla de ejemplo para ver ambas versiones. O bien, seleccione `Decompile` para cargar su propia plantilla JSON y ver el archivo Bicep equivalente.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre Bicep, consulte el [Tutorial de Bicep](./bicep-tutorial-create-first-bicep.md).
