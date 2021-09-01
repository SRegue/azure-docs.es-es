---
title: Implementación de plantillas de aplicaciones lógicas
description: Obtenga información sobre cómo implementar plantillas de Azure Resource Manager creadas para Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8be8361b58e4b1b1fad3f41b29958a360e206890
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733777"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Implementación de plantillas de Azure Resource Manager para Azure Logic Apps

Después de crear una plantilla de Azure Resource Manager para la aplicación lógica, puede implementar la plantilla de las siguientes maneras:

* [Azure Portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [CLI de Azure](#cli)
* [API de REST del Administrador de recursos de Azure](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Implementación a través de Azure Portal

Para implementar automáticamente una plantilla de aplicación lógica en Azure, puede elegir el siguiente botón **Implementar en Azure**, que inicia sesión en Azure Portal y le pide información sobre la aplicación lógica. A continuación, haga los cambios necesarios en la plantilla o los parámetros de la aplicación lógica.

[![Implementación en Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.logic%2Flogic-app-create%2Fazuredeploy.json)

Por ejemplo, se le pedirá la siguiente información después de iniciar sesión en Azure Portal:

* El nombre de la suscripción a Azure
* El grupo de recursos que desea usar
* La ubicación de la aplicación lógica
* Nombre de la aplicación lógica
* Un URI de prueba
* La aceptación de los términos y condiciones especificados

Para más información, consulte los temas siguientes:

* [Información general: Implementación automatizada de aplicaciones lógicas con plantillas de Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)
* [Implementación de recursos con las plantillas de Resource Manager y Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Implementación con Visual Studio

Para implementar una plantilla de aplicación lógica desde un proyecto de grupo de recursos de Azure creado con Visual Studio, siga estos [pasos para implementar manualmente la aplicación lógica](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) en Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Implementación con Azure PowerShell

Para implementar en un determinado *grupo de recursos de Azure*, use el siguiente comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.logic/logic-app-create/azuredeploy.json
```

Para más información, consulte los temas siguientes:

* [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Implementación con la CLI de Azure

Para implementar en un determinado *grupo de recursos de Azure*, use el siguiente comando:

```azurecli
az deployment group create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.logic/logic-app-create/azuredeploy.json
```

Para más información, consulte los temas siguientes:

* [Implementación de recursos con plantillas de Resource Manager y la CLI de Azure](../azure-resource-manager/templates/deploy-cli.md)
* [`az deployment group create`](/cli/azure/deployment/group#az_deployment_group_create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Implementación con Azure DevOps

Para implementar las plantillas de aplicación lógica y administrar entornos, los equipos suelen usan una herramienta como [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) en [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines proporciona una [tarea Implementación de un grupo de recursos de Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2), que se puede agregar a cualquier canalización de la compilación o versión. Para que la autorización implemente y genere la canalización de versión, también necesita una [entidad de servicio](../active-directory/develop/app-objects-and-service-principals.md) de Azure Active Directory. Obtenga más información sobre [el uso de entidades de servicio con Azure Pipelines](/azure/devops/pipelines/library/connect-to-azure).

Para más información acerca de la integración continua e implementación continua (CI/CD) para plantillas de Azure Resource Manager con Azure Pipelines, consulte estos temas y ejemplos:

* [Integración de plantillas de Resource Manager con Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Tutorial: Integración continua de plantillas de Azure Resource Manager en Azure Pipelines](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Ejemplo: Organización de Azure Pipelines mediante Azure Logic Apps](https://github.com/Azure-Samples/azure-logic-apps-pipeline-orchestration)
* [Ejemplo: Conexión a cuentas de Azure Storage desde Azure Logic Apps e implementación con Azure Pipelines en Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/storage-account-connections)
* [Ejemplo: Conexión a colas de Azure Service Bus desde Azure Logic Apps e implementación con Azure Pipelines en Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/service-bus-connections)
* [Ejemplo: Conexión a una acción de Azure Functions para Azure Logic Apps e implementación con Azure Pipelines en Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/function-app-actions)
* [Ejemplo: Conexión a una cuenta de integración desde Azure Logic Apps e implementación con Azure Pipelines en Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/integration-account-connections)

Aquí puede encontrar los pasos generales de alto nivel para usar Azure Pipelines:

1. En Azure Pipelines, cree una canalización vacía.

1. Elija los recursos que necesita para la canalización, como la plantilla de aplicación lógica y los archivos de parámetros de plantilla, que genera manualmente o como parte del proceso de compilación.

1. Para el trabajo del agente, busque y agregue la tarea de **implementación del grupo de recursos de Azure**.

   ![Incorporación de la tarea "Implementación de un grupo de recursos de Azure"](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Configúrelo con una [entidad de servicio](/azure/devops/pipelines/library/connect-to-azure).

1. Agregue referencias a la plantilla de la aplicación lógica y los archivos de parámetros de la plantilla.

1. Siga compilando los pasos del proceso de lanzamiento de otros entornos, pruebas automatizadas o aprobadores según sea necesario.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autorización de conexiones de OAuth

Después de la implementación, la aplicación lógica funciona de un extremo a otro con parámetros válidos, pero para generar tokens de acceso válidos para la [autenticación de las credenciales](../active-directory/develop/authentication-vs-authorization.md), todavía tiene que autorizar o usar conexiones de OAuth previamente autorizadas. Sin embargo, solo tiene que implementar y autenticar los recursos de conexión de API una vez, lo que significa que no tiene que incluir esos recursos de conexión en las implementaciones posteriores, a menos que tenga que actualizar la información de conexión. Si usa la integración continua y la canalización de implementación continua, solo debe implementar los recursos de Logic Apps actualizados y no tendrá que volver a autorizar las conexiones cada vez.

Estas son algunas sugerencias para controlar la autorización de las conexiones:

* Para autorizar manualmente las conexiones de OAuth, abra la aplicación lógica en el diseñador de aplicaciones lógicas, ya sea en Azure Portal o en Visual Studio. Al autorizar la conexión, puede aparecer una página de confirmación para permitir el acceso.

* Autorice previamente y comparta los recursos de conexión de API entre las aplicaciones lógicas que se encuentran en la misma región. Las conexiones de API existen como recursos de Azure independientemente de las aplicaciones lógicas. Aunque las aplicaciones lógicas tengan dependencias de los recursos de conexión de API, estos no tienen dependencias de las aplicaciones lógicas y permanecen después de eliminar las aplicaciones lógicas dependientes. Además, las aplicaciones lógicas pueden usar conexiones de API que existen en otros grupos de recursos. Sin embargo, el diseñador de aplicaciones lógicas admite la creación de conexiones de API solo en el mismo grupo de recursos que las aplicaciones lógicas.

  > [!NOTE]
  > Si está pensando en compartir las conexiones de API, asegúrese de que la solución pueda [controlar los posibles problemas de limitación](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling). La limitación se produce en el nivel de conexión, por lo que reutilizar la misma conexión entre varias aplicaciones lógicas podría aumentar la posibilidad de que se produzcan problemas de limitación.

* A menos que su escenario implique servicios y sistemas que requieran la autenticación multifactor, puede usar un script de PowerShell para dar su consentimiento a cada conexión de OAuth mediante la ejecución de un trabajo de integración continua, como una cuenta de usuario normal en una máquina virtual que tenga sesiones activas del explorador con las autorizaciones y el consentimiento ya proporcionado. Por ejemplo, puede reasignar el script de ejemplo proporcionado por el [proyecto LogicAppConnectionAuth en el repositorio de GitHub de Logic Apps](https://github.com/logicappsio/LogicAppConnectionAuth).

* Si usa una [entidad de servicio](../active-directory/develop/app-objects-and-service-principals.md) de Azure Active Directory (Azure AD) en lugar de autorizar las conexiones, aprenda a [especificar parámetros de entidad de servicio en la plantilla de aplicación lógica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Supervisión de aplicaciones lógicas](../logic-apps/monitor-logic-apps.md)
