---
title: API de envío del Centro de partners para incorporar aplicaciones de Azure en marketplace comercial de Microsoft
description: Conozca los requisitos previos para usar la API de envío del Centro de partners para aplicaciones de Azure en Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 07/01/2021
ms.openlocfilehash: c6ef7acb4f28f33a8726060854914d12a8cf76eb
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113232149"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>API de envío del Centro de partners para incorporar aplicaciones de Azure en el centro de Partners

Use la *API de envío del Centro de partners* para consultar, crear envíos y publicar ofertas de Azure mediante programación.  Esta API es útil si su cuenta administra muchas ofertas y desea automatizar y optimizar el proceso de envío de estas ofertas.

## <a name="api-prerequisites"></a>Requisitos previos de la API

Para usar la API del Centro de partners para los productos de Azure necesita algunos recursos de programación: 

- una aplicación de Azure Active Directory.
- un token de acceso de Azure Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Paso 1: Cumplimentación los requisitos previos para el uso de la API de envío del Centro de partners

Antes de empezar a escribir código para llamar a la API de envío del Centro de partners, asegúrese de que ha completado los requisitos previos a continuación.

- Usted (o su organización) tiene que tener un directorio de Azure AD y el permiso de [Administrador global](../active-directory/roles/permissions-reference.md) para el directorio. Si usa Microsoft 365 u otros servicios empresariales de Microsoft, ya tiene el directorio de Azure AD. De lo contrario, puede [crear un nuevo Azure AD en el Centro de partners](/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) sin cargo adicional.

- Tiene que [asociar una aplicación Azure AD a su cuenta del Centro de partners](/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) y obtener el identificador de inquilino, el identificador de cliente y la clave. Necesitará estos valores para obtener un token de acceso de Azure AD, que usará en las llamadas a la API de envío de Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Cómo asociar una aplicación de Azure AD con la cuenta del Centro de partners

Para usar la API de envío de Microsoft Store, tiene que asociar una aplicación Azure AD a su cuenta del Centro de partners, recuperar el identificador de inquilino y el identificador de cliente para la aplicación, y generar una clave. La aplicación de Azure AD representa la aplicación o el servicio desde donde desea llamar a la API de envío del Centro de partners. Necesita el identificador de inquilino, el identificador de cliente y la clave para obtener un token de acceso de Azure AD para pasar a la API.

>[!Note]
>Solo tiene que realizar esta operación una vez. Una vez que tenga el identificador de inquilino, el identificador de cliente y la clave, puede volver a usarlos cada vez que tenga que crear un nuevo token de acceso de Azure AD.

1. En el Centro de partners, [asocie la cuenta del Centro de partners de la organización con el directorio de Azure AD de la organización](/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. A continuación, en la página **Usuarios** en la sección **Configuración** de la cuenta del Centro de partners, [agregue la aplicación de Azure AD](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) que representa la aplicación o el servicio que usará para obtener acceso a los envíos de la cuenta del Centro de partners. Asegúrese de asignar a esta aplicación el rol **Administrador**. Si la aplicación aún no existe en el directorio de Azure AD, puede [crear una nueva aplicación de Azure AD en el Centro de partners](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Vuelva a la página **Usuarios**, haga clic en el nombre de la aplicación de Azure AD para ir a la configuración de la aplicación y, a continuación, copie los valores de **Identificador de inquilino** e **Identificador de cliente**.
1. Haga clic en **Agregar nueva clave**. En la pantalla siguiente, copie el valor de **Clave**. Después de salir de esta página no podrá tener acceso de nuevo a esta información. Para más información, consulte [Administrar claves para una aplicación de Azure AD](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Paso 2: Obtención de un token de acceso de Azure AD

Antes de llamar a cualquiera de los métodos de la API de envío del Centro de partners, primero tiene que obtener un token de acceso de Azure AD que pase al encabezado de **autorización** de cada método de la API. Una vez que haya obtenido un token de acceso, tiene 60 minutos para usarlo antes de que expire. Una vez que expire el token, puede actualizarlo para poder seguir utilizándolo en llamadas futuras a la API.

Para obtener el token de acceso, siga las instrucciones de [Llamadas entre servicios mediante las credenciales del cliente](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) para enviar una `HTTP POST` al punto de conexión `https://login.microsoftonline.com/<tenant_id>/oauth2/token`. Esta es una solicitud de ejemplo:

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Para el valor *tenant_id* en el `POST URI` y los parámetros *client_id* y *client_secret*, especifique el identificador de inquilino, el identificador de cliente y la clave de la aplicación que recuperó del Centro de partners en la sección anterior. Para el parámetro *resource*, tiene que especificar `https://api.partner.microsoft.com`.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Paso 3: Uso de la API de envío de Microsoft Store

Una vez que tenga un token de acceso de Azure AD, puede llamar a los métodos en la API de envío del Centro de partners. Para crear o actualizar envíos, normalmente se llama a varios métodos en la API de envío del Centro de partners en un orden específico. Para información acerca de cada escenario y la sintaxis de cada método, consulte el swagger de la API de ingesta.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>Pasos siguientes

* [Creación de un recurso técnico de contenedor de Azure](azure-container-technical-assets.md)
* [Creación de una oferta de contenedor de Azure](azure-container-offer-setup.md)
