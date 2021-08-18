---
title: Configuración de reglas de firewall de IP para Azure Service Bus
description: Uso de las reglas de firewall para permitir las conexiones desde direcciones IP específicas a Azure Service Bus.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: 84ba9f71d3d1da590d8ec43bfe1cfefb91b7c8ae
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112415719"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>Permitir el acceso al espacio de nombres de Azure Service Bus desde intervalos o direcciones IP específicas
De forma predeterminada, los espacios de nombres de Service Bus son accesibles desde Internet, siempre que la solicitud venga con una autenticación y una autorización válidas. Con el firewall de IP, puede restringirlo aún más a solo un conjunto de direcciones o intervalos de direcciones IPv4 en notación [CIDR (Enrutamiento de interdominios sin clases)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Esta característica es útil en escenarios en los que Azure Service Bus debe ser accesible únicamente desde ciertos sitios conocidos. Las reglas de firewall permiten configurar reglas para aceptar el tráfico procedente de direcciones IPv4 concretas. Por ejemplo, si usa Service Bus con [Azure ExpressRoute][express-route], puede crear una **regla de firewall** para permitir el tráfico procedente únicamente de las direcciones IP de la infraestructura local o de las direcciones de una puerta de enlace de NAT corporativa. 

## <a name="ip-firewall-rules"></a>Reglas de firewall de IP
Las reglas de firewall de IP se aplican en el nivel de espacio de nombres de Service Bus. Por lo tanto, las reglas se aplican a todas las conexiones de clientes que usan cualquier protocolo admitido. Cualquier intento de conexión desde una dirección IP que no coincida con una regla IP admitida en el espacio de nombres de Service Bus se rechaza como no autorizado. La respuesta no menciona la regla IP. Las reglas de filtro IP se aplican en orden y la primera regla que coincida con la dirección IP determina la acción de aceptar o rechazar.

## <a name="important-points"></a>Observaciones importantes
- Solo se admiten firewalls y redes virtuales en el nivel **premium** de Service Bus. Si no considera la posibilidad de actualizar al nivel **Premier**, se recomienda que proteja el token de firma de acceso compartido (SAS) y que lo comparta solo con usuarios autorizados. Para más información sobre la autenticación de SAS, consulte [Autenticación y autorización](service-bus-authentication-and-authorization.md#shared-access-signature).
- Especifique **al menos una regla de firewall de IP o una regla de red virtual** para que el espacio de nombres permita el tráfico solo desde las direcciones IP especificadas o la subred de una red virtual. Si no hay ninguna regla de red virtual y de IP, se puede acceder al espacio de nombres a través de la red pública de Internet (mediante la clave de acceso).  
- La implementación de reglas de firewall puede evitar que otros servicios de Azure interactúen con Service Bus. Como excepción, puede permitir el acceso a los recursos de Service Bus desde determinados **servicios de confianza**, aunque esté habilitado el filtrado de IP. Para ver una lista de servicios de confianza, consulte [Servicios de confianza](#trusted-microsoft-services). 

    Los siguientes servicios de Microsoft deben estar en una red virtual
    - Azure App Service
    - Azure Functions

## <a name="use-azure-portal"></a>Usar Azure Portal
En esta sección se muestra cómo usar Azure Portal para crear reglas de firewall de IP para un espacio de nombres de Service Bus. 

1. Vaya al **espacio de nombres de Service Bus** en [Azure Portal](https://portal.azure.com).
2. En el menú de la izquierda, seleccione la opción **Redes** en **Configuración**.  

    > [!NOTE]
    > Puede ver la pestaña **Redes** solo para los espacios de nombres **premium**.  
    
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Página de redes: predeterminada" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Si selecciona la opción **Todas las redes**, el espacio de nombres de Service Bus aceptará conexiones procedentes de cualquier dirección IP. Esta configuración predeterminada es equivalente a una regla que acepta el intervalo de direcciones IP 0.0.0.0/0. 

    ![Captura de pantalla de la página Redes de Azure Portal. La opción para permitir el acceso desde todas las redes está seleccionada en la pestaña Firewalls y redes virtuales.](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Para permitir el acceso solo desde una dirección IP especificada, seleccione la opción **Redes seleccionadas** si aún no lo está. En la sección **Firewall**, haga lo siguiente:
    1. Seleccione la opción **Agregar la dirección IP del cliente** para dar acceso a esa IP de cliente actual al espacio de nombres. 
    2. En **Intervalo de direcciones**, escriba una dirección IPv4 específica o un intervalo de direcciones IPv4 en notación CIDR. 
    3. Especifique si quiere **permitir que los servicios de confianza de Microsoft omitan este firewall**. 

        >[!WARNING]
        > Si selecciona la opción **Redes seleccionadas** y no agrega al menos una regla de firewall de IP o una red virtual en esta página, se podrá acceder al espacio de nombres desde la red pública de Internet (mediante la clave de acceso).    

        ![Captura de pantalla de la página Redes de Azure Portal. La opción para permitir el acceso desde las redes seleccionadas está seleccionada y la sección Firewall está resaltada.](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Seleccione **Guardar** en la barra de herramientas para guardar la configuración. Espere unos minutos hasta que la confirmación se muestre en las notificaciones de Azure Portal.

    > [!NOTE]
    > Para restringir el acceso a redes virtuales específicas, consulte [Permitir el acceso desde redes específicas](service-bus-service-endpoints.md).

[!INCLUDE [service-bus-trusted-services](./includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Uso de plantillas de Resource Manager
En esta sección se incluye una plantilla de Azure Resource Manager de ejemplo que agrega una red virtual y una regla de firewall a un espacio de nombres de Service Bus existente.

**ipMask** es una única dirección IPv4 o un bloque de direcciones IP en la notación CIDR. Por ejemplo, en notación CIDR 70.37.104.0/24 representa las 256 direcciones IPv4 de 70.37.104.0 a 70.37.104.255, donde 24 indica el número de bits de prefijo significativos para el intervalo.

Al agregar reglas de red virtual o de firewalls, establezca el valor de `defaultAction` en `Deny`.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implementar la plantilla, siga las instrucciones para [Azure Resource Manager][lnk-deploy].

> [!IMPORTANT]
> Si no hay ninguna regla de red virtual y de IP, todo el tráfico fluye al espacio de nombres, aunque establezca `defaultAction` en `deny`. Se puede acceder al espacio de nombres a través de la red pública de Internet (mediante la clave de acceso). Especifique al menos una regla de IP o una regla de red virtual para que el espacio de nombres permita el tráfico solo desde las direcciones IP o la subred especificadas de una red virtual.  


## <a name="next-steps"></a>Pasos siguientes

Para restringir el acceso a Service Bus a redes virtuales de Azure, visite el siguiente vínculo:

- [Puntos de conexión de servicio de red virtual para Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  ../expressroute/expressroute-faqs.md#supported-services