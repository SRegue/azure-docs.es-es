---
title: 'Tutorial: configuración de Bonusly para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Obtenga información sobre cómo configurar Azure Active Directory para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Bonusly.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: bf14a381f58b484fd45060ed354b418d3f94d195
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113764677"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Tutorial: configuración de Bonusly para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es mostrar los pasos que se realizan en Bonusly y Azure Active Directory (Azure AD) para configurar Azure AD con el objetivo de aprovisionar y desaprovisionar automáticamente usuarios o grupos en Bonusly.

> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Requisitos previos

En el escenario que se describe en este tutorial se supone que ya cuenta con los elementos siguientes:

* Un inquilino de Azure AD
* Un [inquilino de Bonusly](https://bonus.ly/pricing)
* Una cuenta de usuario de Bonusly con permisos de administrador

> [!NOTE]
> El aprovisionamiento de integración de Azure AD se basa en la [API REST de Bonusly](https://konghq.com/solutions/gateway/) que está disponible para desarrolladores de Bonusly.

## <a name="adding-bonusly-from-the-gallery"></a>Adición de Bonusly desde la galería

Antes de configurar Bonusly para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar Bonusly desde la galería de aplicaciones de Azure AD hasta la lista de aplicaciones SaaS administradas.

**Para agregar Bonusly desde la galería de aplicaciones de Azure AD, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)** , haga clic en el icono de **Azure Active Directory**.

    ![Botón Azure Active Directory](common/select-azuread.png)

2. Vaya a **Aplicaciones empresariales** y seleccione la opción **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación](common/add-new-app.png)

4. En el cuadro de búsqueda, escriba **Bonusly**, seleccione **Bonusly** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Bonusly en la lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Asignación de usuarios a Bonusly

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han "asignado" a una aplicación en Azure AD. 

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a Bonusly. Una vez que lo decida, puede seguir estas instrucciones para asignar dichos usuarios o grupos a Bonusly:

* [Asignar un usuario o grupo a una aplicación empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Sugerencias importantes para asignar usuarios a Bonusly

* Se recomienda asignar un único usuario de Azure AD a Bonusly para probar la configuración de aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

* Al asignar un usuario a Bonusly, debe seleccionar un rol válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Configuración del aprovisionamiento automático de usuarios en Bonusly

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de AD Azure para crear, actualizar y deshabilitar usuarios o grupos en Bonusly en función de las asignaciones de grupos y usuarios de Azure AD.

> [!TIP]
> También puede optar por habilitar el inicio de sesión único basado en SAML para Bonusly siguiendo las instrucciones del [tutorial de inicio de sesión único de Bonusly](bonus-tutorial.md). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático de usuarios, aunque estas dos características se complementan entre sí.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Bonusly en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y seleccione **Aplicaciones empresariales**, **Todas las aplicaciones** y, a continuación, **Bonusly**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Bonusly**.

    ![Vínculo a Bonusly en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningTab.png" alt-text="Captura de pantalla de la pestaña de aprovisionamiento de Bonusly. En Administrar, el aprovisionamiento está resaltado." border="false":::

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png" alt-text="Captura de pantalla que muestra un cuadro de lista del modo de aprovisionamiento, con la opción Automático seleccionada y resaltada." border="false":::

5. En la sección **Credenciales de administrador**, escriba el **token secreto** de la cuenta de Bonusly, tal como se ha descrito en el paso 6.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/secrettoken.png" alt-text="Captura de pantalla de la sección de credenciales de administrador. El cuadro del token secreto está vacío, pero está resaltado." border="false":::

6. El **token secreto** para su cuenta de Bonusly está ubicado en **Administración > API > Configuración**. En la sección **If you want to code** (Si desea codificar), haga clic en **API > Create New API Access Token** (Crear nuevo token de acceso de API) para crear un nuevo token secreto.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png" alt-text="Captura de pantalla del menú de Bonusly. En Administrador, la opción Compañía está resaltada. En Empresa, las integraciones están resaltadas." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonsulyRestApi.png" alt-text="Captura de pantalla de la sección &quot;If you want to code&quot; (Si quiere codificar) del sitio de Bonusly, con la A P I resaltada." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/CreateToken.png" alt-text="Captura de pantalla del sitio de Bonusly. La pestaña de servicios está abierta. En los tokens de acceso a la A P I, la opción &quot;Create new A P I access token&quot; (Crear un nuevo token de acceso de A P I) está resaltada." border="false":::

7. En la siguiente pantalla, escriba un nombre para el token de acceso en el cuadro de texto proporcionado y presione **Create Api Key** (Crear clave de API). El nuevo token de acceso aparecerá durante unos segundos en una ventana emergente.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token01.png" alt-text="Captura de pantalla de la página &quot;New access token&quot; (Nuevo token de acceso) del sitio de Bonusly. Un cuadro sin etiqueta contiene la opción Mi token y el botón para crear una clave de A P I está resaltado." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token02.png" alt-text="Captura de pantalla del sitio de Bonusly. Aparece una notificación que muestra el nuevo token de acceso creado, seguido de un token que no se puede descifrar." border="false":::

8. Tras rellenar los campos que se muestran en el paso 5, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Bonusly. Si la conexión no se establece, asegúrese de que la cuenta de Bonusly tiene permisos de administrador y pruebe de nuevo.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/TestConnection.png" alt-text="Captura de pantalla de la sección Credenciales de administrador de Azure Portal. El botón de conexión de texto está resaltado." border="false":::

9. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/EmailNotification.png" alt-text="Captura de pantalla que muestra un cuadro de correo electrónico de notificación vacío. Se puede ver una opción que tiene la etiqueta Enviar una notificación por correo electrónico cuando se produce un error." border="false":::

10. Haga clic en **Save**(Guardar).

11. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Bonusly** (Sincronizar usuarios de Azure Active Directory con Bonusly).

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserMappings.png" alt-text="Captura de pantalla de la sección de asignaciones. En el nombre, está resaltada la sincronización de los usuarios de Azure Active Directory con Bonusly." border="false":::

12. Revise los atributos de usuario que se sincronizan entre Azure AD y Bonusly en la sección **Attribute Mapping** (Asignación de atributos). Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Bonusly con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserAttributeMapping.png" alt-text="Captura de pantalla de la página de asignaciones de atributos. En la tabla se enumeran los atributos, los atributos de Azure Active Directory, los atributos correspondientes a Bonusly y el estado de coincidencia." border="false":::

13. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar el aprovisionamiento del servicio de aprovisionamiento de Azure AD para Bonusly, cambie el **estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningStatus.png" alt-text="Captura de pantalla de la sección de configuración. El comando de alternancia del estado de aprovisionamiento está establecido como Desactivado." border="false":::

15. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en Bonusly.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ScopeSync.png" alt-text="Captura de pantalla que muestra el cuadro de lista del ámbito. La opción para sincronizar solo los usuarios y grupos asignados está seleccionada en el cuadro." border="false":::

16. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/SaveProvisioning.png" alt-text="Captura de pantalla de la página de aprovisionamiento de Bonusly con el botón Guardar resaltado." border="false":::

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y seguir los vínculos al informe de actividad de aprovisionamiento, donde se describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en Bonusly.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png