---
title: 'Inicio rápido: Creación de una cuenta de Automation: plantilla de Azure'
titleSuffix: Azure Automation
description: En esta guía de inicio rápido se muestra cómo crear una cuenta de Automation mediante la plantilla de Azure Resource Manager.
services: automation
ms.author: magoedte
ms.date: 07/20/2021
ms.topic: quickstart
ms.workload: infrastructure-services
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: 17183bffcfe9607a774d1205180c284d49cd7503
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652017"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Inicio rápido: Creación de una cuenta de Automation mediante una plantilla de ARM

Azure Automation ofrece un servicio de configuración y de automatización basado en la nube que facilita una administración coherente en los entornos que se encuentren dentro y fuera de Azure. En esta guía de inicio rápido se muestra cómo implementar una plantilla de Azure Resource Manager (plantilla de ARM) que crea una cuenta de Automation. El uso de una plantilla de ARM requiere menos pasos que otros métodos de implementación.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si su entorno cumple los requisitos previos y está familiarizado con el uso de plantillas de Resource Manager, seleccione el botón **Implementar en Azure**. La plantilla se abrirá en Azure Portal.

[![Implementación en Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.automation%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="review-the-template"></a>Revisión de la plantilla

En esta plantilla de ejemplo se realiza lo siguiente:

* Automatiza la creación de un área de trabajo de Log Analytics en Azure Monitor.
* Automatiza la creación de una cuenta de Azure Automation.
* El vínculo de la cuenta de Automation al área de trabajo de Log Analytics.
* Agrega runbooks de Automation de ejemplo a la cuenta.

>[!NOTE]
>No se admite la creación de una cuenta de ejecución de Automation cuando se usa una plantilla de ARM. Para crear una cuenta de ejecución manualmente desde el portal o mediante PowerShell, consulte cómo [crear una cuenta de ejecución](create-run-as-account.md).

Después de completar estos pasos, debe [Configurar las opciones de diagnóstico](automation-manage-send-joblogs-log-analytics.md) para que la cuenta de Automation envíe el estado del trabajo de runbook y los flujos de trabajo al área de trabajo de Log Analytics vinculada.

La plantilla usada en este inicio rápido forma parte de las [plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.automation/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>Versiones de API

En la tabla siguiente se muestra la versión de API de los recursos usados en este ejemplo.

| Resource | Tipo de recurso | Versión de API |
|:---|:---|:---|
| [Área de trabajo](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 01-03-2020 versión preliminar |
| [Cuenta de Automation](/azure/templates/microsoft.automation/automationaccounts) | automation | 2020-01-13-preview |
| [Servicios vinculados del área de trabajo](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | workspaces | 01-03-2020 versión preliminar |

### <a name="before-you-use-the-template"></a>Antes de usar la plantilla

La plantilla de parámetros JSON está configurada para que especifique:

* El nombre del área de trabajo.
* La región en la que crear un área de trabajo.
* El nombre de la cuenta de Automation.
* La región en la que crear la cuenta de Automation.

Los siguientes parámetros de la plantilla se configuran con un valor predeterminado para el área de trabajo de Log Analytics:

* *SKU* el valor predeterminado es el plan de tarifa por GB publicado en el modelo de precios de abril de 2018.
* *dataRetention* tiene como valor predeterminado 30 días.

>[!WARNING]
>Si quiere crear o configurar un área de trabajo de Log Analytics en una suscripción que ha elegido el modelo de precios de abril de 2018, el único plan de tarifa válido de Log Analytics es *PerGB2018*.
>

La plantilla JSON especifica un valor predeterminado para el resto de parámetros que es probable que se utilice como configuración estándar en el entorno. Puede almacenar la plantilla en una cuenta de Azure Storage para el acceso compartido en la organización. Para más información sobre el trabajo con plantillas, consulte [Implementación de recursos con plantillas de ARM y la CLI de Azure](../azure-resource-manager/templates/deploy-cli.md).

Si no está familiarizado con Azure Automation y Azure Monitor, es importante que comprenda los siguientes detalles de configuración. Pueden ayudarle a evitar errores al intentar crear, configurar y usar un área de trabajo de Log Analytics vinculada a la nueva cuenta de Automation.

* Revise los [detalles adicionales](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) para conocer totalmente las opciones de configuración del área de trabajo, como el modo de control de acceso, el plan de tarifa, la retención y el nivel de reserva de capacidad.

* Revise las [asignaciones de área de trabajo](how-to/region-mappings.md) para especificar las regiones admitidas en línea o en un archivo de parámetros. Solo en determinadas regiones se puede vincular un área de trabajo de Log Analytics y una cuenta de Automation en la suscripción.

* Si no está familiarizado con Azure Monitor registros y aún no ha implementado un área de trabajo, debe revisar la [guía de diseño del área de trabajo](../azure-monitor/logs/design-logs-deployment.md). Le ayudará a obtener información sobre el control de acceso y a comprender las estrategias de implementación de diseño que recomendamos para su organización.

## <a name="deploy-the-template"></a>Implementación de la plantilla

1. Seleccione la imagen siguiente para iniciar sesión en Azure y abrir una plantilla. La plantilla crea una cuenta de Azure Automation, un área de trabajo de Log Analytics y vincula la cuenta de Automation al área de trabajo.

    [![Implementación en Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.automation%2F101-automation%2Fazuredeploy.json)

2. Escriba los valores.

    Cuando intenta ejecutar la plantilla de ARM desde PowerShell, la CLI o la característica Plantillas del portal, si el parámetro `_artifactsLocation` no está configurado correctamente, recibirá un mensaje de error parecido al siguiente:

    `"message": "Deployment template validation failed: 'The template resource '_artifactsLocation' at line '96' and column '31' is not valid: The language expression property 'templateLink' doesn't exist, available properties are 'template, templateHash, parameters, mode, debugSetting, provisioningState'.. Please see https://aka.ms/arm-template-expressions for usage details.'."`

    Para evitarlo, cuando se ejecute desde la característica Plantillas del portal, especifique lo siguiente para el parámetro `_artifactsLocation`: `https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.

    Si se ejecuta desde PowerShell, incluya el parámetro y su valor `-TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.

    Si se ejecuta desde PowerShell, incluya el parámetro y su valor `--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.

    Para obtener más referencias acerca del uso de PowerShell o la CLI, consulte [Creación de una cuenta de Azure Automation (microsoft.com)](https://azure.microsoft.com/resources/templates/101-automation/) en la sección **Uso de la plantilla**.

3. La implementación puede tardar unos minutos en finalizar. Cuando se completa, la salida es similar a la siguiente:

    ![Resultado de ejemplo cuando se completa la implementación](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Revisión de los recursos implementados

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En Azure Portal, abra la cuenta de Automation que acaba de crear.

3. En el panel izquierdo, seleccione **Runbooks**. En la página **Runbooks**, se muestran tres runbooks de tutorial creados con la cuenta de Automation.

    ![Runbooks de tutorial creados con la cuenta de Automation](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. En el panel izquierdo, seleccione **Área de trabajo vinculada**. En la página **Área de trabajo vinculada**, se muestra el área de trabajo de Log Analytics que especificó anteriormente como vinculada a la cuenta de Automation.

    ![Cuenta de Automation vinculada al área de trabajo Log Analytics](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no las necesite, desvincule la cuenta de Automation del área de trabajo de Log Analytics y, a continuación, elimine la cuenta de Automation y el área de trabajo.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una cuenta de Automation y un área de trabajo de Log Analytics y las ha vinculado.

Para más información, continúe con los tutoriales de Azure Automation.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Automation](learn/automation-tutorial-runbook-graphical.md)
