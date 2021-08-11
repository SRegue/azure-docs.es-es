---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/16/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 69de67169f030e129550b730e23f1addb0b85024
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114388792"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[El servicio API Management debe usar una SKU que admita redes virtuales](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |Con las SKU compatibles de API Management, el servicio de implementación en una red virtual desbloquea las características avanzadas de seguridad y redes de API Management, lo que proporciona un mayor control sobre la configuración de seguridad de red. Más información en: [https://aka.ms/apimvnet](../../../../articles/api-management/api-management-using-with-vnet.md). |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[Los servicios de API Management deben usar una red virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |La implementación de Azure Virtual Network ofrece una seguridad y aislamiento mejorados, y permite colocar el servicio de API Management en una red enrutable sin conexión a Internet cuyo acceso puede controlar. Estas redes se pueden conectar a las redes locales mediante diversas tecnologías de VPN, lo que permite el acceso a los servicios de back-end dentro de la red o de forma local. El portal para desarrolladores y la puerta de enlace de API pueden configurarse para que sea accesible desde Internet o solo dentro de la red virtual. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |