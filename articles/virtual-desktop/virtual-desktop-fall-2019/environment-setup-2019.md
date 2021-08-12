---
title: 'Entorno de Azure Virtual Desktop (clásico): Azure'
description: Elementos básicos de un entorno de Azure Virtual Desktop (clásico).
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9062565f5c13d7b278b57068df79c1bb4de66424
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745068"
---
# <a name="azure-virtual-desktop-classic-environment"></a>Entorno de Azure Virtual Desktop (clásico)

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop (clásico), que no admite objetos de Azure Resource Manager. Si está intentando administrar objetos de Azure Virtual Desktop para Azure Resource Manager, consulte [este artículo](../environment-setup.md).

Azure Virtual Desktop es un servicio que ofrece a los usuarios un acceso fácil y seguro a sus escritorios virtualizados y a RemoteApps. Este tema le proporcionará más información sobre la estructura general del entorno de Azure Virtual Desktop.

## <a name="tenants"></a>Inquilinos

El inquilino de Azure Virtual Desktop es la interfaz principal para administrar su entorno de Azure Virtual Desktop. Cada inquilino de Azure Virtual Desktop debe estar asociado con la instancia de Azure Active Directory que contiene los usuarios que iniciarán sesión en el entorno. Desde el inquilino de Azure Virtual Desktop, puede comenzar a crear grupos de hosts para ejecutar las cargas de trabajo de sus usuarios.

## <a name="host-pools"></a>Grupos de host

Un grupo de hosts es una colección de máquinas virtuales de Azure que se registran en Azure Virtual Desktop como hosts de sesión cuando ejecuta el agente de Azure Virtual Desktop. Todas las máquinas virtuales de host de sesión en un grupo de hosts deben provenir de la misma imagen para poder obtener una experiencia de usuario consistente.

Un grupo de hosts puede ser uno de estos dos tipos:

- Personal, donde cada host de sesión se asigna a usuarios individuales.
- Agrupado, donde los hosts de sesión pueden aceptar conexiones de cualquier usuario autorizado a un grupo de aplicaciones dentro del grupo de hosts.

Puede configurar propiedades adicionales en el grupo de hosts para cambiar el comportamiento de equilibrio de carga, cuántas sesiones puede tener cada host de sesión y lo que el usuario puede hacer con los hosts de sesión en el grupo de hosts mientras inicia sesión en Azure Virtual Desktop. Puede controlar los recursos publicados a los usuarios a través de grupos de aplicaciones.

## <a name="app-groups"></a>Grupos de aplicaciones

Un grupo de aplicaciones es una agrupación lógica de aplicaciones instaladas en hosts de sesión del grupo de hosts. Un grupo de aplicaciones puede ser uno de estos dos tipos:

- RemoteApp, donde los usuarios acceden a las instancias de RemoteApp que usted mismo seleccione y publique individualmente en el grupo de aplicaciones.
- Escritorio, donde los usuarios acceden al escritorio completo.

De forma predeterminada, un grupo de aplicaciones de escritorio (denominado "Grupo de aplicaciones de escritorio") se crea automáticamente cada vez que crea un grupo de hosts. Puede eliminar este grupo de aplicaciones en cualquier momento. Sin embargo, no puede crear otro grupo de aplicaciones de escritorio en el grupo de hosts mientras ya exista otro grupo de aplicaciones de escritorio. Para publicar instancias de RemoteApp, debe crear un grupo de aplicaciones de RemoteApp. Puede crear varios grupos de aplicaciones de RemoteApp para adaptarse a diferentes escenarios de trabajo. Los diferentes grupos de aplicaciones de RemoteApp también pueden contener instancias de RemoteApp superpuestas.

Para publicar recursos para los usuarios, debe asignarlos a grupos de aplicaciones. Cuando asigne usuarios a los grupos de aplicaciones, tenga en cuenta lo siguiente:

- No se puede asignar un usuario a un grupo de aplicaciones de escritorio y a un grupo de aplicaciones de RemoteApp en el mismo grupo de hosts.
- Se puede asignar un usuario a varios grupos de aplicaciones dentro del mismo grupo de hosts, y su fuente será una acumulación de ambos grupos de aplicaciones.

## <a name="tenant-groups"></a>Grupos de inquilinos

En Azure Virtual Desktop, el inquilino de Azure Virtual Desktop es donde ocurre la mayor parte de la configuración e instalación. El inquilino de Azure Virtual Desktop contiene los grupos de hosts, los grupos de aplicaciones y las asignaciones de usuarios del grupo de aplicaciones. Sin embargo, puede haber ciertas situaciones en las que necesite administrar varios inquilinos de Azure Virtual Desktop a la vez, especialmente si es un proveedor de servicios en la nube (CSP) o un asociado de hospedaje. En estas situaciones, puede usar un grupo personalizado de inquilinos de Azure Virtual Desktop para ubicar a cada uno de los inquilinos de Azure Virtual Desktop de los clientes y administrar el acceso de forma centralizada. Sin embargo, si solo está administrando un único inquilino de Azure Virtual Desktop, el concepto de grupo de inquilinos no se aplica y puede continuar supervisando y administrando el inquilino que existe en el grupo de inquilinos predeterminado.

## <a name="end-users"></a>Usuarios finales

Después de asignar usuarios a sus grupos de aplicaciones, pueden conectarse a una implementación de Azure Virtual Desktop con cualquiera de los clientes de Azure Virtual Desktop.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre el acceso delegado y cómo asignar roles a los usuarios en [Acceso delegado en Azure Virtual Desktop](delegated-access-virtual-desktop-2019.md).

Para información sobre cómo configurar el inquilino de Azure Virtual Desktop, consulte [Creación de un inquilino en Azure Virtual Desktop](tenant-setup-azure-active-directory.md).

Para información sobre cómo conectarse a Azure Virtual Desktop, consulte uno de los artículos siguientes:

- [Conexión desde Windows 10 o Windows 7](connect-windows-7-10-2019.md)
- [Conexión desde un explorador web](connect-web-2019.md)
