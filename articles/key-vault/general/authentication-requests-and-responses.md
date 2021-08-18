---
title: Autenticación, solicitudes y respuestas
description: Obtenga información sobre el modo en que Azure Key Vault usa solicitudes y respuestas con formato JSON y la autenticación necesaria para usar un almacén de claves.
services: key-vault
author: mbaldwin
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: fb8e929ad12fcb240cd47d3bec21a96087a38f4a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456686"
---
# <a name="authentication-requests-and-responses"></a>Autenticación, solicitudes y respuestas

Azure Key Vault proporciona dos tipos de contenedores para almacenar y administrar secretos de las aplicaciones en la nube:

|Tipo de contenedor|Tipos de objetos admitidos|Punto de conexión del plano de datos|
|--|--|--|
| **Almacenes**|<ul><li>Claves protegidas con software</li><li>Claves protegidas con HSM (con SKU Premium)</li><li>Certificados</li><li>Claves de cuenta de almacenamiento</li></ul> | https://{vault-name}.vault.azure.net
|**HSM administrado** |<ul><li>Claves protegidas con HSM</li></ul> | https://{hsm-name}.managedhsm.azure.net

Estos son los sufijos de dirección URL que se usan para acceder a cada tipo de objeto

|Tipo de objeto|Sufijo de dirección URL|
|--|--|
|Claves protegidas con software| /keys |
|Claves protegidas con HSM| /keys |
|Secretos|/secrets|
|Certificados| /certificates|
|Claves de cuenta de almacenamiento|/storageaccounts
||

Azure Key Vault admite solicitudes y respuestas con formato JSON. Las solicitudes a Azure Key Vault se dirigen a una dirección URL válida de Azure Key Vault mediante HTTPS con algunos parámetros de URL y cuerpos de solicitud y respuesta codificados en JSON.

En este tema se tratan los detalles específicos del servicio Azure Key Vault. Para más información acerca del uso de interfaces REST de Azure, incluida la autenticación y autorización, y cómo adquirir un token de acceso, consulte la [referencia de las API de REST de Azure](/rest/api/azure).

## <a name="request-url"></a>URL de la solicitud  
 Las operaciones de administración de claves utilizan HTTP DELETE, GET, PATCH, PUT y HTTP POST y las operaciones criptográficas contra objetos de clave existentes utilizan HTTP POST. Los clientes que no admiten verbos HTTP específicos también pueden usar HTTP POST con el encabezado X-HTTP-REQUEST para especificar el verbo deseado; las solicitudes que normalmente no requieren un cuerpo deben incluir un cuerpo vacío cuando se usa HTTP POST, por ejemplo cuando se usa POST en lugar de DELETE.  

 Para trabajar con objetos en Azure Key Vault, las siguientes son direcciones URL de ejemplo:  

- Para crear una clave llamada TESTKEY en una instancia de Key Vault, utilice `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Para importar una clave llamada IMPORTEDKEY a una instancia de Key Vault, utilice `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Para obtener un secreto denominado MYSECRET en una instancia de Key Vault, utilice `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Para firmar una síntesis del mensaje mediante una tecla llamada TESTKEY en una instancia de Key Vault, utilice `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

- La autoridad para una solicitud a una instancia de Key Vault es siempre la siguiente, 
  - Para almacenes: `https://{keyvault-name}.vault.azure.net/`
  - Para HSM administrados: `https://{HSM-name}.managedhsm.azure.net/`

  Las claves se almacenan siempre bajo la ruta de acceso /keys, los secretos se almacenan siempre bajo la ruta /secrets.  

## <a name="api-version"></a>Versión de API  
 El servicio Azure Key Vault admite el control de versiones de protocolos para proporcionar compatibilidad con clientes de nivel inferior, aunque no todas las funcionalidades estarán disponibles para esos clientes. Los clientes deben utilizar el parámetro `api-version` de la cadena de consulta para especificar la versión del protocolo que admiten, ya que no hay ninguno predeterminado.  

 Las versiones del protocolo de Azure Key Vault siguen un esquema de numeración de fechas con el formato {AAAA}.{MM}.{DD}.  

