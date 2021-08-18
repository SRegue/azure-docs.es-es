---
title: 'Conexión a Azure Virtual Desktop (clásico) desde iOS: Azure'
description: Conexión a Azure Virtual Desktop (clásico) mediante el cliente iOS.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 6dcd14f12b557ef63337890b10e69d6cdf92498a
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113031182"
---
# <a name="connect-to-azure-virtual-desktop-classic-with-the-ios-client"></a>Conexión a Azure Virtual Desktop (clásico) con el cliente iOS

> Se aplica a: iOS 13.0 una versión posterior. Compatible con iPhone, iPad y iPod touch.

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop (clásico), que no admite objetos de Azure Resource Manager. Si está intentando administrar objetos de Azure Virtual Desktop para Azure Resource Manager, consulte [este artículo](../user-documentation/connect-ios.md).

Puede acceder a los recursos de Azure Virtual Desktop desde su dispositivo iOS con nuestro cliente, que se puede descargar. En esta guía se le indicará cómo configurar el cliente iOS.

## <a name="install-the-ios-client"></a>Instalación del cliente de iOS

Para empezar, [descargue](https://aka.ms/rdios) e instale el cliente en su dispositivo iOS.

## <a name="subscribe-to-a-feed"></a>Suscripción a una fuente

Suscríbase a la fuente que proporciona su administrador para obtener la lista de recursos administrados a los que puede acceder en el dispositivo iOS.

Para suscribirse a una fuente:

1. En el centro de conexiones, pulse **+** y, después, pulse **Add Workspace** (Agregar área de trabajo).
2. Escriba la dirección URL de la fuente en el campo **Feed URL** (Dirección URL de la fuente). La dirección URL de la fuente puede ser una dirección URL o una dirección de correo electrónico.
   - Si usa una dirección URL, use la que le proporcionó el administrador. Normalmente, la dirección URL es <https://rdweb.wvd.microsoft.com>.
   - Para usar el correo electrónico, escriba su dirección de correo electrónico. Esto indica al cliente que busque una dirección URL asociada a su dirección de correo electrónico si el administrador configuró el servidor de esa forma.
3. Pulse **Next** (Siguiente).
4. Escriba sus credenciales cuando se le solicite.
   - En **User Name** (Nombre de usuario), asigne al nombre de usuario permiso para acceder a los recursos.
   - En **Password** (Contraseña), escriba la contraseña asociada al nombre de usuario.
   - También se le puede solicitar que proporcione otra información si el administrador configuró la autenticación de esa forma.
5. Pulse **Save** (Guardar).

Después, el centro de conexiones debería mostrar los recursos remotos.

Una vez que se haya suscrito a una fuente, el contenido de la misma se actualizará automáticamente de forma periódica. Pueden agregarse, cambiarse o quitarse recursos de acuerdo con los cambios hechos por el administrador.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo usar el cliente de iOS, consulte el documento [Introducción al cliente de iOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/).
