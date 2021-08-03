---
title: Logro de los niveles de seguridad del autenticador de NIST con Azure Active Directory
description: Una introducción a la configuración de
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: ''
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 500b22546089335563fd12953414aa74612cedf3
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2021
ms.locfileid: "110537076"
---
# <a name="configure-azure-active-directory-to-meet-nist-authenticator-assurance-levels"></a>Configure Azure Active Directory para cumplir los niveles de seguridad del autenticador de NIST.

La provisión de servicios para agencias federales es complicada por el número y la complejidad de los estándares que se deben cumplir. Como proveedor de servicios en la nube (CSP) o agencia federal, es responsabilidad suya garantizar el cumplimiento de todos los estándares pertinentes. Azure y Azure Active Directory (Azure AD) facilitan esta tarea, ya que permiten usar nuestras certificaciones y, luego, configurar sus requisitos específicos.

Azure está certificado para más de 90 ofertas de cumplimiento en el momento de redactar este artículo. Para más información, consulte [Confíe en su nube](https://azure.microsoft.com/overview/trusted-cloud/).

## <a name="why-meet-nist-standards"></a>¿Por qué es necesario cumplir los estándares de NIST? 

El Instituto Nacional de Estándares y Tecnología (NIST, por sus siglas en inglés) desarrolla los requisitos técnicos de las agencias federales de Estados Unidos que implementan soluciones de identidad. Las organizaciones que trabajan con agencias federales también deben cumplir estos requisitos. Para obtener más información sobre los requisitos de identidad de NIST, vea la [Publicación especial 800-63, revisión 3](https://pages.nist.gov/800-63-3/sp800-63-3.html) (NIST SP 800-63-3).

También se hace referencia a NIST SP 800-63 en:
* El programa Electronic Prescription of Controlled Substances [EPCS](https://deadiversion.usdoj.gov/ecomm/e_rx/). 
* En los [requisitos de Financial Industry Regulatory Authority](https://www.finra.org/rules-guidance). 
* En las asociaciones del sector sanitario, de defensa y otras del sector, que suelen usar NIST SP 800-63-3 como línea de base para los requisitos de administración de identidades y acceso.

Se hace referencia a las directrices del NIST en otros estándares, especialmente en el Federal Risk and Authorization Management Program (FedRAMP) para los CSP. Azure está certificado para el nivel de impacto de FedRAMP High. 

Las directrices de identidad digital de NIST abarcan la prueba y la autenticación de usuarios, como empleados, asociados, proveedores y clientes o ciudadanos. 

Las directrices de identidad digital de NIST SP 800-63-3 abarcan tres áreas:

* [SP 800-63A](https://pages.nist.gov/800-63-3/sp800-63a.html) abarca la revisión de inscripción e identidad.

* [SP 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html) abarca la administración del ciclo de vida y la autenticación.

* [SP 800-63C](https://pages.nist.gov/800-63-3/sp800-63c.html) abarca la federación y las aserciones.

Cada área tiene asignados niveles de seguridad. En este conjunto de artículos se proporcionan instrucciones para alcanzar los niveles de seguridad del autenticador (AAL) en NIST SP 800-63B mediante Azure AD y otras soluciones de Microsoft.

## <a name="next-steps"></a>Pasos siguientes 

[Más información sobre los AAL](nist-about-authenticator-assurance-levels.md)

[Conceptos básicos sobre autenticación](nist-authentication-basics.md)

[Tipos de autenticadores de NIST](nist-authenticator-types.md)

[Alcanzar el nivel 1 de seguridad del autenticador de NIST con Azure Active Directory](nist-authenticator-assurance-level-1.md)

[Logro del nivel 2 de garantía del autenticador de NIST con Azure Active Directory](nist-authenticator-assurance-level-2.md)

[Alcanzar el nivel 3 de garantía del autenticador de NIST con Azure Active Directory](nist-authenticator-assurance-level-3.md) 
