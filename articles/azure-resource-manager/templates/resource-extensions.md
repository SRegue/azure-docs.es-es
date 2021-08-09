---
title: Configuración posterior a la implementación con extensiones
description: Aprenda a usar las extensiones de plantilla de Azure Resource Manager para las configuraciones posteriores a la implementación.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: 3d458673163454a6d7914de958cb1d338ed12687
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026669"
---
# <a name="post-deployment-configurations-by-using-extensions"></a>Configuraciones posteriores a la implementación mediante extensiones

Las extensiones de plantilla de Azure Resource Manager son pequeñas aplicaciones que proporcionan tareas de automatización y configuración posterior a la implementación sobre los recursos de Azure. El más popular son las extensiones de máquina virtual. Consulte [Características y extensiones de las máquinas virtuales para Windows](../../virtual-machines/extensions/features-windows.md) o [Características y extensiones de las máquinas virtuales para Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Extensiones

Las extensiones existentes son:

- [Microsoft.Compute/virtualMachines/extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clusters/extensions](/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft.Sql servers/databases/extensions](/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)
- [Microsoft.Web/sites/siteextensions](/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Para averiguar las extensiones disponibles, vaya a la [referencia de plantilla](/azure/templates/). En **Filtrar por título**, escriba **extensión**.

Para aprender cómo usar estas extensiones, consulte:

- [Tutorial: Implementación de extensiones de máquina virtual con plantillas de Resource Manager](template-tutorial-deploy-vm-extensions.md).
- [Tutorial: Importación de archivos BACPAC de SQL con plantillas de Resource Manager](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Implementación de extensiones de máquina virtual con plantillas de Resource Manager](template-tutorial-deploy-vm-extensions.md)
