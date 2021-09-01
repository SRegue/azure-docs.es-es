---
title: 'Conectividad SSL/TLS: Azure Database for MySQL'
description: Información para configurar Azure Database for MySQL y las aplicaciones asociadas, a fin de usar correctamente las conexiones SSL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: be2023f47906aeef41884f21f67f1adb926b0b72
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653355"
---
# <a name="ssltls-connectivity-in-azure-database-for-mysql"></a>Conectividad SSL/TLS en Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL permite conectar el servidor de bases de datos a las aplicaciones cliente con la Capa de sockets seguros (SSL). Aplicar conexiones SSL entre el servidor de base de datos y las aplicaciones cliente ayuda a proteger contra los ataques de tipo "man in the middle" mediante el cifrado del flujo de datos entre el servidor y la aplicación.

> [!NOTE]
> La actualización del valor del parámetro de servidor `require_secure_transport` no afecta al comportamiento del servicio MySQL. Use las características de cumplimiento de SSL y TLS que se describen en este artículo para proteger las conexiones a su base de datos.

>[!NOTE]
> En base a los comentarios de los clientes se ha extendido la puesta en desuso del certificado raíz de la entidad de certificación raíz Baltimore existente hasta el 15 de febrero de 2021 (15/02/2021).

> [!IMPORTANT] 
> El certificado raíz de SSL se ha establecido para que expire a partir del 15 de febrero de 2021 (15/02/2021). Actualice la aplicación para que use el [nuevo certificado](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). Para más información, consulte el artículo sobre las [actualizaciones de certificados planeadas](concepts-certificate-rotation.md).

## <a name="ssl-default-settings"></a>Configuración predeterminada de SSL

De forma predeterminada, el servicio de base de datos debe configurarse para requerir conexiones SSL al conectar con MySQL.  Se recomienda evitar la desactivación de la opción SSL siempre que sea posible.

Al aprovisionar un nuevo servidor de Azure Database for MySQL en Azure Portal o con la CLI de Azure, el establecimiento de conexiones SSL está habilitado de forma predeterminada. 

Las cadenas de conexión para distintos lenguajes de programación se muestran en Azure Portal. Estas cadenas de conexión incluyen los parámetros SSL que se requieren para conectarse a la base de datos. En Azure Portal, seleccione el servidor. En el encabezado **Configuración**, seleccione las **cadenas de conexión**. El parámetro SSL varía según el conector, por ejemplo, "ssl = true" o "sslmode = require" o "sslmode = required" y otras variaciones.

En algunos casos, las aplicaciones requieren un archivo de certificado local generado a partir de un archivo de certificado de una entidad de certificación (CA) de confianza para conectarse de forma segura. Actualmente, los clientes **solo pueden usar** el certificado predefinido para conectarse a un servidor de Azure Database for MySQL que se encuentra en https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. 

De forma similar, los vínculos siguientes apuntan a los certificados de los servidores en nubes soberanas: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) y [Azure Alemania](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

Para información sobre cómo habilitar o deshabilitar la conexión SSL al desarrollar la aplicación, consulte [Configuración de SSL](howto-configure-ssl.md).

## <a name="tls-enforcement-in-azure-database-for-mysql"></a>Aplicación de TLS en Azure Database for MySQL

Azure Database for MySQL admite el cifrado de los clientes que se conectan al servidor de bases de datos mediante la Seguridad de la capa de transporte (TLS). TLS es un protocolo estándar del sector que garantiza conexiones de red seguras entre el servidor de bases de datos y las aplicaciones cliente, lo que le permite ajustarse a los requisitos de cumplimiento.

### <a name="tls-settings"></a>Configuración de TLS

Azure Database for MySQL proporciona la capacidad de aplicar la versión de TLS para las conexiones cliente. Para aplicar la versión de TLS, use el valor de la opción **Versión de TLS mínima**. Se permiten los siguientes valores para esta configuración de opción:

|  Versión de TLS mínima             | Versión de TLS compatible con el cliente                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (valor predeterminado) | No se requiere TLS                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 y versiones posteriores           |
| TLS1_1                           | TLS 1.1, TLS 1.2 y versiones posteriores                   |
| TLS1_2                           | TLS versión 1.2 y posteriores                     |


Por ejemplo, si se establece el valor de la versión de TLS mínima en TLS 1.0, significa que el servidor permitirá conexiones de los clientes que usen TLS 1.0, 1.1 y 1.2 y versiones posteriores. Como alternativa, si se establece en 1.2 significa que solo se permiten conexiones de los clientes que usan TLS 1.2 o versión posterior y se rechazan todas las conexiones con TLS 1.0 y TLS 1.1.

> [!Note] 
> De forma predeterminada, Azure Database for MySQL no aplica una versión de TLS mínima (opción de configuración `TLSEnforcementDisabled`).
>
> Una vez que se aplica una versión de TLS mínima, esta no se puede deshabilitar posteriormente.

La configuración de la versión de TLS mínima no requiere que se pueda reiniciar el servidor mientras el servidor está en línea. Para obtener información sobre cómo establecer la configuración de TLS para Azure Database for MySQL, consulte [Configuración de los valores de TLS](howto-tls-configurations.md).

## <a name="cipher-support-by-azure-database-for-mysql-single-server"></a>Cifrado admitido por el servidor único de Azure Database for MySQL

Como parte de la comunicación SSL/TLS, los conjuntos de cifrado se validan y son los únicos que tienen permitido comunicarse con el servidor de la base de datos. La validación del conjunto de cifrado se controla en el [nivel de puerta de enlace](concepts-connectivity-architecture.md#connectivity-architecture) y no de manera explícita en el propio nodo. Si los conjuntos de cifrado no coinciden con algunos de los conjuntos mencionados a continuación, se rechazarán las conexiones de cliente entrantes.

### <a name="cipher-suite-supported"></a>Conjunto de cifrado compatible

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Pasos siguientes

- [Bibliotecas de conexiones de Azure Database for MySQL](concepts-connection-libraries.md)
- Obtenga más información sobre cómo [configurar SSL](howto-configure-ssl.md).
- Obtenga más información sobre [cómo configurar TLS](howto-tls-configurations.md).
