---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8c559cd62092d3235d27a94c40cc39457b18a82e
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123474811"
---
|Nombre |Descripción |Directivas |Versión |
|---|---|---|---|
|[Auditar las máquinas que tengan una configuración de seguridad de contraseña no segura](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Esta iniciativa implementa los requisitos de la directiva y audita las máquinas que cuenten con una configuración de seguridad de contraseña no segura. Para más información sobre las directivas de configuración de invitados, visite [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md) |9 |1.0.0 |
|[Implementar los requisitos previos para habilitar directivas de configuración de invitado en máquinas virtuales](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Esta iniciativa agrega una identidad administrada asignada por el sistema e implementa la extensión de configuración de invitado adecuada para la plataforma en las máquinas virtuales que se puedan supervisar mediante directivas de configuración de invitado. Este es un requisito previo para todas las directivas de configuración de invitado y se debe agregar al ámbito de asignación de directivas antes de usar cualquier directiva de configuración de invitado. Para más información sobre la configuración de invitado, visite [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md). |4 |1.0.0 |
|[\[Versión preliminar\]: las máquinas Windows deben cumplir los requisitos de línea base de seguridad de Azure Compute](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Esta iniciativa audita las máquinas Windows con una configuración que no cumple la línea base de seguridad de Azure. Para obtener detalles, consulte: [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md) |29 |2.0.1-preview |