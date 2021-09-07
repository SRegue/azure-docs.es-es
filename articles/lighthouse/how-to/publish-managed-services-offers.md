---
title: Publicación de una oferta de servicio administrado en Azure Marketplace
description: Aprenda a publicar una oferta de servicio administrado que incorpore clientes a Azure Lighthouse.
ms.date: 08/10/2021
ms.topic: how-to
ms.openlocfilehash: af5ca37d312f5bdfcfae179997b920a466f01462
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742187"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publicación de una oferta de servicio administrado en Azure Marketplace

En este artículo, aprenderá a publicar una oferta de servicio administrado pública o privada en [Azure Marketplace](https://azuremarketplace.microsoft.com) con el programa [Marketplace comercial](../../marketplace/overview.md) del Centro de partners. Los clientes que compran la oferta delegarán suscripciones o grupos de recursos, lo que le permitirá administrarlas desde [Azure Lighthouse](../overview.md).

## <a name="publishing-requirements"></a>Requisitos de publicación

Debe tener una [cuenta en el Centro de partners](../../marketplace/create-account.md) valida para crear y publicar ofertas. Si aún no tiene una cuenta, el [proceso de registro](https://aka.ms/joinmarketplace) le guiará por los pasos necesarios para crear una cuenta en el Centro de partners e inscribirse en el programa comercial de Marketplace.

Según los [Requisitos de certificación de la oferta de servicio administrado](/legal/marketplace/certification-policies#700-managed-services), debe tener un [nivel de competencia de plataforma en la nube Silver o Gold](/partner-center/learn-about-competencies) o ser un [MSP experto de Azure](https://partner.microsoft.com/membership/azure-expert-msp) para publicar una oferta de servicio administrado. También debe [escribir un destino de clientes potenciales que creará un registro en el sistema CRM](../../marketplace/plan-managed-service-offer.md#customer-leads) cada vez que un cliente implemente la oferta.

Si no quiere publicar una oferta en Azure Marketplace o no cumple todos los requisitos, puede incorporar clientes manualmente mediante plantillas de Azure Resource Manager. Para obtener más información, consulte [Incorporación de un cliente a Azure Lighthouse](onboard-customer.md).

La tabla siguiente puede ayudar a determinar si se deben incorporar clientes mediante la publicación de una oferta de servicio administrada o usando plantillas de Azure Resource Manager.

|**Consideración**  |**Oferta de servicio administrado**  |**Plantillas de ARM**  |
|---------|---------|---------|
|Se necesita una [cuenta del Centro de partners](../../marketplace/create-account.md).   |Sí         |No        |
|Se necesita un [nivel de competencia de la plataforma de nube Silver o Gold](/partner-center/learn-about-competencies) o un [MSP experto de Azure](https://partner.microsoft.com/membership/azure-expert-msp).      |Sí         |No         |
|Disponible para nuevos clientes mediante Azure Marketplace     |Sí     |No       |
|Se puede limitar la oferta a clientes específicos     |Sí (solo con ofertas privadas, que no son compatibles con las suscripciones que se establecen a través de un revendedor del programa Proveedor de soluciones en la nube [CSP]).         |Sí         |
|Se requiere la aceptación del cliente en Azure Portal     |Sí     |No   |
|Se puede usar automatización para incorporar varias suscripciones, grupos de recursos o clientes |No     |Sí    |
|Acceso inmediato a nuevos roles integrados y características de Azure Lighthouse     |No siempre (disponible generalmente después de un pequeño retraso)         |Sí         |
|Los clientes pueden revisar y aceptar las ofertas actualizadas en Azure Portal | Sí | No |

> [!NOTE]
> Es posible que las ofertas de servicios administrados no estén disponibles en Azure Government y otras nubes nacionales.

## <a name="create-your-offer"></a>Creación de la oferta

Para instrucciones detalladas sobre cómo crear la oferta, incluida toda la información y los recursos que deberá proporcionar, consulte [Creación de una oferta de servicio administrado](../../marketplace/create-managed-service-offer.md).

Para más información sobre el proceso de publicación general, consulte la [documentación de Marketplace comercial](../../marketplace/overview.md). También debe revisar [las directivas de certificación de Marketplace comercial](/legal/marketplace/certification-policies), en especial la sección [Servicios administrados](/legal/marketplace/certification-policies#700-managed-services).

Una vez que un cliente agregue su oferta, podrá delegar uno o varios grupos de recursos o suscripciones, que, posteriormente, se [incorporarán a Azure Lighthouse](#the-customer-onboarding-process).

> [!IMPORTANT]
> Cada plan de una oferta de servicio administrado incluye la sección **Detalles del manifiesto**, donde se definen las entidades de Azure Active Directory (Azure AD) del inquilino que tendrán acceso a las suscripciones o los grupos de recursos delegados para los clientes que adquieran el plan. Es importante tener en cuenta que cualquier grupo (o entidad de servicio o usuario) que incluya tendrá los mismos permisos para todos los clientes que compren el plan. Para asignar grupos diferentes para que trabajen con cada cliente, puede publicar un [plan privado](../../marketplace/private-offers.md) independiente que sea exclusivo para cada cliente. Tenga en cuenta que los planes privados no son compatibles con las suscripciones que se establecen a través de un revendedor del programa Proveedor de soluciones en la nube (CSP).

## <a name="publish-your-offer"></a>Publicación de la oferta

Cuando haya completado todas las secciones, el siguiente paso es publicar la oferta en Azure Marketplace. Seleccione el botón **Publicar** para iniciar el proceso de publicación de la oferta. [Aquí](../../marketplace/review-publish-offer.md) puede encontrar más información sobre este proceso.

Puede [publicar una versión actualizada de la oferta](../../marketplace/update-existing-offer.md) en cualquier momento. Por ejemplo, puede querer agregar una nueva definición de roles a una oferta publicada previamente. Al hacerlo, los clientes que ya hayan agregado la oferta verán un icono en la página [**Proveedores de servicios**](view-manage-service-providers.md) de Azure Portal que les informa que hay disponible una actualización. Cada cliente podrá [revisar los cambios](view-manage-service-providers.md#update-service-provider-offers) y decidir si desea actualizar a la nueva versión. 

## <a name="the-customer-onboarding-process"></a>Proceso de incorporación del cliente

Una vez que un cliente agregue su oferta, podrá [delegar uno o varios grupos de recursos o suscripciones concretos](view-manage-service-providers.md#delegate-resources) que, posteriormente, se incorporarán a Azure Lighthouse. Si un cliente ha aceptado una oferta pero aún no ha delegado los recursos, verá una nota en la parte superior de la sección **Ofertas de proveedor** de la página [**Service providers**](view-manage-service-providers.md) (Proveedores de servicio) en Azure Portal.

> [!IMPORTANT]
> La delegación debe realizarse desde una cuenta que no sea de invitado en el inquilino del cliente, que tenga el rol con el permiso `Microsoft.Authorization/roleAssignments/write`, como [Propietario](../../role-based-access-control/built-in-roles.md#owner), para la suscripción que se va a incorporar (o que contenga los grupos de recursos que se están incorporando). Para buscar usuarios que puedan delegar la suscripción, un usuario del inquilino del cliente puede seleccionar la suscripción en Azure Portal, abrir **Control de acceso (IAM)** y [ver todos los usuarios con el rol Propietario](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Una vez que el cliente delega una suscripción (o uno o varios grupos de recursos dentro de una suscripción), el proveedor de recursos **Microsoft.ManagedServices** se registrará para esa suscripción y los usuarios del inquilino podrán acceder a los recursos delegados según las autorizaciones de la oferta.

> [!NOTE]
> Para delegar suscripciones o grupos de recursos adicionales en la misma oferta más adelante, el cliente deberá [registrar manualmente el proveedor de recursos **Microsoft.ManagedServices**](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) en cada suscripción antes de delegar.

Si publica una versión actualizada de la oferta, el cliente puede [revisar los cambios en Azure Portal y aceptar la nueva versión](view-manage-service-providers.md#update-service-provider-offers).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [Marketplace comercial](../../marketplace/overview.md).
- [Vincule el identificador de asociado](partner-earned-credit.md) para realizar un seguimiento de su impacto en la involucración de los clientes.
- Más información sobre las [experiencias de administración entre inquilinos](../concepts/cross-tenant-management-experience.md).
- Puede [ver y administrar clientes](view-manage-customers.md) desde **Mis clientes**, en Azure Portal.