---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: cf0ec65f03efb913cfcc33441d829b0efbbbc5d9
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123477471"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La definición de aplicación para la aplicación administrada debe usar la cuenta de almacenamiento proporcionada por el cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633). |Use su propia cuenta de almacenamiento para controlar los datos de definición de la aplicación cuando se trate de un requisito de cumplimiento normativo. Puede optar por almacenar la definición de aplicación administrada en la cuenta de almacenamiento que ha proporcionado durante la creación para que pueda administrar totalmente su ubicación y acceso para cumplir con los requisitos de cumplimiento normativo. |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Implementación de asociaciones para aplicaciones administradas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Implementa un recurso de asociación que asocia los tipos de recursos seleccionados con la aplicación administrada especificada.  Esta implementación de directiva no admite los tipos de recursos anidados. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
