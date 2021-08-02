---
title: Configuración de la autenticación mutua TLS
description: Aprenda a autenticar certificados de cliente en TLS. Azure App Service puede hacer que el certificado de cliente esté disponible en el código de la aplicación para su comprobación.
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.topic: article
ms.date: 12/11/2020
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 6b58b73235bba53bb174ebb17a63ad76cf71bcf1
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110787807"
---
# <a name="configure-tls-mutual-authentication-for-azure-app-service"></a>Configuración de la autenticación mutua de TLS en Azure App Service

Puede restringir el acceso a una aplicación de Azure App Service habilitando diferentes tipos de autenticación. Una manera de hacerlo consiste en solicitar un certificado de cliente cuando la solicitud de cliente se realice a través de TLS/SSL y validar el certificado. Este mecanismo se denomina autenticación mutua de TLS o autenticación de certificado de cliente. En este artículo se muestra cómo configurar la aplicación para usar la autenticación de certificado de cliente.

> [!NOTE]
> Si accede a su sitio a través de HTTP y no de HTTPS, no recibirá ningún certificado de cliente. Por lo tanto, si la aplicación requiere certificados de cliente, no debe permitir solicitudes a la aplicación mediante HTTP.
>

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="enable-client-certificates"></a>Habilitación de certificados de cliente

Para configurar la aplicación a fin de que requiera certificados de cliente:

1. En la navegación de la izquierda de la página de administración de la aplicación, seleccione **Configuración** > **Configuración general**.

1. Establezca **Modo de certificado de cliente** en **Requerir**. Haga clic en **Guardar** en la parte superior de la página.

Para hacer lo mismo con la CLI de Azure, ejecute el siguiente comando en [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp update --set clientCertEnabled=true --name <app-name> --resource-group <group-name>
```

## <a name="exclude-paths-from-requiring-authentication"></a>Exclusión de rutas de acceso para que no requieran autenticación

Si habilita la autenticación mutua en su aplicación, todas las rutas de acceso situadas bajo la raíz de la aplicación necesitan un certificado de cliente para obtener acceso. Para eliminar este requisito para determinadas rutas de acceso, defina las rutas de acceso de exclusión como parte de la configuración de la aplicación.

1. En la navegación de la izquierda de la página de administración de la aplicación, seleccione **Configuración** > **Configuración general**.

1. Junto a **Client exclusion paths** (Rutas de acceso de exclusión del cliente), haga clic en el icono de edición.

1. Haga clic en **Nueva ruta de acceso**, especifique una ruta de acceso o una lista de rutas de acceso separadas por `,` o `;`, y haga clic en **Aceptar**.

1. Haga clic en **Guardar** en la parte superior de la página.

En la siguiente captura de pantalla, toda ruta de acceso para la aplicación que empiece por `/public` no solicitará un certificado de cliente. La coincidencia de rutas de acceso no distingue mayúsculas y minúsculas.

![Rutas de exclusión de certificados][exclusion-paths]

## <a name="access-client-certificate"></a>Acceso al certificado de cliente

En App Service, la terminación TLS de la solicitud tiene lugar en el equilibrador de carga de front-end. Al reenviar la solicitud al código de la aplicación con [certificados de cliente habilitados](#enable-client-certificates), App Service inserta un encabezado de solicitud `X-ARR-ClientCert` con el certificado de cliente. App Service no hace nada con este certificado de cliente aparte de reenviarlo a la aplicación. El código de la aplicación es responsable de validar el certificado de cliente.

En ASP.NET, el certificado de cliente está disponible mediante la propiedad **HttpRequest.ClientCertificate**.

En otras pilas de aplicación (Node.js, PHP, etc.), el certificado de cliente está disponible en la aplicación mediante un valor codificado en base64 en el encabezado de solicitud `X-ARR-ClientCert`.

## <a name="aspnet-5-aspnet-core-31-sample"></a>Ejemplo de ASP.NET 5+, ASP.NET Core 3.1

Para ASP.NET Core, se proporciona middleware para analizar los certificados reenviados. Se proporciona middleware independiente para usar los encabezados de protocolo reenviados. Ambos deben estar presentes para que se acepten los certificados reenviados. Puede colocar la lógica de validación de certificados personalizada en las [opciones de CertificateAuthentication](/aspnet/core/security/authentication/certauth).

```csharp
public class Startup
{
    public Startup(IConfiguration configuration)
    {
        Configuration = configuration;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        // Configure the application to use the protocol and client ip address forwared by the frontend load balancer
        services.Configure<ForwardedHeadersOptions>(options =>
        {
            options.ForwardedHeaders =
                ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        });       
        
        // Configure the application to client certificate forwarded the frontend load balancer
        services.AddCertificateForwarding(options => { options.CertificateHeader = "X-ARR-ClientCert"; });

        // Add certificate authentication so when authorization is performed the user will be created from the certificate
        services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate();
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }
        else
        {
            app.UseExceptionHandler("/Home/Error");
            app.UseHsts();
        }
        
        app.UseForwardedHeaders();
        app.UseCertificateForwarding();
        app.UseHttpsRedirection();

        app.UseAuthentication()
        app.UseAuthorization();

        app.UseStaticFiles();

        app.UseRouting();
        
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllerRoute(
                name: "default",
                pattern: "{controller=Home}/{action=Index}/{id?}");
        });
    }
}
```

## <a name="aspnet-webforms-sample"></a>Ejemplo de WebForms de ASP.NET

```csharp
    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class Cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                return true;
            }
        }
    }
