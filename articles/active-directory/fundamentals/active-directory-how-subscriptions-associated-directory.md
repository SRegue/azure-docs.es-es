---
title: 'Incorporación de una suscripción de Azure existente al inquilino: Azure AD'
description: Instrucciones sobre cómo agregar una suscripción de Azure existente al inquilino de Azure Active Directory (Azure AD).
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc428a79e38f390d926f268bd8147eb7ee06a648
ms.sourcegitcommit: 04d2dec0316576388e6e10c1558b0a0b3688d01c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112421841"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Asociación o incorporación de una suscripción de Azure al inquilino de Azure Active Directory

Una suscripción de Azure tiene una relación de confianza con Azure Active Directory (Azure AD). Una suscripción confía en Azure AD para autenticar usuarios, servicios y dispositivos.

Varias suscripciones pueden confiar en el mismo directorio de Azure AD. Cada suscripción solo puede confiar en un único directorio.

Una o varias suscripciones de Azure pueden establecer una relación de confianza con una instancia de Azure Active Directory (Azure AD) para autenticar y autorizar las entidades de seguridad y los dispositivos en los servicios de Azure.  Cuando expira una suscripción, la instancia de confianza del servicio Azure AD se conserva, pero las entidades de seguridad pierden el acceso a los recursos de Azure.

Cuando un usuario se suscribe a un servicio en la nube de Microsoft, se crea un nuevo inquilino de Azure AD y el usuario se convierte en miembro del rol de administrador global. Sin embargo, cuando un propietario de una suscripción une su suscripción a un inquilino existente, el propietario no se asigna al rol de administrador global.

Todos los usuarios tienen un único directorio *particular* para la autenticación. Los usuarios también pueden ser invitados en otros directorios. Puede ver los directorios principales e invitados para cada usuario en Azure AD.

:::image type="content" source="media/active-directory-how-subscriptions-associated-directory/trust-relationship-azure-ad.png" alt-text="Captura de pantalla en la que se muestra la relación de confianza entre las suscripciones de Azure y los directorios activos de Azure.":::

> [!Important]
> Al asociar una suscripción a otro directorio, los usuarios que tienen roles asignados mediante el [control de acceso basado en rol de Azure](../../role-based-access-control/role-assignments-portal.md) pierden el acceso. Los administradores de suscripciones clásicas, incluidos el administrador y los coadministradores del servicio, también pierden el acceso.
>
> El traslado del clúster de Azure Kubernetes Service (AKS) a otra suscripción o el traslado de la suscripción propietaria del clúster a un nuevo inquilino, provoca que el clúster pierda funcionalidad debido a la pérdida de asignaciones de roles y derechos de las entidades de servicio. Para obtener más información sobre AKS, consulte [Azure Kubernetes Service (AKS)](../../aks/index.yml).

## <a name="before-you-begin"></a>Antes de empezar

Antes de poder asociar o agregar la suscripción, realice las siguientes tareas:

- Revise la siguiente lista de cambios que se producirán después de asociar o agregar su suscripción e infórmese sobre cómo podría verse afectado:

  - Los usuarios a los que se haya asignado roles mediante Azure RBAC perderán el acceso.
  - Tanto el administrador como los coadministradores del servicio perderán el acceso.
  - Si tiene almacenes de claves, no se podrá acceder a ellos y tendrá que repararlos después de la asociación.
  - Si tiene identidades administradas para recursos, como Virtual Machines o Logic Apps, debe volver a habilitarlas o a crearlas después de la asociación.
  - Si tiene una instancia de Azure Stack registrada, tendrá que volver a registrarla después de la asociación.
  - Para más información, consulte [Transferencia de una suscripción de Azure a otro directorio de Azure AD](../../role-based-access-control/transfer-subscription.md).

