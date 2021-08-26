---
title: Uso de PowerShell para supervisar y escalar una base de datos única en Azure SQL Database
description: Utilice un script de ejemplo de Azure PowerShell para supervisar y escalar una base de datos única en Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.date: 03/12/2019
ms.openlocfilehash: 267a90fadb43e61ad55c39ce78bd199487d5b71c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730437"
---
# <a name="use-powershell-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Uso de PowerShell para supervisar y escalar una base de datos única en Azure SQL Database

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este script de ejemplo de PowerShell supervisa las métricas de rendimiento de una base de datos única, la escala a un tamaño de proceso superior y crea una regla de alertas en una de las métricas de rendimiento.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar PowerShell de manera local, en este tutorial se requiere la versión 1.4.0 de Azure PowerShell o posterior. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-az-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzAccount` para crear una conexión con Azure.

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=15-16 "Monitor and scale single database")]

> [!NOTE]
> Para obtener una lista completa de las métricas, consulte [Métricas compatibles](../../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases).
> [!TIP]
> Use [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) para obtener el estado de las operaciones de base de datos y utilice [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity) para cancelar una operación de actualización de la base de datos.

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Use el siguiente comando para quitar el grupo de recursos y todos los recursos que tenga asociados.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un servidor que hospeda una base de datos única o un grupo elástico. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | Muestra la información de uso del tamaño de la base de datos.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Actualiza las propiedades de la base de datos o traslada la base de datos a un grupo elástico, fuera de este o entre grupos elásticos. |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Establece una regla de alerta para supervisar automáticamente las métricas en el futuro. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |
|||

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/).

Puede encontrar más ejemplos de scripts de PowerShell en [Scripts de Azure PowerShell](../powershell-script-content-guide.md).
