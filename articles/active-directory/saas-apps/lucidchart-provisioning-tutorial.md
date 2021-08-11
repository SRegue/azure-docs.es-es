---
title: 'Tutorial: Configuración de Lucidchart para aprovisionar usuarios automáticamente con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar automáticamente cuentas de usuario de Azure AD en Lucidchart.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: thwimmer
ms.openlocfilehash: ba9e629e0b46442546919a1ca31ed2ef7fd90fb1
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113763217"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Tutorial: Configuración de Lucidchart para aprovisionar usuarios automáticamente

En este tutorial se indican los pasos necesarios en Lucidchart y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y desaprovisiona usuarios y grupos de manera automática en [Lucidchart](https://www.lucidchart.com/user/117598685#/subscriptionLevel) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Crear usuarios en Lucidchart
> * Quitar usuarios de Lucidchart cuando ya no necesitan acceso
> * Mantener los atributos de usuario sincronizados entre Azure AD y Lucidchart
> * Aprovisionar grupos y pertenencias a grupos en Lucidchart
> * [Inicio de sesión único](./lucidchart-tutorial.md) en Lucidchart (recomendado)

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Un inquilino de Lucidchart con el [plan Enterprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) o uno superior habilitado.
* Una cuenta de usuario de Lucidchart con permisos de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos quiere [asignar entre Azure AD y Lucidchart](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-lucidchart-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de Lucidchart para admitir el aprovisionamiento con Azure AD

1. Inicie sesión en la [consola de administración de Lucidchart](https://www.lucidchart.com). Vaya a **Team > App Integration** (Equipo > Integración de aplicaciones).

      :::image type="content" source="./media/lucidchart-provisioning-tutorial/team1.png" alt-text="Captura de pantalla de la consola de administración de Lucidchart. El menú Team (Equipo) está resaltado y abierto. En Admin (Administración), está resaltada la opción App Integration (Integración de la aplicación)." border="false":::

2. Vaya a **SCIM**.

      :::image type="content" source="./media/lucidchart-provisioning-tutorial/scim.png" alt-text="Captura de pantalla de la consola de administración de Lucidchart. Dentro de un botón SCIM grande, está resaltado el texto SCIM y está visible un banner habilitado." border="false":::

3. Desplácese hacia abajo para ver **Bearer token** (Token de portador) y **Lucidchart Base URL** (Dirección URL base de Lucidchart). Copie y guarde el **token de portador**. Este valor se escribe en el campo **Secret Token** (Token secreto) * de la pestaña "Provisioning" (Aprovisionamiento) de la aplicación Lucidchart en Azure Portal. 

      ![Token de Lucidchart](./media/lucidchart-provisioning-tutorial/token.png)

## <a name="step-3-add-lucidchart-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de Lucidchart desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento en Lucidchart, agregue Lucidchart desde la galería de aplicaciones de Azure AD. Si antes ha configurado Lucidchart para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a Lucidchart, debe seleccionar un rol que no sea **Default Access** (Acceso predeterminado). Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-lucidchart"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en Lucidchart 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-lucidchart-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Lucidchart en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Lucidchart**.

    ![Vínculo a Lucidchart en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla de las opciones de administración con la opción Aprovisionamiento seleccionada.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla de la lista desplegable Modo de aprovisionamiento con la opción Automático seleccionada.](common/provisioning-automatic.png)

5. En la sección **Admin Credentials** (Credenciales de administrador), escriba el valor del **token de portador** recuperado anteriormente en el campo **Secret Token** (Token secreto). Haga clic en **Test Connection** (Probar conexión) para asegurarse de que Azure AD puede conectarse a Lucidchart. Si la conexión no se establece, asegúrese de que la cuenta de Lucidchart tiene permisos de administrador e inténtelo de nuevo.

      ![Aprovisionamiento](./media/Lucidchart-provisioning-tutorial/lucidchart1.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Mappings** (Asignaciones), seleccione **Synchronize Azure Active Directory Users to Lucidchart** (Sincronizar usuarios de Azure Active Directory con Lucidchart).

9. Revise los atributos de usuario que se sincronizan entre Azure AD y Lucidchart en la sección **Attribute-Mapping** (Asignación de atributos). Los atributos seleccionados como propiedades **coincidentes** se usan para buscar coincidencias con las cuentas de usuario de Lucidchart para realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), tiene que asegurarse de que la API de Lucidchart admita el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |emails[type eq "work"].value|String|
   |active|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referencia|
   |urn:ietf:params:scim:schemas:extension:lucidchart:1.0:User:canEdit|Boolean|

10. En la sección **Mappings** (Asignaciones), seleccione **Synchronize Azure Active Directory Groups to Lucidchart** (Sincronizar grupos de Azure Active Directory con Lucidchart).

11. Revise los atributos de grupo que se sincronizan entre Azure AD y Lucidchart en la sección **Attribute-Mapping** (Asignación de tributo). Los atributos seleccionados como propiedades **coincidentes** se usan para buscar coincidencias con los grupos de Lucidchart para realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

      |Atributo|Tipo|
      |---|---|
      |DisplayName|String|
      |members|Referencia|

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para Lucidchart, cambie el valor de **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Seleccione los valores que quiera en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que quiere que se aprovisionen en Lucidchart.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

15. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación
Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

1. Use los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
2. Consulte la [barra de progreso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
3. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Registro de cambios

* 30/04/2020: se ha agregado compatibilidad con el atributo de extensión de empresa y el atributo personalizado "CanEdit" para los usuarios.
* 15/06/2020: se habilita la eliminación temporal de usuarios (admite el atributo [active](https://tools.ietf.org/html/rfc7643)).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)