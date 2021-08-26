---
title: 'Administración de grupos de aplicaciones en Azure Virtual Desktop (clásico): Azure'
description: Aprenda a configurar inquilinos de Azure Virtual Desktop (clásico) en Azure Active Directory (AD).
author: Heidilohr
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 6fe87fb7fc0cbe9e727fe1539d8424d9ee1fe1b1
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253267"
---
# <a name="tutorial-manage-app-groups-for-azure-virtual-desktop-classic"></a>Tutorial: Administración de grupos de aplicaciones en Azure Virtual Desktop (clásico)

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop (clásico), que no admite objetos de Azure Resource Manager. Si está intentando administrar objetos de Azure Virtual Desktop para Azure Resource Manager, consulte [este artículo](../manage-app-groups.md).

El grupo de aplicaciones predeterminado creado para un nuevo grupo de hosts de Azure Virtual Desktop también publica el escritorio completo. Además, puede crear uno o varios grupos de aplicaciones de RemoteApp para el grupo host. Siga este tutorial para crear un grupo de aplicaciones de RemoteApp y publicar aplicaciones individualesl de menú **Inicio**.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un grupo de RemoteApp
> * Conceder acceso a programas de RemoteApp.

Antes de empezar y, si aún no lo ha hecho, [descargue e importe el módulo de PowerShell de Azure Virtual Desktop](/powershell/windows-virtual-desktop/overview/), ya que lo usará en la sesión de PowerShell. Después, ejecute el siguiente cmdlet para iniciar sesión en su cuenta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Creación de un grupo de RemoteApp

1. Ejecute el siguiente cmdlet de PowerShell para crear un grupo de aplicaciones de RemoteApp vacío.

   ```powershell
   New-RdsAppGroup -TenantName <tenantname> -HostPoolName <hostpoolname> -Name <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Opcional) Para comprobar que se creó el grupo de aplicaciones, puede ejecutar el siguiente cmdlet para ver una lista de todos los grupos de aplicaciones del grupo host.

   ```powershell
   Get-RdsAppGroup -TenantName <tenantname> -HostPoolName <hostpoolname>
   ```

3. Ejecute el siguiente cmdlet para obtener una lista de aplicaciones del menú **Inicio** en la imagen de máquina virtual del grupo host. Anote los valores de **FilePath**, **IconPath**, **IconIndex** y otra información importante para la aplicación que quiere publicar.

   ```powershell
   Get-RdsStartMenuApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
   ```

4. Ejecute el siguiente cmdlet para instalar la aplicación según `AppAlias`. `AppAlias` se vuelve visible cuando se ejecuta la salida del paso 3.

   ```powershell
   New-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Opcional) Ejecute el siguiente cmdlet para publicar un nuevo programa de RemoteApp en el grupo de aplicaciones creado en el paso 1.

   ```powershell
    New-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Para comprobar que se publicó la aplicación, ejecute el siguiente cmdlet.

   ```powershell
    Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
   ```

7. Repita los pasos del 1 al 5 para cada aplicación de este grupo de aplicaciones que quiera publicar.
8. Ejecute el siguiente cmdlet para conceder a los usuarios acceso a los programas de RemoteApp del grupo de aplicaciones.

   ```powershell
   Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a crear un grupo de aplicaciones, rellenarlo con programas de RemoteApps y asignar usuarios al grupo de aplicaciones. Para información sobre cómo crear un grupo de hosts de validación, consulte el tutorial siguiente. Puede usar un grupo de hosts de validación para supervisar las actualizaciones del servicio antes de implementarlas en el entorno de producción.

> [!div class="nextstepaction"]
> [Creación de un grupo de hosts para validar las actualizaciones del servicio](create-validation-host-pool-2019.md)
