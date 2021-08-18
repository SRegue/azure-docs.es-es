---
title: Reglas de firewall de Azure Event Hubs | Microsoft Docs
description: Use las reglas de firewall para permitir las conexiones desde direcciones IP específicas a Azure Event Hubs.
ms.topic: article
ms.date: 05/10/2021
ms.openlocfilehash: eb8f83d03fffe514fcd34a394943d4a0fef27c0c
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112417125"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>Permitir el acceso a los espacios de nombres de Azure Event Hubs desde intervalos o direcciones IP específicas
Los espacios de nombres de Azure Event Hubs son accesibles de forma predeterminada desde Internet, siempre que la solicitud venga con una autenticación y una autorización válidas. Con el firewall de IP, puede restringirlo aún más a solo un conjunto de direcciones o intervalos de direcciones IPv4 en notación [CIDR (Enrutamiento de interdominios sin clases)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Esta característica es útil en escenarios en los que Azure Event Hubs debe ser accesible únicamente desde ciertos sitios conocidos. Las reglas de firewall permiten configurar reglas para aceptar el tráfico procedente de direcciones IPv4 concretas. Por ejemplo, si usa Event Hubs con [Azure ExpressRoute][express-route], puede crear una **regla de firewall** para permitir el tráfico procedente única y exclusivamente de las direcciones IP de la infraestructura local. 

## <a name="ip-firewall-rules"></a>Reglas de firewall de IP
Las reglas de firewall de IP se aplican en el nivel del espacio de nombres de Event Hubs. Por lo tanto, las reglas se aplican a todas las conexiones de clientes que usan cualquier protocolo admitido. Cualquier intento de conexión desde una dirección IP que no coincida con una regla IP admitida en el espacio de nombres de Event Hubs se rechaza como no autorizado. La respuesta no menciona la regla IP. Las reglas de filtro IP se aplican en orden y la primera regla que coincida con la dirección IP determina la acción de aceptar o rechazar.


## <a name="important-points"></a>Observaciones importantes
- Esta característica no se admite en el nivel **Básico**.
- La activación de las reglas de firewall en el espacio de nombres de Event Hubs bloquea las solicitudes entrantes de forma predeterminada, a menos que las solicitudes se originen en un servicio que funciona desde direcciones IP públicas permitidas. Las solicitudes que bloquean incluyen aquellas de otros servicios de Azure, desde Azure Portal, desde los servicios de registro y de métricas, etc. Como excepción, puede permitir el acceso a recursos de Event Hubs desde determinados **servicios de confianza**, aunque esté habilitado el filtrado de IP. Para obtener una lista de servicios de confianza, consulte [Servicios de confianza de Microsoft](#trusted-microsoft-services).
- Especifique **al menos una regla de firewall de IP o una regla de red virtual** para que el espacio de nombres permita el tráfico solo desde las direcciones IP especificadas o la subred de una red virtual. Si no hay ninguna regla de red virtual y de IP, se puede acceder al espacio de nombres a través de la red pública de Internet (mediante la clave de acceso).  


## <a name="use-azure-portal"></a>Usar Azure Portal
En esta sección se muestra cómo usar Azure Portal para crear reglas de firewall de IP para un espacio de nombres de Event Hubs. 

1. Vaya a su **espacio de nombres de Event Hubs** en [Azure Portal](https://portal.azure.com).
4. Seleccione **Redes** en **Configuración** en el menú de la izquierda. 
    
    > [!WARNING]
    > Si selecciona la opción **Redes seleccionadas** y no agrega al menos una regla de firewall de IP o una red virtual en esta página, se podrá acceder al espacio de nombres desde la **red pública de Internet** (mediante la clave de acceso).  

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Pestaña Redes: opción redes seleccionadas" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Si selecciona la opción **Todas las redes**, el centro de eventos aceptará conexiones procedentes de cualquier dirección IP (mediante la tecla de acceso). Esta configuración equivale a una regla que acepta el intervalo de direcciones IP 0.0.0.0/0. 

    ![Captura de pantalla que muestra la página "Firewall y redes virtuales" con la opción "Todas las redes" seleccionada.](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Para restringir el acceso a únicamente algunas direcciones IP, asegúrese de que la opción **Redes seleccionadas** está seleccionada. En la sección **Firewall**, haga lo siguiente:
    1. Seleccione la opción **Agregar la dirección IP del cliente** para dar acceso a esa IP de cliente actual al espacio de nombres. 
    2. En **Intervalo de direcciones**, escriba una dirección IPv4 específica o un intervalo de direcciones IPv4 en notación CIDR. 

    >[!WARNING]
    > Si selecciona la opción **Redes seleccionadas** y no agrega al menos una regla de firewall de IP o una red virtual en esta página, se podrá acceder al espacio de nombres desde la red pública de Internet (mediante la clave de acceso).
1. Especifique si quiere **permitir que los servicios de confianza de Microsoft omitan este firewall**. Consulte [Servicios de Microsoft de confianza](#trusted-microsoft-services) para más información. 

      ![Firewall: opción Todas las redes seleccionada](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Seleccione **Guardar** en la barra de herramientas para guardar la configuración. Espere unos minutos hasta que la confirmación se muestre en las notificaciones de Azure Portal.

    > [!NOTE]
    > Para restringir el acceso a redes virtuales específicas, consulte [Permitir el acceso desde redes específicas](event-hubs-service-endpoints.md).

[!INCLUDE [event-hubs-trusted-services](./includes/event-hubs-trusted-services.md)]


## <a name="use-resource-manager-template"></a>Uso de plantillas de Resource Manager

> [!IMPORTANT]
> La característica de firewall no se admite en el nivel Básico.

La siguiente plantilla de Resource Manager permite agregar una regla de filtro de IP a un espacio de nombres de Event Hubs.

**ipMask** en la plantilla es una única dirección IPv4 o un bloque de direcciones IP en la notación CIDR. Por ejemplo, en notación CIDR 70.37.104.0/24 representa las 256 direcciones IPv4 de 70.37.104.0 a 70.37.104.255, donde 24 indica el número de bits de prefijo significativos para el intervalo.

Al agregar reglas de red virtual o de firewalls, establezca el valor de `defaultAction` en `Deny`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
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
> Si no hay ninguna regla de red virtual y de IP, todo el tráfico fluye al espacio de nombres, aunque establezca `defaultAction` en `deny`.  Se puede acceder al espacio de nombres a través de la red pública de Internet (mediante la clave de acceso). Especifique al menos una regla de IP o una regla de red virtual para que el espacio de nombres permita el tráfico solo desde las direcciones IP o la subred especificadas de una red virtual.  

## <a name="next-steps"></a>Pasos siguientes

Para restringir el acceso a Event Hubs a las redes virtuales de Azure, visite el siguiente vínculo:

- [Puntos de conexión de servicio de red virtual para Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