- Iniciar sesión con una cuenta que:

  - Tiene una asignación de rol [Propietario](../../role-based-access-control/built-in-roles.md#owner) para la suscripción. Para más información sobre la asignación del rol Propietario, consulte [Asignaciones de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md).
  - Exista en el directorio actual y en el nuevo directorio. El directorio actual está asociado a la suscripción. Va a asociar el nuevo directorio a la suscripción. Para más información sobre cómo obtener acceso a otro directorio, consulte [Adición de usuarios de colaboración B2B de Azure Active Directory en Azure Portal](../external-identities/add-users-administrator.md).

- Asegúrese de que no usa una suscripción de proveedores de servicios en la nube de Azure (CSP) (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), una suscripción interna de Microsoft (MS-AZR-0015P) ni una suscripción a Microsoft Azure for Students Starter (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Asociación de una suscripción a un directorio<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Para asociar una suscripción existente al directorio de Azure AD, siga estos pasos:

1. Inicie sesión y seleccione la suscripción que quiere usar en la [Página de suscripciones de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Seleccione **Cambiar directorio**.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png" alt-text="Captura de pantalla que muestra la página Suscripciones, con la opción Cambiar directorio resaltada.":::

1. Revise las advertencias que aparecen y, a continuación, seleccione **Cambiar**.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png" alt-text="Captura de pantalla que muestra la página Cambiar el directorio con un directorio de ejemplo y el botón Cambiar resaltado.":::

   Una vez que se cambie el directorio para la suscripción, se mostrará un mensaje de confirmación.

1. Seleccione **Cambiar directorios** en la página suscripción para ir al nuevo directorio.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/directory-switcher.png" alt-text="Captura de pantalla que muestra la página del conmutador de directorio con información de ejemplo.":::

   Puede tardar varias horas hasta que todo se muestre correctamente. Si parece que tarda demasiado tiempo, compruebe el **filtro de suscripción global**. Asegúrese de que la suscripción que se ha trasladado no esté oculta. Es posible que tenga que cerrar la sesión de Azure Portal y volver a iniciarla para ver el directorio nuevo.

Cambiar el directorio de suscripción es una operación de nivel de servicio, por lo que no afecta a la propiedad de facturación de suscripción. Para eliminar el directorio original, debe transferir la propiedad de facturación de suscripción a un nuevo administrador de cuenta. Para más información acerca de cómo transferir la propiedad de facturación, vea [Transferencia de la propiedad de una suscripción de Azure a otra cuenta](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Pasos posteriores a la asociación

Después de asociar una suscripción a un directorio diferente, puede que tenga que realizar las siguientes tareas para reanudar las operaciones:

- Si tiene almacenes de claves, debe cambiar el Id. de inquilino del almacén de claves. Para más información, consulte [Cambio del identificador de inquilino de Key Vault después de mover una suscripción](../../key-vault/general/move-subscription.md).

- Si usaba identidades administradas asignadas por el sistema para los recursos, debe volver a habilitar estas identidades. Si usaba identidades administradas asignadas por el usuario, debe volver a crear estas identidades. Después de volver a habilitar o crear las identidades administradas, debe volver a restablecer los permisos asignados a esas identidades. Para más información, consulte [¿Qué es Managed Identities for Azure Resources?](../managed-identities-azure-resources/overview.md)

- Si ha registrado una instancia de Azure Stack que usa esta suscripción, debe volver a registrarla. Para más información, consulte [Registro de Azure Stack Hub con Azure](/azure-stack/operator/azure-stack-registration).

- Para más información, consulte [Transferencia de una suscripción de Azure a otro directorio de Azure AD](../../role-based-access-control/transfer-subscription.md).

## <a name="next-steps"></a>Pasos siguientes

- Para crear un nuevo inquilino de Azure AD, consulte [Inicio rápido: Creación de un inquilino en Azure Active Directory](active-directory-access-create-new-tenant.md).

- Para más información sobre cómo controla Microsoft Azure el acceso a los recursos, consulte [Roles de administrador de la suscripción clásica, roles de Azure y roles de administrador de Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Para más información sobre cómo asignar roles en Azure AD, consulte [Asignación de roles de administrador y no administrador a los usuarios con Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
