---
title: 'Tutorial: Configuración de MindTickle para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar Azure Active Directory para aprovisionar y desaprovisionar automáticamente cuentas de usuario de MindTickle.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: thwimmer
ms.openlocfilehash: b0da76f17be54f29783018d854419739a0407263
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768998"
---
# <a name="tutorial-configure-mindtickle-for-automatic-user-provisioning"></a>Tutorial: Configuración de MindTickle para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es mostrar los pasos que se realizan en MindTickle y Azure Active Directory (Azure AD) para configurar Azure AD con el objetivo de aprovisionar y desaprovisionar automáticamente usuarios o grupos en MindTickle.

> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector está actualmente en versión preliminar pública. Para más información sobre los términos de uso generales de Microsoft Azure para las características en versión preliminar, consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* Un inquilino de Azure AD
* [Un inquilino de MindTickle](https://www.mindtickle.com/)
* Una cuenta de usuario de MindTickle con permisos de administrador.

## <a name="assigning-users-to-mindtickle"></a>Asignación de usuarios a MindTickle

Azure Active Directory usa un concepto denominado *asignaciones* para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han asignado a una aplicación en Azure AD.

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceso a MindTickle. Una vez que lo decida, puede seguir estas instrucciones para asignar dichos usuarios o grupos a MindTickle:
* [Asignar un usuario o grupo a una aplicación empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mindtickle"></a>Sugerencias importantes para asignar usuarios a MindTickle

* Se recomienda asignar un único usuario de Azure AD a MindTickle para probar la configuración de aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

* Al asignar un usuario a MindTickle, debe seleccionar un rol válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="setup-mindtickle-for-provisioning"></a>Configuración de MindTickle para el aprovisionamiento

Antes de configurar MindTickle para el aprovisionamiento automático de usuarios con Azure AD, deberá habilitar el aprovisionamiento de SCIM en MindTickle.


1.  Para obtener el token de JWT necesario para configurar el aprovisionamiento de SCIM, póngase en contacto con el [equipo de soporte técnico de MindTickle](mailto:help@mindtickle.com).


## <a name="add-mindtickle-from-the-gallery"></a>Adición de MindTickle desde la galería

Para configurar MindTickle para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar MindTickle desde la galería de aplicaciones de Azure AD a la lista de aplicaciones SaaS administradas.

**Para agregar MindTickle desde la galería de aplicaciones de Azure AD, siga estos pasos:**

1. En **[Azure Portal](https://portal.azure.com)** , en el panel de navegación izquierdo, seleccione **Azure Active Directory**.

    ![Botón Azure Active Directory](common/select-azuread.png)

2. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del panel.

    ![Botón Nueva aplicación](common/add-new-app.png)

4. En el cuadro de búsqueda, escriba **MindTickle**, seleccione **MindTickle** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![MindTickle en la lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mindtickle"></a>Configuración del aprovisionamiento automático de usuarios en MindTickle 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD para crear, actualizar y deshabilitar usuarios o grupos en MindTickle en función de las asignaciones de grupos y usuarios de Azure AD.

> [!TIP]
> También puede optar por habilitar el inicio de sesión único basado en SAML para MindTickle siguiendo las instrucciones del [tutorial de inicio de sesión único de MindTickle](mindtickle-tutorial.md). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático de usuarios, aunque estas dos características se complementan entre sí.

### <a name="to-configure-automatic-user-provisioning-for-mindtickle-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para MindTickle en Azure AD, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **MindTickle**.

    ![Vínculo a MindTickle en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla de las opciones de administración con la opción Aprovisionamiento seleccionada.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla de la lista desplegable Modo de aprovisionamiento con la opción Automático seleccionada.](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba `https://admin.mindtickle.com/scim` en la **URL de inquilino**. Escriba el valor de **token JWT** recuperado anteriormente en el cuadro de texto Token secreto y especifique el proporcionado por el equipo de soporte técnico de MindTickle. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a myPolicies. Si la conexión no se establece, asegúrese de que la cuenta de MindTickle tenga permisos de administrador e inténtelo de nuevo.

    ![URL de inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Haga clic en **Save**(Guardar).

8. En la sección **Asignaciones**, seleccione **Sincronizar usuarios de Azure Active Directory con MindTickle**.

    :::image type="content" source="media/mindtickle-provisioning-tutorial/usermapping.png" alt-text="Captura de pantalla de la sección Asignaciones. En el nombre, está visible la sincronización de los usuarios de Azure Active Directory con MindTickle." border="false":::

9. Revise los atributos de usuario que se sincronizan entre Azure AD y MindTickle en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de MindTickle con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    :::image type="content" source="media/mindtickle-provisioning-tutorial/userattribute.png" alt-text="Captura de pantalla de la página Asignaciones de atributos. En una tabla se enumeran los atributos de Azure Active Directory y MindTickle y la precedencia coincidente." border="false":::

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para MindTickle, cambie el valor de **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en MindTickle.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

15. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las sincronizaciones posteriores. Para más información sobre el tiempo que se tarda en aprovisionar los usuarios o los grupos, consulte [¿Cuánto tiempo se tarda en aprovisionar usuarios?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) 

Puede usar la sección **Estado actual** para supervisar el progreso y seguir los vínculos al informe de actividad de aprovisionamiento, donde se describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en MindTickle. Para obtener más información, vea [Comprobación del estado de aprovisionamiento](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para leer los registros de aprovisionamiento de Azure AD, consulte [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
