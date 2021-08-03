---
title: 'Conexión a Azure Virtual Desktop (clásico) desde Android: Azure'
description: Cómo conectarse a Azure Virtual Desktop (clásico) mediante el cliente Android.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 53f4ed035fc9f3a010a0677fa448a05ef189e20b
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111750000"
---
# <a name="connect-to-azure-virtual-desktop-classic-with-the-android-client"></a>Conexión a Azure Virtual Desktop (clásico) con el cliente Android

> Se aplica a: Android 4.1 y posteriores, Chromebooks con ChromeOS 53 y posteriores.

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop (clásico), que no admite objetos de Azure Resource Manager. Si está intentando administrar objetos de Azure Virtual Desktop para Azure Resource Manager, consulte [este artículo](../connect-android.md).

Puede acceder a los recursos de Azure Virtual Desktop desde su dispositivo Android con nuestro cliente, que se puede descargar. El cliente Android también se puede usar en dispositivos Chromebook que admitan Google Play Store. En esta guía se le indicará cómo configurar el cliente Android.

## <a name="install-the-android-client"></a>Instalación del cliente Android

Para empezar, [descargue](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) e instale el cliente en su dispositivo Android.

## <a name="subscribe-to-a-feed"></a>Suscripción a una fuente

Suscríbase a la fuente que le proporcionó su administrador para obtener la lista de recursos administrados a los que puede acceder en el dispositivo Android.

Para suscribirse a una fuente:

1. En el centro de conexiones, pulse **+** y, después, pulse **Remote Resource Feed** (Fuente de recursos remotos).
2. Escriba la dirección URL de la fuente en el campo **Feed URL** (Dirección URL de la fuente). La dirección URL de la fuente puede ser una dirección URL o una dirección de correo electrónico.
   - Si usa una dirección URL, use la que le proporcionó el administrador, que lo habitual es que sea <https://rdweb.wvd.microsoft.com>.
   - Para usar el correo electrónico, escriba su dirección de correo electrónico. El cliente buscará una dirección URL asociada a su dirección de correo electrónico si el administrador configuró el servidor de esa forma.
3. Pulse **NEXT** (Siguiente).
4. Escriba sus credenciales cuando se le solicite.
   - En **User Name** (Nombre de usuario), asigne al nombre de usuario permiso para acceder a los recursos.
   - En **Password** (Contraseña), escriba la contraseña asociada al nombre de usuario.
   - También se le puede solicitar que proporcione otra información si el administrador configuró la autenticación de esa forma.

Después de suscribirse, el centro de conexiones debería mostrar los recursos remotos.

Una vez que se haya suscrito a una fuente, el contenido de la misma se actualizará automáticamente de forma periódica. Pueden agregarse, cambiarse o quitarse recursos de acuerdo con los cambios hechos por el administrador.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo usar el cliente Android, consulte [Introducción al cliente de Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).