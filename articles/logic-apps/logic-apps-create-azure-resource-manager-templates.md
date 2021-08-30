---
title: Creación de plantillas de aplicación lógica para implementación
description: Aprenda a crear plantillas de Azure Resource Manager para automatizar la implementación en Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 07/20/2021
ms.openlocfilehash: 3378c04fd6a43d50b670767d1d3fc4f59043e1e9
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456609"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Creación de plantillas de Azure Resource Manager para automatizar la implementación de Azure Logic Apps

Para ayudarle a automatizar la creación e implementación de su aplicación lógica, en este artículo se describen las formas en que puede crear una [plantilla de Azure Resource Manager](../azure-resource-manager/management/overview.md) para la aplicación lógica. Para obtener información general sobre la estructura y la sintaxis de una plantilla que incluye la definición de flujo de trabajo y otros recursos necesarios para la implementación, consulte [Introducción: Implementación automatizada de aplicaciones lógicas con plantillas de Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md).

Azure Logic Apps proporciona una [plantilla precompilada de Azure Resource Manager para aplicaciones lógicas](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.logic/logic-app-create/azuredeploy.json) que puede reutilizar no solo para crear aplicaciones lógicas, sino también para definir los recursos y parámetros que desea usar para la implementación. Puede usar esta plantilla para sus propios escenarios empresariales o personalizar para satisfacer sus requisitos.

> [!IMPORTANT]
> Asegúrese de que las conexiones de la plantilla usen el mismo grupo de recursos y la misma ubicación de Azure que la aplicación lógica.

Para obtener más información acerca de las plantillas de Azure Resource Manager, consulte estos temas:

* [Estructura y sintaxis de las plantillas de Azure Resource Manager](../azure-resource-manager/templates/syntax.md)
* [Creación de plantillas del Administrador de recursos de Azure](../azure-resource-manager/templates/syntax.md)
* [Desarrollo de plantillas de Azure Resource Manager para mantener la coherencia en la nube](../azure-resource-manager/templates/template-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Creación de plantillas con Visual Studio

Para crear de manera fácil plantillas de aplicación lógica parametrizadas válidas que estén principalmente listas para la implementación, use Visual Studio (la edición Community gratis o superior) y las herramientas de Azure Logic Apps para Visual Studio. A continuación, puede [crear la aplicación lógica en Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) o [buscar y descargar una aplicación lógica existente desde Azure Portal en Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Al descargar la aplicación lógica, obtiene una plantilla que incluye las definiciones de la aplicación lógica y otros recursos, como las conexiones. La plantilla también *parametriza* o define los parámetros de los valores que se usan para implementar la aplicación lógica y otros recursos. Puede asignar los valores a estos parámetros en un archivo de parámetros independiente. De este modo, puede cambiar estos valores más fácilmente en función de sus necesidades de implementación. Para más información, consulte los temas siguientes:

* [Creación de aplicaciones lógicas con Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Administración de aplicaciones lógicas con Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Creación de plantillas con Azure PowerShell

Puede crear plantillas de Resource Manager mediante Azure PowerShell con el [módulo LogicAppTemplate](https://github.com/jeffhollan/LogicAppTemplateCreator). Este módulo de código abierto evalúa primero la aplicación lógica y las conexiones que usa. Después, el módulo genera los recursos de plantilla con los parámetros necesarios para la implementación.

Por ejemplo, suponga que tiene una aplicación lógica que recibe un mensaje de una cola de Azure Service Bus y sube datos a Azure SQL Database. El módulo conserva toda la lógica de orquestación y parametriza las cadenas de conexión de SQL y Service Bus con el fin de que se puedan proporcionar y cambiar esos valores en función de sus necesidades de implementación.

En estos ejemplos se muestra cómo crear e implementar aplicaciones lógicas mediante plantillas de Azure Resource Manager, Azure Pipelines en Azure DevOps y Azure PowerShell:

* [Ejemplo: Organización de Azure Pipelines mediante Azure Logic Apps](https://github.com/Azure-Samples/azure-logic-apps-pipeline-orchestration)
* [Ejemplo: Conexión a cuentas de Azure Storage desde Azure Logic Apps e implementación con Azure Pipelines en Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/storage-account-connections)
* [Ejemplo: Conexión a colas de Azure Service Bus desde Azure Logic Apps e implementación con Azure Pipelines en Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/service-bus-connections)
* [Ejemplo: Conexión a una acción de Azure Functions para Azure Logic Apps e implementación con Azure Pipelines en Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/function-app-actions)
* [Ejemplo: Conexión a una cuenta de integración desde Azure Logic Apps e implementación con Azure Pipelines en Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/integration-account-connections)

### <a name="install-powershell-modules"></a>Instalación de módulos PowerShell

1. Si todavía no lo ha hecho, instale [Azure PowerShell](/powershell/azure/install-az-ps).

1. La forma más fácil de instalar el módulo LogicAppTemplate es desde la [Galería de PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate). Para ello, ejecute este comando:

   ```powershell
   Install-Module -Name LogicAppTemplate
   ```

   Para actualizar a la versión más reciente, ejecute este comando:

   ```powershell
   Update-Module -Name LogicAppTemplate
   ```

O bien, para realizar la instalación manualmente, siga los pasos de GitHub para el [Creador de plantillas de aplicación lógica](https://github.com/jeffhollan/LogicAppTemplateCreator).

### <a name="install-azure-resource-manager-client"></a>Instalación del cliente de Azure Resource Manager

Para que el módulo LogicAppTemplate funcione con cualquier inquilino de Azure y cualquier token de acceso de suscripción, instale la [herramienta de cliente de Azure Resource Manager](https://github.com/projectkudu/ARMClient), que es una sencilla herramienta de línea de comandos que llama a la API de Azure Resource Manager.

Al ejecutar el comando `Get-LogicAppTemplate` con esta herramienta, el comando obtiene primero un token de acceso mediante la herramienta ARMClient, canaliza el token al script de PowerShell y crea la plantilla como un archivo JSON. Para obtener más información sobre la herramienta, consulte este [artículo acerca de la herramienta cliente de Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Generación de plantillas con PowerShell

Para generar la plantilla después de instalar el módulo LogicAppTemplate y la [CLI de Azure](/cli/azure/), ejecute este comando de PowerShell:

```powershell
$parameters = @{
    Token = (az account get-access-token | ConvertFrom-Json).accessToken
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

Para seguir la recomendación de la canalización en un token desde la [herramienta de cliente de Azure Resource Manager](https://github.com/projectkudu/ARMClient), ejecute este comando como alternativa donde `$SubscriptionId` sea su identificador de suscripción de Azure:

```powershell
$parameters = @{
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

armclient token $SubscriptionId | Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

Después de la extracción, puede crear un archivo de parámetros a partir de la plantilla. Para ello, ejecute este comando:

```powershell
Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Para la extracción con referencias de Azure Key Vault (solo estática), ejecute este comando:

```powershell
Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parámetros | Obligatorio | Descripción |
|------------|----------|-------------|
| TemplateFile | Sí | Ruta de acceso al archivo de plantilla |
| KeyVault | No | Una enumeración que describe cómo controlar los posibles valores del almacén de claves. El valor predeterminado es `None`. |
||||

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación de plantillas de aplicaciones lógicas](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)