---
title: Escalado de un servicio en la nube de Azure (clásico) en Windows PowerShell | Microsoft Docs
description: (modelo clásico) Aprenda a usar PowerShell para escalar o reducir horizontalmente un rol web o de trabajo en Azure.
ms.topic: article
ms.service: cloud-services
ms.subservice: autoscale
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: de198a3a2449d43461547e216eb0acfc6b02d527
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822279"
---
# <a name="how-to-scale-an-azure-cloud-service-classic-in-powershell"></a>Escalado de un servicio en la nube de Azure (clásico) en PowerShell

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Puede usar Windows PowerShell para escalar o reducir horizontalmente un rol web o de trabajo mediante la adición o eliminación de instancias.  

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Para poder realizar cualquier operación en su suscripción a través de PowerShell, debe iniciar sesión:

```powershell
Add-AzureAccount
```

Si tiene varias suscripciones asociadas a su cuenta, puede que deba cambiar la suscripción actual (según donde resida el servicio en la nube). Para comprobar la suscripción actual, ejecute:

```powershell
Get-AzureSubscription -Current
```

Si necesita cambiar la suscripción actual, ejecute:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Comprobación del número actual de instancias para el rol

Para comprobar el estado actual de su rol, ejecute:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Obtendrá información sobre el rol, como su versión actual de SO y el número de instancias. En este caso, el rol tiene una sola instancia.

![Información sobre el rol](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Escalado horizontal del rol mediante la adición de más instancias

Para escalar horizontalmente su rol, pase el número deseado de instancias como el parámetro **Count** al cmdlet **Set-AzureRole**:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

El cmdlet se bloquea momentáneamente mientras las nuevas instancias se aprovisionan e inician. Durante este tiempo, si abre una nueva ventana de PowerShell y llama a **Get-AzureRole** como se mostró anteriormente, verá el nuevo recuento de instancias de destino. Y si examina el estado del rol en el portal, verá que se inicia la nueva instancia:

![Instancia de máquina virtual iniciándose en el portal](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Cuando se hayan iniciado las nuevas instancias, el cmdlet devolverá resultados correctamente:

![Éxito del aumento de instancias de rol](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Reducción horizontal del rol mediante la eliminación de instancias

Puede reducir un rol horizontalmente quitando instancias de la misma manera. Establezca el parámetro **Count** de **Set-AzureRole** en el número de instancias que quiere tener después de que finalice la operación de reducción horizontal.

## <a name="next-steps"></a>Pasos siguientes

No es posible configurar el escalado automático para los servicios en la nube de PowerShell. Para ello, consulte [Cómo escalar automáticamente un servicio en la nube](cloud-services-how-to-scale-portal.md).
