---
title: 'Problemas de diagnóstico de Azure Virtual Desktop: Azure'
description: Procedimientos para usar la característica de diagnóstico de Azure Virtual Desktop para diagnosticar problemas.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: de140f83c4f00d92379b1ff0b70f627234480295
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111757884"
---
# <a name="identify-and-diagnose-azure-virtual-desktop-issues"></a>Identificación y diagnóstico de problemas de Azure Virtual Desktop

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop con objetos de Azure Resource Manager. Si usa Azure Virtual Desktop (clásico) sin objetos de Azure Resource Manager, consulte [este artículo](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).

Azure Virtual Desktop ofrece una característica de diagnóstico que permite al administrador detectar problemas a través de una única interfaz. Para obtener más información sobre las funcionalidades de diagnóstico de Azure Virtual Desktop, consulte [Uso de Log Analytics para la característica de diagnóstico](diagnostics-log-analytics.md).

Las conexiones que no lleguen a Azure Virtual Desktop no aparecerán en los resultados de diagnóstico, ya que el propio servicio de rol de diagnóstico forma parte de Azure Virtual Desktop. Se pueden producir problemas de conexión a Azure Virtual Desktop si el usuario final está experimentando problemas de conectividad de red.

## <a name="common-error-scenarios"></a>Escenarios de error habituales

La tabla WVDErrors realiza un seguimiento de los errores en todos los tipos de actividad. La columna llamada "ServiceError" proporciona una marca adicional que indica "true" o "false". Esta marca le indicará si el error está relacionado con el servicio.

* Si el valor es "true", es posible que el equipo de servicio ya haya investigado este problema. Si este afecta a la experiencia del usuario y aparece muchas veces, le recomendamos que envíe una incidencia de soporte técnico para Azure Virtual Desktop.
* Si el valor es "false", puede que haya un error de configuración que pueda corregir por su cuenta. El mensaje de error puede proporcionarle una pista acerca de por dónde empezar.

En la siguiente tabla se enumeran los errores comunes que los administradores pueden encontrarse.

>[!NOTE]
>Esta lista incluye los errores más comunes y se actualiza de manera periódica. Para estar seguro de que dispone de la información más actualizada, procure consultar en este artículo al menos una vez al mes.

## <a name="management-errors"></a>Errores de administración

|Mensaje de error|Solución propuesta|
|---|---|
|Failed to create registration key. (No se pudo crear la clave de registro). |No se pudo crear el token de registro. Pruebe a crearlo de nuevo con un tiempo de expiración más breve (entre 1 hora y 1 mes). |
|Failed to delete registration key. (No se pudo eliminar la clave de registro).|No se pudo eliminar el token de registro. Vuelva a intentar eliminarlo. Si sigue sin funcionar, use PowerShell para comprobar si el token sigue ahí. Si es así, elimínelo con PowerShell.|
|Failed to change session host drain mode. (No se pudo cambiar el modo de purga del host de sesión). |No se pudo cambiar el modo de purga en la VM. Compruebe el estado de la VM. Si no está disponible, no se puede cambiar el modo de purga.|
|Failed to disconnect user sessions. (No se pudieron desconectar las sesiones del usuario). |No se pudo desconectar el usuario de la VM. Compruebe el estado de la VM. Si no está disponible, la sesión de usuario no se puede desconectar. Si está disponible, compruebe el estado de la sesión de usuario para saber si está desconectado. |
|Failed to log off all user(s) within the session host. (No se pudo cerrar la sesión de todos los usuarios en el host de sesión). |No se pudo cerrar la sesión de los usuarios en la VM. Compruebe el estado de la VM. Si no está disponible, no se puede cerrar la sesión de los usuarios. Compruebe el estado de la sesión del usuario para averiguar si ya ha cerrado la sesión. Puede forzar el cierre de sesión con PowerShell. |
|Failed to unassign user from application group. (No se pudo cancelar la asignación del usuario del grupo de aplicaciones).|No se pudo anular la publicación de un grupo de aplicaciones para un usuario. Compruebe si el usuario está disponible en Azure AD. Consulte si el usuario forma parte de un grupo de usuarios en que está publicado el grupo de aplicaciones. |
|There was an error retrieving the available locations. (Error al recuperar las ubicaciones disponibles). |Compruebe la ubicación de la VM usada en el asistente para crear grupos de hosts. Si la imagen no está disponible en esa ubicación, agregue la imagen en la ubicación o elija otra ubicación de VM. |

