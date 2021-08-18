---
title: Descripción del inicio de sesión único basado en OIDC
titleSuffix: Azure AD
description: Descripción del inicio de sesión único (SSO) basado en OIDC para aplicaciones en Azure Active Directory
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/19/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4cca31c844d4955df69a5fbdca450b67fd0f8bc9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738813"
---
# <a name="understand-oidc-based-single-sign-on"></a>Descripción del inicio de sesión único basado en OIDC

En la [serie de guías de inicio rápido](view-applications-portal.md) sobre la administración de aplicaciones, aprendió a usar Azure AD como proveedor de identidades (IdP) para una aplicación. En este artículo se detallan más exhaustivamente las aplicaciones que usan el estándar OpenID Connect para implementar el inicio de sesión único.

## <a name="before-you-begin"></a>Antes de empezar

El proceso de agregar una aplicación al inquilino de Azure Active Directory depende del tipo de inicio de sesión único de la aplicación implementado. Para obtener más información sobre las opciones de inicio de sesión único disponibles para las aplicaciones que pueden usar Azure AD para la administración de identidades, consulte [Opciones de inicio de sesión único](sso-options.md). En este artículo se tratan las aplicaciones basadas en OIDC.

## <a name="basic-oidc-configuration"></a>Configuración básica de OIDC

En la [serie de guías de inicio rápido](add-application-portal-setup-oidc-sso.md), hay un artículo sobre la configuración del inicio de sesión único. En él, aprenderá a agregar una aplicación basada en OIDC al inquilino de Azure.

Lo bueno de agregar una aplicación que usa el estándar OIDC para el inicio de sesión único es que la configuración es mínima. Este es un breve vídeo en el que se muestra cómo agregar una aplicación basada en OIDC al inquilino.

Adición de una aplicación basada en OIDC en Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Para más información sobre el consentimiento del usuario y del administrador, consulte [Consentimiento del usuario y del administrador](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Pasos siguientes

- [Serie de guías de inicio rápido sobre la administración de aplicaciones](add-application-portal-setup-oidc-sso.md)
- [Opciones de inicio de sesión único](sso-options.md)
- [Cómo: Inicio de sesión de cualquier usuario de Azure Active Directory mediante el patrón de aplicación multiinquilino](../develop/howto-convert-app-to-be-multi-tenant.md)
