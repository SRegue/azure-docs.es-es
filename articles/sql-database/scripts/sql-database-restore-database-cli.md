---
title: 'CLI de Azure: Restaurar una copia de seguridad'
description: Script de ejemplo de la CLI de Azure para restaurar una base de datos de Azure SQL Database a un momento anterior a partir de copias de seguridad automáticas.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: SQLSourabh
ms.author: sourabha
ms.reviewer: carlrab
ms.date: 03/27/2019
ms.openlocfilehash: 5dc1516595aa74d12cbcb55a1a19b34cf952f86d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748052"
---
# <a name="use-cli-to-restore-a-single-database-in-azure-sql-database-to-an-earlier-point-in-time"></a>Uso de la CLI para restaurar una base de datos única de Azure SQL Database a un momento anterior

En este ejemplo de la CLI de Azure se restaura una base de datos única de Azure SQL Database a un momento dado.  

Si decide instalar y usar la CLI localmente, para este artículo es preciso que ejecute la versión 2.0 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de ejemplo

### <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Ejecute el script.

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/restore-database/restore-database.sh "Restore SQL Database")]

### <a name="clean-up-deployment"></a>Limpieza de la implementación

Use el siguiente comando para quitar el grupo de recursos y todos los recursos que tenga asociados.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referencia de ejemplo

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Descripción |
|---|---|
| [az sql db restore](/cli/azure/sql/db#az_sql_db_restore) | Comando de restauración de base de datos. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

Encontrará más ejemplos de scripts de la CLI de SQL Database en la [documentación de Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
