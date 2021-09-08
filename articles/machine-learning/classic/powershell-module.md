---
title: 'ML Studio (clásico): módulos de PowerShell (Azure)'
description: Use PowerShell para crear y administrar áreas de trabajo de Estudio de Machine Learning (clásico), experimentos, servicios web y mucho más.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 060cea47bf70f1e13310ecc408f89a46365a8595
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580303"
---
# <a name="powershell-modules-for-machine-learning-studio-classic"></a>Módulos de PowerShell para Machine Learning Studio (clásico)

**SE APLICA A:**  ![Se aplica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clásico)   ![No se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Con los módulos de PowerShell, puede administrar mediante programación los recursos de Studio (clásico), como áreas de trabajo, conjuntos de datos y servicios web.

Puede interactuar con recursos de Studio (clásico) mediante tres módulos de PowerShell:

* [Azure PowerShell Az](#az-rm), lanzado en 2018, que incluye toda la funcionalidad de AzureRM, aunque con distintos nombres de cmdlets
* [AzureRM](#az-rm) publicado en 2016, se sustituye por PowerShell Az
* [PowerShell clásico para Machine Learning](#classic), lanzado en 2016

Aunque estos módulos de PowerShell presentan algunas similitudes, cada uno está diseñado para escenarios determinados. En este artículo se describen las diferencias entre los módulos de PowerShell y se ofrece orientación para decidir cuál elegir.  

Consulte la [tabla de compatibilidad](#support-table) siguiente para comprobar qué recursos son compatibles con cada módulo. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell Az y AzureRM

Az ahora es el módulo deseado de PowerShell para interactuar con Azure e incluye toda la funcionalidad anterior de AzureRM. AzureRM seguirá recibiendo las correcciones de errores, pero no recibirá ninguna característica o cmdlets nuevos.  Tanto Az como AzureRM administran soluciones implementadas con el modelo de implementación de **Azure Resource Manager**. Estos recursos incluyen las áreas de trabajo de Studio (clásico) y los servicios web "nuevos" de Studio (clásico). 

PowerShell clásico se puede instalar junto con Az o AzureRM para abarcar los tipos de recurso "nuevo" y "clásico". Sin embargo, no se recomienda tener Az y AzureRM instalados al mismo tiempo. Para decidir entre Az y AzureRM, Microsoft recomienda Az para todas las implementaciones futuras.  Obtenga más información sobre Az frente a AzureRM y la ruta de migración en [Introducción a Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Para empezar a trabajar con Az, siga las [instrucciones de instalación de Azure Az](/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a> PowerShell clásico

El [módulo PowerShell clásico](https://aka.ms/amlps) para Studio (clásico) permite administrar los recursos implementados con el **modelo de implementación clásica**. Estos recursos incluyen recursos de usuario, servicios web "clásicos" y puntos de conexión de servicios web "clásicos" de Studio (clásico).

Pero Microsoft recomienda usar el modelo de implementación de Resource Manager para todos los recursos futuros a fin de simplificar la implementación y administración de los recursos. Si desea obtener más información sobre los modelos de implementación, vea el artículo [Implementación mediante Azure Resource Manager frente a la implementación clásica](../../azure-resource-manager/management/deployment-models.md).

Para empezar a utilizar PowerShell clásico, descargue el [paquete de versión](https://github.com/hning86/azuremlps/releases) de GitHub y siga las [instrucciones de instalación](https://github.com/hning86/azuremlps/blob/master/README.md). Estas instrucciones explican cómo desbloquear el archivo DLL descargado y descomprimido y, después, importarlo en el entorno de PowerShell.

PowerShell clásico se puede instalar junto con Az o AzureRM para abarcar los tipos de recurso "nuevo" y "clásico".

## <a name="powershell-support-table"></a><a name="support-table"></a> Tabla de compatibilidad con PowerShell


| Tarea | **Az** |  **PowerShell clásico** |
| --- | --- | --- |
| Creación o eliminación de áreas de trabajo | [Plantillas de Resource Manager](./deploy-with-resource-manager-template.md) |  |
| Administración de planes de compromiso de área de trabajo | [New-AzMlCommitmentPlan](/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Administrar usuarios del área de trabajo |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Administración de servicios web | [New-AzMlWebService](/powershell/module/az.machinelearning/new-azmlwebservice) <br>(servicios web "nuevos")| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(servicios web "clásicos") |
| Administración de puntos de conexión y claves de servicios web |  [Get-AzMlWebServiceKey](/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Administración de conjuntos de datos de usuario o modelos entrenados| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Administración de experimentos de usuario |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Administrar módulos personalizados | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Pasos siguientes
Consulte la documentación completa de estos módulos de PowerShell:
* [PowerShell clásico](https://aka.ms/amlps)
* [Azure PowerShell Az](/powershell/module/az.machinelearning/#machine_learning)