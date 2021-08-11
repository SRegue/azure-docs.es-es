---
title: 'Tutorial: Configuración de Zscaler Three para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: En este tutorial aprenderá a configurar Azure Active Directory para aprovisionar y desaprovisionar automáticamente cuentas de usuario en Zscaler Three.
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
ms.openlocfilehash: 9ae6888ada7ec1a03d4401ee5f56130ca45e05b1
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760417"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Tutorial: Configuración de Zscaler Three para el aprovisionamiento automático de usuarios

En este tutorial aprenderá a configurar Azure Active Directory (Azure AD) para aprovisionar y desaprovisionar automáticamente usuarios o grupos en Zscaler Three.

> [!NOTE]
> En este tutorial se describe un conector que se basa en el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio y de su funcionamiento, así como ver las respuestas proporcionadas a las preguntas que se realizan con más frecuencia al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerrequisitos

Para completar los pasos que describen en este tutorial, necesitará lo siguiente:

* Un inquilino de Azure AD.
* Un inquilino de Zscaler Three.
* Una cuenta de usuario de Zscaler Three con permisos de administrador.

> [!NOTE]
> La integración del aprovisionamiento de Azure AD usa SCIM API de Zscaler ZSCloud, que está disponible para cuentas Enterprise.

## <a name="adding-zscaler-three-from-the-gallery"></a>Incorporación de Zscaler Three desde la galería

Antes de configurar Zscaler Three para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar Zscaler Three desde la galería de aplicaciones de Azure AD hasta la lista de aplicaciones SaaS administradas.

