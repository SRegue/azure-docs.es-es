---
title: Eliminaciones del historial de implementación
description: Describe cómo Azure Resource Manager elimina automáticamente las implementaciones del historial de implementaciones. Las implementaciones se eliminan cuando el historial está próximo a superar el límite de 800.
ms.topic: conceptual
ms.date: 06/04/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: eaffae3ea5e901719969632cb1f889c8914978a0
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963362"
---
# <a name="automatic-deletions-from-deployment-history"></a>Eliminaciones automáticas del historial de implementaciones

Cada vez que se implementa una plantilla, la información sobre la implementación se escribe en el historial de implementaciones. Cada grupo de recursos está limitado a 800 implementaciones en su historial de implementaciones.

Azure Resource Manager elimina automáticamente las implementaciones del historial a medida que se acerca al límite. La eliminación automática es un cambio respecto al comportamiento anterior. Anteriormente, tenía que eliminar manualmente las implementaciones del historial de implementaciones para evitar un error. Este cambio se implementó el 6 de agosto de 2020.

**Las eliminaciones automáticas son compatibles con las implementaciones de grupo de recursos y suscripción. Actualmente, las implementaciones del historial de implementaciones de [grupo de administración](deploy-to-management-group.md) e [inquilino](deploy-to-tenant.md) no se eliminan automáticamente**.

> [!NOTE]
> La eliminación de una implementación del historial no afecta a ninguno de los recursos implementados.

## <a name="when-deployments-are-deleted"></a>Cuando las implementaciones se eliminan

Las implementaciones se eliminan del historial cuando se alcanzan 700 o más. Azure Resource Manager elimina las implementaciones hasta que el historial baja hasta 600. Las implementaciones más antiguas siempre se eliminan primero.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.png" alt-text="Diagrama de eliminación del historial de implementación.":::

> [!IMPORTANT]
> Si el grupo de recursos ya está en el límite de 800, se producirá un error en la siguiente implementación. El proceso de eliminación automática se inicia inmediatamente. Puede volver a probar la implementación después de una breve espera.

Además de las implementaciones, también se desencadenan eliminaciones al ejecutar la [operación what-if](./deploy-what-if.md) o validar una implementación.

Cuando asigna a una implementación el mismo nombre que el de una existente en el historial, se restablece su lugar en el historial. La implementación se mueve a la posición más reciente en el historial. El lugar de una implementación también se restablece cuando se [revierte a esa implementación](rollback-on-error.md) después de un error.

## <a name="remove-locks-that-block-deletions"></a>Quitar los bloqueos que bloquean las eliminaciones

Si tiene un [bloqueo CanNotDelete](../management/lock-resources.md) en un grupo de recursos, no se podrán eliminar las implementaciones para ese grupo de recursos. Debe quitar el bloqueo para aprovechar las eliminaciones automáticas en el historial de implementaciones.

Para usar PowerShell para eliminar un bloqueo, ejecute los siguientes comandos:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Para usar la CLI de Azure para eliminar un bloqueo, ejecute los siguientes comandos:

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="required-permissions"></a>Permisos necesarios

Las eliminaciones se solicitan bajo la identidad del usuario que ha implementado la plantilla. Para eliminar las implementaciones, el usuario debe tener acceso a la acción **Microsoft.Resources/deployments/delete**. Si el usuario no tiene los permisos necesarios, las implementaciones no se eliminan del historial.

Si el usuario actual no tiene los permisos necesarios, se volverá a intentar la eliminación automática durante la siguiente implementación.

## <a name="opt-out-of-automatic-deletions"></a>Rechazo de eliminaciones automáticas

Puede rechazar las eliminaciones automáticas del historial. **Use esta opción solamente si desea administrar el historial de implementaciones usted mismo.** Se sigue aplicando el límite de 800 implementaciones en el historial. Si supera las 800 implementaciones, recibirá un error y la implementación no se realizará.

Para deshabilitar las eliminaciones automáticas, registre la marca de característica `Microsoft.Resources/DisableDeploymentGrooming`. Cuando registra la marca de característica, se rechazan las eliminaciones automáticas de toda la suscripción de Azure. No puede rechazar solamente un grupo de recursos concreto. Para volver a habilitar las eliminaciones automáticas, cancele el registro de la marca de característica.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para PowerShell, use [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Para ver el estado actual de la suscripción, use:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Para volver a habilitar las eliminaciones automáticas, use la API de REST de Azure o la CLI de Azure.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para la CLI de Azure, use [az feature register](/cli/azure/feature#az_feature_register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Para ver el estado actual de la suscripción, use:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Para volver a habilitar las eliminaciones automáticas, use [az feature unregister](/cli/azure/feature#az_feature_unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Para API REST, use [Características - Registrar](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Para ver el estado actual de la suscripción, use:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Para volver a habilitar las eliminaciones automáticas, use [Características - Anular el registro](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información sobre cómo ver el historial de implementaciones, vea [Visualización del historial de implementación con Azure Resource Manager](deployment-history.md).