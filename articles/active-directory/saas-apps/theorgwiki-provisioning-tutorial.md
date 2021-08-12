---
title: 'Tutorial: Configuración de OrgWiki para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar Azure Active Directory para aprovisionar y desaprovisionar automáticamente las cuentas de usuario de OrgWiki.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: thwimmer
ms.openlocfilehash: 477b0c02c80592d653cc7a9a182f0a4c030cff2f
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113759006"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Tutorial: Configuración de OrgWiki para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es mostrar los pasos que se realizan en OrgWiki y Azure Active Directory (Azure AD) para configurar Azure AD a fin de aprovisionar y desaprovisionar automáticamente usuarios y grupos en OrgWiki.

> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector está actualmente en versión preliminar pública. Para más información sobre los términos de uso generales de Microsoft Azure para las características en versión preliminar, consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* Un inquilino de Azure AD.
* [Un inquilino de OrgWiki](https://www.theorgwiki.com/welcome/).
* Una cuenta de usuario de OrgWiki con permisos de administrador.

## <a name="assign-users-to-theorgwiki"></a>Asignación de usuarios a OrgWiki

Azure Active Directory usa el concepto de asignación para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han asignado a una aplicación en Azure AD.

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a OrgWiki. Una vez que lo decida, puede seguir estas instrucciones para asignar dichos usuarios o grupos a OrgWiki:

* [Asignar un usuario o grupo a una aplicación empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Sugerencias importantes para asignar usuarios a OrgWiki

* Se recomienda asignar un único usuario de Azure AD a OrgWiki para probar la configuración de aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

* Al asignar un usuario a OrgWiki, debe seleccionar un rol válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="set-up-theorgwiki-for-provisioning"></a>Configuración de OrgWiki para el aprovisionamiento

Antes de configurar OrgWiki para el aprovisionamiento automático de usuarios con Azure AD, deberá habilitar el aprovisionamiento de SCIM en OrgWiki.

1. Inicie sesión en la [consola de administración de OrgWiki](https://www.theorgwiki.com/login/). Haga clic en **Admin Console** (Consola de administración).

    ![Captura de pantalla de OrgWiki con el avatar del usuario y la consola de administración resaltados.](media/theorgwiki-provisioning-tutorial/login.png)

2. En la consola de administración, haga clic en la pestaña **Settings** (Configuración). 

    ![Captura de pantalla de la consola de administración de OrgWiki con la pestaña Configuración resaltada.](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Vaya a **Service Accounts** (Cuentas de servicio).

    ![Captura de pantalla de la página Cuentas de servicio en la consola de administración de OrgWiki.](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Haga clic en **+Service Account** (+Cuenta de servicio). En **Service Account Type** (Tipo de cuenta de servicio), seleccione **Token Based** (Basada en token). Haga clic en **Save**(Guardar).

    ![Captura de pantalla del cuadro de diálogo Cuenta de servicio con las opciones Tipo de cuenta de servicio, Basada en token y Guardar resaltadas.](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Copie los **tokens activos**. Este valor se escribirá en el campo Token secreto de la pestaña Aprovisionamiento de la aplicación OrgWiki en Azure Portal.
     
    ![Captura de pantalla del cuadro de diálogo Administrar tokens para el aprovisionamiento de SCIM.](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Adición de OrgWiki desde la galería

Para configurar OrgWiki para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar OrgWiki desde la galería de aplicaciones de Azure AD a la lista de aplicaciones SaaS administradas.

1. En **[Azure Portal](https://portal.azure.com)** , en el panel de navegación izquierdo, seleccione **Azure Active Directory**.

    ![Botón Azure Active Directory](common/select-azuread.png)

2. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del panel.

    ![Botón Nueva aplicación](common/add-new-app.png)

4. En el cuadro de búsqueda, escriba **OrgWiki** y seleccione **OrgWiki** en el panel de resultados. 

    ![OrgWiki en la lista de resultados](common/search-new-app.png)

5. Seleccione el botón **Registrarse para OrgWiki**; se le redirigirá a la página de inicio de sesión de OrgWiki. 

    ![Captura de pantalla de la página de inicio de sesión de OrgWiki con la dirección URL resaltada](media/theorgwiki-provisioning-tutorial/image00.png)

6.  En la esquina superior derecha, seleccione **Login** (Inicio de sesión).

    ![Captura de pantalla de la esquina superior derecha de la página de inicio de sesión con la opción Iniciar sesión resaltada.](media/theorgwiki-provisioning-tutorial/image02.png)

7. Como OrgWiki es una aplicación de OpenIDConnect, elija iniciar sesión en OrgWiki con su cuenta Microsoft profesional.

    ![Captura de pantalla de la página de inicio de sesión de OrgWiki con la opción Iniciar sesión con Microsoft resaltada.](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Después de una autenticación correcta, la aplicación se agregará automáticamente al inquilino y se le redirigirá a su cuenta de OrgWiki.

    ![OrgWiki: Agregar SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Configuración del aprovisionamiento automático de usuarios en OrgWiki 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD para crear, actualizar y deshabilitar usuarios o grupos en OrgWiki en función de las asignaciones de grupos y usuarios de Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para OrgWiki en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **OrgWiki**.

    ![Vínculo a OrgWiki en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla de las opciones de administración con la opción Aprovisionamiento seleccionada.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla de la lista desplegable Modo de aprovisionamiento con la opción Automático seleccionada.](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` en la **URL de inquilino**. 

    Ejemplo: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> La opción **Subdomain Value** (Valor de subdominio) solo se puede establecer durante el proceso de registro inicial de OrgWiki.
 
6. Escriba el valor del token en el campo **Token secreto**, que recuperó anteriormente de OrgWiki. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a OrgWiki. Si la conexión no se establece, asegúrese de que la cuenta de OrgWiki tenga permisos de administrador y pruebe de nuevo.

    ![URL de inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

7. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

8. Haga clic en **Save**(Guardar).

9. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to TheOrgWiki** (Sincronizar usuarios de Azure Active Directory con OrgWiki).

    ![Asignaciones de usuarios de OrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Examine los atributos de usuario que se sincronizan entre Azure AD y OrgWiki en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Coincidentes** se usan para buscar coincidencias con las cuentas de usuario de OrgWiki para las operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    ![Atributos de usuario de OrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar el servicio de aprovisionamiento de Azure AD para OrgWiki, cambie **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

13. Seleccione los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que quiere aprovisionar en OrgWiki.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

14. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las sincronizaciones posteriores. Para más información sobre el tiempo que se tarda en aprovisionar los usuarios o los grupos, consulte [¿Cuánto tiempo se tarda en aprovisionar usuarios?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Puede usar la sección **Estado actual** para supervisar el progreso y seguir los vínculos al informe de actividad de aprovisionamiento, donde se describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en OrgWiki. Para obtener más información, vea [Comprobación del estado de aprovisionamiento](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para leer los registros de aprovisionamiento de Azure AD, consulte [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md).