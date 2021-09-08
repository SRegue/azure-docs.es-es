---
title: Sustitución de claves de firma en la Plataforma de identidad de Microsoft
description: En este artículo se analizan las prácticas recomendadas de sustitución de claves de firma de Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/03/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: f6073ac0da9163756be353c2ed695dfb20430160
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123469399"
---
# <a name="signing-key-rollover-in-the-microsoft-identity-platform"></a>Sustitución de claves de firma en la Plataforma de identidad de Microsoft
En este artículo se habla de lo que se necesita saber sobre las claves públicas que se usan en la Plataforma de identidad de Microsoft para firmar los tokens de seguridad. Es importante tener en cuenta que estas claves se sustituyen de forma periódica y, en caso de emergencia, podrían ser sustituidas inmediatamente. Todas las aplicaciones que usan la Plataforma de identidad de Microsoft deben poder controlar el proceso de sustitución de claves mediante programación. Siga leyendo para comprender cómo funcionan las claves, cómo evaluar el impacto de la sustitución en la aplicación y cómo actualizar la aplicación o establecer un proceso de sustitución manual periódico para controlar la sustitución de claves si fuera necesario.

## <a name="overview-of-signing-keys-in-the-microsoft-identity-platform"></a>Introducción a las claves de firma en la Plataforma de identidad de Microsoft
La Plataforma de identidad de Microsoft emplea una criptografía de clave pública basada en estándares del sector con el fin de establecer una relación de confianza entre ella y las aplicaciones que la usan. En la práctica, esto funciona de la manera siguiente: La Plataforma de identidad de Microsoft usa una clave de firma que consta de un par de claves pública y privada. Cuando un usuario inicia sesión en una aplicación que usa la Plataforma de identidad de Microsoft para la autenticación, esta plataforma crea un token de seguridad que contiene información sobre el usuario. La Plataforma de identidad de Microsoft firma este token con su clave privada antes de enviarlo a la aplicación. Para comprobar que el token es válido y que se ha originado en la Plataforma de identidad de Microsoft, la aplicación debe validar la firma del token mediante las claves públicas expuestas por la Plataforma de identidad de Microsoft incluida en el [documento de detección de OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html) del inquilino o el [documento de metadatos de federación](../azuread-dev/azure-ad-federation-metadata.md) de SAML/WS-Fed.

Por motivos de seguridad, la Plataforma de identidad de Microsoft sustituye la clave de firma de forma periódica y, en caso de emergencia, podría hacerlo de inmediato. No hay ningún tiempo establecido ni garantizado entre estas sustituciones de claves: cualquier aplicación que se integre con la Plataforma de identidad de Microsoft debe estar preparada para controlar un evento de sustitución de claves independientemente de la frecuencia con que se produzca. Si la aplicación no controla las actualizaciones repentinas e intenta usar una clave expirada para comprobar la firma en un token, la aplicación rechazará incorrectamente el token.  La comprobación de actualizaciones cada 24 horas es un procedimiento recomendado, con actualizaciones inmediatas limitadas (una vez cada cinco minutos como máximo) del documento de claves si se encuentra un token que no valida con las claves de la memoria caché de la aplicación. 

Siempre hay más de una clave válida disponible en el documento de detección de OpenID Connect y en el de metadatos de federación. La aplicación debe estar preparada para usar todas y cada una de las claves especificadas en el documento, ya que una clave se puede sustituir pronto, otra puede ser su reemplazo, etc.  El número de claves presentes puede cambiar con el tiempo en función de la arquitectura interna de Plataforma de identidad de Microsoft a media que se admitan nuevas plataformas, nuevas nubes o nuevos protocolos de autenticación. Ni el orden de las claves en la respuesta JSON ni el orden en el que se hayan expuesto se deben considerar significativos para la aplicación. 

Las aplicaciones que solo admiten una única clave de firma, o las que requieren actualizaciones manuales de las claves de firma, son intrínsecamente menos seguras y menos confiables.  Deben actualizarse para usar [bibliotecas estándar](reference-v2-libraries.md) a fin de garantizar que siempre usen claves de firma actualizadas, entre otros procedimientos recomendados. 

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Cómo evaluar si su aplicación se verá afectada y qué hacer al respecto
La forma que tiene la aplicación de controlar la sustitución de claves depende de ciertas variables, como el tipo de aplicación o qué protocolo de identidad y biblioteca se han usado. Las secciones siguientes evalúan si los tipos más comunes de aplicaciones se ven afectados por la sustitución de claves y ofrecen orientación sobre cómo actualizar la aplicación para admitir la sustitución automática o actualizar manualmente la clave.

