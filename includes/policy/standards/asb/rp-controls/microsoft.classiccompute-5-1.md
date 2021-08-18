---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/13/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ad51b1fd29521f7143f3f5bf8bdafde19f3833e3
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122262321"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Debe habilitarse una solución de evaluación de vulnerabilidades en sus máquinas virtuales](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Audita las máquinas virtuales para detectar si ejecutan una solución de evaluación de vulnerabilidades admitida. Un componente fundamental de cada programa de seguridad y riesgo cibernético es la identificación y el análisis de las vulnerabilidades. El plan de tarifa estándar de Azure Security Center incluye el análisis de vulnerabilidades de las máquinas virtuales sin costo adicional. Además, Security Center puede implementar automáticamente esta herramienta. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
