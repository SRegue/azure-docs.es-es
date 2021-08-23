---
title: 'Tutorial: Configuración de askSpoke para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a aprovisionar y desaprovisionar de forma automática las cuentas de usuario de Azure AD para askSpoke.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: f9458aac-f576-49ce-aba4-fc8302ed6360
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2021
ms.author: Zhchia
ms.openlocfilehash: 6d9f4d46c46d2a8963f70761f8e02654c302b35a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112034935"
---
# <a name="tutorial-configure-askspoke-for-automatic-user-provisioning"></a>Tutorial: Configuración de askSpoke para el aprovisionamiento automático de usuarios

En este tutorial se describen los pasos que debe realizar en askSpoke y Azure Active Directory (Azure AD) para configurar el aprovisionamiento automático de usuarios. Cuando se configura, Azure AD aprovisiona y desaprovisiona usuarios y grupos de manera automática en [askSpoke](https://www.askspoke.com/) mediante su servicio de aprovisionamiento. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="capabilities-supported"></a>Funcionalidades admitidas

> [!div class="checklist"]
>
> -  Crear usuarios en askSpoke
> -  Quitar usuarios de askSpoke cuando ya no necesiten acceso
> -  Mantener los atributos de usuario sincronizados entre Azure AD y askSpoke
> -  Aprovisionar grupos y pertenencias a grupos en askSpoke
> -  [Inicio de sesión único](https://docs.microsoft.com/azure/active-directory/saas-apps/askspoke-tutorial) para askSpoke (recomendado)

## <a name="prerequisites"></a>Prerrequisitos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

-  [Un inquilino de Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
-  Una cuenta de usuario en Azure AD con [permiso](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar el aprovisionamiento (por ejemplo, Administrador de aplicaciones, Administrador de aplicaciones en la nube, Propietario de la aplicación o Administrador global).
-  Una cuenta de usuario de askSpoke con permisos de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Paso 1. Planeación de la implementación de aprovisionamiento

1. Obtenga información sobre [cómo funciona el servicio de aprovisionamiento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quién estará en el [ámbito de aprovisionamiento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine qué datos quiere [asignar entre Azure AD y askSpoke](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-askspoke-to-support-provisioning-with-azure-ad"></a>Paso 2. Configuración de Snowflake para admitir el aprovisionamiento con Azure AD

1. Inicie sesión en la consola de administración de askSpoke.

2. Navegue a **Settings** (Configuración).

3. Haga clic en la pestaña **Integraciones** .

4. Desplácese hasta la tarjeta SCIM. Haga clic en **Conectar**.

   ![Editar](media/askspoke-provisioning-tutorial/connection.png)

5. Haga clic en **Enable SCIM** (Habilitar SCIM).

6. Copie y guarde el **token de API**. Este valor se escribirá en el campo **Token secreto** de la pestaña Aprovisionamiento en la aplicación askSpoke en Azure Portal.

   ![API](media/askspoke-provisioning-tutorial/scim.png)

7. La dirección URL del inquilino es la dirección URL de askSpoke seguida de **/scim/v2**. Por ejemplo: `https://example.askspoke.com/scim/v2`. Este valor se escribirá en el campo **URL del inquilino** de la pestaña Aprovisionamiento de la aplicación askSpoke en Azure Portal.

## <a name="step-3-add-askspoke-from-the-azure-ad-application-gallery"></a>Paso 3. Adición de SnaskSpoke desde la galería de aplicaciones de Azure AD

Para empezar a administrar el aprovisionamiento askSpoke, agregue askSpoke desde la galería de aplicaciones de Azure AD. Si ha configurado previamente askSpoke para el inicio de sesión único, puede usar la misma aplicación. Sin embargo, se recomienda que cree una aplicación independiente al probar la integración inicialmente. Puede encontrar más información sobre cómo agregar una aplicación desde la galería [aquí](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Paso 4. Determinar quién estará en el ámbito de aprovisionamiento

El servicio de aprovisionamiento de Azure AD le permite definir quién se aprovisionará, en función de la asignación a la aplicación y de los atributos del usuario o grupo. Si elige el ámbito del que se aprovisionará en la aplicación en función de la asignación, puede usar los pasos [siguientes](../manage-apps/assign-user-or-group-access-portal.md) para asignar usuarios y grupos a la aplicación. Si elige el ámbito del que se aprovisionará en función únicamente de los atributos del usuario o grupo, puede usar un filtro de ámbito, tal como se describe [aquí](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

-  Al asignar usuarios y grupos a askSpoke, debe seleccionar un rol que no sea **Acceso predeterminado**. Los usuarios con el rol de acceso predeterminado se excluyen del aprovisionamiento y se marcarán como no autorizados en los registros de aprovisionamiento. Si el único rol disponible en la aplicación es el rol de acceso predeterminado, puede [actualizar el manifiesto de aplicación](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para agregar roles adicionales.

-  Empiece por algo pequeño. Pruebe con un pequeño conjunto de usuarios y grupos antes de implementarlo en todos. Cuando el ámbito del aprovisionamiento se define en los usuarios y grupos asignados, puede controlarlo asignando uno o dos usuarios o grupos a la aplicación. Cuando el ámbito se establece en todos los usuarios y grupos, puede especificar un [filtro de ámbito basado en atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-askspoke"></a>Paso 5. Configuración del aprovisionamiento automático de usuarios en askSpoke

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD a fin de crear, actualizar y deshabilitar usuarios o grupos en TestApp en función de las asignaciones de grupos o usuarios de Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-askspoke-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para askSpoke en Azure AD:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

   ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **askSpoke**.

   ![Vínculo a askSpoke en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

   ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

   ![Pestaña de aprovisionamiento automático](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba la URL de inquilino y el token secreto de askSpoke. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a askSpoke. Si la conexión no se establece, asegúrese de que la cuenta de askSpoke tiene permisos de administrador y vuelva a intentarlo.

   ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que deba recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

   ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Seleccione **Guardar**.

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to askSpoke** (Sincronizar usuarios de Azure Active Directory con askSpoke).

9. Examine los atributos de grupo que se sincronizan entre Azure AD y askSpoke en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de askSpoke con el objetivo de realizar operaciones de actualización. Si decide cambiar el [atributo de destino coincidente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), deberá asegurarse de que la API de askSpoke admita el filtrado de usuarios basado en ese atributo. Seleccione el botón **Guardar** para confirmar los cambios.

   | Atributo                                                             | Tipo      | Compatible con el filtrado |
   | --------------------------------------------------------------------- | --------- | ----------------------- |
   | userName                                                              | String    | &check;                 |
   | emails[type eq "work"].value                                          | String    |
   | active                                                                | Boolean   |
   | title                                                                 | String    |
   | name.givenName                                                        | String    |
   | name.familyName                                                       | String    |
   | name.formatted                                                        | String    |
   | addresses[type eq "work"].locality                                    | String    |
   | addresses[type eq "work"].country                                     | String    |
   | addresses[type eq "work"].region                                      | String    |
   | externalId                                                            | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager    | Referencia |
   | urn:ietf:params:scim:schemas:extension:SpokeCustom:2.0:User:startDate | String    |

10.   En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to askSpoke** (Sincronizar grupos de Azure Active Directory con askSpoke).

11.   Examine los atributos de grupo que se sincronizan entre Azure AD y askSpoke en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para establecer la correspondencia de los grupos en askSpoke para las operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

      | Atributo   | Tipo      | Compatible con el filtrado |
      | ----------- | --------- | ----------------------- |
      | DisplayName | String    | &check;                 |
      | members     | Referencia |

12.   Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13.   Para habilitar el servicio de aprovisionamiento de Azure AD para askSpoke, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

      ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14.   Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que quiere que se aprovisionen en askSpoke.

      ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

15.   Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

      ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia el ciclo de sincronización inicial de todos los usuarios y grupos definidos en **Ámbito** en la sección **Configuración**. El ciclo de sincronización inicial tarda más tiempo en realizarse que los ciclos posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose.

## <a name="step-6-monitor-your-deployment"></a>Paso 6. Supervisión de la implementación

Una vez configurado el aprovisionamiento, use los recursos siguientes para supervisar la implementación:

1. Use los [registros de aprovisionamiento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar qué usuarios se han aprovisionado correctamente o sin éxito.
2. Consulte la [barra de progreso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver el estado del ciclo de aprovisionamiento y cuánto falta para que finalice.
3. Si la configuración de aprovisionamiento parece estar en mal estado, la aplicación pasará a estar en cuarentena. Más información sobre los estados de cuarentena [aquí](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Recursos adicionales

-  [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../manage-apps/configure-automatic-user-provisioning-portal.md)
-  [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

-  [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../manage-apps/check-status-user-account-provisioning.md)
