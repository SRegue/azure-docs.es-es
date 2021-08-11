---
title: Puertas de enlace de red virtual con redundancia de zona en Azure Availability Zones
description: Aprenda a implementar puertas de enlace de VPN y ExpressRoute en Azure Availability Zones para aportar resistencia, escalabilidad y mayor disponibilidad a las puertas de enlace de red virtual.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: cherylmc
ms.openlocfilehash: 0482146a7070083c795a60a4b01fdede7e1b3bf1
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110067391"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Acerca de las puertas de enlace de red virtual con redundancia de zona en Azure Availability Zones

Puede implementar puertas de enlace de VPN Gateway y ExpressRoute en [Azure Availability Zones](../availability-zones/az-overview.md). Esto aporta una mayor disponibilidad, escalabilidad y resistencia a las puertas de enlace de red virtual. Implementar puertas de enlace en Azure Availability Zones separa de forma física y lógica las puertas de enlace dentro de una región, y protege la conectividad de red local en Azure de errores de nivel de zona.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Puertas de enlace con redundancia de zona

Para implementar automáticamente las puertas de enlace de red virtual en distintas zonas de disponibilidad, puede usar puertas de enlace de red virtual con redundancia de zona. Las puertas de enlace con redundancia de zona permiten aprovechar la resistencia de zona para tener acceso a los servicios de Azure críticos para la misión y escalables.

<br>
<br>

![gráfico de puertas de enlace con redundancia de zona](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Puertas de enlace zonales

Para implementar puertas de enlace en una zona específica, puede usar puertas de enlace zonales. Al implementar una puerta de enlace zonal, todas las instancias de la puerta de enlace se implementan en la misma zona de disponibilidad.

<br>
<br>

![gráfico de puertas de enlace zonales](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>SKU de puerta de enlace

Las puertas de enlace zonales y con redundancia de zona están disponibles como SKU de puerta de enlace. Hemos agregado nuevas SKU de puerta de enlace de red virtual en las regiones de Azure AZ. Estas SKU son similares a las SKU correspondientes existentes para ExpressRoute y VPN Gateway, salvo que son específicas para puertas de enlace zonales y con redundancia de zona. Puede identificar estas SKU mediante "AZ" en el nombre de la SKU.

Para obtener información sobre las SKU de puerta de enlace, consulte [SKU de puerta de enlace de VPN](vpn-gateway-about-vpngateways.md#gwsku) y [SKU de puerta de enlace de ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku).

## <a name="public-ip-skus"></a><a name="pipskus"></a>SKU de IP pública

Las puertas de enlace con redundancia de zona y las puertas de enlace zonales se basan en el recurso de IP pública de Azure SKU *Estándar*. La configuración del recurso de IP pública de Azure determina si la puerta de enlace que se implementa tiene redundancia de zona o es zonal. Si crea un recurso de IP pública con una SKU *Básica*, la puerta de enlace no tendrá redundancia de zona y los recursos de puerta de enlace serán regionales.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Puertas de enlace con redundancia de zona

Cuando se crea una dirección IP pública con la SKU de IP pública **Estándar** sin especificar una zona, el comportamiento es diferente según si la puerta de enlace es una puerta de enlace de VPN o una puerta de enlace de ExpressRoute. 

* En el caso de las puertas de enlace de VPN, las dos instancias de la puerta de enlace se implementarán en dos de estas tres zonas para proporcionar redundancia de zona. 
* En el caso de las puertas de enlace de ExpressRoute, como puede haber más de dos instancias, la puerta de enlace puede abarcar las tres zonas.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Puertas de enlace zonales

Cuando se crea una dirección IP pública con la SKU de IP pública **Estándar** y se especifica la zona (1, 2 o 3), todas las instancias de la puerta de enlace se implementarán en la misma zona.

### <a name="regional-gateways"></a><a name="piprg"></a>Puertas de enlace regionales

Cuando se crea una dirección IP pública con la SKU de IP pública **Básica**, la puerta de enlace se implementa como una puerta de enlace regional y no se agrega redundancia de zona a la puerta de enlace.

## <a name="faq"></a><a name="faq"></a>P+F

### <a name="what-will-change-when-i-deploy-these-skus"></a>¿Qué cambiará cuando implemente estas SKU?

Desde su perspectiva, puede implementar puertas de enlace con redundancia de zona. Esto significa que todas las instancias de las puertas de enlace se implementarán en distintas zonas de disponibilidad de Azure, y cada zona de disponibilidad es un dominio de error y actualización diferente. Por ello, las puertas de enlace son más confiables, disponibles y resistentes a los errores de la zona.

### <a name="can-i-use-the-azure-portal"></a>¿Puedo usar Azure Portal?

Sí, puede usar Azure Portal para implementar estos SKU. Sin embargo, estos nuevos SKU solo se verán en las regiones de Azure que tengan Azure Availability Zones.

### <a name="what-regions-are-available-for-me-to-use-these-skus"></a>¿Qué regiones están disponibles para usar estos SKU?

Estos SKU están disponible en las regiones de Azure que tienen Azure Availability Zones. Para más información, consulte la sección [Regiones de Azure con Availability Zones](../availability-zones/az-region.md#azure-regions-with-availability-zones).

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>¿Puedo convertir/migrar/actualizar mis puertas de enlace de red virtual existentes en puertas de enlace zonales o con redundancia de zona?

Actualmente no se permite migrar las puertas de enlace de red virtual existentes a puertas de enlace zonales o con redundancia de zona. Sin embargo, puede eliminar la puerta de enlace existente y volver a crear una puerta de enlace zonal o con redundancia de zona.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>¿Puedo implementar puertas de enlace de VPN y ExpressRoute en la misma red virtual?

Se admite la coexistencia de puertas de enlace de VPN y ExpressRoute en la misma red virtual. Sin embargo, le recomendamos que reserve un intervalo de direcciones IP /27 para la subred de puerta de enlace.

## <a name="next-steps"></a>Pasos siguientes

[Crear una puerta de enlace de red virtual con redundancia de zona](create-zone-redundant-vnet-gateway.md)
