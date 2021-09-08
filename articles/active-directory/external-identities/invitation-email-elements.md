---
title: 'Elementos del correo electrónico de invitación de B2B: Azure Active Directory | Microsoft Docs'
description: Plantilla de correo electrónico de invitación de colaboración B2B de Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 324b37931fff91779b8d25c744ae63671bae315c
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113224317"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementos del correo electrónico de invitación para la colaboración B2B: Azure Active Directory

Los correos electrónicos de invitación son un componente fundamental para incorporar a los asociados como usuarios de colaboración B2B en Azure AD. Si bien [no es necesario que envíe un correo electrónico para invitar a alguien a usar la colaboración B2B](add-user-without-invite.md), al hacerlo, se le proporciona al usuario toda la información que necesita para tomar una decisión sobre si aceptar la invitación. También le proporciona un vínculo al que siempre puede hacer referencia en el futuro cuando necesiten volver a los recursos.

![Captura de pantalla que muestra el correo electrónico de invitación de B2B](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Explicación del correo electrónico

Se van a tratar algunos elementos del correo electrónico para saber cómo hacer el mejor uso de estas funcionalidades.

### <a name="subject"></a>Asunto

El asunto del correo electrónico sigue este patrón:

&lt;username&gt; lo ha invitado a acceder a las aplicaciones de su organización.

### <a name="from-address"></a>Dirección De

Se usa un patrón similar a LinkedIn para la dirección De. Este patrón debe aclarar que, aunque el correo electrónico procede de invites@microsoft.com, la invitación viene de otra organización. El formato es: Microsoft Invitations <invites@microsoft.com> o invitaciones de Microsoft en nombre de &lt;tenantname&gt; <invites@microsoft.com>. 

> [!NOTE]
> Para el servicio de Azure operado por 21Vianet en China, la dirección del remitente es Invites@oe.21vianet.com.  
> En el caso de Azure AD Government, la dirección del remitente es invites@azuread.us.

### <a name="reply-to"></a>Responder a

En la respuesta al correo electrónico se indica el correo electrónico del invitador si está disponible, para que al responder al correo electrónico se vuelva a enviar un correo al invitador.

### <a name="phishing-warning"></a>Advertencia de suplantación de identidad (phishing)

El correo electrónico comienza con una breve advertencia para el usuario sobre la suplantación de identidad (phishing) y lo alerta de que solo debe aceptar las invitaciones que espera. Es recomendable asegurarse de que los asociados a los que está invitando no se vean sorprendidos por su invitación, así es que es bueno que lo mencione con tiempo.

![Imagen de la advertencia de suplantación de identidad (phishing) en el correo electrónico](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information-and-invitation-message"></a>Información del invitador y mensaje de invitación

El correo electrónico incluye el nombre y el dominio principal asociados a la organización que envía la invitación. Esta información debe ayudar al invitado a tomar una decisión fundamentada sobre la aceptación de la invitación. Si el invitador incluye un mensaje como parte de su invitación cuando [invita a un usuario invitado al directorio, grupo o aplicación](add-users-administrator.md) o cuando [usa la API de invitación](customize-invitation-api.md), el mensaje aparece resaltado en la sección principal del correo electrónico. También se incluye el nombre y la imagen de perfil del invitador, si se estableció. El mensaje mismo es un área de texto por lo que, por motivos de seguridad, no procesa etiquetas HTML.

![Imagen del mensaje de invitación en el correo electrónico](media/invitation-email-elements/invitation-message-inviters-info.png)

### <a name="accept-button-and-redirect-url"></a>Botón Aceptar y URL de redireccionamiento

La sección siguiente del correo electrónico contiene información sobre adónde irá el invitado después de aceptar la invitación, así como un botón para hacerlo.  En el futuro, el invitado siempre podrá usar este vínculo para volver directamente a los recursos.

![Imagen del botón Aceptar y la URL de redireccionamiento en el correo electrónico](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Sección de pie de página

El pie de página contiene más información sobre la invitación que se envía. Siempre debe haber una opción para que el invitado bloquee las invitaciones futuras. Si la organización [estableció una declaración de privacidad](../fundamentals/active-directory-properties-area.md), aquí debe aparecer el vínculo a dicha declaración.  De lo contrario, una nota indica que la organización no ha establecido una declaración de privacidad.

![Imagen de la sección de pie de página del correo electrónico](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>Bloqueo de una organización (cancelación de suscripción)

En la invitación de una organización, el pie de página contiene una opción para **bloquear las invitaciones futuras**. Un usuario invitado puede seleccionar este vínculo para bloquear cualquier invitación futura de la organización. Esta acción también agrega la organización a la lista de suscripciones canceladas del usuario en [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage).

### <a name="viewing-organizations-youve-blocked"></a>Visualización de las organizaciones que ha bloqueado

Un usuario invitado puede seguir estos pasos para ver o exportar las organizaciones que ha bloqueado:

1. Vaya a [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage).
2. Escriba su correo electrónico y siga los pasos de inicio de sesión para la autenticación de código de acceso de un solo uso de correo electrónico.
3. Puede ver las organizaciones que ha bloqueado o exportar los nombres mediante copiar y pegar.
   > [!NOTE]
   > Si desea permitir que una organización que ha bloqueado le invite de nuevo, puede elegir la organización y seleccionar **Siguiente**.

## <a name="how-the-language-is-determined"></a>Cómo se determina el idioma

Las opciones siguientes determinan el idioma que el usuario invitado ve en el correo electrónico de invitación. Estas opciones de configuración se muestran en el siguiente orden de prioridad. Si una opción no está configurada, la siguiente de la lista será la que determine el idioma.

- La propiedad **messageLanguage** del objeto [guestUserMessageInfo](/graph/api/resources/invitedusermessageinfo), si se usa la API para crear una invitación.
-   La propiedad **preferredLanguage** especificada en el [objeto de usuario](/graph/api/resources/user) del invitado.
-   El **idioma de notificación** establecido en las propiedades del inquilino principal del usuario invitado (solo para inquilinos de Azure AD).
-   El **idioma de notificación** establecido en las propiedades del inquilino del recurso.

Si ninguna de estas opciones está configurada, el idioma se establece de forma predeterminada en inglés (EE. UU.).

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos sobre la colaboración de B2B de Azure AD:

- [¿Qué es la colaboración B2B de Azure AD?](what-is-b2b.md)
- [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](add-users-administrator.md)
- [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](add-users-information-worker.md)
- [Canje de invitación de colaboración B2B](redemption-experience.md)
- [Incorporación de usuarios de colaboración B2B sin invitación](add-user-without-invite.md)
