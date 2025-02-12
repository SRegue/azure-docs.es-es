---
title: 'Tutorial: Configuración de Zoom para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y cancelar el aprovisionamiento de forma automática de las cuentas de usuario de Azure AD para Zoom.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: thwimmer
ms.openlocfilehash: 393b93d3d5e2a8d9088fe8ec759971fd096eb76c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760496"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Tutorial: Configuración de Zoom para el aprovisionamiento automático de usuarios

En este tutorial, se describen los pasos que debe realizar en Zoom y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y cancela el aprovisionamiento de usuarios y grupos de manera automática en [Zoom](https://zoom.us/pricing/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Crear usuarios en Zoom
> * Quitar usuarios de Zoom cuando ya no necesiten acceso
> * Mantener los atributos de usuario sincronizados entre Azure AD y Zoom
> * [Inicio de sesión único](./zoom-tutorial.md) en Zoom (recomendado)

## <a name="prerequisites"></a>Prerrequisitos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md).
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* [Un inquilino de Zoom](https://zoom.us/pricing).
* Una cuenta de usuario de Zoom con permisos de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos quiere [asignar entre Azure AD y Zoom](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de Zoom para admitir el aprovisionamiento con Azure AD

1. Inicie sesión en la [consola de administración de Zoom](https://zoom.us/signin). Vaya a **Advanced > App Marketplace** (Opciones avanzadas > Marketplace de aplicaciones) en el panel de navegación izquierdo.

    ![Integrations (Integraciones) de Zoom](media/zoom-provisioning-tutorial/zoom01.png)

2. Navegue a **Manage** (Administrar) en la esquina superior derecha de la página. 

    ![Captura de pantalla del marketplace de la aplicación Zoom con la opción de administración resaltada.](media/zoom-provisioning-tutorial/zoom02.png)

3. Navegue a la aplicación de Azure AD que ha creado. 
    
    ![Captura de pantalla de la sección de aplicaciones creadas con la aplicación Azure A D resaltada.](media/zoom-provisioning-tutorial/zoom03.png)

4. Seleccione **Credenciales de la aplicación** en el panel de navegación izquierdo.

    ![Captura de pantalla del panel de navegación izquierdo con la opción de credenciales de la aplicación resaltada.](media/zoom-provisioning-tutorial/zoom04.png)

5. Copie y guarde el **token de JWT**. Este valor se escribirá en el campo **Token secreto** de la pestaña Aprovisionamiento de su aplicación de Zoom en Azure Portal. Si necesita un nuevo token que no expire, tendrá que volver a configurar la fecha de expiración, lo que generará automáticamente un nuevo token. 

    ![Captura de pantalla de la página de credenciales de la aplicación.](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de Zoom desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento de Zoom, agregue Zoom desde la galería de aplicaciones de Azure AD. Si ha configurado previamente Zoom para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a para empezar a administrar el aprovisionamiento en Zoom, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en Zoom 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Zoom en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Zoom**.

    ![Vínculo a Zoom en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla de las opciones de administración con la opción Aprovisionamiento seleccionada.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla de la lista desplegable Modo de aprovisionamiento con la opción Automático seleccionada.](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba `https://api.zoom.us/scim` en **URL de inquilino**. Escriba el valor del **token de JWT** recuperado anteriormente en **Token secreto**. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Zoom. Si la conexión no se establece, asegúrese de que la cuenta de Zoom tiene permisos de administrador y pruebe de nuevo.

    ![Aprovisionamiento de Zoom](./media/zoom-provisioning-tutorial/provisioning.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Zoom** (Sincronizar usuarios de Azure Active Directory con Zoom).

9. Examine los atributos de usuario que se sincronizan entre Azure AD y Zoom en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Coincidentes** se usan para establecer correspondencia con las cuentas del usuario en Zoom a fin de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), deberá asegurarse de que la API de Zoom admite el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |active|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"]|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar el servicio de aprovisionamiento de Azure AD para Zoom, cambie la opción **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

12. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en Zoom.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

13. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

1. Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
2. Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
3. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitaciones del conector
* Actualmente Zoom solo permite un máximo de 9999 usuarios básicos.

## <a name="change-log"></a>Registro de cambios
* 14/05/2020: Se agregó compatibilidad con las operaciones de actualización agregadas para el atributo emails[type eq "work"].
* 20/10/2020: se ha agregado compatibilidad con dos nuevos roles "Con licencia" y "Local" para reemplazar los roles existentes "Pro" y "Corp". La compatibilidad con los roles "Pro" y "Corp" se eliminará en el futuro.

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)