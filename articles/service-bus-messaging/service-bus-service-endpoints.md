---
title: Configuración de puntos de conexión de servicio de red virtual con Azure Service Bus
description: En este artículo se proporciona información sobre cómo agregar un punto de conexión de servicio de Microsoft.ServiceBus a una red virtual.
ms.topic: article
ms.date: 03/29/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 63a2f556739f7f3eaec3874e6d02cc996e2aaec4
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112416729"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-virtual-networks"></a>Permitir el acceso al espacio de nombres de Azure Service Bus desde redes virtuales específicas
La integración de Service Bus con los [puntos de conexión de servicio de una red virtual (VNet)][vnet-sep] permite el acceso seguro a las funcionalidades de mensajería de cargas de trabajo como las de máquinas virtuales que están enlazadas a redes virtuales, con una ruta de acceso del tráfico de red que está protegida en ambos extremos.

Una vez realizada la configuración para enlazarse con al menos un punto de conexión de servicio de subred de red virtual, el espacio de nombres respectivo de Service Bus solo aceptará ya el tráfico procedente de redes virtuales autorizadas y, opcionalmente, direcciones IP de Internet específicas. Desde la perspectiva de la red virtual, el enlace de un espacio de nombres de Service Bus a un punto de conexión de servicio configura un túnel de redes aislado desde la subred de la red virtual al servicio de mensajería.

El resultado es una relación privada y aislada entre las cargas de trabajo enlazadas a la subred y el espacio de nombres respectivo de Service Bus, a pesar de que la dirección de red que se puede observar en el punto de conexión de servicio de mensajería esté en un intervalo IP público.