### <a name="connection-error-codes"></a>Códigos de error de conexión

|Código numérico|Código de error|Solución propuesta|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|El host de sesión no está unido correctamente a Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Las conexiones no se pudieron establecer porque el host de sesión no está disponible. Compruebe el estado del host de sesión.|
|-2146233088|ConnectionFailedClientDisconnect|Si ve este error con frecuencia, asegúrese de que el equipo del usuario está conectado a la red.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|La sesión a la que el usuario de host ha intentado conectarse no está en un estado adecuado. Depure la máquina virtual.|
|-2146233088|ConnectionFailedUserNotAuthorized|El usuario no tiene permiso para acceder al escritorio o la aplicación publicada. Este error puede aparecer después de que el administrador quite recursos publicados. Pida al usuario que actualice la fuente en la aplicación de Escritorio remoto.|
|2|FileNotFound|La aplicación a la que el usuario intentó tener acceso incorrectamente está instalada o establecida en una ruta de acceso incorrecta.<br><br>Al publicar nuevas aplicaciones mientras el usuario tiene una sesión activa, este no podrá acceder a esta aplicación. La sesión debe cerrarse y reiniciarse para que el usuario pueda acceder a la aplicación. |
|3|InvalidCredentials|El nombre de usuario o contraseña especificados por el usuario no coincide con el nombre de usuario o contraseña existentes. Revise las credenciales para ver si hay errores tipográficos e inténtelo de nuevo.|
|8|ConnectionBroken|La conexión entre el cliente y la puerta de enlace o servidor se ha interrumpido. No es necesario hacer nada a menos que esto ocurra de forma inesperada.|
|14|UnexpectedNetworkDisconnect|La conexión a la red se ha interrumpido. Pida al usuario que vuelva a conectarse.|
|24|ReverseConnectFailed|La máquina virtual host no tiene línea de visión directa a la puerta de enlace de Escritorio remoto. Asegúrese de que la dirección IP de la puerta de enlace se puede resolver.|

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>Error: No se pueden agregar asignaciones de usuario a un grupo de aplicaciones

Después de asignar un usuario a un grupo de aplicaciones, Azure Portal muestra una advertencia que indica "La sesión está finalizando" o "Experimentando problemas de autenticación: extensión Microsoft_Azure_WVD". Seguidamente, la página de asignación no se carga y, después de eso, las páginas dejan de cargarse en Azure Portal (por ejemplo, Azure Monitor, Log Analytics, Service Health, etc.).

**Causa:** Hay un problema con la directiva de acceso condicional. Azure Portal está intentando obtener un token para Microsoft Graph, que depende de SharePoint Online. El cliente tiene una directiva de acceso condicional denominada "Términos de uso de almacenamiento de datos de Microsoft Office 365" que requiere que los usuarios acepten los términos de uso para acceder al almacenamiento de datos. Sin embargo, aún no han iniciado sesión, por lo que Azure Portal no puede obtener el token.

**Solución:** Antes de iniciar sesión en Azure Portal, el administrador primero debe iniciar sesión en SharePoint y aceptar los términos de uso. Después, debería poder iniciar sesión en Azure Portal con normalidad.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los roles de Azure Virtual Desktop, consulte [Entorno de Azure Virtual Desktop](environment-setup.md).

Para ver una lista de cmdlets de PowerShell disponibles para Azure Virtual Desktop, consulte la [referencia de PowerShell](/powershell/windows-virtual-desktop/overview).
