---
title: 'Tutorial: Integración de Azure Active Directory con Bonusly | Microsoft Docs'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Bonusly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: 3bc61bdbca64b8a4aacc8aa8937d74081029d9f2
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112286247"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Integración de Azure Active Directory con Bonusly

En este tutorial, aprenderá a integrar Bonusly con Azure Active Directory (Azure AD). Al integrar Bonusly con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Bonusly.
* Permitir que los usuarios inicien sesión automáticamente en Bonusly con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Bonusly, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener [una cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único en Bonusly.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Bonusly admite inicio de sesión único iniciado por **IDP**.

> [!NOTE]
> El identificador de esta aplicación es un valor de cadena fijo, por lo que solo se puede configurar una instancia en un inquilino.

## <a name="add-bonusly-from-the-gallery"></a>Adición de Bonusly desde la galería

Para configurar la integración de Bonusly en Azure AD, debe agregar Bonusly desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en Azure Portal con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Bonusly** en el cuadro de búsqueda.
1. Seleccione **Bonusly** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-sso-for-bonusly"></a>Configuración y prueba del inicio de sesión único de Azure AD para Bonusly

Configure y pruebe el inicio de sesión único de Azure AD con Bonusly mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario correspondiente de Bonusly.

Para configurar y probar el inicio de sesión único de Azure AD con Bonusly, siga estos pasos:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)**, para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Bonusly](#configure-bonusly-sso)** , para configurar los valores del inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Bonusly](#create-bonusly-test-user)** : para tener un homólogo de B. Simon en Bonusly que esté vinculado a su representación en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En Azure Portal, en la página de integración de la aplicación **Bonusly**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configuración del inicio de sesión único con SAML**, haga clic en el icono de lápiz de **Configuración básica de SAML** para editar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://Bonus.ly/saml/<TENANT_NAME>`

    > [!NOTE]
    > Este valor no es real. Actualícelo con la dirección URL de respuesta real. Póngase en contacto con el [equipo de soporte técnico al cliente de Bonusly](https://bonus.ly/contact) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. En la sección **Certificado de firma de SAML**, haga clic en el botón **Editar** para abrir el cuadro de diálogo **Certificado de firma de SAML**.

    ![Edición del certificado de firma de SAML](common/edit-certificate.png)

6. En la sección **Certificado de firma de SAML**, copie el valor de **Huella digital** y guárdelo en el equipo.

    ![Copia del valor de la huella digital](common/copy-thumbprint.png)

7. En la sección **Set up Bonusly** (Configurar Bonusly), copie las direcciones URL adecuadas según sus necesidades.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, va a crear un usuario de prueba llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B.Simon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon acceda a Bonusly mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Bonusly**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.
1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que se asigne un rol a los usuarios, puede seleccionarlo en la lista desplegable **Seleccionar un rol**. Si no se ha configurado ningún rol para esta aplicación, verá seleccionado el rol "Acceso predeterminado".
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-bonusly-sso"></a>Configuración del inicio de sesión único de Bonusly

1. En una ventana del explorador diferente, inicie sesión en su inquilino de **Bonusly**.

1. En la barra de herramientas de la parte superior, haga clic en **Configuración** y seleccione **Integraciones y aplicaciones**.

    ![Sección Bonusly Social](./media/bonus-tutorial/settings.png "Bonusly")
1. En **Inicio de sesión único**, seleccione **SAML**.

1. En la página de diálogo **SAML** , realice los pasos siguientes:

    ![Página del cuadro de diálogo de SAML de Bonusly](./media/bonus-tutorial/dialog-page.png "Bonusly")

    a. En el cuadro de texto **IdP SSO Target URL** (Dirección URL de destino de SSO de IdP), pegue el valor de **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    b. En el cuadro de texto **IdP Login URL** (URL de inicio de sesión del IdP), pegue el valor de la **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    c. En el cuadro de texto **Idp Issuer** (Emisor de IdP), pegue el valor de **Identificador de Azure AD** que ha copiado de Azure Portal.
    
    d. Pegue el valor de **Huella digital** de Azure Portal en el cuadro de texto **Cert Fingerprint** (Huella digital de certificado).
    
    e. Haga clic en **Save**(Guardar).

### <a name="create-bonusly-test-user"></a>Creación de un usuario de prueba de Bonusly

Para permitir que los usuarios de Azure AD inicien sesión en Bonusly, tienen que aprovisionarse en Bonusly. En el caso de Bonusly, el aprovisionamiento es una tarea manual.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Bonusly que proporcione Bonusly para aprovisionar cuentas de usuario de Azure AD. 

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. En una ventana de explorador web diferente, inicie sesión en su inquilino de Bonusly.

1. Haga clic en **Configuración**.

    ![Configuración](./media/bonus-tutorial/users.png "Configuración")

1. Haga clic en la pestaña **Usuarios y bonificaciones** .

    ![Usuarios y bonificaciones](./media/bonus-tutorial/manage-user.png "Usuarios y bonificaciones")

1. Haga clic en **Administrar usuarios**.

    ![Administración de usuarios](./media/bonus-tutorial/new-users.png "Administrar usuarios")

1. Haga clic en **Agregar usuario**.

    ![Captura de pantalla que muestra Administrar usuarios, donde puede seleccionar Agregar usuario.](./media/bonus-tutorial/add-tab.png "Agregar usuario")

1. En el cuadro de diálogo **Agregar usuario** , realice los pasos siguientes:

    ![Captura de pantalla que muestra el cuadro de diálogo Agregar usuario, donde puede especificar esta información.](./media/bonus-tutorial/select-user.png "Agregar usuario")  

    a. En el cuadro de texto **Nombre**, escriba el nombre de usuario, en este caso **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba el apellido del usuario, en este caso **Simon**.

    c. En el cuadro de texto **E-mail** (Correo electrónico), escriba el correo electrónico del usuario con el formato `brittasimon@contoso.com`.

    d. Haga clic en **Save**(Guardar).

    > [!NOTE]
    > El titular de la cuenta de Azure AD recibirá un mensaje de correo electrónico que incluye un vínculo para confirmar la cuenta antes de que se active.  

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD con las siguientes opciones.

* Haga clic en Probar esta aplicación en Azure Portal y debería iniciar sesión automáticamente en la instancia de Bonusly para la que ha configurado el inicio de sesión único.

* Puede usar Mis aplicaciones de Microsoft. Al hacer clic en el icono de Bonusly en Aplicaciones debería iniciar sesión automáticamente en la versión de Bonusly para la que configuró el inicio de sesión único. Para más información acerca de Aplicaciones, consulte [Inicio de sesión e inicio de aplicaciones desde el portal Aplicaciones](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado Bonusly, puede aplicar el control de sesión, que protege a la organización en tiempo real frente a la filtración e infiltración de información confidencial. El control de sesión procede del acceso condicional. [Aprenda a aplicar el control de sesión con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
