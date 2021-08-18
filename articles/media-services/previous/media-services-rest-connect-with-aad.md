---
title: Usar autenticación de Azure AD para acceder a la API de Azure Media Services con REST | Microsoft Docs
description: Aprenda a acceder a la API de Azure Media Services con la autenticación de Azure Active Directory mediante REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: f20b4f2ac34a7e601fe07a9b631bb561bf46f54f
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114712413"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Uso de la autenticación de Azure AD para acceder a la API de Media Services con REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [v2 deprecation notice](../latest/includes/v2-deprecation-notice.md)]

Si usa la autenticación de Azure AD con Azure Media Services, puede autenticarse de alguna de estas dos formas:

- **Autenticación de usuario** autentica a una persona que está usando la aplicación para interactuar con los recursos de Azure Media Services. La aplicación interactiva en primer lugar debe solicitar al usuario las credenciales. Un ejemplo es una aplicación de consola de administración que usan los usuarios autorizados para supervisar trabajos de codificación o streaming en vivo. 
- **Autenticación de entidad de servicio** autentica un servicio. Las aplicaciones que normalmente utilizan este método de autenticación son aplicaciones que ejecutan servicios de demonio, servicios de nivel intermedio o trabajos programados, como Web Apps, Function Apps, Logic Apps, API o microservicios.

    Este tutorial muestra cómo usar la autenticación de la **entidad de servicio** de Azure AD para tener acceso a las API de AMS con REST. 

    > [!NOTE]
    > La **entidad de servicio** es la práctica recomendada para la mayoría de las aplicaciones que se conectan a Azure Media Services. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Obtener la información de autenticación desde Azure Portal
> * Obtener el token de acceso mediante Postman
> * Probar la API **Assets** con el token de acceso


> [!IMPORTANT]
> Actualmente Media Services es compatible con el modelo de autenticación de Azure Access Control Service. No obstante, la autenticación de Access Control dejará de usarse el 1 de junio de 2018. Se recomienda migrar tan pronto como sea posible al modelo de autenticación de Azure AD.

