---
title: Integración con Logic Apps
titleSuffix: Azure Digital Twins
description: Obtenga información sobre cómo conectar Logic Apps a Azure Digital Twins mediante un conector personalizado.
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: ee6ebf8c115a76b19badb7d08409493b87d5b02e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110078893"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Integración con Logic Apps mediante un conector personalizado

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) es un servicio en la nube que le ayuda a automatizar flujos de trabajo entre aplicaciones y servicios. Al conectar Logic Apps a las API de Azure Digital Twins, puede crear esos flujos automatizados en torno a Azure Digital Twins y sus datos.

Azure Digital Twins actualmente no tiene un conector certificado (precompilado) para Logic Apps. En su lugar, el proceso actual para usar Logic Apps con Azure Digital Twins es crear un [conector personalizado de Logic Apps](../logic-apps/custom-connector-overview.md) mediante una instancia [personalizada de Swagger de Azure Digital Twins](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) que se ha modificado para funcionar con Logic Apps.

> [!NOTE]
> Hay varias versiones de Swagger incluidas en el ejemplo personalizado de Swagger vinculado anteriormente. La versión más reciente se encontrará en la subcarpeta con la fecha más reciente, pero también se admiten las versiones anteriores incluidas en el ejemplo.

En este artículo, usará [Azure Portal](https://portal.azure.com) para **crear un conector personalizado** que se puede usar para conectar Logic Apps a una instancia de Azure Digital Twins. A continuación, **creará una aplicación lógica** que usa esta conexión para un escenario de ejemplo, en el que los eventos desencadenados por un temporizador actualizarán automáticamente un gemelo de la instancia de Azure Digital Twins. 

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
Inicie sesión en [Azure Portal](https://portal.azure.com) con esta cuenta. 

También debe completar los siguientes elementos como parte de la configuración de requisitos previos. El recordatorio de esta sección le guiará a través de este proceso:
- Configurará una instancia de Azure Digital Twins.
- Adición de un gemelo digital

### <a name="set-up-azure-digital-twins-instance"></a>Configuración de la instancia de Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="add-a-digital-twin"></a>Adición de un gemelo digital

En este artículo se usa Logic Apps para actualizar un gemelo de la instancia de Azure Digital Twins. Para continuar, debe agregar al menos un gemelo a la instancia. 

Puede agregar gemelos mediante las [API de Digital Twins](/rest/api/digital-twins/dataplane/twins), el [SDK de .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) o la [CLI de Azure Digital Twins](concepts-cli.md). Para obtener pasos detallados sobre cómo crear gemelos con estos métodos, consulte [Procedimiento: Administración de Digital Twins](how-to-manage-twin.md).

Necesitará el **_Identificador de gemelo_** de un gemelo de la instancia que ha creado.

## <a name="set-up-app-registration"></a>Configuración del registro de aplicaciones

[!INCLUDE [digital-twins-prereq-registration.md](../../includes/digital-twins-prereq-registration.md)]

### <a name="get-app-registration-client-secret"></a>Obtención del secreto de cliente del registro de la aplicación

También tendrá que crear un **_secreto de cliente_** para el registro de la aplicación de Azure AD. Para ello, vaya a la página [Registros de aplicaciones](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) de Azure Portal (puede usar este vínculo o buscarlo en la barra de búsqueda del portal). Seleccione el registro que creó en la sección anterior de la lista para abrir los detalles. 

Seleccione *Certificados y secretos* en el menú del registro y, después, *+ Nuevo secreto de cliente*.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Vista del portal de un registro de aplicaciones de Azure AD. Hay un resaltado alrededor de &quot;Certificados y secretos&quot; en el menú de recursos y un resaltado en la página alrededor de &quot;Nuevo secreto de cliente&quot;.":::

Escriba los valores que desee para *Descripción* y *Expiración* y seleccione *Agregar*.

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="Agregar secreto de cliente":::

Ahora, compruebe que el secreto de cliente está visible en la página _Certificados y secretos_ con los campos _Expiración_ y _Valor_. Tome nota de su _Valor_ para usarlo más adelante (también puede copiarlo en el Portapapeles con el icono de copia).

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="Copia del valor del secreto de cliente":::

## <a name="create-custom-logic-apps-connector"></a>Creación de un conector personalizado de Logic Apps

Ahora, va a crear un [conector personalizado de Logic Apps](../logic-apps/custom-connector-overview.md) para las API de Azure Digital Twins. Después de hacerlo, podrá enlazar Azure Digital Twins al crear una aplicación lógica en la sección siguiente.

Vaya a la página [Conector personalizado de Logic Apps](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) de Azure Portal (puede usar este vínculo o buscarlo en la barra de búsqueda del portal). Seleccione *+Agregar*.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Página &quot;Conector personalizado de Logic Apps&quot; de Azure Portal. Hay un resaltado alrededor del botón &quot;Agregar&quot;.":::

En la página *Crear conector personalizado de Logic Apps* que se muestra a continuación, seleccione la suscripción y el grupo de recursos y un nombre y una ubicación de implementación para el nuevo conector. Seleccione *Revisar + crear*. 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Página &quot;Crear conector personalizado de Logic Apps&quot; de Azure Portal.":::

Esto le llevará a la pestaña *Revisar y crear*, donde podrá seleccionar *Crear* en la parte inferior para crear el recurso.

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Pestaña &quot;Revisar y crear&quot; de la página &quot;Revisar conector personalizado de Logic Apps&quot; de Azure Portal. Hay un resaltado alrededor del botón &quot;Crear&quot;.":::

Se le dirigirá a la página de implementación del conector. Cuando haya terminado la implementación, seleccione el botón *Ir al recurso* para ver los detalles del conector en el portal.

### <a name="configure-connector-for-azure-digital-twins"></a>Configuración del conector para Azure Digital Twins

A continuación, va a configurar el conector que ha creado para la conexión con Azure Digital Twins.

En primer lugar, descargue un archivo de Swagger de Azure Digital Twins personalizado que se ha modificado para que funcione con Logic Apps. Descargue el ejemplo [Swaggers personalizados de Azure Digital Twins (conector de Logic Apps)](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) mediante el botón *Descargar archivo ZIP*. Vaya a la carpeta descargada *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip* y descomprímala. 

La instancia de Swagger personalizada para este tutorial se encuentra en la carpeta ***Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps**_. Esta carpeta contiene subcarpetas denominadas _Estable* y *Versión preliminar*, y ambas contienen versiones diferentes de Swagger organizadas por fecha. La carpeta con la fecha más reciente incluirá la última copia de Swagger. Sea cual sea la versión que seleccione, el archivo Swagger se denomina _** digitaltwins.json**_.

> [!NOTE]
> A menos que esté trabajando con una característica en vista previa, por lo general se recomienda usar la versión más reciente de *Estable* de Swagger. Sin embargo, las versiones anteriores y las versiones preliminares de Swagger siguen siendo compatibles. 

A continuación, diríjase a la página de información general del conector en [Azure Portal](https://portal.azure.com) y seleccione *Editar*.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Página de información general del conector creado en el paso anterior. Hay un resaltado alrededor del botón &quot;Editar&quot;.":::

En la página *Editar conector personalizado de Logic Apps* que aparece a continuación, configure esta información:
* **Conectores personalizados**
    - Punto de conexión de API: REST (dejar el valor predeterminado)
    - Modo de importación: archivo OpenAPI (dejar el valor predeterminado)
    - Archivo: archivo de Swagger personalizado que descargó anteriormente. Seleccione *Importar*, busque el archivo en la máquina (*Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps\....\digitaltwins.json*) y elija *Abrir*.
* **Información general**
    - Icono: cargue el icono que desee.
    - Color de fondo del icono: escriba código hexadecimal con el formato "#xxxxxx" para el color.
    - Descripción: rellene los valores que desee.
    - Esquema: HTTPS (dejar el valor predeterminado)
    - Host: *nombre de host* de la instancia de Azure Digital Twins.
    - URL base: / (dejar el valor predeterminado)

A continuación, seleccione el botón *Seguridad* situado en la parte inferior de la ventana para continuar con el siguiente paso de configuración.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Captura de pantalla de la parte inferior de la página &quot;Editar conector personalizado de Logic Apps&quot;. Hay un resaltado alrededor del botón para continuar con la sección Seguridad.":::

En el paso Seguridad, seleccione *Editar* y configure esta información:
* **Tipo de autenticación**: OAuth 2.0
* **OAuth 2.0**:
    - Proveedor de identidades: Azure Active Directory
    - Identificador de cliente: *identificador de la aplicación (cliente)* del registro de la aplicación de Azure AD
    - Secreto del cliente: *secreto de cliente* creado en la sección [Requisitos previos](#prerequisites) para el registro de la aplicación de Azure AD
    - Dirección URL de inicio de sesión: https://login.windows.net (dejar el valor predeterminado)
    - Identificador de inquilino: *identificador de directorio (inquilino)* del registro de aplicación de Azure AD
    - Dirección URL del recurso: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Ámbito: Directory.AccessAsUser.All
    - URL de redireccionamiento: (dejar el valor predeterminado por ahora)

Observe que el campo dirección URL de redireccionamiento indica *Guardar el conector personalizado para generar la URL de redireccionamiento*. Para hacerlo ahora, seleccione *Actualizar conector* en la parte superior del panel para confirmar la configuración del conector.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Captura de pantalla de la parte superior de la página &quot;Editar conector personalizado de Logic Apps&quot;. Hay un resaltado alrededor del botón &quot;Actualizar conector&quot;.":::

<!-- Success message? didn't see one -->

Vuelva al campo dirección URL de redireccionamiento y copie el valor que se ha generado. Lo usará en el paso siguiente.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="El campo dirección URL de redireccionamiento de la página &quot;Editar conector personalizado de Logic Apps&quot; tiene ahora el valor &quot;https://logic-apis-westus2.consent.azure-apim.net/redirect&quot;. El botón para copiar el valor está resaltado.":::

Esta es toda la información necesaria para crear el conector (no es necesario continuar del paso de seguridad al paso de definición). Puede cerrar el panel *Editar conector personalizado de Logic Apps*.

>[!NOTE]
>De nuevo en la página de información general del conector donde seleccionó *Editar* originalmente, tenga en cuenta que al seleccionar *Editar* de nuevo se reiniciará todo el proceso de especificación de las opciones de configuración. No se rellenarán los valores desde la última vez que entró, por lo que si desea guardar una configuración actualizada con cualquier valor cambiado, debe volver a escribir todos los demás valores para evitar que los valores predeterminados los sobrescriban.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Concesión de permisos al conector en la aplicación de Azure AD

A continuación, use el valor de la *dirección URL de redireccionamiento* del conector personalizado que copió en el último paso para conceder los permisos al conector en el registro de la aplicación de Azure AD.

Vaya a la página [Registros de aplicaciones](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) de Azure Portal y seleccione el registro en la lista. 

Seleccione *Autenticación* en el menú del registro y agregue un identificador URI.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Página de autenticación del registro de la aplicación en Azure Portal. &quot;Autenticación&quot; en el menú está resaltado y, en la página, el botón &quot;Agregar identificador URI&quot; está resaltado."::: 

Escriba la *dirección URL de redireccionamiento* del conector personalizado en el campo nuevo y seleccione el icono *Guardar*.

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Página de autenticación del registro de la aplicación en Azure Portal. La nueva dirección URL de redireccionamiento y el botón &quot;Guardar&quot; de la página están resaltados.":::

Ya ha terminado de configurar un conector personalizado que puede acceder a las API de Azure Digital Twins. 

## <a name="create-logic-app"></a>Creación de la aplicación lógica

A continuación, va a crear una aplicación lógica que usará el nuevo conector para automatizar las actualizaciones de Azure Digital Twins.

En [Azure Portal](https://portal.azure.com), busque *Aplicaciones lógicas* en la barra de búsqueda del portal. Cuando lo seleccione, irá a la página de *Aplicaciones lógicas*. Seleccione el botón *Crear aplicación lógica* para crear una nueva aplicación lógica.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Página &quot;Logic Apps&quot; en Azure Portal. Seleccione el botón &quot;Agregar&quot;":::

En la página *Aplicación lógica* que aparece, escriba su suscripción y grupo de recursos. Asimismo, elija un nombre para la aplicación lógica y seleccione la ubicación de implementación.

Seleccione el botón _Revisar y crear_.

Esto le llevará a la pestaña *Revisar y crear*, donde podrá revisar los detalles y seleccionar *Crear* en la parte inferior para crear el recurso.

Se le dirigirá a la página de implementación de la aplicación lógica. Cuando haya terminado la implementación, seleccione el botón *Ir al recurso* para continuar con el *Diseñador de aplicaciones lógicas*, donde rellenará la lógica del flujo de trabajo.

### <a name="design-workflow"></a>Diseño del flujo de trabajo

En *Diseñador de aplicaciones lógicas*, en *Empezar con un desencadenador común*, seleccione _**Repetición**_.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Página &quot;Diseñador de aplicaciones lógicas&quot; en Azure Portal. Hay un resaltado alrededor del desencadenador común &quot;Repetición&quot;.":::

En la página *Diseñador de aplicaciones lógicas* que aparece a continuación, cambie la frecuencia de **Repetición** a *Segundo*, de modo que el evento se desencadene cada 3 segundos. Esto hará que sea más fácil ver los resultados más adelante sin tener que esperar mucho tiempo.

Seleccione *+ New step*(+ Nuevo paso).

Esto abrirá el cuadro *Elegir una acción*. Cambie a la pestaña *Personalizada*. Debería ver el conector personalizado anterior en el cuadro superior.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Crear un flujo en el Diseñador de aplicaciones lógicas en Azure Portal. En el cuadro &quot;Elegir una acción&quot;,está seleccionada la pestaña &quot;Personalizada&quot;. El conector personalizado anterior del usuario se muestra en el cuadro, con un resaltado alrededor.":::

Selecciónelo para mostrar la lista de las API contenidas en ese conector. Use la barra de búsqueda o desplácese por la lista para seleccionar **DigitalTwins_Add**. (Esta es la API que se usa en este artículo, pero también puede seleccionar cualquier otra API como una opción válida para una conexión de Logic Apps).

Es posible que se le pida que inicie sesión con sus credenciales de Azure para conectarse al conector. Si aparece el cuadro de diálogo *Permisos solicitados*, siga las indicaciones para conceder consentimiento para la aplicación y acepte.

En el nuevo cuadro *DigitalTwinsAdd*, rellene los campos como se indica a continuación:
* _id_: rellene el *Identificador de gemelo* del gemelo digital de la instancia que desea que la aplicación lógica actualice.
* _twin_: en este campo especificará el cuerpo que requiere la solicitud de API seleccionada. En el caso de *DigitalTwinsUpdate*, el cuerpo tiene el formato de código de revisión JSON. Para más información sobre cómo estructurar una revisión JSON para actualizar el gemelo, consulte la sección [Actualización de un gemelo digital](how-to-manage-twin.md#update-a-digital-twin) de *Procedimiento: Administración de Digital Twins*.
* _api-version_: La versión de API más reciente. Actualmente, este valor es *2020-10-31*.

Seleccione *Guardar* en el Diseñador de aplicaciones lógicas.

Puede elegir otras operaciones seleccionando _+ Nuevo paso_ en la misma ventana.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Vista finalizada de la aplicación en el conector de Logic apps. El cuadro DigitalTwinsAdd se rellena con los valores descritos anteriormente, incluido un cuerpo de revisión JSON de ejemplo. El botón &quot;Guardar&quot; de la ventana está resaltado.":::

## <a name="query-twin-to-see-the-update"></a>Consulta del gemelo para ver la actualización

Ahora que se ha creado la aplicación lógica, el evento de actualización del gemelo que definió en el Diseñador de aplicaciones lógicas debe aparecer con una periodicidad de cada tres segundos. Esto significa que, después de tres segundos, debería poder consultar el gemelo y ver reflejados los nuevos valores de revisión.

Puede consultar el gemelo mediante el método que elija (como una [aplicación cliente personalizada](tutorial-command-line-app.md), la [aplicación de ejemplo Explorador de Azure Digital Twins](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), los [SDK y las API](concepts-apis-sdks.md) o la [CLI](concepts-cli.md)). 

Para más información sobre cómo consultar la instancia de Azure Digital Twins, consulte [Procedimiento: Consulta del grafo de gemelos](how-to-query-graph.md).

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha creado una aplicación lógica que actualiza periódicamente un gemelo en la instancia de Azure Digital Twins con una revisión que ha proporcionado. Puede probar a seleccionar otras API en el conector personalizado para crear aplicaciones lógicas para varias acciones en la instancia.

Para más información sobre las operaciones de API disponibles y los detalles que requieren, visite [Conceptos: API y SDK de Azure Digital Twins](concepts-apis-sdks.md).
