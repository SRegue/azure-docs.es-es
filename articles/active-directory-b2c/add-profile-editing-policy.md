---
title: Configuración de un flujo de edición de perfiles
titleSuffix: Azure AD B2C
description: Obtenga información sobre cómo configurar un flujo de edición de perfiles en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/07/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f019fdc64ca30954017afc34267dcef998cf5fb8
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112198843"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Configuración de un flujo de edición de perfil en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>Flujo de edición de perfiles

La directiva de edición de perfiles permite a los usuarios administrar sus atributos de perfil, como el nombre para mostrar, los apellidos, el nombre dado, la ciudad y otros. El flujo de edición de perfiles implica los siguientes pasos: 

1. Registro o inicio de sesión con cuentas locales y de redes sociales. Si la sesión sigue activa, Azure AD B2C autoriza al usuario y pasa al siguiente paso.
1. Azure AD B2C lee el perfil de usuario del directorio y permite al usuario editar los atributos.

![Flujo de edición de perfiles](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>Prerrequisitos

Si todavía no lo ha hecho, [registre una aplicación web en Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>Creación de un flujo de usuario de edición de perfil

Si desea permitir que los usuarios editen sus perfiles en la aplicación, debe usar un flujo de usuario de edición de perfiles.

1. En el menú de la página de información general del inquilino de Azure AD B2C, seleccione **Flujos de usuario** y **Nuevo flujo de usuario**.
1. En la página **Crear un flujo de usuario**, seleccione el flujo de usuario **Edición de perfiles**. 
1. En **Seleccione una versión**, elija **Recomendada** y, luego, seleccione **Crear**.
1. Escriba un **nombre** para el flujo de usuario. Por ejemplo, *profileediting1*.
1. En **Proveedores de identidades**, seleccione al menos un proveedor de identidades:

   * En **Cuentas locales**, seleccione una de las opciones siguientes: **Email signin** (Inicio de sesión de correo electrónico), **User ID signin** (Inicio de sesión de usuario), **Phone signin** (Inicio de sesión de teléfono), **Phone/Email signin** (Inicio de sesión de teléfono o correo electrónico), **User ID/Email signin** (Inicio de sesión de id. de usuario o correo electrónico) o **None** (Ninguno). [Más información](sign-in-options.md).
   * En **Proveedores de identidades sociales**, seleccione cualquiera de los proveedores de identidades sociales o empresariales externos que haya configurado. [Más información](add-identity-provider.md).
1. En **Autenticación multifactor**, si quiere exigir a los usuarios que comprueben su identidad con un segundo método de autenticación, elija el tipo de método y cuándo aplicar la autenticación multifactor (MFA). [Más información](multi-factor-authentication.md).
1. En **Acceso condicional**, si ha configurado directivas de acceso condicional para el inquilino de Azure AD B2C y quiere habilitarlas para este flujo de usuario, active la casilla **Aplicar directivas de acceso condicional**. No es necesario especificar un nombre de directiva. [Más información](conditional-access-user-flow.md?pivots=b2c-user-flow).
1. En **Atributos de usuario**, elija los atributos que quiere que el cliente pueda modificar en su perfil. Para obtener la lista completa de valores, seleccione **Mostrar más**, elija los valores y, después, seleccione **Aceptar**.
1. Seleccione **Crear** para agregar el flujo de usuario. El prefijo *B2C_1* se anexa automáticamente al nombre.

### <a name="test-the-user-flow"></a>Prueba del flujo de usuario

1. Seleccione el flujo de usuario que ha creado para abrir su página de información general y, luego, **Ejecutar flujo de usuario**.
1. En **Aplicación**, seleccione la aplicación web denominada *webapp1* que registró anteriormente. La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Haga clic en **Ejecutar flujo de usuario** y, a continuación, inicie sesión con la cuenta que creó anteriormente.
1. Ahora tiene la oportunidad de cambiar el nombre para mostrar y el puesto del usuario. Haga clic en **Continuar**. El token se devuelve al `https://jwt.ms` y debe mostrarse.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>Creación de una directiva de edición de perfil

Las directivas personalizadas son un conjunto de archivos XML que se cargan en el inquilino de Azure AD B2C para definir recorridos de usuario. Proporcionamos paquetes de inicio con varias directivas predefinidas, entre las que se incluyen: registro e inicio de sesión, restablecimiento de contraseña y directiva de edición de perfiles. Para obtener información, consulte [Introducción a las directivas personalizadas en Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

* [Iniciar sesión con un proveedor de identidades de redes sociales](add-identity-provider.md)