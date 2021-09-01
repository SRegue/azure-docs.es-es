---
title: 'Delegación de la gobernanza del acceso en administradores de paquetes de acceso en la administración de derechos de Azure AD: Azure Active Directory'
description: Aprenda a delegar la gobernanza del acceso de los administradores de TI en los administradores de paquetes de acceso y los jefes de proyecto para que ellos mismos puedan encargarse del acceso.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95c875c1a4ded86686120bb4de67904ef69a53c6
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286561"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Delegación de la gobernanza del acceso en administradores de paquetes de acceso en la administración de derechos de Azure AD

Para delegar la creación y administración de paquetes de acceso en un catálogo, se agregan usuarios al rol de administrador de paquetes de acceso. Estos administradores deben estar familiarizados con la necesidad de que los usuarios soliciten acceso a los recursos de un catálogo. Por ejemplo, si se utiliza un catálogo en un proyecto, el administrador de paquetes de acceso para ese catálogo podría ser un responsable del proyecto.  Los administradores de paquetes de acceso no pueden agregar recursos a un catálogo, pero sí pueden administrar los paquetes de acceso y las directivas del catálogo.  Cuando se delegar en un administrador de paquetes de acceso, esta persona puede responsabilizarse después de los siguientes aspectos:

- Qué roles tendrá un usuario para los recursos de un catálogo
- Quién necesitará acceso
- Quién necesita aprobar las solicitudes de acceso
- Cuánto tiempo durará el proyecto

En este vídeo se ofrece información general sobre el modo en que el propietario del catálogo delega la gobernanza del acceso en un administrador de paquetes de acceso.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

Además de los roles de propietario del catálogo y administrador de paquetes de acceso, también puede agregar usuarios al rol de lector del catálogo, que proporciona acceso de solo vista al catálogo, o al rol de administrador de asignaciones de paquetes de acceso, que permite a los usuarios cambiar las asignaciones, pero no acceder a paquetes o directivas.

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Como propietario del catálogo, delegación en un administrador de paquetes de acceso

Siga estos pasos para asignar un usuario al rol de administrador de paquetes de acceso:

**Requisitos previos de rol:** administrador global, administrador de Identity Governance, administrador de usuarios o administrador de catálogos

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos** y abra el catálogo al que quiere agregar administradores.

1. En el menú izquierdo, haga clic en **Roles y administradores**.

    ![Roles y administradores de catálogos](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Haga clic en **Agregar administradores del paquete de acceso** para seleccionar los miembros de este rol.

1. Haga clic en **Seleccionar** para agregar estos miembros.

## <a name="remove-an-access-package-manager"></a>Eliminación de un administrador de paquetes de acceso

Siga estos pasos para quitar a un usuario del rol de administrador de paquetes de acceso:

**Rol necesario:** administrador global, administrador de usuarios o propietario del catálogo.

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos** y abra el catálogo al que quiere agregar administradores.

1. En el menú izquierdo, haga clic en **Roles y administradores**.

1. Agregue una marca de verificación junto al administrador de paquetes de acceso que desea quitar.

1. Haga clic en **Quitar**.

## <a name="next-steps"></a>Pasos siguientes

- [Creación de un paquete de acceso](entitlement-management-access-package-create.md)