## <a name="important-points"></a>Observaciones importantes
- Solo se admiten redes virtuales en espacios de nombres de Service Bus del [nivel Premium](service-bus-premium-messaging.md). Al utilizar los puntos de conexión de servicio de la red virtual con Service Bus, no debería habilitar estos puntos de conexión en aplicaciones que combinan espacios de nombres de Service Bus de los niveles Estándar y Premium. Dado que el nivel Estándar no admite redes virtuales, el punto de conexión está restringido solo a los espacios de nombres de nivel Premium.
- La implementación de la integración de instancias de redes virtuales puede evitar que otros servicios de Azure interactúen con Service Bus. Como excepción, puede permitir el acceso a los recursos de Service Bus desde determinados **servicios de confianza**, incluso cuando los puntos de conexión de servicio de red están habilitados. Para ver una lista de servicios de confianza, consulte [Servicios de confianza](#trusted-microsoft-services).

    Los siguientes servicios de Microsoft deben estar en una red virtual
    - Azure App Service
    - Azure Functions
- Especifique **al menos una regla de IP o una regla de red virtual** para que el espacio de nombres permita el tráfico solo desde las direcciones IP o la subred especificadas de una red virtual. Si no hay ninguna regla de red virtual y de IP, se puede acceder al espacio de nombres a través de la red pública de Internet (mediante la clave de acceso).  

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Escenarios de seguridad avanzados que habilita la integración de VNet 

En el caso de soluciones que requieren una estricta seguridad compartimentalizada y en las que las subredes de redes virtuales proporcionan la segmentación entre los servicios compartimentalizados, estas siguen necesitando, por lo general, rutas de comunicación entre los servicios que residen en esos compartimientos.

Cualquier ruta IP inmediata entre compartimientos, incluidos aquellos que transportan HTTPS a través de TCP/IP, tienen el riesgo de sufrir ataques por puntos vulnerables en la capa de red o en capas superiores. Los servicios de mensajería proporcionan rutas de comunicación completamente aisladas, donde los mensajes incluso se escriben en discos cuando se trasladan entre entidades. Las cargas de trabajo de dos redes virtuales distintas enlazadas a la misma instancia de Service Bus pueden comunicarse de forma eficaz y confiable mediante mensajes, al tiempo que se preserva la integridad de los respectivos límites de aislamiento de red.
 
Esto significa que sus soluciones confidenciales en la nube no solo obtienen acceso a las funcionalidades de mensajería asíncrona líderes del sector en fiabilidad y escalabilidad, sino que también pueden usar ahora la mensajería para crear rutas de comunicación entre compartimientos seguros de la solución que son intrínsecamente más seguros de lo que se puede lograr con cualquier otro modo de comunicación entre iguales, incluido HTTPS y los protocolos de socket protegidos por TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Enlace de Service Bus a Virtual Networks

Las *reglas de red virtual* son una característica de firewall que controla si el servidor de Azure Service Bus acepta las conexiones de una subred determinada de una red virtual.

Enlazar un espacio de nombres de Service Bus a una red virtual es un proceso de dos pasos. Primero debe crear un **punto de conexión de servicio de red virtual** en una subred de red virtual y habilitarlo para **Microsoft.ServiceBus**, tal como se explicó en la [introducción a los puntos de conexión de servicio][vnet-sep]. Una vez que ha agregado el punto de conexión de servicio, enlácelo con el espacio de nombres de Service Bus con una **regla de red virtual**.

La regla de red virtual es una asociación del espacio de nombres de Service Bus con una subred de red virtual. Mientras exista la regla, se les concederá acceso a todas las cargas de trabajo enlazadas a la subred al espacio de nombres de Service Bus. Service Bus no establece nunca por sí mismo conexiones de salida, no necesita obtener acceso y, por tanto, nunca se le concede acceso a la subred habilitando esta regla.

> [!NOTE]
> Recuerde que un punto de conexión de servicio de red proporciona a las aplicaciones que se ejecutan en la red virtual acceso al espacio de nombres de Service Bus. La red virtual controla la disponibilidad del punto de conexión, pero no las operaciones que se pueden realizar en entidades de Service Bus (colas, temas o suscripciones). Use Azure Active Directory (Azure AD) para autorizar las operaciones que las aplicaciones pueden realizar en el espacio de nombres y sus entidades. Para obtener más información, vea [Autenticación y autorización de una aplicación con Azure Active Directory para acceder a entidades de Azure Service Bus](authenticate-application.md).


## <a name="use-azure-portal"></a>Usar Azure Portal
En esta sección se muestra cómo usar Azure Portal para agregar un punto de conexión de servicio de red virtual. Para limitar el acceso, debe integrar el punto de conexión de servicio de red virtual para este espacio de nombres de Event Hubs.

1. Vaya al **espacio de nombres de Service Bus** en [Azure Portal](https://portal.azure.com).
2. En el menú de la izquierda, seleccione la opción **Redes** en **Configuración**.  

    > [!NOTE]
    > Puede ver la pestaña **Redes** solo para los espacios de nombres **premium**.  
    
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Página de redes: predeterminada" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Si selecciona la opción **Todas las redes**, el espacio de nombres de Service Bus aceptará conexiones procedentes de cualquier dirección IP. Esta configuración predeterminada es equivalente a una regla que acepta el intervalo de direcciones IP 0.0.0.0/0. 

    ![Firewall: opción Todas las redes seleccionada](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
2. Para restringir el acceso a redes virtuales específicas, seleccione la opción **Redes seleccionadas** si aún no está seleccionada.
1. En la sección **Red virtual** de la página, seleccione la opción **+Agregar red virtual existente**. 

    ![adición de una red virtual existente](./media/service-endpoints/add-vnet-menu.png)

    >[!WARNING]
    > Si selecciona la opción **Redes seleccionadas** y no agrega al menos una regla de firewall de IP o una red virtual en esta página, se podrá acceder al espacio de nombres desde la red pública de Internet (mediante la clave de acceso).
3. En la lista de redes virtuales, seleccione la red virtual y después elija la **subred**. Debe habilitar el punto de conexión de servicio antes de agregar la red virtual a la lista. Si no está habilitado el punto de conexión de servicio, el portal le pedirá que lo habilite.
   
   ![selección de una subred](./media/service-endpoints/select-subnet.png)

4. Debería ver el siguiente mensaje correcto después de habilitar el punto de conexión de servicio para la subred para **Microsoft. ServiceBus**. Seleccione **Agregar** en la parte inferior de la página para agregar la red. 

    ![selección de una subred y habilitación de punto de conexión](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Si no puede habilitar el punto de conexión de servicio, puede ignorar el punto de conexión de servicio de red virtual que falta mediante la plantilla de Resource Manager. Esta funcionalidad no está disponible en Azure Portal.
6. Seleccione **Guardar** en la barra de herramientas para guardar la configuración. Espere unos minutos hasta que la confirmación se muestre en las notificaciones del portal. El botón **Guardar** debe estar deshabilitado. 

    ![Guardar red](./media/service-endpoints/save-vnet.png)

    > [!NOTE]
    > Para obtener instrucciones para permitir el acceso desde intervalos o direcciones concretos, consulte [Permitir el acceso desde intervalos o direcciones IP específicos](service-bus-ip-filtering.md).

[!INCLUDE [service-bus-trusted-services](./includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Uso de plantillas de Resource Manager
La siguiente plantilla de Resource Manager de ejemplo agrega una regla de red virtual a un espacio de nombres de Service Bus existente. Para la regla de red, especifica el identificador de una subred en una red virtual. 

El identificador es una ruta de acceso de Resource Manager completa para la subred de la red virtual. Por ejemplo, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para la subred predeterminada de una red virtual.

Al agregar reglas de red virtual o de firewalls, establezca el valor de `defaultAction` en `Deny`.

Plantilla:

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
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

Para obtener más información acerca de las redes virtuales, consulte los siguientes vínculos:

- [Punto de conexión de servicio de red virtual de Azure][vnet-sep]
- [Filtrado de IP de Azure Service Bus][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
