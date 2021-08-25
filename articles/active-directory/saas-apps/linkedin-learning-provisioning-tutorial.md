---
title: 'Tutorial: Configuración de LinkedIn Learning para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar automáticamente cuentas de usuario de Azure AD en LinkedIn Learning.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 21e2f470-4eb1-472c-adb9-4203c00300be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: thwimmer
ms.openlocfilehash: 515a3e2cdc5073ce902a22597ae6e5efac32b97a
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322928"
---
# <a name="tutorial-configure-linkedin-learning-for-automatic-user-provisioning"></a>Tutorial: Configuración de LinkedIn Learning para el aprovisionamiento automático de usuarios

En este tutorial se indican los pasos necesarios en LinkedIn Learning y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y desaprovisiona usuarios y grupos de manera automática en [LinkedIn Learning](https://learning.linkedin.com/) mediante el servicio de aprovisionamiento de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades admitidas
> [!div class="checklist"]
> * Creación de usuarios en LinkedIn Learning
> * Quitar usuarios de LinkedIn Learning cuando ya no necesitan acceso
> * Mantener los atributos de usuario sincronizados entre Azure AD y LinkedIn Learning
> * Aprovisionar grupos y pertenencias a grupos en LinkedIn Learning
> * [Inicio de sesión único](linkedinlearning-tutorial.md) en LinkedIn Learning (recomendado)

## <a name="prerequisites"></a>Prerrequisitos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* [Un inquilino de Azure AD](../develop/quickstart-create-new-tenant.md) 
* Una cuenta de usuario en Azure AD con [permiso](../roles/permissions-reference.md) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global). 
* Aprobación y SCIM habilitado para LinkedIn Learning (póngase en contacto por correo electrónico).

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento
1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](../app-provisioning/user-provisioning.md).
2. Determine quién estará en el [ámbito de aprovisionamiento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine qué datos quiere [asignar entre Azure AD y LinkedIn Learning](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-linkedin-learning-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de LinkedIn Learning para admitir el aprovisionamiento con Azure AD
1. Inicie sesión en la [configuración de LinkedIn Learning](https://www.linkedin.com/learning-admin/settings/global). Seleccione **SCIM Setup** (Configuración de SCIM) y seleccione **Add new SCIM configuration** (Agregar nueva configuración de SCIM).

   ![Configuración de SCIM](./media/linkedin-learning-provisioning-tutorial/learning-scim-settings.png)

2. Escriba un nombre para la configuración y establezca **Auto-assign licenses** (Asignar licencias automáticamente) en On (Activado). Luego haga clic en **Generate Token** (Generar token).

   ![Nombre de la configuración de SCIM](./media/linkedin-learning-provisioning-tutorial/learning-scim-configuration.png)

3. Una vez creada la configuración, se debe generar un **Access token** (Token de acceso). Cópielo para usarlo más adelante.

   ![Token de acceso de SCIM](./media/linkedin-learning-provisioning-tutorial/learning-scim-token.png)

4. Puede volver a emitir cualquier configuración existente (lo que generará un token nuevo) o quitarla.

## <a name="step-3-add-linkedin-learning-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de LinkedIn Learning desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento en LinkedIn Learning, agregue LinkedIn Learning desde la galería de aplicaciones de Azure AD. Si antes ha configurado LinkedIn Learning para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento 

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Al asignar usuarios y grupos a LinkedIn Learning, debe seleccionar un rol que no sea **Default Access** (Acceso predeterminado). Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](../develop/howto-add-app-roles-in-azure-ad-apps.md) para agregar roles adicionales. 

* Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-linkedin-learning"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en LinkedIn Learning 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-linkedin-learning-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios de LinkedIn Learning en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **LinkedIn Learning**.

    ![Vínculo a LinkedIn Learning en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Captura de pantalla de las opciones de administración con la opción Aprovisionamiento seleccionada.](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Captura de pantalla de la lista desplegable Modo de aprovisionamiento con la opción Automático seleccionada.](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba `https://api.linkedin.com/scim` en la **URL de inquilino**. Escriba el valor del token de acceso recuperado anteriormente en **Token secreto**. Haga clic en **Prueba de conexión** para asegurarse de que Azure AD puede conectarse a LinkedIn Learning. Si la conexión no se establece, asegúrese de que la cuenta de LinkedIn Learning tiene permisos de administrador e inténtelo de nuevo.

    ![Captura de pantalla que muestra el cuadro de diálogo Credenciales de administrador, en el que se puede especificar el URL de inquilino y el secreto de inquilino.](./media/linkedin-learning-provisioning-tutorial/provisioning.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Aprovisionar usuarios de Azure Active Directory**.

9. Revise los atributos de usuario que se sincronizan entre Azure AD y LinkedIn Learning en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de LinkedIn Learning con el objetivo de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](../app-provisioning/customize-application-attributes.md), tiene que asegurarse de que la API de LinkedIn Learning admita el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   |Atributo|Tipo|Compatible con el filtrado|
   |---|---|---|
   |externalId|String|&check;|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |DisplayName|String|
   |addresses[type eq "work"].locality|String|
   |title|String|
   |emails[type eq "work"].value|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referencia|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. En la sección **Asignaciones**, seleccione **Aprovisionar grupos de Azure Active Directory**.

11. Revise los atributos del grupo que se sincronizan entre Azure AD y LinkedIn Learning en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con los grupos de LinkedIn Learning con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    |Atributo|Tipo|Compatible con el filtrado|
    |---|---|---|
    |DisplayName|String|&check;|
    |members|Referencia|
    |externalId|String|

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para LinkedIn Learning, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en LinkedIn Learning.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

15. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. 

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