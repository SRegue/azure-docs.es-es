---
title: Configuración de la tunelización forzada para conexiones de sitio a sitio
description: Obtenga información sobre el procedimiento para redirigir (forzar) todo el tráfico de Internet a la ubicación local.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cherylmc
ms.openlocfilehash: 383636d07aa453266be43b33d6f62b93255ac24a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729537"
---
# <a name="configure-forced-tunneling"></a>Configuración de la tunelización forzada

La tunelización forzada permite redirigir o forzar todo el tráfico vinculado a Internet de vuelta a su ubicación local a través de un túnel VPN de sitio a sitio con fines de inspección y auditoría. Se trata de un requisito de seguridad crítico en la mayoría de las directivas de las empresas de TI. Si no configura la tunelización forzada, el tráfico de Internet desde las máquinas virtuales de Azure siempre va desde la infraestructura de red de Azure directamente a Internet, sin la opción que permite inspeccionarlo o auditarlo. Un acceso no autorizado a Internet puede provocar la divulgación de información u otros tipos de infracciones de seguridad.

La tunelización forzada puede configurarse mediante Azure PowerShell. No se puede configurar con Azure Portal. Este artículo ayuda a configurar la tunelización forzada en las redes virtuales creadas mediante el [modelo de implementación de Resource Manager](../azure-resource-manager/management/deployment-models.md). Si quiere configurar la tunelización forzada en el modelo de implementación clásico, vea [Tunelización forzada: clásica](vpn-gateway-about-forced-tunneling.md).

## <a name="about-forced-tunneling"></a>Información acerca de la tunelización forzada

El siguiente diagrama ilustra cómo funciona la tunelización forzada.

:::image type="content" source="./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png" alt-text="Diagrama que muestra la tunelización forzada.":::

En este ejemplo, la subred de front-end no usa tunelización forzada. Las cargas de trabajo de la subred Frontend pueden continuar para aceptar y responder a las solicitudes de los clientes directamente desde Internet. Las subredes Mid-tier y Backend usan la tunelización forzada. Las conexiones salientes desde estas dos subredes a Internet se fuerzan o redirigen a un sitio local a través de uno de los túneles VPN de sitio a sitio (S2S).

Esto permite restringir e inspeccionar el acceso a Internet desde sus máquinas virtuales o servicios en la nube en Azure, al tiempo que continúa posibilitando la arquitectura de varios niveles de servicio necesaria. Si no hay ninguna carga de trabajo a través de Internet en las redes virtuales, también tiene la opción de aplicar la tunelización forzada a redes virtuales completas.

## <a name="requirements-and-considerations"></a>Requisitos y consideraciones

La tunelización forzada en Azure se configura mediante rutas personalizadas definidas por el usuario de redes virtuales. La redirección del tráfico a un sitio local se expresa como una ruta predeterminada a la puerta de enlace de VPN de Azure. Para obtener más información sobre las redes virtuales y las rutas definidas por el usuario, vea [Rutas personalizadas definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md#user-defined).

* Cada subred de la red virtual tiene una tabla de enrutamiento del sistema integrada. La tabla de enrutamiento del sistema tiene los siguientes tres grupos de rutas:
  
  * **Rutas de redes virtuales locales:** directamente a las máquinas virtuales de destino en la misma red virtual.
  * **Rutas locales:** a Azure VPN Gateway.
  * **Ruta predeterminada** : directamente a Internet. Los paquetes destinados a las direcciones IP privadas que no están cubiertos por las dos rutas anteriores se anularán.
* Este procedimiento usa las rutas definidas por el usuario para crear una tabla de enrutamiento para agregar una ruta predeterminada y, a continuación, asociar la tabla de enrutamiento a las subredes de la red virtual para habilitar la tunelización forzada en esas subredes.
* La tunelización forzada debe asociarse a una red virtual que tenga una puerta de enlace de VPN basada en rutas. Deberá establecer un "sitio predeterminado" entre los sitios locales entre entornos conectados a la red virtual. Además, el dispositivo VPN local debe configurarse con 0.0.0.0/0 como selectores de tráfico. 
* La tunelización forzada ExpressRoute no se configura mediante este mecanismo, sino que se habilita mediante el anuncio de una ruta predeterminada a través de las sesiones de emparejamiento BGP de ExpressRoute. Para obtener más información, consulte la [documentación de ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).
* Cuando se implementan la instancia de VPN Gateway y la puerta de enlace de ExpressRoute en la misma red virtual, ya no se necesitan rutas definidas por el usuario (UDR), ya que la puerta de enlace de ExpressRoute anunciará la configuración del "sitio predeterminado" en la red virtual.

## <a name="configuration-overview"></a>Información general sobre la configuración

El procedimiento siguiente lo ayudará a crear un grupo de recursos y una red virtual. Después, creará una puerta de enlace de VPN y configurará la tunelización forzada. En el procedimiento, la red virtual 'MultiTier-VNet' tiene tres subredes: 'Frontend', 'Midtier' y 'Backend' con cuatro conexiones entre entornos: 'DefaultSiteHQ' y tres 'ramas'.

Los pasos del procedimiento establecerán 'DefaultSiteHQ' como la conexión de sitio predeterminada para la tunelización forzada y configurarán las subredes 'Midtier' y 'Backend' para que usen dicha tunelización.

## <a name="before-you-begin"></a><a name="before"></a>Antes de empezar

Instale la versión más reciente de los cmdlets de PowerShell de Azure Resource Manager. Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/) para obtener más información sobre cómo instalar los cmdlets de PowerShell.

> [!IMPORTANT]
> Es necesario instalar la versión más reciente de los cmdlets de PowerShell. En caso contrario, puede recibir errores de validación al ejecutar algunos de los cmdlets.
>
>

### <a name="to-sign-in"></a>Para iniciar sesión

[!INCLUDE [Sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Configuración de la tunelización forzada

> [!NOTE]
> Puede que vea advertencias que indican que "el tipo de objeto de salida de este cmdlet se va a modificar en una versión futura". Este es el comportamiento esperado y puede ignorar estas advertencias.
>
>


1. Cree un grupo de recursos.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Cree una red virtual y especifique las subredes.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Cree las puertas de enlace de red local.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Cree la tabla de enrutamiento y la regla de enrutamiento.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Asocie la tabla de enrutamiento creada anteriormente a las subredes Midtier y Back-end.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Cree la puerta de enlace de red virtual. La creación de una puerta de enlace suele tardar 45 minutos o más, según la SKU de la puerta de enlace seleccionada. Si ve errores ValidateSet relacionados con el valor de GatewaySku, compruebe que tiene instalada la [versión más reciente de los cmdlets de PowerShell](#before). La versión más reciente de los cmdlets de PowerShell contiene los nuevos valores validados para las SKU más recientes de la puerta de enlace.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Asigne un sitio predeterminado a la puerta de enlace de red virtual. **-GatewayDefaultSite** es el parámetro de cmdlet que permite que funcione la configuración de enrutamiento forzado, así que tenga cuidado al configurar este valor correctamente. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Establezca las conexiones VPN de sitio a sitio.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
