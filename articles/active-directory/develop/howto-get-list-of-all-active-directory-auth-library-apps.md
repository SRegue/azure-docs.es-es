---
title: 'Procedimiento: obtención de una lista completa de todas las aplicaciones que usan la Biblioteca de autenticación de Active Directory (ADAL) en el inquilino | Azure'
titleSuffix: Microsoft identity platform
description: En esta guía paso a paso, se obtiene una lista completa de todas las aplicaciones que usan ADAL en el inquilino.
services: active-directory
author: SHERMANOUKO
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/22/2021
ms.author: shermanouko
ms.custom: aaddev
ms.reviewer: aiwang, marsma
ms.openlocfilehash: 7f4dfaaaaae39245c54c8ee3fc7879fd58b8ea9e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725756"
---
# <a name="how-to-get-a-complete-list-of-apps-using-adal-in-your-tenant"></a>Procedimiento: obtención de una lista completa de aplicaciones que usan ADAL en el inquilino

La compatibilidad con la Biblioteca de autenticación de Active Directory (ADAL) finalizará el 30 de junio de 2022. Las aplicaciones que usan ADAL en las versiones existentes del sistema operativo seguirán funcionando, pero no recibirán soporte técnico ni actualizaciones de seguridad. Sin actualizaciones de seguridad continuadas, las aplicaciones que usan ADAL se volverán cada vez más vulnerables a los patrones de ataque de seguridad más recientes. En este artículo se proporcionan instrucciones sobre cómo usar libros de Azure Monitor para obtener una lista de todas las aplicaciones que usan ADAL en el inquilino.

## <a name="sign-ins-workbook"></a>Libro de inicios de sesión

Los libros son un conjunto de consultas que recopilan y visualizan información que está disponible en los registros de Azure Active Directory (Azure AD). [Obtenga más información sobre el esquema de registros de inicio de sesión aquí](../reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md). El libro Inicios de sesión del portal de administración de Azure AD ahora tiene una tabla que le ayudará a determinar qué aplicaciones usan ADAL y con qué frecuencia se usan. En primer lugar, detallaremos cómo acceder al libro antes de mostrar la visualización de la lista de aplicaciones.

## <a name="step-1-integrate-audit-logs-with-azure-monitor"></a>Paso 1: Integración de registros de auditoría con Azure Monitor

Siga los pasos descritos en [Integración de registros de inicio de sesión y auditoría de Azure AD con Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) antes de acceder al libro. Solo se almacenarán los eventos de inicio de sesión y auditoría creados después de la integración de Azure Monitor.

## <a name="step-2-access-sign-ins-workbook-in-azure-portal"></a>Paso 2: Acceso al libro de inicios de sesión en Azure Portal

Una vez que haya integrado los registros de inicio de sesión y auditoría de Azure AD con Azure Monitor como se especifica en la integración de Azure Monitor, acceda al libro de inicios de sesión:

   1. Inicie sesión en Azure Portal. 
   1. Vaya a  **Azure Active Directory** > **Supervisión** > **Libros**. 
   1. En la sección **Uso**, abra el libro  **Inicios de sesión**. 

   :::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/sign-in-workbook.png" alt-text="Captura de pantalla de la interfaz de libros del portal de Azure Active Directory con el libro de inicios de sesión resaltado.":::

## <a name="step-3-identify-apps-that-use-adal"></a>Paso 3: Identificación de las aplicaciones que usan ADAL

En la tabla de la parte inferior de la página del libro Inicios de sesión se enumeran las aplicaciones que han usado ADAL recientemente. También puede exportar una lista de las aplicaciones. Actualice estas aplicaciones para usar MSAL.
    
:::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/active-directory-auth-library-apps-present.png" alt-text="Captura de pantalla del libro de inicios de sesión que muestra las aplicaciones que usan la Biblioteca de autenticación de Active Directory.":::
    
Si no hay aplicaciones que usan ADAL, el libro mostrará una vista como la siguiente. 
    
:::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/no-active-directory-auth-library-apps.png" alt-text="Captura de pantalla del libro de inicios de sesión cuando ninguna aplicación usa la Biblioteca de autenticación de Active Directory.":::

## <a name="step-4-update-your-code"></a>Paso 4: Actualización del código

Después de identificar las aplicaciones que usan ADAL, mígrelas a MSAL en función del tipo de aplicación, como se muestra a continuación.

[!INCLUDE [application type](includes/adal-msal-migration.md)]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre MSAL, incluida la información de uso y las bibliotecas disponibles para diferentes lenguajes de programación y tipos de aplicación, consulte:

- [Adquisición y almacenamiento en caché de tokens con MSAL](msal-acquire-cache-tokens.md)
- [Opciones de configuración de aplicaciones](msal-client-application-configuration.md)
- [Lista de bibliotecas de autenticación de MSAL](reference-v2-libraries.md)
