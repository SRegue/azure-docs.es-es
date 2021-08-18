---
title: Configuración de la asignación a la aplicación de autoservicio | Microsoft Docs
description: Habilitar el acceso de autoservicio a las aplicaciones para permitir a los usuarios buscar sus propias aplicaciones
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: phsignor
ms.openlocfilehash: 98848b4ee117a0c2e34e21b30a920fe22c8e02be
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749118"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Configuración de la asignación a la aplicación de autoservicio

Para que los usuarios puedan detectar automáticamente aplicaciones desde su página Aplicaciones, debe habilitar **Acceso de autoservicio a las aplicaciones** en las aplicaciones que quiera que los usuarios detecten automáticamente y puedan solicitar acceso. Esta funcionalidad está disponible para las aplicaciones que se agregaron desde la [Galería de Azure AD](./add-application-portal.md), [Azure AD Application Proxy](../app-proxy/application-proxy.md) o se agregaron mediante el [consentimiento del usuario o administrador](../develop/application-consent-experience.md).

Esta característica es una excelente manera de ahorrar tiempo y dinero como grupo de TI y es muy recomendable como parte de una implementación de aplicaciones moderna con Azure Active Directory.

Con esta característica, puede:

- Permitir que los usuarios detecten automáticamente las aplicaciones desde la página [Aplicaciones](https://myapps.microsoft.com/) sin molestar al grupo de TI.

- Agregar esos usuarios a un grupo preconfigurado para que pueda ver quién ha solicitado acceso, quitar el acceso y administrar los roles que tienen asignados.

- Si lo desea, puede permitir que un aprobador de la empresa apruebe las solicitudes de acceso a la aplicación para que no tenga que hacerlo el grupo de TI.

- También puede configurar un máximo de 10 usuarios que pueden aprobar el acceso a esta aplicación.

- Permitir que un aprobador de la empresa establezca las contraseñas que los usuarios pueden usar para iniciar sesión en la aplicación directamente desde la página [Aplicaciones](https://myapps.microsoft.com/) del aprobador.

- Asignar automáticamente usuarios asignados de autoservicio directamente a un rol de aplicación.

> [!NOTE]
> Se requiere una licencia de Azure Active Directory Premium (P1 o P2) para que los usuarios puedan solicitar unirse a una aplicación de autoservicio y para que los propietarios puedan aprobar o denegar solicitudes. Sin una licencia de Azure Active Directory Premium, los usuarios no pueden agregar aplicaciones de autoservicio.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Habilitar el acceso de autoservicio a las aplicaciones para permitir a los usuarios buscar sus propias aplicaciones

El autoservicio de acceso a las aplicaciones es una excelente manera de permitir que los usuarios detecten automáticamente las aplicaciones y, si lo desea, permitir que el grupo de negocios apruebe el acceso a esas aplicaciones. Respecto a las aplicaciones de inicio de sesión único con contraseña, también puede permitir que el grupo de negocios administre las credenciales asignadas a esos usuarios desde sus propios paneles de acceso Mis aplicaciones.

Para habilitar el acceso de autoservicio a las aplicaciones, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global.

2. Seleccione **Azure Active Directory**. En el menú de navegación de la izquierda, seleccione **Aplicaciones empresariales**.

3. Seleccione la aplicación de la lista. Si no la ve, empiece a escribir su nombre en el cuadro de búsqueda. También puede usar controles de filtro para seleccionar el tipo de aplicación, el estado o la visibilidad. Tras ello, seleccione **Aplicar**.

4. En el menú de navegación de la izquierda, seleccione **Autoservicio**.

5. Para habilitar el acceso de autoservicio a las aplicaciones para esta aplicación, establezca la opción **¿Quiere permitir que los usuarios soliciten acceso a esta aplicación?** en **Sí.**

6. Junto a **¿A qué grupo se deberían agregar los usuarios asignados?** , haga clic en **Seleccionar grupo**. Elija el grupo y, después, haga clic en **Seleccionar**. Cuando la solicitud de un usuario se apruebe, se agregará a este grupo. Al ver la pertenencia de este grupo, podrá ver a quién se le ha concedido acceso a la aplicación a través del acceso de autoservicio.
  
    > [!NOTE]
    > Esta configuración no admite grupos sincronizados del entorno local.

7. **Opcional:** para requerir la aprobación de la empresa antes de permitir el acceso a los usuarios, establezca la opción **¿Quiere requerir una aprobación para conceder acceso a esta aplicación?** en **Sí**.

8. **Opcional: para las aplicaciones que solo usan el inicio de sesión único con contraseña,** para permitir que los aprobadores de la empresa especifiquen las contraseñas que se envían a esta aplicación para los usuarios aprobados, establezca la opción **¿Quiere permitir que los aprobadores establezcan las contraseñas de los usuarios de esta aplicación?** en **Sí**.

9. **Opcional:** para especificar los aprobadores de la empresa que tienen permiso para aprobar el acceso a esta aplicación, junto a **¿Quién tiene permiso para aprobar el acceso a esta aplicación?** , haga clic en **Seleccionar aprobadores** y seleccione hasta 10 aprobadores individuales de la empresa. Después, haga clic en **Seleccionar**.

    >[!NOTE]
    >No se admiten grupos. Puede seleccionar hasta 10 aprobadores individuales de la empresa. Si especifica varios aprobadores, cualquier aprobador individual puede aprobar una solicitud de acceso.

10. **Opcional:** **en el caso de las aplicaciones que exponen roles**, para asignar usuarios aprobados de autoservicio a un rol, junto a **¿A qué rol deben asignarse los usuarios de esta aplicación?** , haga clic en **Seleccionar rol** y seleccione el rol al que deban estar asignados estos usuarios. Después, haga clic en **Seleccionar**.

11. Haga clic en el botón **Guardar** de la parte superior del panel para terminar.

Cuando se haya completado la configuración de las aplicaciones de autoservicio, los usuarios pueden ir a su página [Aplicaciones](https://myapps.microsoft.com/) y hacer clic en el botón **Add self-service apps** (Agregar aplicaciones de autoservicio) para buscar las aplicaciones con acceso de autoservicio. Los aprobadores de la empresa pueden ver también una notificación en su página [Aplicaciones](https://myapps.microsoft.com/). Puede habilitar un correo electrónico que les informa cuando un usuario ha solicitado el acceso a una aplicación que requiere su aprobación.

## <a name="next-steps"></a>Pasos siguientes

[Configuración de Azure Active Directory para la administración de grupos de autoservicio](../enterprise-users/groups-self-service-management.md)
