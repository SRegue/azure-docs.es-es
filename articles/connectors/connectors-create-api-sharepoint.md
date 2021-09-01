---
title: Conexión a SharePoint
description: Supervisión y administración de recursos en SharePoint Online o SharePoint Server en el entorno local con Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: article
ms.date: 08/11/2021
tags: connectors
ms.openlocfilehash: e53fca8b0e9f8dbca3dba8bd684e71ef25c88738
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722524"
---
# <a name="connect-to-sharepoint-resources-using-azure-logic-apps"></a>Conexión a recursos de SharePoint mediante Azure Logic Apps

Para automatizar las tareas que supervisan y administran recursos, como archivos, carpetas, listas y artículos, en SharePoint Online o en SharePoint Server local, puede crear flujos de trabajo de integración automatizados con Azure Logic Apps y el conector de SharePoint.

En la lista siguiente se describen tareas de ejemplo que puede automatizar:

* Supervisar cuándo se crean, se cambian o se eliminan archivos o elementos.
* Crear, obtener, actualizar o eliminar elementos.
* Agregar, obtener o eliminar datos adjuntos. Obtener el contenido de los datos adjuntos.
* Crear, copiar, actualizar o eliminar archivos. 
* Actualizar propiedades de archivo. Obtener el contenido, los metadatos o las propiedades de un archivo.
* Enumerar o extraer carpetas.
* Obtener listas o vistas de lista.
* Definir el estado de aprobación del contenido.
* Resolver personas.
* Enviar solicitudes HTTP a SharePoint.
* Obtener valores de entidad.

En el flujo de trabajo de la aplicación lógica, puede usar un desencadenador que supervise los eventos en SharePoint y hacer que la salida esté disponible para otras acciones. Luego, puede usar acciones para realizar diversas tareas en SharePoint. También puede incluir otras acciones que usen la salida de las acciones de SharePoint. Por ejemplo, si recupera regularmente archivos de SharePoint, puede enviar por correo electrónico alertas sobre esos archivos y su contenido mediante el conector Office 365 Outlook o el conector Outlook.com. Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](../logic-apps/logic-apps-overview.md) O bien, pruebe este [inicio rápido para crear su primer flujo de trabajo de aplicación lógica de ejemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Requisitos previos

* Las credenciales de la cuenta de Microsoft Office 365 que usa con SharePoint en las que inicia sesión con una [cuenta profesional o educativa](https://support.microsoft.com/office/what-account-to-use-with-office-and-you-need-one-914e6610-2763-47ac-ab36-602a81068235#bkmk_msavsworkschool).

  Necesita estas credenciales para poder autorizar el acceso del flujo de trabajo a la cuenta de SharePoint.

  > [!NOTE]
  > Si usa [Microsoft Azure operado por 21Vianet](https://portal.azure.cn), la autenticación de Azure Active Directory (Azure AD) solo funcionará con una cuenta de Microsoft Office 365 operada por 21Vianet (.cn), no con cuentas .com.

* Su dirección del sitio de SharePoint

* Una cuenta y una suscripción de Azure. Si no tiene una suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* En el caso de las conexiones a un servidor de SharePoint local, debe [instalar y configurar la puerta de enlace de datos local](../logic-apps/logic-apps-gateway-install.md) en un equipo local y un [recurso de puerta de enlace de datos que ya se haya creado en Azure](../logic-apps/logic-apps-gateway-connection.md).

  Luego, puede seleccionar el recurso de puerta de enlace que se va a usar al crear la conexión de SharePoint Server desde el flujo de trabajo.

* Flujo de trabajo de la aplicación lógica en el que necesita acceder al sitio o servidor de SharePoint.

  * Para iniciar el flujo de trabajo con un desencadenador de SharePoint, necesita un flujo de trabajo de aplicación lógica en blanco.
  * Para agregar una acción de SharePoint, el flujo de trabajo ya debe tener un desencadenador.

## <a name="connector-reference"></a>Referencia de conectores

Si necesita más detalles técnicos sobre este conector, como los desencadenadores, las acciones y los límites que se describen en el archivo de Swagger del conector, revise la [página de referencia del conector](/connectors/sharepoint/).

## <a name="connect-to-sharepoint"></a>Conexión a SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

## <a name="add-a-trigger"></a>Incorporación de un desencadenador

1. Desde Azure Portal, Visual Studio Code o Visual Studio, abra el flujo de la aplicación lógica en el diseñador visual, si aún no está abierto.

1. En el cuadro de búsqueda del diseñador, escriba `sharepoint` como término de búsqueda. Seleccione el conector **SharePoint**.

1. En la lista **Desencadenadores**, seleccione el desencadenador que quiera usar.

1. Cuando se le pida que inicie sesión y cree una conexión, elija una de las siguientes opciones:

   * En SharePoint Online, seleccione **Iniciar sesión** y autentique las credenciales de usuario.
   * En SharePoint Server, seleccione **Conectarse mediante una puerta de enlace de datos local**. Proporcione la información que se solicita sobre el recurso de puerta de enlace que se usará, el tipo de autenticación y otros detalles necesarios.

1. Seleccione **Crear** cuando haya terminado.

   Una vez que el flujo de trabajo crea correctamente la conexión, aparece el desencadenador seleccionado.

1. Proporcione la información para configurar el desencadenador y continuar con la creación del flujo de trabajo.

## <a name="add-an-action"></a>Agregar una acción

1. Desde Azure Portal, Visual Studio Code o Visual Studio, abra el flujo de la aplicación lógica en el diseñador visual, si aún no está abierto.

1. Elija una de las siguientes opciones:

   * Para agregar una acción en el último paso, elija **Nuevo paso**.
   * Para agregar una acción entre un paso y otro, mueva el puntero por encima de la flecha entre ellos. Haga clic en el signo más ( **+** ) y, luego, seleccione **Agregar una acción**.

1. En **Elegir una operación**, en el cuadro de búsqueda, escriba `sharepoint` como término de búsqueda. Seleccione el conector **SharePoint**.

1. En la lista **Acciones**, seleccione la acción que quiera usar.

1. Cuando se le pida que inicie sesión y cree una conexión, elija una de las siguientes opciones:

   * En SharePoint Online, seleccione **Iniciar sesión** y autentique las credenciales de usuario.
   * En SharePoint Server, seleccione **Conectarse mediante una puerta de enlace de datos local**. Proporcione la información que se solicita sobre el recurso de puerta de enlace que se usará, el tipo de autenticación y otros detalles necesarios.

1. Seleccione **Crear** cuando haya terminado.

   Una vez que el flujo de trabajo crea correctamente la conexión, aparece la acción seleccionada.

1. Proporcione la información para configurar la acción y continúe con la creación del flujo de trabajo.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre otros [conectores de Logic Apps](../connectors/apis-list.md)