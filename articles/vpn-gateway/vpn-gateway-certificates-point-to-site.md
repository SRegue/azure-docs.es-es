---
title: 'Generación y exportación de certificados para la conexión de punto a sitio: PowerShell'
titleSuffix: Azure VPN Gateway
description: Obtenga información sobre cómo crear un certificado raíz autofirmado, exportar una clave pública y generar certificados de cliente para conexiones de VPN Gateway de punto a sitio.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/03/2021
ms.author: cherylmc
ms.openlocfilehash: 48240793cee233d8e97ab79e6421b02eddc86f23
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2021
ms.locfileid: "111527575"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generación y exportación de certificados para conexiones de punto a sitio con PowerShell

Las conexiones de punto a sitio utilizan certificados para realizar la autenticación. En este artículo, se muestra cómo crear un certificado raíz autofirmado y generar certificados de cliente usando PowerShell en Windows 10 o Windows Server 2016. Si busca otras instrucciones de certificado, vea los artículos sobre [certificados con Linux](vpn-gateway-certificates-point-to-site-linux.md) o [certificados con MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Los pasos de este artículo se aplican a Windows 10 o Windows Server 2016. Los cmdlets de PowerShell que se usan para generar certificados forman parte del sistema operativo y no funcionan en otras versiones de Windows. El equipo con Windows 10 o Windows Server 2016 solo es necesario para generar los certificados. Una vez que se generan los certificados, puede cargarlos o instalarlos en cualquier sistema operativo cliente compatible.

Si no tiene acceso a un equipo con Windows 10 o Windows Server 2016, puede usar [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) para generar certificados. Los certificados que genera mediante cualquiera de estos métodos pueden instalarse en cualquier sistema operativo cliente [compatible](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalación de un certificado de cliente exportado

Cada cliente que se conecta a la red virtual a través de una conexión de punto a sitio requiere que haya un certificado de cliente instalado localmente.

Para instalar un certificado de cliente, consulte cómo [instalar un certificado de cliente para conexiones de punto a sitio](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Pasos siguientes

Continúe con la configuración de punto a sitio.

* Para conocer los pasos del modelo de implementación de **Resource Manager**, consulte cómo [configurar una conexión de punto a sitio mediante la autenticación de certificados de Azure nativa](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Para ver los pasos del modelo de implementación **clásica**, consulte el artículo [Configuración de una conexión VPN de punto a sitio a una red virtual mediante el Portal clásico](vpn-gateway-howto-point-to-site-classic-azure-portal.md).