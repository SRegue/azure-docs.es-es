---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a79aca306c86432f466071ce7042ec2f01dc3bfe
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123103741"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[El punto de conexión de Bot Service debe ser un URI de HTTPS válido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Los datos se pueden alterar durante la transmisión. Hay protocolos que proporcionan cifrado para solucionar problemas de uso indebido y manipulación. Para asegurarse de que los bots se comunican solo por canales cifrados, establezca el punto de conexión en un URI de HTTPS válido. Esto garantiza que se usa el protocolo HTTPS para cifrar los datos en tránsito; además, suele ser un requisito de cumplimiento de los estándares normativos o del sector. Visite: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](/azure/bot-service/bot-builder-security-guidelines). |audit, deny, disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Bot Service se debe cifrar con una clave administrada por el cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service cifra automáticamente el recurso para proteger sus datos y satisfacer los compromisos de cumplimiento y seguridad de la organización. De forma predeterminada, se usan claves de cifrado administradas por Microsoft. Para una mayor flexibilidad en la administración de claves o el control del acceso a su suscripción, seleccione claves administradas por el cliente, también conocidas como Bring your own key (BYOK). Más información acerca del cifrado de Azure Bot Service: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](/azure/bot-service/bot-service-encryption). |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
|[Bot Service debe tener el modo aislado habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52152f42-0dda-40d9-976e-abb1acdd611e) |Los bots deben establecerse en el modo "solo aislado". Este valor configura los canales de Bot Service que requieren que se deshabilite el tráfico a través de la red pública de Internet. |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_NetworkIsolatedEnabled_Audit.json) |
|[Bot Service debe tener deshabilitados los métodos de autenticación local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fffea632e-4e3a-4424-bf78-10e179bb2e1a) |Deshabilitar los métodos de autenticación local mejora la seguridad al garantizar que un bot use exclusivamente AAD para la autenticación. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_DisableLocalAuth_Audit.json) |