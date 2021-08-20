---
title: 'Tutorial: Configuración de Looop para aprovisionar usuarios automáticamente con Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar Azure Active Directory para aprovisionar y desaprovisionar automáticamente cuentas de usuario en Looop.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: thwimmer
ms.openlocfilehash: c17c56651dda65d5bd151f0f274af6ed377fb7df
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229801"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Tutorial: Configuración de Looop para aprovisionar usuarios automáticamente

El objetivo de este tutorial es mostrar los pasos que se deben realizar en Looop y Azure Active Directory (Azure AD) a fin de configurar Azure AD para aprovisionar y desaprovisionar usuarios o grupos automáticamente en Looop.

> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector está actualmente en versión preliminar pública. Para más información sobre los términos de uso generales de Microsoft Azure para las características en versión preliminar, consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* Un inquilino de Azure AD
* [Un inquilino de Looop](https://www.looop.co/pricing/)
* Una cuenta de usuario de Looop con permisos de administrador.

## <a name="assign-users-to-looop"></a>Asignación de usuarios a Looop

Azure Active Directory usa el concepto de asignación para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han asignado a una aplicación en Azure AD.

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a Looop. Una vez que lo decida, puede asignar esos usuarios o grupos a Looop mediante estas instrucciones:

* [Asignar un usuario o grupo a una aplicación empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Sugerencias importantes para asignar usuarios a Looop

* Se recomienda asignar un único usuario de Azure AD a Looop para probar la configuración de aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

* Al asignar un usuario a Looop, debe seleccionar un rol válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="set-up-looop-for-provisioning"></a>Configurar Looop para el aprovisionamiento

Antes de configurar Looop para el aprovisionamiento automático de usuarios con Azure AD, debe recuperar alguna información de aprovisionamiento de Looop.

1. Inicie sesión en la [consola de administración de Looop](https://app.looop.co/#/login) y seleccione **Cuenta**. En **Configuración de la cuenta**, seleccione **Autenticación**.

    ![Administrador de Looop](media/looop-provisioning-tutorial/admin.png)

2. Para generar un nuevo token, haga clic en **Reset Token** (Restablecer token) en **SCIM Integration** (Integración de SCIM).

    ![Token de Looop](media/looop-provisioning-tutorial/resettoken.png)

3. Copie los valores de **SCIM Endpoint** (Punto de conexión SCIM) y **Token**. Estos valores se escriben en el campo **URL de inquilino** y **Token secreto** de la pestaña Aprovisionamiento de la aplicación Looop en Azure Portal. 

    ![Looop Create Token](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Adición de Looop desde la galería

Para configurar Looop para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar Looop desde la galería de aplicaciones de Azure AD a la lista de aplicaciones SaaS administradas.

1. En **[Azure Portal](https://portal.azure.com)** , en el panel de navegación izquierdo, seleccione **Azure Active Directory**.

    ![Botón Azure Active Directory](common/select-azuread.png)

2. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del panel.

    ![Botón Nueva aplicación](common/add-new-app.png)

4. En el cuadro de búsqueda, escriba **Looop** y seleccione **Looop** en el panel de resultados. 

    ![Looop en la lista de resultados](common/search-new-app.png)

5. Seleccione el botón **Registrarse en Looop**, que le redirigirá a la página de inicio de sesión de Looop. 

    ![Looop OIDC Add](media/looop-provisioning-tutorial/signup.png)

6. Como Looop es una aplicación de OpenIDConnect, inicie sesión en Looop con su cuenta profesional de Microsoft.

    ![Looop OIDC login](media/looop-provisioning-tutorial/msftlogin.png)

7. Después de autenticarse, acepte la petición de consentimiento de la página de consentimiento. La aplicación se agregará automáticamente a su inquilino y será redirigido a su cuenta de Looop.

    ![Looop OIDc Consent](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Configuración del aprovisionamiento automático de usuarios en Looop 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD para crear, actualizar y deshabilitar usuarios o grupos en Looop en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Looop en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Looop**.

    ![Vínculo a Looop en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla de las opciones de administración con la opción Aprovisionamiento seleccionada.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla de la lista desplegable Modo de aprovisionamiento con la opción Automático seleccionada.](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba `https://<organisation_domain>.looop.co/scim/v2` en la **URL de inquilino**. Por ejemplo, `https://demo.looop.co/scim/v2`. Escriba el valor que recuperó y guardó anteriormente de Looop en **Token secreto**. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Looop. Si no se establece la conexión, asegúrese de que la cuenta de Looop tiene permisos de administrador y pruebe de nuevo.

    ![URL de inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Haga clic en **Save**(Guardar).

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Looop** (Sincronizar usuarios de Azure Active Directory con Looop).

    ![Asignaciones de usuario de Looop](media/looop-provisioning-tutorial/usermappings.png)

9. Revise los atributos de usuario que se sincronizan entre Azure AD y Looop en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Matching** (Coincidencia) se usan para buscar coincidencias con las cuentas de usuario de Looop con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:area|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_1|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_2|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_3|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:location|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:position|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:startAt|String|

10. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to Meta Networks Connector** (Sincronizar grupos de Azure Active Directory con Meta Networks Connector).

    ![Asignaciones de grupo de Looop](media/looop-provisioning-tutorial/groupmappings.png)

11. Revise los atributos de grupo que se sincronizan entre Azure AD y Meta Networks Connector en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con los grupos de Meta Networks Connector con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    |Atributo|Tipo|Compatible con el filtrado|
    |---|---|---|
    |DisplayName|String|&check;|
    |members|Referencia|
    |externalId|String|


10. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar el servicio de aprovisionamiento de Azure AD para Looop, cambie **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

12. Seleccione los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que quiere aprovisionar en Looop.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

13. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y seguir los vínculos al informe de actividad de aprovisionamiento, donde se describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en Looop.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="change-log"></a>Registro de cambios

* 15/07/2021: Se han agregado los atributos de usuario de extensión empresarial **urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department**, **urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber** y **urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager**.
* 15/07/2021: Se han eliminado los atributos de usuario de extensión personalizados **urn:ietf:params:scim:schemas:extension:Looop:2.0:User:department** y **urn:ietf:params:scim:schemas:extension:Looop:2.0:User:employee_id**.

## <a name="more-resources"></a>Más recursos

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)


