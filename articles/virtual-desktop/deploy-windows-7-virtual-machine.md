---
title: 'Implementación de una máquina virtual con Windows 7 en Azure Virtual Desktop: Azure'
description: Implementación de una máquina virtual con Windows 7 en Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 4e52594578202046d36e2cbd5a727d4973f26f39
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751979"
---
# <a name="deploy-a-windows-7-virtual-machine-on-azure-virtual-desktop"></a>Implementación de una máquina virtual con Windows 7 en Azure Virtual Desktop

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop con objetos de Azure Resource Manager. Si usa Azure Virtual Desktop (clásico) sin objetos de Azure Resource Manager, consulte [este artículo](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

El proceso de implementación de una máquina virtual con Windows 7 en Azure Virtual Desktop es ligeramente diferente al de las máquinas virtuales que ejecutan versiones posteriores de Windows. En esta guía se explica cómo implementar Windows 7.

## <a name="prerequisites"></a>Prerrequisitos

Antes de empezar, siga las instrucciones de [Creación de un grupo hosts con PowerShell](create-host-pools-powershell.md) para crear un grupo de hosts. Si va a usar el portal, siga las instrucciones de los pasos 1 a 9 del artículo [Creación de un grupo de hosts con Azure Portal](create-host-pools-azure-marketplace.md). Después, seleccione **Revisar y crear** para crear un grupo de hosts vacío.

## <a name="configure-a-windows-7-virtual-machine"></a>Configuración de una máquina virtual con Windows 7

Una vez que haya realizado los requisitos previos, estará listo para configurar la máquina virtual de Windows 7 para su implementación en Azure Virtual Desktop.

Para configurar una máquina virtual de Windows 7 en Azure Virtual Desktop:

1. Inicie sesión en el Azure Portal y busque la imagen de Windows 7 Enterprise o cargue su propia imagen personalizada de Windows 7 Enterprise (x64).
2. Implemente una o varias máquinas virtuales con Windows 7 Enterprise como sistema operativo host. Asegúrese de que las máquinas virtuales permiten el Protocolo de escritorio remoto (RDP) (el puerto TCP/3389).
3. Conéctese al host de Windows 7 Enterprise mediante el RDP y autentíquese con las credenciales que definió al configurar la implementación.
4. Agregue la cuenta que usó al conectarse al host con RDP al grupo "Usuario de Escritorio remoto". Si no la agrega, es posible que no pueda conectarse a la máquina virtual después de conectarla a su dominio de Active Directory.
5. Vaya a Windows Update en la máquina virtual.
6. Instale todas las actualizaciones de Windows en la categoría Importante.
7. Instale todas las actualizaciones de Windows en la categoría Opcional (excepto los paquetes de idioma). Este proceso instala la actualización del Protocolo de escritorio remoto 8.0 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) que necesita para completar estas instrucciones.
8. Abra el Editor de directivas de grupo local y vaya a **Configuración del equipo** > **Plantillas administrativas** > **Componentes de Windows** > **Servicios de escritorio remoto** > **Host de sesión de escritorio remoto** > **Entorno de sesión remota**.
9. Habilite la directiva del Protocolo de escritorio remoto 8.0.
10. Una esta máquina virtual al dominio de Active Directory.
11. Reinicie la máquina virtual, para lo que debe ejecutar el siguiente comando:

     ```cmd
     shutdown /r /t 0
     ```

12. Siga las instrucciones descritas [aquí](/powershell/module/az.desktopvirtualization/new-azwvdregistrationinfo) para obtener un token de registro.

      - Si prefiere usar Azure Portal, también puede ir a la página de información general del grupo de hosts al que desea agregar la máquina virtual y crear un token en ella.

13. [Descargue el agente de Azure Virtual Desktop para Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Descargue Agent Manager de Azure Virtual Desktop para Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Abra el instalador del agente de Azure Virtual Desktop y siga las instrucciones. Cuando se le solicite, proporcione la clave de registro que ha creado en el paso 12.
16. Abra Agent Manager de Azure Virtual Desktop y siga las instrucciones.
17. Opcionalmente, bloquee el puerto TCP/3389 para quitar el acceso directo del protocolo de escritorio remoto a la máquina virtual.
18. También puede confirmar que .NET Framework se encuentra al menos en la versión 4.7.2. Esta actualización es especialmente importante si va a crear una imagen personalizada.

## <a name="next-steps"></a>Pasos siguientes

La implementación de Azure Virtual Desktop ya está lista para usarse. [Descargue la versión más reciente del cliente de Azure Virtual Desktop](https://aka.ms/wvd/clients/windows) para comenzar.

Para obtener una lista de los problemas conocidos, así como instrucciones para la solución de problemas de Windows 7 en Azure Virtual Desktop, consulte nuestro artículo para la solución de problemas en [Solución de los problemas de las máquinas virtuales con Windows 7 en Azure Virtual Desktop](./virtual-desktop-fall-2019/troubleshoot-windows-7-vm.md).