En [Azure Portal](https://portal.azure.com), en el panel izquierdo, seleccione **Azure Active Directory**:

![Seleccione Azure Active Directory.](common/select-azuread.png)

Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**:

![Aplicaciones empresariales](common/enterprise-applications.png)

Para agregar una aplicación, seleccione **Nueva aplicación** en la parte superior de la ventana:

![Seleccionar Nueva aplicación](common/add-new-app.png)

En el cuadro de búsqueda, escriba **Zscaler Three**. Seleccione **Zscaler Three** en los resultados y, después, seleccione **Agregar**.

![Lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Asignación de usuarios a Zscaler Three

Para que los usuarios de Azure AD puedan usar determinadas aplicaciones es preciso que se les asigne acceso a ellas. En el contexto de aprovisionamiento automático de usuarios, solo se sincronizan los usuarios o grupos que se han asignado a una aplicación en Azure AD.

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a Zscaler Three. Una vez que lo haya decidido, puede asignar dichos usuarios a Zscaler Three siguiendo las instrucciones de [Asignación de un usuario o un grupo a una aplicación empresarial](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Sugerencias importantes para asignar usuarios a Zscaler Three

* Se recomienda asignar primero un usuario individual de Azure AD a Zscaler Three para probar la configuración del aprovisionamiento automático de usuarios. Puede asignar otros usuarios y grupos más adelante.

* Al asignar un usuario a Zscaler Three, es preciso seleccionar un rol específico de la aplicación válido (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="set-up-automatic-user-provisioning"></a>Configuración del aprovisionamiento automático de usuarios

Esta sección le guía por los pasos que debe seguir para configurar el servicio de aprovisionamiento de AD Azure para crear, actualizar y deshabilitar usuarios o grupos en Zscaler Three en función de las asignaciones de grupos y usuarios de Azure AD.

> [!TIP]
> También puede habilitar el inicio de sesión único basado en SAML en Zscaler Three. Si lo hace, siga las instrucciones del [Tutorial del inicio de sesión único de Zscaler Three](zscaler-three-tutorial.md). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático de usuarios, pero las dos características se complementan entre sí.

> [!NOTE]
> Cuando se aprovisionan o se desaprovisionan usuarios y grupos, se recomienda reiniciar el aprovisionamiento periódicamente para garantizar que las pertenencias a grupos se actualicen correctamente. Al ejecutar un reinicio, el servicio tendrá que evaluar de nuevo todos los grupos y actualizar las pertenencias. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y seleccione **Aplicaciones empresariales** > **Todas las aplicaciones** > **Zscaler Three**:

    ![Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Zscaler Three**:

    ![Lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Provisioning** (Aprovisionamiento):

    ![Aprovisionamiento de Zscaler Three](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. En **Provisioning Mode** (Modo de aprovisionamiento), seleccione **Automatic** (Automático):

    ![Establecer el modo de aprovisionamiento](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. En la sección **Admin Credentials** (Credenciales de administrador), especifique los valores de **Tenant URL** (URL de inquilino) y **Secret Token** (Token secreto) de la cuenta de Zscaler Three, como se describe en el paso siguiente.

6. Para obtener los valores de **Tenant URL** (URL de inquilino) y **Secret Token**, (Token secreto) vaya a **Administration** > **Authentication Settings** (Administración > Configuración de autenticación) en el portal de Zscaler Three y seleccione **SAML** en **Authentication Type** (Tipo de autenticación):

    ![Configuración de autenticación de Zscaler Three](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Seleccione **Configure SAML** (Configurar SAML) para abrir la ventana **Configure SAML** (Configurar SAML):

    ![Ventana Configure SAML (Configurar SAML)](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Seleccione **Enable SCIM-Based Provisioning** (Habilitar aprovisionamiento basado en SCIM) y copie los valores de **Base URL** (URL Base) y **Bearer Token** (Token de portador) y, después, guarde la configuración. En Azure Portal, pegue el valor de **Base URL** (URL Base) en el cuadro **URL de inquilino** y el valor de **Bearer Token** (Token de portador) en el cuadro **Token secreto**.

7. Después de especificar los valores de los cuadros **URL de inquilino** y **Token secreto**, seleccione **Test Connection** (Probar conexión) para asegurarse de que Azure AD puede conectarse a Zscaler Three. Si la conexión no se establece, asegúrese de que la cuenta de Zscaler Three tiene permisos de administrador e inténtelo de nuevo.

    ![Comprobación de la conexión](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. En el cuadro **Notification Email** (Dirección de correo electrónico para notificaciones), escriba la dirección de correo electrónico de la persona o grupo que deben recibir las notificaciones de error de aprovisionamiento. Seleccione **Send an email notification when a failure occurs** (Enviar una notificación por correo electrónico cuando se produzca un error):

    ![Configurar la dirección de correo electrónico para notificaciones](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Seleccione **Guardar**.

10. En la sección **Mappings** (Asignaciones), seleccione **Synchronize Azure Active Directory Users to ZscalerThree** (Sincronizar usuarios de Azure Active Directory con ZscalerThree):

    ![Sincronizar usuarios de Azure AD](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Examine los atributos de usuario que se sincronizan entre Azure AD y Zscaler Three en la sección **Attribute Mappings** (Asignaciones de atributos). Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Zscaler Three con el objetivo de realizar operaciones de actualización. Para confirmar los cambios, seleccione **Guardar**.

    ![Captura de pantalla de la sección Asignaciones de atributos, con siete asignaciones mostradas.](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. En la sección **Mappings** (Asignaciones), seleccione **Synchronize Azure Active Directory Groups to ZscalerThree** (Sincronizar grupos de Azure Active Directory con ZscalerThree):

    ![Sincronizar grupos de Azure AD](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Examine los atributos de grupo que se sincronizan entre Azure AD y Zscaler Three en la sección **Attribute Mappings** (Asignaciones de atributos). Los atributos seleccionados como propiedades de **Matching** (Coincidencia) se usan para buscar coincidencias con los grupos de Zscaler Three para las operaciones de actualización. Para confirmar los cambios, seleccione **Guardar**.

    ![Captura de pantalla de la sección Asignaciones de atributos, con tres asignaciones mostradas.](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de ámbito, consulte las instrucciones del artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar el servicio de aprovisionamiento de Azure AD para Zscaler Three, cambie el valor de **Provisioning Status** (Estado de aprovisionamiento) a **On** (Activado) en la sección **Settings** (Configuración):

    ![Estado de aprovisionamiento](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Elija los valores que desee en **Scope** (Ámbito), en la sección **Settings** (Configuración), para definir los usuarios o grupos que desea aprovisionar en Zscaler Three:

    ![Valores de ámbito](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Cuando esté listo para realizar el aprovisionamiento, seleccione **Save** (Guardar):

    ![Seleccionar Guardar](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Scope** (Ámbito) en la sección **Settings** (Configuración). La sincronización inicial tarda más tiempo que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. El progreso se puede supervisar en la sección **Synchronization Details** (Detalles de la sincronización). También puede seguir los vínculos al informe de actividad de aprovisionamiento, donde se describen todas las acciones que ha realizado el servicio de aprovisionamiento de Azure AD en Zscaler Three.

Para obtener información acerca de cómo leer los registros de aprovisionamiento de Azure AD, consulte [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png