```

## <a name="nodejs-sample"></a>Ejemplo de Node.js

El siguiente código de ejemplo de Node.js obtiene el encabezado `X-ARR-ClientCert` y usa [nodo-forge](https://github.com/digitalbazaar/forge) para convertir la cadena PEM codificada en base64 en un objeto de certificado y validarlo:

```javascript
import { NextFunction, Request, Response } from 'express';
import { pki, md, asn1 } from 'node-forge';

export class AuthorizationHandler {
    public static authorizeClientCertificate(req: Request, res: Response, next: NextFunction): void {
        try {
            // Get header
            const header = req.get('X-ARR-ClientCert');
            if (!header) throw new Error('UNAUTHORIZED');

            // Convert from PEM to pki.CERT
            const pem = `-----BEGIN CERTIFICATE-----${header}-----END CERTIFICATE-----`;
            const incomingCert: pki.Certificate = pki.certificateFromPem(pem);

            // Validate certificate thumbprint
            const fingerPrint = md.sha1.create().update(asn1.toDer(pki.certificateToAsn1(incomingCert)).getBytes()).digest().toHex();
            if (fingerPrint.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate time validity
            const currentDate = new Date();
            if (currentDate < incomingCert.validity.notBefore || currentDate > incomingCert.validity.notAfter) throw new Error('UNAUTHORIZED');

            // Validate issuer
            if (incomingCert.issuer.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate subject
            if (incomingCert.subject.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            next();
        } catch (e) {
            if (e instanceof Error && e.message === 'UNAUTHORIZED') {
                res.status(401).send();
            } else {
                next(e);
            }
        }
    }
}
```

## <a name="java-sample"></a>Ejemplo de Java

La siguiente clase Java codifica el certificado de `X-ARR-ClientCert` a una instancia de `X509Certificate`. `certificateIsValid()` valida que la huella digital del certificado coincida con la proporcionada en el constructor y que el certificado no ha expirado.


```java
import java.io.ByteArrayInputStream;
import java.security.NoSuchAlgorithmException;
import java.security.cert.*;
import java.security.MessageDigest;

import sun.security.provider.X509Factory;

import javax.xml.bind.DatatypeConverter;
import java.util.Base64;
import java.util.Date;

public class ClientCertValidator { 

    private String thumbprint;
    private X509Certificate certificate;

    /**
     * Constructor.
     * @param certificate The certificate from the "X-ARR-ClientCert" HTTP header
     * @param thumbprint The thumbprint to check against
     * @throws CertificateException If the certificate factory cannot be created.
     */
    public ClientCertValidator(String certificate, String thumbprint) throws CertificateException {
        certificate = certificate
                .replaceAll(X509Factory.BEGIN_CERT, "")
                .replaceAll(X509Factory.END_CERT, "");
        CertificateFactory cf = CertificateFactory.getInstance("X.509");
        byte [] base64Bytes = Base64.getDecoder().decode(certificate);
        X509Certificate X509cert =  (X509Certificate) cf.generateCertificate(new ByteArrayInputStream(base64Bytes));

        this.setCertificate(X509cert);
        this.setThumbprint(thumbprint);
    }

    /**
     * Check that the certificate's thumbprint matches the one given in the constructor, and that the
     * certificate has not expired.
     * @return True if the certificate's thumbprint matches and has not expired. False otherwise.
     */
    public boolean certificateIsValid() throws NoSuchAlgorithmException, CertificateEncodingException {
        return certificateHasNotExpired() && thumbprintIsValid();
    }

    /**
     * Check certificate's timestamp.
     * @return Returns true if the certificate has not expired. Returns false if it has expired.
     */
    private boolean certificateHasNotExpired() {
        Date currentTime = new java.util.Date();
        try {
            this.getCertificate().checkValidity(currentTime);
        } catch (CertificateExpiredException | CertificateNotYetValidException e) {
            return false;
        }
        return true;
    }

    /**
     * Check the certificate's thumbprint matches the given one.
     * @return Returns true if the thumbprints match. False otherwise.
     */
    private boolean thumbprintIsValid() throws NoSuchAlgorithmException, CertificateEncodingException {
        MessageDigest md = MessageDigest.getInstance("SHA-1");
        byte[] der = this.getCertificate().getEncoded();
        md.update(der);
        byte[] digest = md.digest();
        String digestHex = DatatypeConverter.printHexBinary(digest);
        return digestHex.toLowerCase().equals(this.getThumbprint().toLowerCase());
    }

    // Getters and setters

    public void setThumbprint(String thumbprint) {
        this.thumbprint = thumbprint;
    }

    public String getThumbprint() {
        return this.thumbprint;
    }

    public X509Certificate getCertificate() {
        return certificate;
    }

    public void setCertificate(X509Certificate certificate) {
        this.certificate = certificate;
    }
}
```

[exclusion-paths]: ./media/app-service-web-configure-tls-mutual-auth/exclusion-paths.png
