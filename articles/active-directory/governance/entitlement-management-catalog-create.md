---
title: 'Creación y administración de un catálogo de recursos en la administración de derechos: Azure AD'
description: Obtenga información sobre cómo crear un contenedor de recursos y paquetes de acceso en la administración de derechos de Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32b848f6a34fbd25322c53cd35dc0db600743c88
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730215"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Creación y administración de un catálogo de recursos en la administración de derechos de Azure AD

## <a name="create-a-catalog"></a>Creación de un catálogo

Un catálogo es un contenedor de recursos y paquetes de acceso. Creará un catálogo cuando quiera agrupar recursos relacionados y paquetes de acceso. Quien cree el catálogo se convertirá en su primer propietario. El propietario de un catálogo puede agregar otros propietarios.

**Requisitos previos de rol:** administrador global, administrador de Identity Governance, administrador de usuarios o creador de catálogos

> [!NOTE]
> Los usuarios a los que se les haya asignado el rol Administrador de usuarios ya no podrán crear catálogos ni administrar paquetes de acceso en un catálogo que no sea de su propiedad. Si a los usuarios de la organización se les asignó el rol Administrador de usuarios para configurar catálogos, paquetes de acceso o directivas en la administración de derechos, en su lugar debe asignarles a estos usuarios el rol **Administrador de Identity Governance**.

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos**.

    ![Catálogos de la administración de derechos en Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Haga clic en **Nuevo catálogo**.

1. Escriba un nombre único para el catálogo y proporcione una descripción.

    Los usuarios verán esta información en los detalles de un paquete de acceso.

1. Si quiere que los usuarios puedan solicitar los paquetes de acceso de este catálogo en cuanto se creen, establezca **Habilitado** en **Sí**.

1. Si quiere permitir que los usuarios de directorios externos seleccionados puedan solicitar los paquetes de acceso de este catálogo, establezca **Enabled for external users** (Habilitado para usuarios externos) en **Sí**.

    ![Panel Nuevo catálogo](./media/entitlement-management-shared/new-catalog.png)

1. Haga clic en **Crear** para crear el catálogo.

## <a name="create-a-catalog-programmatically"></a>Creación de un catálogo mediante programación
### <a name="create-a-catalog-with-microsoft-graph"></a>Creación de un catálogo con Microsoft Graph

También puede crear un catálogo mediante Microsoft Graph.  Un usuario de un rol adecuado con una aplicación con el permiso `EntitlementManagement.ReadWrite.All`, o una aplicación con ese permiso de aplicación, puede llamar a la API para [crear un elemento accessPackageCatalog](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true).

### <a name="create-a-catalog--with-powershell"></a>Creación de un catálogo con PowerShell

Puede crear un catálogo en PowerShell con el cmdlet `New-MgEntitlementManagementAccessPackageCatalog` de los [cmdlets de Microsoft Graph PowerShell para el módulo Identity Governance](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) versión 1.6.0 o posterior.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$catalog = New-MgEntitlementManagementAccessPackageCatalog -DisplayName "Marketing"
```

## <a name="add-resources-to-a-catalog"></a>Adición de recursos a un catálogo

Para incluir recursos en un paquete de acceso, deben estar en un catálogo. Los tipos de recursos que puede agregar son grupos, aplicaciones y sitios de SharePoint Online.

* Los grupos pueden ser grupos de Microsoft 365 creados en la nube o grupos de seguridad de Azure AD creados en la nube.  Los grupos que se originan en una instancia local de Active Directory no pueden asignarse como recursos porque su propietario o los atributos de miembro no se pueden cambiar en Azure AD.   Los grupos que se originan en Exchange Online como grupos de distribución tampoco se pueden modificar en Azure AD.
* Las aplicaciones pueden ser aplicaciones empresariales de Azure AD, incluidas las aplicaciones SaaS y sus propias aplicaciones integradas en Azure AD. Para obtener más información sobre cómo seleccionar los recursos adecuados para las aplicaciones con varios roles, consulte [Adición de roles de recursos](entitlement-management-access-package-resources.md#add-resource-roles).
* Los sitios pueden ser sitios de SharePoint Online o colecciones de sitios de SharePoint Online.

**Rol necesario:** vea [Roles necesarios para agregar recursos a un catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos** y abra el catálogo al que quiere agregar recursos.

1. En el menú izquierdo, haga clic en **Recursos**.

1. Haga clic en **Agregar recursos**.

1. Haga clic en un tipo de recurso: **Grupos y equipos**, **Aplicaciones** o **Sitios de SharePoint**.

    Si no ve un recurso que quiere agregar o no puede agregar un recurso, asegúrese de que tiene los roles de administración de derechos y de directorio de Azure AD que se requieren. Es posible que alguien que tenga los roles necesarios tenga que agregar el recurso al catálogo. Para obtener más información, vea [Roles necesarios para agregar recursos a un catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Seleccione uno o varios recursos del tipo que quiera agregar al catálogo.

    ![Adición de recursos a un catálogo](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Cuando haya finalizado, haga clic en **Agregar**.

    Ahora, estos recursos se pueden incluir en paquetes de acceso del catálogo.

### <a name="add-a-multi-geo-sharepoint-site"></a>Incorporación de un sitio de SharePoint con varias ubicaciones geográficas

1. Si tiene habilitado [Multi-Geo](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) para SharePoint, seleccione el entorno desde el que quiere seleccionar sitios.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multi-geo-select.png" alt-text="Paquete de acceso - Agregar roles de recursos - Seleccionar sitios Multi-Geo de SharePoint":::

1. A continuación, seleccione los sitios que quiere que se agreguen al catálogo. 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Adición de un recurso a un catálogo mediante programación

También puede agregar un recurso a un catálogo mediante Microsoft Graph.  Un usuario de un rol adecuado, o un propietario de catálogo y recurso, con una aplicación con el permiso `EntitlementManagement.ReadWrite.All` delegado puede llamar a la API para [crear un elemento accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true).  Pero una aplicación con permisos de aplicación todavía no puede agregar un recurso mediante programación sin un contexto de usuario en el momento de la solicitud.

## <a name="remove-resources-from-a-catalog"></a>Eliminación de recursos de un catálogo

Puede quitar recursos de un catálogo. Solo se puede quitar un recurso de un catálogo si no se está usando en ninguno de los paquetes de acceso del catálogo.

**Rol necesario:** vea [Roles necesarios para agregar recursos a un catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos** y abra el catálogo del que quiere quitar recursos.

1. En el menú izquierdo, haga clic en **Recursos**.

1. Seleccione los recursos que quiera quitar.

1. Haga clic en **Quitar** (o haga clic en el botón de puntos suspensivos ( **...** ) y, luego, en **Quitar recurso**).


## <a name="add-additional-catalog-owners"></a>Incorporación de otros propietarios del catálogo

El usuario que crea un catálogo se convierte en su primer propietario. Para delegar la administración de un catálogo, se agregan usuarios al rol de propietario del catálogo. Esto ayuda a compartir las responsabilidades de administración de los catálogos. 

Siga estos pasos para asignar un usuario al rol de propietario del catálogo:

**Requisitos previos de rol:** administrador global, administrador de Identity Governance, administrador de usuarios o administrador de catálogos

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos** y abra el catálogo al que quiere agregar administradores.

1. En el menú izquierdo, haga clic en **Roles y administradores**.

    ![Roles y administradores de catálogos](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Haga clic en **Agregar propietarios** para seleccionar miembros para estos roles.

1. Haga clic en **Seleccionar** para agregar estos miembros.

## <a name="edit-a-catalog"></a>Edición de un catálogo

Puede editar el nombre y la descripción de un catálogo. Los usuarios ven esta información en los detalles de un paquete de acceso.

**Requisitos previos de rol:** administrador global, administrador de Identity Governance, administrador de usuarios o administrador de catálogos

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos** y abra el catálogo que quiere editar.

1. En la página **Introducción** del catálogo, haga clic en **Editar**.

1. Edite el nombre, la descripción o la configuración habilitada del catálogo.

    ![Edición de la configuración del catálogo](./media/entitlement-management-shared/catalog-edit.png)

1. Haga clic en **Save**(Guardar).

## <a name="delete-a-catalog"></a>Eliminación de un catálogo

Puede eliminar un catálogo, pero solo si no tiene ningún paquete de acceso.

**Requisitos previos de rol:** administrador global, administrador de Identity Governance, administrador de usuarios o administrador de catálogos

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos** y abra el catálogo que quiere eliminar.

1. En la página **Introducción** del catálogo, haga clic en **Eliminar**.

1. En el cuadro de mensaje que aparece, haga clic en **Sí**.

### <a name="deleting-a-catalog-programmatically"></a>Eliminación de un catálogo mediante programación

También puede eliminar un catálogo mediante Microsoft Graph.  Un usuario de un rol adecuado con una aplicación con el permiso `EntitlementManagement.ReadWrite.All` delegado puede llamar a la API para [eliminar un elemento accessPackageCatalog](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true).

## <a name="next-steps"></a>Pasos siguientes

- [Delegación de la gobernanza del acceso en administradores de paquetes de acceso](entitlement-management-delegate-managers.md)
