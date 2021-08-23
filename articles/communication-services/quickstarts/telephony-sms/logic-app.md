---
title: 'Guía de inicio rápido: Envío de mensajes SMS en Azure Logic Apps mediante Azure Communication Services'
titleSuffix: An Azure Communication Services quickstart
description: En esta guía de inicio rápido, aprenderá a enviar mensajes SMS en flujos de trabajo de Azure Logic Apps mediante el conector de Azure Communication Services.
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 2764eede3aebd0110e2cc2de8a1813bc1f28cb86
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444352"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>Inicio rápido: Envío de mensajes SMS en Azure Logic Apps mediante Azure Communication Services

Con el conector [SMS de Azure Communication Services](../../overview.md) y [Azure Logic Apps](../../../logic-apps/logic-apps-overview.md), puede crear flujos de trabajo automatizados que pueden enviar mensajes SMS. En esta guía de inicio rápido se muestra cómo enviar mensajes de texto automáticamente en respuesta a un evento desencadenador, que es el primer paso de un flujo de trabajo de aplicación lógica. Un evento desencadenador puede ser un mensaje de correo electrónico entrante, una programación periódica, un evento de un recurso de [Azure Event Grid](../../../event-grid/overview.md) o cualquier otro desencadenador [admitido por Azure Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors).

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="Captura de pantalla que muestra Azure Portal, con el diseñador de aplicaciones lógicas abierto, con una aplicación lógica de ejemplo que usa la acción Enviar SMS del conector de Azure Communication Services.":::

Aunque esta guía de inicio rápido se centra en el uso del conector para responder a un desencadenador, también puede usar el conector para responder a otras acciones, que son los pasos que siguen al desencadenador en un flujo de trabajo. Si no está familiarizado con las aplicaciones lógicas, consulte [Introducción: ¿Qué es Azure Logic Apps?](../../../logic-apps/logic-apps-overview.md) antes de comenzar.

> [!NOTE]
> Este inicio rápido supone un pequeño costo en su cuenta de Azure.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa o también puede [crear una cuenta de Azure de forma gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Un recurso de Azure Communication Services activo o también puede [crear un recurso de Communication Services](../create-communication-resource.md).

- Un recurso de Logic Apps activo (aplicación lógica) o también puede [crear una aplicación lógica en blanco, pero con el desencadenador que quiere usar](../../../logic-apps/quickstart-create-first-logic-app-workflow.md). Actualmente, el conector SMS de Azure Communication Services solo proporciona acciones, por lo que la aplicación lógica requiere un desencadenador, como mínimo.

  En esta guía de inicio rápido se usa el desencadenador **Cuando llega un nuevo correo electrónico**, que está disponible con el conector de [Outlook de Office 365](/connectors/office365/).

- Un número de teléfono habilitado para SMS o también puede [obtener un número de teléfono](./get-phone-number.md).

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

## <a name="add-an-sms-action"></a>Adición de una acción de SMS

Para agregar la acción **Enviar SMS** como un nuevo paso en el flujo de trabajo mediante el conector SMS de Azure Communication Services, siga estos pasos en [Azure Portal](https://portal.azure.com) con el flujo de trabajo de la aplicación lógica abierto en el diseñador de aplicaciones lógicas:

1. En el diseñador, en el paso en el que desea agregar la nueva acción, seleccione **Nuevo paso**. Como alternativa, para agregar la nueva acción entre pasos, mueva el puntero sobre la flecha entre esos pasos, seleccione el signo más ( **+** ) y seleccione **Agregar una acción**.

1. En el cuadro de búsqueda **Elegir una operación**, escriba `Azure Communication Services`. En la lista de acciones, seleccione **Enviar SMS**.

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="Captura de pantalla que muestra el diseñador de aplicaciones lógicas y el conector de Azure Communication Services con la acción Enviar SMS seleccionada.":::

1. A continuación, cree una conexión al recurso de Communication Services.
    1. Dentro de la misma suscripción:

       1. Proporcione un nombre para la conexión.

       1. Seleccione el recurso de Azure Communication Services.

       1. Seleccione **Crear**.

       :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="Captura de pantalla que muestra la configuración de la acción Enviar SMS con información de ejemplo.":::

    1. Utilizando la cadena de conexión del recurso de Communication Services:
        
        1. Proporcione un nombre para la conexión.
        
        1. Seleccione ConnectionString Authentication (Autenticación de ConnectionString) en las opciones desplegables.
        
        1. Escriba la cadena de conexión del recurso de Communication Services.
        
        1. Seleccione **Crear**.
        
        :::image type="content" source="./media/logic-app/connection-string-auth.png" alt-text="Captura de pantalla que muestra la configuración de la autenticación de la cadena de conexión.":::
        
    1. Mediante la entidad de servicio ([consulte el artículo sobre la creación de entidades de servicio](../identity/service-principal-from-cli.md)):
        1. Proporcione un nombre para la conexión.
        
        1. Seleccione Service principal (Azure AD application) Authentication [Autenticación de entidad de servicio (aplicación de Azure AD)] en las opciones desplegables.
        
        1. Escriba el identificador del inquilino, el identificador del cliente y el secreto del cliente de la entidad de servicio.
        
        1. Escriba el valor Dirección URL del punto de conexión de Communication Services de su recurso de Communication Services.
        
        1. Seleccione **Crear**.
        
        :::image type="content" source="./media/logic-app/service-principal-auth.png" alt-text="Captura de pantalla que muestra la configuración de autenticación de entidad de servicio.":::     

1. En **Enviar SMS**, proporcione la siguiente información: 

   * Los números de teléfono de origen y de destino. Con fines de prueba, puede usar su propio número de teléfono como el número de teléfono de destino.

   * El contenido del mensaje que desea enviar, por ejemplo, "¡Hola desde Logic Apps!".

   A continuación se muestra una acción **Enviar SMS** con información de ejemplo:

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="Captura de pantalla que muestra la acción Enviar SMS con información de ejemplo.":::

1. Cuando esté listo, seleccione **Guardar** en la barra de herramientas del diseñador.

A continuación, ejecute el flujo de trabajo de la aplicación lógica para las pruebas.

## <a name="test-your-logic-app"></a>Comprobación de la aplicación lógica

Para iniciar manualmente el flujo de trabajo, seleccione **Ejecutar** en la barra de herramientas del diseñador. O bien, puede esperar a que se active el desencadenador. En ambos casos, el flujo de trabajo debe enviar un mensaje SMS al número de teléfono de destino especificado. Para más información, revise [cómo ejecutar el flujo de trabajo](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-workflow).

## <a name="clean-up-resources"></a>Limpieza de recursos

Para eliminar una suscripción de Communication Services, elimine el recurso o el grupo de recursos de Communication Services. Al eliminar el grupo de recursos también se eliminan los demás recursos de ese grupo. Para más información, consulte [Limpieza de recursos](../create-communication-resource.md#clean-up-resources).

Para limpiar el flujo de trabajo de la aplicación lógica y los recursos relacionados, consulte [Limpieza de recursos](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources).

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido a enviar mensajes SMS mediante Azure Logic Apps y Azure Communication Services. Para más información, continúe con la suscripción a eventos SMS:

> [!div class="nextstepaction"]
> [Suscripción a eventos SMS](./handle-sms-events.md)

Para más información sobre los mensajes SMS en Azure Communication Services, consulte estos artículos:

- [Conceptos de SMS](../../concepts/telephony-sms/concepts.md)
- [Tipos de número de teléfono](../../concepts/telephony-sms/plan-solution.md)
- [Información general de la biblioteca cliente de SMS](../../concepts/telephony-sms/sdk-features.md)
