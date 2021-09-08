---
title: 'ML Studio (clásico): Implementación de áreas de trabajo con Azure Resource Manager: Azure'
description: Implementación de un área de trabajo para Estudio de Machine Learning (clásico) mediante una plantilla de Azure Resource Manager
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-azurepowershell
ms.date: 02/05/2018
ms.openlocfilehash: 9a915a3e5df97da522c8e566a857e04d7491c471
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112581599"
---
# <a name="deploy-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Implementación de un área de trabajo de Estudio de Machine Learning (clásico) mediante Azure Resource Manager

**SE APLICA A:**  ![Se aplica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clásico)   ![No se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

El uso de una plantilla de implementación de Azure Resource Manager le permite ahorrar tiempo proporcionándole una manera escalable de implementar componentes interconectados con un mecanismo de validación y reintento. Para configurar áreas de trabajo de Estudio de Machine Learning (clásico), por ejemplo, debe configurar primero una cuenta de Azure Storage y, a continuación, implementar el área de trabajo. Imagine que tiene que hacer esto manualmente para cientos de áreas de trabajo. Una alternativa más sencilla es utilizar una plantilla de Azure Resource Manager para implementar un área de trabajo de Studio (clásico) y todas sus dependencias. Este artículo le guiará por este proceso paso a paso. Para ver una introducción excelente sobre Azure Resource Manager, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Paso a paso: Creación de un área de trabajo de Machine Learning
Crearemos un grupo de recursos de Azure e implementaremos una nueva cuenta de almacenamiento de Azure y una nueva área de trabajo de Estudio de Machine Learning (clásico) mediante una plantilla de Resource Manager. Una vez completada la implementación, se imprimirá información importante acerca de las áreas de trabajo que se crearon (la clave principal, el identificador y la dirección URL del área de trabajo).

### <a name="create-an-azure-resource-manager-template"></a>Creación de una plantilla de Azure Resource Manager

Un área de trabajo de Machine Learning requiere una cuenta de almacenamiento de Azure para almacenar el conjunto de datos vinculado a ella.
La siguiente plantilla usa el nombre del grupo de recursos para generar el nombre de la cuenta de almacenamiento y el nombre del área de trabajo.  También utiliza el nombre de la cuenta de almacenamiento como una propiedad al crear el área de trabajo.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Guarde esta plantilla como archivo mlworkspace.json en c:\temp.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Implementación del grupo de recursos basado en la plantilla

* Abra PowerShell
* Instale los módulos de Azure Resource Manager y Azure Service Management

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   Estos pasos permiten descargar e instalar los módulos necesarios para completar los pasos restantes. Esto solo debe realizarse una vez en el entorno donde se ejecutan los comandos de PowerShell.

* Autentíquese en Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Este paso debe repetirse para cada sesión. Una vez autenticado, aparecerá la información de la suscripción.

![Cuenta de Azure](./media/deploy-with-resource-manager-template/azuresubscription.png)

Ahora que tenemos acceso a Azure, podemos crear el grupo de recursos.

* Crear un grupo de recursos

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Compruebe que se ha aprovisionado correctamente el grupo de recursos. **ProvisioningState** debe ser "Succeeded" (Correcto).
La plantilla utiliza el nombre del grupo de recursos para generar el nombre de la cuenta de almacenamiento. El nombre de la cuenta de almacenamiento debe tener entre 3 y 24 caracteres, y usar solo números y letras minúsculas.

![Grupo de recursos](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Mediante la implementación del grupo de recursos, implemente una nueva área de trabajo de Machine Learning.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Una vez completada la implementación, resulta sencillo tener acceso a las propiedades del área de trabajo que implementó. Por ejemplo, puede tener acceso al token de clave principal.

```powershell
# Access Machine Learning Studio (classic) Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Otra manera de recuperar los tokens del área de trabajo existente es usar el comando Invoke-AzResourceAction. Por ejemplo, puede enumerar los tokens principales y secundarios de todas las áreas de trabajo.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Una vez aprovisionada el área de trabajo, también puede automatizar muchas tareas de Estudio de Machine Learning (clásico) mediante el [módulo de PowerShell para Estudio de Machine Learning (clásico)](https://aka.ms/amlps).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre la [creación de plantillas de Azure Resource Manager](../../azure-resource-manager/templates/syntax.md).
* Eche un vistazo al [repositorio de plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates).
* Vea este vídeo acerca de [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Consulte la [ayuda de referencia de plantillas de Resource Manager](/azure/templates/microsoft.machinelearning/allversions).

<!--Link references-->