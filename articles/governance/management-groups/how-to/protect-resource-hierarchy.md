---
title: 'Procedimiento para proteger la jerarquía de recursos: Gobernanza en Azure'
description: Obtenga información sobre cómo proteger la jerarquía de recursos con configuraciones de jerarquía que incluyen la configuración del grupo de administración predeterminado.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 4315160030657b5aca0b293677ec0ec44d19adab
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323154"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Procedimiento para proteger la jerarquía de recursos

Los recursos, grupos de recursos, suscripciones, grupos de administración y el inquilino forman la jerarquía de recursos. La configuración en el grupo de administración raíz, como los roles de Azure personalizados o las asignaciones de directiva de Azure Policy, puede afectar a todos los recursos de la jerarquía. Es importante proteger la jerarquía de recursos de los cambios que podrían afectar negativamente a todos los recursos.

Ahora los grupos de administración tienen una configuración de jerarquía que permite al administrador de inquilinos controlar estos comportamientos. En este artículo se describe cada una de las configuraciones de jerarquía disponibles y cómo establecerlas.

## <a name="azure-rbac-permissions-for-hierarchy-settings"></a>Permisos de Azure RBAC para la configuración de jerarquía

La configuración de cualquiera de las opciones de jerarquía requiere las dos operaciones de proveedor de recursos siguientes en el grupo de administración raíz:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Estas operaciones solo permiten que un usuario lea y actualice la configuración de la jerarquía. Las operaciones no proporcionan ningún otro acceso a la jerarquía de grupos de administración ni a los recursos de la jerarquía. Las dos operaciones están disponibles en el rol de Azure integrado **Hierarchy Settings Administrator** (Administrador de configuración de jerarquía).

## <a name="setting---default-management-group"></a>Configuración: Grupo de administración predeterminado

De forma predeterminada, una nueva suscripción agregada dentro de un inquilino se agrega como miembro del grupo de administración raíz. Si hay asignaciones de directivas, el control de acceso basado en rol de Azure (Azure RBAC) y otras construcciones de gobernanza se asignan al grupo de administración raíz, por lo que afectan inmediatamente a estas nuevas suscripciones. Por este motivo, muchas organizaciones no aplican estas construcciones en el grupo de administración raíz, aunque es el lugar deseado para asignarlas. En otros casos, se recomienda un conjunto de controles más restrictivos para las suscripciones nuevas, pero no se deben asignar a todas las suscripciones. Este valor admite los dos casos de uso.

Al permitir que se defina el grupo de administración predeterminado para las suscripciones nuevas, se pueden aplicar construcciones de gobernanza para toda la organización en el grupo de administración raíz y definir un grupo de administración independiente con asignaciones de directivas o de roles de Azure más adecuadas para una suscripción nueva.

### <a name="set-default-management-group-in-portal"></a>Configuración del grupo de administración predeterminado en Azure Portal

Para configurar esta opción en Azure Portal, siga estos pasos:

1. Use la barra de búsqueda para buscar y seleccionar "Grupos de administración".

1. En el grupo de administración raíz, seleccione **Detalles** junto al nombre del grupo de administración.

1. En **Configuración**, seleccione **Configuración de jerarquía**.

1. Seleccione el botón **Cambiar el grupo de administración predeterminado**.

   > [!NOTE]
   > Si el botón **Cambiar el grupo de administración predeterminado** está deshabilitado, eso significa que el grupo de administración que se está viendo no es el grupo de administración raíz o que la entidad de seguridad no tiene los permisos necesarios para modificar la configuración de la jerarquía.

1. Seleccione un grupo de administración de la jerarquía y utilice el botón **Seleccionar**.

### <a name="set-default-management-group-with-rest-api"></a>Establecimiento del grupo de administración predeterminado con la API REST

Para configurar este valor con la API REST, se llama al punto de conexión de la [Configuración de jerarquía](/rest/api/managementgroups/hierarchysettings). Para ello, use el siguiente formato de URI de la API REST y de cuerpo. Reemplace `{rootMgID}` por el identificador del grupo de administración raíz y `{defaultGroupID}` por el identificador del grupo de administración para que se convierta en el grupo de administración predeterminado:

- URI DE LA API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-05-01
  ```

- Cuerpo de la solicitud

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Para volver a establecer el grupo de administración predeterminado en el grupo de administración raíz, use el mismo punto de conexión y establezca **defaultManagementGroup** en un valor de `/providers/Microsoft.Management/managementGroups/{rootMgID}`.

## <a name="setting---require-authorization"></a>Configuración: Requerir autorización

De forma predeterminada, cualquier usuario puede crear grupos de administración dentro de un inquilino. Es posible que los administradores de un inquilino solo quieran proporcionar estos permisos a usuarios específicos para mantener la coherencia y la conformidad en la jerarquía de grupos de administración. Si está habilitada, un usuario requiere la operación `Microsoft.Management/managementGroups/write` en el grupo de administración raíz para crear grupos de administración secundarios.

### <a name="set-require-authorization-in-portal"></a>Definición de la autorización necesaria en el portal

Para configurar esta opción en Azure Portal, siga estos pasos:

1. Use la barra de búsqueda para buscar y seleccionar "Grupos de administración".

1. En el grupo de administración raíz, seleccione **Detalles** junto al nombre del grupo de administración.

1. En **Configuración**, seleccione **Configuración de jerarquía**.

1. Cambie la opción **Requerir permisos para crear nuevos grupos de administración** a activado.

   > [!NOTE]
   > Si la opción **Requerir permisos para crear nuevos grupos de administración** está deshabilitada, eso significa que el grupo de administración que se está viendo no es el grupo de administración raíz o que la entidad de seguridad no tiene los permisos necesarios para modificar la configuración de la jerarquía.

### <a name="set-require-authorization-with-rest-api"></a>Configuración: requerir autorización con la API REST

Para configurar este valor con la API REST, se llama al punto de conexión de la [Configuración de jerarquía](/rest/api/managementgroups/hierarchysettings). Para ello, use el siguiente formato de URI de la API REST y de cuerpo. Este es un valor _booleano_, por lo que debe proporcionarle **true** o **false**. Un valor de **true** habilita este método de protección de la jerarquía de grupos de administración:

- URI DE LA API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-05-01
  ```

- Cuerpo de la solicitud

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Para volver a desactivar la configuración, use el mismo punto de conexión y establezca **requireAuthorizationForGroupCreation** en un valor de **false**.

## <a name="powershell-sample"></a>Ejemplo de PowerShell

PowerShell no tiene un comando "Az" para establecer el grupo de administración predeterminado o definir la autorización necesaria, pero como solución alternativa puede utilizar la API REST con el ejemplo de PowerShell siguiente:

```powershell
$root_management_group_id = "Enter the ID of root management group"
$default_management_group_id = "Enter the ID of default management group (or use the same ID of the root management group)"

$body = '{
     "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/' + $default_management_group_id + '",
          "requireAuthorizationForGroupCreation": true
     }
}'

$token = (Get-AzAccessToken).Token
$headers = @{"Authorization"= "Bearer $token"; "Content-Type"= "application/json"}
$uri = "https://management.azure.com/providers/Microsoft.Management/managementGroups/$root_management_group_id/settings/default?api-version=2020-05-01"

Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los grupos de administración, consulte:

- [Creación de grupos de administración para organizar los recursos de Azure](../create-management-group-portal.md)
- [Cambio, eliminación y administración de los grupos de administración](../manage.md)