## <a name="prerequisites"></a>Prerequisites

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.
- [Cree una cuenta de Azure Media Services mediante Azure Portal](media-services-portal-create-account.md).
- Revise el artículo [Acceso a Azure Media Services API con la autenticación de Azure AD](media-services-use-aad-auth-to-access-ams-api.md).
- Instale el cliente de REST de [Postman](https://www.getpostman.com/) para ejecutar las API de REST que aparecen en este artículo. 

    En este tutorial usamos **Postman**, pero cualquier herramienta de REST sería adecuada. Otras alternativas son: **Visual Studio Code** con el complemento de REST o **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Obtener la información de autenticación desde Azure Portal

### <a name="overview"></a>Información general

Para obtener acceso a la API de Media Services, debe recopilar los siguientes puntos de datos.

|Configuración|Ejemplo|Descripción|
|---|-------|-----|
|Dominio del inquilino de Azure Active Directory|microsoft.onmicrosoft.com|Azure AD como punto de conexión de servicio de token seguro (STS) se crea con el siguiente formato: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>. Azure AD emite un JWT para tener acceso a los recursos (un token de acceso).|
|Punto de conexión de la API de REST|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Este es el punto de conexión en el que se realizan todas las llamadas API de REST de Media Services en la aplicación.|
|Identificador de cliente (identificador de aplicación)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Identificador de aplicación de Azure AD (cliente). El identificador de cliente es necesario para obtener el token de acceso. |
|Secreto del cliente|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Claves de aplicación de Azure AD (secreto del cliente). El secreto de cliente es necesario para obtener el token de acceso.|

### <a name="get-azure-active-directory-auth-info-from-the-azure-portal"></a>Obtención de la información de autorización de Azure Active Directory desde Azure Portal

Para obtener la información, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Vaya a la instancia de APIM.
3. Seleccione el **acceso API**.
4. Haga clic en **Conectarse a la API de Azure Media Services con la entidad de servicio**.

    ![Captura de pantalla que muestra la opción "acceso API" seleccionada en el menú "Media Services" y la opción "Conectarse a la API de Azure Media Services con la entidad de servicio" seleccionada en el panel derecho.](./media/connect-with-rest/connect-with-rest01.png)

5. Seleccione una **aplicación de Azure AD** o cree una nueva (como se muestra a continuación).

    > [!NOTE]
    > Para que la solicitud de REST de Azure Media Services se complete correctamente, el usuario que realiza la llamada debe tener el rol de **colaborador** o **propietario** de la cuenta de Media Services a la que está intentando obtener acceso. Si se produce una excepción que indica "El servidor remoto devolvió un error: (401) No autorizado", vea [Control de acceso](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Si necesita crear una nueva aplicación de AD, siga estos pasos:
    
   1. Presione **Crear nuevo**.
   2. Escriba un nombre.
   3. Presione **Crear nuevo** otra vez.
   4. Presione **Save**(Guardar).

      ![Captura de pantalla que muestra el cuadro de diálogo "Crear nuevo" con el cuadro de texto "Crear aplicación" resaltado y el botón "Guardar" seleccionado.](./media/connect-with-rest/new-app.png)

      La nueva aplicación se muestra en la página.

6. Obtenga el **identificador de cliente** (identificador de aplicación).
    
   1. Seleccione la aplicación.
   2. Obtenga el **identificador de cliente** en la ventana de la derecha. 

      ![Captura de pantalla que muestra las opciones "Aplicación de Azure AD" y "Administrar aplicación" seleccionadas, y el "id. de cliente" resaltado en el panel derecho.](./media/connect-with-rest/existing-client-id.png)

7. Obtenga la **clave** de la aplicación (secreto del cliente). 

   1. Haga clic en el botón **Administrar aplicación** (observe que la información del identificador de cliente figura debajo del **identificador de aplicación**). 
   2. Presione **Claves**.
    
       ![Captura de pantalla que muestra el botón "Administrar aplicación" seleccionado, "Id. de aplicación" en el panel central resaltado y "Claves" seleccionado en el panel derecho.](./media/connect-with-rest/manage-app.png)
   3. Genere la clave de aplicación (secreto del cliente); para ello, complete **DESCRIPCIÓN** y **EXPIRA** y presione **Guardar**.
    
       Una vez que se presione el botón **Guardar**, aparece el valor de la clave. Copie el valor de la clave antes de salir de la hoja.

   ![Acceso de API](./media/connect-with-rest/connect-with-rest03.png)

Puede agregar valores para parámetros de conexión de AD en el archivo web.config o app.config para su uso posterior en el código.

> [!IMPORTANT]
> La **clave de cliente** es un secreto importante y debe protegerse correctamente en un almacén de claves o cifrarse en producción.

## <a name="get-the-access-token-using-postman"></a>Obtener el token de acceso mediante Postman

Esta sección muestra cómo usar **Postman** para ejecutar una API de REST que devuelve un token de portador JWT (token de acceso). Para llamar a cualquier API de REST de Media Services, debe agregar el encabezado "Authorization" a las llamadas y agregar el valor de "Bearer *su_token_de_acceso*" a cada llamada (como se muestra en la siguiente sección de este tutorial). 

1. Abra **Postman**.
2. Seleccione **POST**.
3. Escriba la dirección URL que incluye el nombre de inquilino con el formato siguiente: el nombre del inquilino debe terminar por **. onmicrosoft.com** y la dirección URL debe terminar por **oauth2/token**: 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Seleccione la pestaña **Encabezados**.
5. Escriba la información de **Encabezados** mediante la cuadrícula de datos "Clave/Valor". 

    ![Captura de pantalla que muestra la pestaña "Encabezados" y la acción "Edición en masa" seleccionada.](./media/connect-with-rest/headers-data-grid.png)

    También puede hacer clic en **Edición en masa** a la derecha de la ventana de Postman y pegar el código siguiente.

    ```javascript
    Content-Type:application/x-www-form-urlencoded
    Keep-Alive:true
    ```

6. Presione la pestaña **Cuerpo**.
7. Escriba la información del cuerpo con la cuadrícula de datos "Clave/Valor" (reemplace los valores de identificador y secreto del cliente). 

    ![Cuadrícula de datos](./media/connect-with-rest/data-grid.png)

    También puede hacer clic en **Edición en masa** a la derecha de la ventana de Postman y pegar el siguiente cuerpo (reemplace los valores de identificador y secreto del cliente):

    ```javascript
    grant_type:client_credentials
    client_id:{Your Client ID that you got from your Azure AD Application}
    client_secret:{Your client secret that you got from your Azure AD Application's Keys}
    resource:https://rest.media.azure.net
    ```

8. Presione **Enviar**.

    ![Captura de pantalla que muestra el cuadro de texto "Publicar", las pestañas "Encabezados" y "Cuerpo", y "access_token" resaltado, con el botón "Enviar" detectado.](./media/connect-with-rest/connect-with-rest04.png)

La respuesta que se devuelve contiene el **token de acceso** que debe usar para tener acceso a las API de AMS.

## <a name="test-the-assets-api-using-the-access-token"></a>Probar la API **Assets** con el token de acceso

Esta sección muestra cómo obtener acceso a la API **Assets** mediante **Postman**.

1. Abra **Postman**.
2. Seleccione **GET**.
3. Pegue el punto de conexión de REST API (por ejemplo, https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Seleccione la pestaña **Autorización**. 
5. Seleccione **Token de portador**.
6. Pegue el token que se creó en la sección anterior.

    ![obtención del token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Postman UX puede variar entre un Mac y un PC. Si la versión de Mac no tiene la opción "Token de portador" en el desplegable de la sección **Autenticación**, debe agregar el encabezado **Autorización** manualmente en el cliente de Mac.

   ![Encabezado de autenticación](./media/connect-with-rest/auth-header.png)

7. Seleccione **Encabezados**.
5. Haga clic en el vínculo **Edición en masa** a la derecha de la ventana de Postman.
6. Pegue los encabezados siguientes:

    ```javascript
    x-ms-version:2.19
    Accept:application/json
    Content-Type:application/json
    DataServiceVersion:3.0
    MaxDataServiceVersion:3.0
    ```

7. Presione **Enviar**.

La respuesta que se devuelve contiene los activos que están en su cuenta.

## <a name="next-steps"></a>Pasos siguientes

* Pruebe el código de ejemplo en [Azure AD Authentication for Azure Media Services Access: Both via REST API](https://github.com/willzhan/WAMSRESTSoln) (Autenticación de Azure AD para el acceso a Azure Media Services: a través de la API de REST).
* [Cargar archivos mediante .NET](media-services-dotnet-upload-files.md)
