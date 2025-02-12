---
title: 'Tutorial: Configuración de AuditBoard para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar de forma automática cuentas de usuario de Azure AD en AuditBoard.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: e6ab736b-2bb7-4a5a-9f01-67c33f0ff97d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: thwimmer
ms.openlocfilehash: 8050b5352658412aaa156b1e48290810e45a5894
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323440"
---
# <a name="tutorial-configure-auditboard-for-automatic-user-provisioning"></a>Tutorial: Configuración de AuditBoard para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe realizar en AuditBoard y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando está configurado, Azure AD aprovisiona y desaprovisiona automáticamente a los usuarios en [AuditBoard](https://www.auditboard.com/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Crear usuarios en AuditBoard
> * Quitar usuarios de AuditBoard cuando ya no necesiten acceso
> * Mantener los atributos de usuario sincronizados entre Azure AD y AuditBoard
> * [Inicio de sesión único](./auditboard-tutorial.md) en AuditBoard (recomendado)

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Un sitio de AuditBoard (activo).

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos quiere [asignar entre Azure AD y AuditBoard](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-auditboard-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de AuditBoard para admitir el aprovisionamiento con Azure AD

1. Inicie sesión en AuditBoard. Vaya a **Settings** > **Users & Roles** > **Security** > **SCIM** (Configuración > Usuarios y roles > Seguridad > SCIM).

2. Haga clic en **Generate Token** (Generar token). 

3. Guarde el **token** y la **dirección URL base de SCIM**. Estos valores se escriben en el campo de dirección URL de inquilino y token secreto de la pestaña Provisioning (Aprovisionamiento) de la aplicación AuditBoard en Azure Portal.

   > [!NOTE]
   > La generación de un nuevo token invalidará el token anterior.

4. La instancia de AuditBoard requerirá que se establezcan los siguientes permisos de usuario en el rol de usuario de SCIM (administrador del sistema de forma predeterminada). Póngase en contacto con el soporte técnico de AuditBoard para confirmar que se han establecido correctamente `user:action.administer must be set to allow` y `user:action.edit must be set to allow`.


## <a name="step-3-add-auditboard-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de AuditBoard desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento de AuditBoard, agregue esta aplicación desde la galería de aplicaciones de Azure AD. Si ha configurado previamente AuditBoard para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige delimitar quién se aprovisionará en la aplicación en función de la asignación, puede usar los [pasos](../manage-apps/assign-user-or-group-access-portal.md) siguientes para asignar usuarios a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios a AuditBoard, debe seleccionar un rol que no sea **Default Access** (Acceso predeterminado). Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios asignados, puede controlarlo asignando uno o dos usuarios a la aplicación. Cuando el ámbito se establece en todos los usuarios, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-auditboard"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en AuditBoard 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD para crear, actualizar y deshabilitar usuarios en AuditBoard en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-auditboard-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios en AuditBoard en Azure AD, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **AuditBoard**.

    ![Vínculo a AuditBoard en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña de aprovisionamiento automático](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña Aprovisionamiento](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba los valores de URL de inquilino y token secreto de AuditBoard. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a AuditBoard. Si la conexión no se establece, asegúrese de que la cuenta de AuditBoard tenga permisos de administrador y pruebe de nuevo.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Atea** (Sincronizar usuarios de Azure Active Directory con AuditBoard).

9. Revise los atributos de usuario que se sincronizan entre Azure AD y AuditBoard en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de AuditBoard con el objetivo de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de AuditBoard admita el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |emails[type eq "work"].value|String|&check;|
   |active|Boolean|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   

10. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar el servicio de aprovisionamiento de Azure AD para AuditBoard, cambie **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

12. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios que quiere que se aprovisionen en AuditBoard.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

13. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

1. Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
2. Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
3. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)