## <a name="request-body"></a>Cuerpo de la solicitud  
 Según la especificación HTTP, las operaciones GET NO deben tener un cuerpo de la solicitud, y las operaciones POST y PUT deben tener un cuerpo de la solicitud. El cuerpo en las operaciones DELETE es opcional en HTTP.  

 A menos que se indique lo contrario en la descripción de la operación, el tipo de contenido del cuerpo de la solicitud debe ser application/json y debe contener un objeto JSON serializado conforme al tipo de contenido.  

 A menos que se indique lo contrario en la descripción de la operación, el encabezado de la solicitud Accept debe contener el tipo de medio application/json.  

## <a name="response-body"></a>Cuerpo de la respuesta  
 A menos que se indique lo contrario en la descripción de la operación, el tipo de contenido del cuerpo de respuesta tanto de las operaciones correctas como de las erróneas será application/json y contiene información detallada sobre los errores.  

## <a name="using-http-post"></a>Uso de HTTP POST  
 Es posible que algunos clientes no puedan usar ciertos verbos HTTP, como PATCH o DELETE. Azure Key Vault admite HTTP POST como una alternativa para estos clientes siempre que el cliente también incluya el encabezado "X-HTTP-METHOD" para especificar el verbo HTTP original. La compatibilidad para HTTP POST se indica para cada una de las API definidas en este documento.  

## <a name="error-responses"></a>Respuestas de error  
 El control de errores utilizará códigos de estado HTTP. Los resultados típicos son:  

- 2xx: Correcto: se utiliza para el funcionamiento normal. El cuerpo de la respuesta contendrá el resultado esperado  

- 3xx - Redireccionamiento: se puede devolver el 304 "No modificado" para completar una operación GET condicional. Se pueden usar otros códigos 3xx en el futuro para indicar nombres de sistema de dominio y cambios de ruta de acceso.  

- 4xx - Error de cliente: se utiliza para solicitudes erróneas, claves que faltan, errores de sintaxis, parámetros no válidos, errores de autenticación, etc. El cuerpo de la respuesta contendrá una explicación detallada del error.  

- 5xx - Error de servidor: se utiliza para errores internos del servidor. El cuerpo de la respuesta contendrá información resumida sobre el error.  

  El sistema está diseñado para funcionar detrás de un proxy o firewall. Por lo tanto, el cliente puede recibir otros códigos de error.  

  Azure Key Vault también devuelve información de error en el cuerpo de la respuesta cuando ocurre un problema. El cuerpo de la respuesta tiene formato JSON y adopta la forma:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Authentication  
 Todas las solicitudes a Azure Key Vault DEBEN autenticarse. Azure Key Vault admite tokens de acceso a Azure Active Directory que pueden obtenerse mediante OAuth2[[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Para más información acerca de cómo registrar la aplicación y autenticarse para usar Azure Key Vault, consulte [Registro de la aplicación cliente con Azure AD](/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Los tokens de acceso deben enviarse al servicio mediante el encabezado de autorización HTTP:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Cuando no se proporciona un token de acceso, o cuando el servicio no acepta el token, se devolverá un error HTTP 401 al cliente e incluirá el encabezado WWW-Authenticate, por ejemplo:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Los parámetros del encabezado WWW-Authenticate son:  

-   authorization: la dirección del servicio de autorización de OAuth2 que puede utilizarse para obtener un token de acceso para la solicitud.  

-   resource: Nombre del recurso (`https://vault.azure.net`) que se va a utilizar en la solicitud de autorización.

> [!NOTE]
> Los clientes de Key Vault SDK que solicitan secretos, certificados y claves en la primera llamada a Key Vault no proporcionan ningún token de acceso para recuperar información de los inquilinos. Lo esperable cuando se utiliza un cliente de Key Vault SDK es recibir una respuesta HTTP 401, en la que Key Vault mostrará a la aplicación el encabezado WWW-Authenticate que contiene el recurso y el inquilino en el que debe ir y solicitar el token. Si todo está configurado correctamente, la segunda llamada de la aplicación a Key Vault contendrá un token válido y se realizará correctamente. 