* [Aplicaciones de cliente nativas que acceden a recursos](#nativeclient)
* [Aplicaciones y API web que acceden a recursos](#webclient)
* [Aplicaciones y API web que protegen recursos y creadas mediante Azure App Service](#appservices)
* [Aplicaciones y API web que protegen recursos mediante middleware .NET OWIN OpenID Connect, WS-Fed o WindowsAzureActiveDirectoryBearerAuthentication](#owin)
* [Aplicaciones y API web que protegen recursos mediante el middleware .NET Core OpenID Connect o JwtBearerAuthentication](#owincore)
* [Aplicaciones y API web que protegen recursos mediante el módulo Node.js passport-azure-ad](#passport)
* [Aplicaciones y API web de protección de recursos y creadas con Visual Studio 2015 o versión posterior](#vs2015)
* [Aplicaciones Web de protección de recursos y creadas con Visual Studio 2013](#vs2013)
* API web de protección de recursos y creadas con Visual Studio 2013
* [Aplicaciones web de protección de recursos y creadas con Visual Studio 2012](#vs2012)
* [Aplicaciones y API web de protección de recursos que usan cualquier otra biblioteca o que implementan manualmente cualquiera de los protocolos admitidos](#other)

Esta guía **no** es aplicable para:

* Las aplicaciones agregadas desde la galería de aplicaciones de Azure AD (incluidas las personalizadas) disponen de instrucciones independientes para las claves de firma. [Más información.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Las aplicaciones locales publicadas a través del proxy de la aplicación no tienen que preocuparse acerca de las claves de firma.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Aplicaciones de cliente nativas que acceden a recursos
Las aplicaciones que solo acceden a los recursos (es decir, Microsoft Graph, KeyVault, API de Outlook y otras API de Microsoft) únicamente obtienen por lo general un token y lo pasan al propietario del recurso. Como no protegen ningún recurso, no inspeccionan el token y, por tanto, no tienen que asegurar de que se firmó correctamente.

Las aplicaciones de cliente nativo, ya sean de escritorio o móviles, entran en esta categoría y, por tanto, no se verán afectadas por la sustitución.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Aplicaciones y API web que acceden a recursos
Las aplicaciones que solo acceden a los recursos (es decir, Microsoft Graph, KeyVault, API de Outlook y otras API de Microsoft) únicamente obtienen por lo general un token y lo pasan al propietario del recurso. Como no protegen ningún recurso, no inspeccionan el token y, por tanto, no tienen que asegurar de que se firmó correctamente.

Las aplicaciones web y las API web que usan el flujo solo de aplicación (credenciales de cliente o certificado de cliente) para solicitar tokens entran en esta categoría y, por tanto, no se ven afectadas por la sustitución.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Aplicaciones y API web que protegen recursos y creadas mediante Azure App Service
La funcionalidad de autenticación o autorización (EasyAuth) de Azure App Service ya cuenta con la lógica necesaria para controlar automáticamente la sustitución de claves.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>Aplicaciones y API web que protegen recursos mediante middleware .NET OWIN OpenID Connect, WS-Fed o WindowsAzureActiveDirectoryBearerAuthentication
Si la aplicación está utilizando el middleware .NET OWIN OpenID Connect, WS-Fed o WindowsAzureActiveDirectoryBearerAuthentication, ya tiene la lógica necesaria para controlar automáticamente la sustitución de clave.

Puede confirmar que la aplicación utiliza cualquiera de estos, busque cualquiera de los siguientes fragmentos de código en los archivos Startup.cs o Startup.Auth.cs de la aplicación.

```csharp
app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWindowsAzureActiveDirectoryBearerAuthentication(
    new WindowsAzureActiveDirectoryBearerAuthenticationOptions
    {
        // ...
    });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>Aplicaciones y API web que protegen recursos mediante el middleware .NET Core OpenID Connect o JwtBearerAuthentication
Si la aplicación usa el middleware .NET Core OWIN OpenID Connect o JwtBearerAuthentication, ya tiene la lógica necesaria para controlar automáticamente la sustitución de claves.

Puede confirmar que la aplicación utiliza cualquiera de estos, busque cualquiera de los siguientes fragmentos de código en la aplicación Startup.cs o Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Aplicaciones y API web que protegen recursos mediante el módulo Node.js passport-azure-ad
Si la aplicación está utilizando el módulo Node.js passport-ad, ya tiene la lógica necesaria para controlar automáticamente la sustitución de clave.

Puede confirmar su aplicación passport-ad si busca el siguiente fragmento en el archivo app.js de la aplicación

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Aplicaciones y API web de protección de recursos y creadas con Visual Studio 2015 o versión posterior
Si la aplicación se compiló mediante una plantilla de aplicación web en Visual Studio 2015 o versión posterior y seleccionó **Cuentas profesionales o educativas** en el menú **Cambiar autenticación**, ya tiene la lógica necesaria para controlar automáticamente la sustitución de claves. Esta lógica, inserta en el middleware OWIN OpenID Connect, recupera y almacena en caché las claves del documento de detección OpenID Connect y las actualiza periódicamente.

Si ha agregado manualmente la autenticación a la solución, la aplicación no tendrá la lógica necesaria para la sustitución de claves. Tendrá que escribirlo usted mismo o seguir los pasos que aparecen en [Aplicaciones y API web que usan cualquier otra biblioteca o que implementan manualmente cualquiera de los protocolos admitidos](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Aplicaciones Web de protección de recursos y creadas con Visual Studio 2013
Si la aplicación se compiló mediante una plantilla de aplicación web en Visual Studio 2013 y seleccionó **Cuentas profesionales** en el menú **Cambiar autenticación**, ya tiene la lógica necesaria para controlar automáticamente la sustitución de claves. Esta lógica almacena la información de la clave de firma y el identificador único de la organización en dos tablas de base de datos asociadas al proyecto. Puede encontrar la cadena de conexión de la base de datos en el archivo Web.config del proyecto.

Si ha agregado manualmente la autenticación a la solución, la aplicación no tendrá la lógica necesaria para la sustitución de claves. Tendrá que escribirla usted mismo o seguir los pasos que aparecen en [Aplicaciones y API web de protección de recursos que usan cualquier otra biblioteca o que implementan manualmente cualquiera de los protocolos admitidos](#other).

Los siguientes pasos lo ayudarán a comprobar que la lógica funcione correctamente en la aplicación.

1. En Visual Studio 2013, abra la solución y haga clic en la pestaña **Explorador de servidores** de la ventana derecha.
2. Expanda **Conexiones de datos**, **DefaultConnection** y **Tablas**. Busque la tabla **IssuingAuthorityKeys**, haga clic con el botón derecho en ella y, después, con el botón izquierdo, en **Mostrar datos de tabla**.
3. En la tabla **IssuingAuthorityKeys** habrá al menos una fila, que corresponde al valor de la huella digital de la clave. Elimine las filas de la tabla.
4. Haga clic con el botón derecho en la tabla **Inquilinos** y, después, con el botón izquierdo, en **Mostrar datos de tabla**.
5. En la tabla **Inquilinos** habrá al menos una fila, que corresponde a un identificador único del inquilino de directorio. Elimine las filas de la tabla. Si no elimina las filas de las tablas **Inquilinos** e **IssuingAuthorityKeys**, se producirá un error en el entorno de tiempo de ejecución.
6. Compile y ejecute la aplicación. Una vez que haya iniciado sesión en la cuenta, podrá detener la aplicación.
7. Vuelva a la pestaña **Explorador de servidores** y examine los valores de las tablas **IssuingAuthorityKeys** e **Inquilinos**. Observará que se han vuelto a rellenar automáticamente con la información correspondiente del documento de metadatos de federación.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>API web de protección de recursos y creadas con Visual Studio 2013
Si creó una aplicación API web en Visual Studio 2013 con la plantilla de API web y, después, seleccionó **Cuentas profesionales** en el menú **Cambiar autenticación**, la aplicación ya tiene la lógica necesaria.

Si configura manualmente la autenticación, siga estas instrucciones para aprender a configurar la API web con el fin de actualizar automáticamente la información de claves.

El fragmento de código siguiente muestra cómo obtener las claves más recientes del documento de metadatos de federación y utilizar el [Controlador de token web de JSON](/previous-versions/dotnet/framework/windows-identity-foundation/json-web-token-handler) para validar el token. En el fragmento de código se da por hecho que va a utilizar su propio mecanismo de almacenamiento en caché para conservar la clave con el fin de validar los tokens futuros de la Plataforma de identidad de Microsoft, ya sea en una base de datos, un archivo de configuración o en otro lugar.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Aplicaciones web de protección de recursos y creadas con Visual Studio 2012
Si la aplicación se compiló en Visual Studio 2012, probablemente ha utilizado la herramienta de identidad y acceso para configurar la aplicación. También es probable que esté utilizando el [registro de nombres de emisor de validación (VINR)](/previous-versions/dotnet/framework/windows-identity-foundation/validating-issuer-name-registry). El VINR se encarga de mantener la información sobre los proveedores de identidad de confianza (Plataforma de identidad de Microsoft) y las claves utilizadas para validar los tokens que emiten. El VINR también facilita la tarea de actualizar automáticamente la información de claves almacenada en un archivo Web.config descargando el documento de metadatos de federación más reciente asociado a su directorio, comprobando si la configuración está actualizada con respecto al último documento y actualizando la aplicación para usar la nueva clave según sea necesario.

Si ha creado la aplicación utilizando cualquiera de los ejemplos de código o la documentación de tutorial que proporciona Microsoft, la lógica de sustitución de claves ya estará incluida en el proyecto. Observará que el código siguiente ya existe en el proyecto. Si la aplicación aún no tiene esta lógica, siga estos pasos para agregarla y comprobar que funciona correctamente.

1. En **Explorador de soluciones**, agregue una referencia al ensamblado **System.IdentityModel** del proyecto correspondiente.
2. Abra el archivo **Global.asax.cs** y agregue lo siguiente utilizando directivas:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Agregue el método siguiente al archivo **Global.asax.cs** :
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Invoque el método **RefreshValidationSettings()** en el método **Application_Start()** de **Global.asax.cs**, tal y como se muestra:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Una vez que haya seguido estos pasos, el archivo Web.config de la aplicación se actualizará con la información más reciente del documento de metadatos de federación, incluidas las últimas claves. Esta actualización se producirá cada vez que recicle el grupo de aplicaciones de IIS; de forma predeterminada, esta acción se realizará cada 29 horas.

Siga los pasos que figuran a continuación para comprobar que la lógica de sustitución de claves funciona correctamente.

1. Una vez que haya comprobado que la aplicación está utilizando el código anterior, abra el archivo **Web.config** y desplácese al bloque **\<issuerNameRegistry>** ; busque expresamente las siguientes líneas:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. En la tabla **\<add thumbprint="">** , cambie el valor de la huella digital reemplazando cualquier carácter por otro diferente. Guarde el archivo **Web.config** .
3. Compile la aplicación y, después, ejecútela. Si puede completar el proceso de inicio de sesión, la aplicación actualizará correctamente la clave descargando la información necesaria del documento de metadatos de federación de su directorio. Si tiene problemas para iniciar sesión, asegúrese de que los cambios en la aplicación sean correctos; para ello, consulte el artículo sobre [Incorporación del inicio de sesión único en aplicaciones web mediante la Plataforma de identidad de Microsoft](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect), o descargue e inspeccione el siguiente ejemplo de código: [Multi-Tenant Cloud Application for Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b) (Aplicación multiinquilino en la nube para Azure Active Directory).

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Aplicaciones y API web de protección de recursos que usan cualquier otra biblioteca o que implementan manualmente cualquiera de los protocolos admitidos
Si está utilizando otra biblioteca o implementa manualmente cualquiera de los protocolos admitidos, debe revisar la biblioteca o la implementación para asegurarse de que se está recuperando la clave desde el documento de detección OpenID Connect o el documento de metadatos de federación. Una forma de comprobarlo es realizar una búsqueda en el código o en el de la biblioteca de las llamadas al documento de detección OpenID o al documento de metadatos de federación.

Si la clave se está almacenando en algún lugar o está incrustada directamente en la aplicación, puede recuperarla de forma manual y actualizarla según corresponda; para ello, realice una sustitución manual de acuerdo con las instrucciones al final de este documento de orientación. **Se recomienda encarecidamente mejorar la aplicación para que admita la sustitución automática** mediante cualquiera de los enfoques descritos en este artículo a fin de evitar futuras interrupciones y la sobrecarga si la Plataforma de identidad de Microsoft aumenta su cadencia de sustitución o se produce una sustitución de emergencia no programada.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Cómo probar la aplicación para determinar si se verá afectada
Puede validar si la aplicación admite la sustitución automática de claves descargando los scripts y siguiendo las instrucciones de [este repositorio de GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Cómo realizar una sustitución manual, si la aplicación no admite la sustitución automática
Si la aplicación **no** admite la sustitución automática, debe establecer un proceso que supervise periódicamente las claves de firma de la Plataforma de identidad de Microsoft y realizar una sustitución manual cuando corresponda. [Este repositorio de GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contiene scripts e instrucciones sobre cómo hacerlo.
