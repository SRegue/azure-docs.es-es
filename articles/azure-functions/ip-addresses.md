---
title: Direcciones IP en Azure Functions
description: Aprenda a buscar las direcciones IP entrantes y salientes de aplicaciones de función y descubra qué es lo que hace que cambien.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: a884edd23fa1538fcc2b00c80190eab6699e1e47
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414492"
---
# <a name="ip-addresses-in-azure-functions"></a>Direcciones IP en Azure Functions

En este artículo se explican los siguientes conceptos relacionados con las direcciones IP de las aplicaciones de funciones:

* Encontrar las direcciones IP que usa actualmente una aplicación de funciones.
* Condiciones que hacen que cambien las direcciones IP de la aplicación de funciones.
* Restringir las direcciones IP que pueden acceder a una aplicación de funciones.
* Definir las direcciones IP dedicadas para una aplicación de funciones.

Las direcciones IP se asocian a aplicaciones de función, no a funciones individuales. Las solicitudes HTTP entrantes no pueden usar la dirección IP de entrada para llamar a funciones individuales; deben usar el nombre de dominio predeterminado (functionappname.azurewebsites.net) o un nombre de dominio personalizado.

## <a name="function-app-inbound-ip-address"></a>Dirección IP de entrada de una aplicación de función

Cada aplicación de función tiene una única dirección IP de entrada. Para encontrar dicha dirección IP:

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Vaya a la aplicación de función.
3. En **Configuración**, seleccione **Propiedades**. La dirección IP de entrada aparece en **Dirección IP virtual**.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azurecli)

Use la utilidad `nslookup` desde el equipo cliente local:

```command
nslookup <APP_NAME>.azurewebsites.net
```

---

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Direcciones IP de salida de una aplicación de función

Cada aplicación de función tiene un conjunto de direcciones IP de salida disponibles. Las conexiones de salida desde cualquier función, como a una base de datos back-end, usan una de las direcciones IP de salida disponibles como dirección IP de origen. No se puede saber con antelación qué dirección IP usará una conexión determinada. Por este motivo, el servicio back-end debe abrir su firewall a todas las direcciones IP de salida de la aplicación de función.

Para buscar las direcciones IP de salida disponibles para una aplicación de función:

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Inicie sesión en [Azure Resource Explorer](https://resources.azure.com).
2. Seleccione **suscriptions (suscripciones) > {su suscripción} > providers (proveedores) > Microsoft.Web > sites (sitios)** .
3. En el panel JSON, busque el sitio con una propiedad `id` que acabe en el nombre de la aplicación de función.
4. Vea `outboundIpAddresses` y `possibleOutboundIpAddresses`. 

# <a name="azure-cli"></a>[CLI de Azure](#tab/azurecli)

```azurecli-interactive
az functionapp show --resource-group <GROUP_NAME> --name <APP_NAME> --query outboundIpAddresses --output tsv
az functionapp show --resource-group <GROUP_NAME> --name <APP_NAME> --query possibleOutboundIpAddresses --output tsv
```
---

El conjunto de `outboundIpAddresses` está actualmente disponible para la aplicación de función. El conjunto de `possibleOutboundIpAddresses` incluye las direcciones IP que estarán disponibles solo si la aplicación de función [se escala a otros planes de tarifa](#outbound-ip-address-changes).

> [!NOTE]
> Cuando se escala una aplicación de función que se ejecuta en el [plan de consumo](consumption-plan.md) o el [plan premium](functions-premium-plan.md), puede asignarse un nuevo intervalo de direcciones IP de salida. Cuando la ejecución se realiza en cualquiera de estos planes, no puede confiar en las direcciones IP salientes notificadas para crear una lista de permitidos definitiva. Para poder incluir todas las direcciones salientes posibles que se usan durante el escalado dinámico, tendrá agregar el centro de datos completo a la lista de permitidos.

## <a name="data-center-outbound-ip-addresses"></a>Direcciones IP de salida del centro de datos

Si tiene que agregar las direcciones IP de salida que usan las aplicaciones de funciones a una lista de permitidos, otra opción es agregar el centro de datos de las aplicaciones de funciones (región de Azure) a una lista de permitidos. Puede [descargar un archivo JSON que contiene las direcciones IP de todos los centros de datos de Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Luego, busque el fragmento JSON que se aplique a la región en la que se ejecuta la aplicación de función.

Por ejemplo, el siguiente fragmento de JSON muestra el aspecto que puede tener la lista de permitidos del Oeste de Europa:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Para obtener información acerca de cuándo se actualiza este archivo y cuando cambian las direcciones IP, expanda la sección **Details** (Detalles) de la [página del Centro de descarga](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Cambios en la dirección IP de entrada

La dirección IP de entrada **puede** cambiar cuándo se:

- Elimina una aplicación de función y se vuelve a crearla en otro grupo de recursos.
- Elimina la última aplicación de función de una combinación de grupo de recursos y región, y se vuelve a crear.
- Elimina un enlace TLS; por ejemplo, cuando se [renueva un certificado](../app-service/configure-ssl-certificate.md#renew-certificate).

Si la aplicación de funciones se ejecuta en un [plan de consumo](consumption-plan.md) o un [plan premium](functions-premium-plan.md), la dirección IP de entrada también podría cambiar aunque no se haya realizado ninguna acción como las que [se indicaron anteriormente](#inbound-ip-address-changes).

## <a name="outbound-ip-address-changes"></a>Cambios en la dirección IP de salida

La estabilidad relativa de la dirección IP de salida depende del plan de hospedaje.  

### <a name="consumption-and-premium-plans"></a>Planes de consumo y prémium

Debido a los comportamientos de escalado automático, la dirección IP de salida puede cambiar en cualquier momento cuando se ejecute en un [Plan de consumo](consumption-plan.md) o en un [Plan prémium](functions-premium-plan.md). 

Si tiene que controlar la dirección IP de salida de la aplicación de funciones, por ejemplo, cuando necesite agregarla a una lista de permitidos, puede implementar una [puerta de enlace NAT de red virtual](#virtual-network-nat-gateway-for-outbound-static-ip) en el plan de hospedaje Premium. También puede hacerlo si se ejecuta en un plan Dedicado (App Service).

### <a name="dedicated-plans"></a>Planes dedicados

Cuando se ejecuta en planes dedicados (App Service), el conjunto de direcciones IP de salida disponibles para una aplicación de funciones puede cambiar si:

* Realiza alguna acción que puede cambiar la dirección IP de entrada.
* Cambia el plan de tarifa del plan dedicado (App Service). La lista de todas las posibles direcciones IP de salida que puede utilizar una aplicación, para todos los planes de tarifa, está en la propiedad `possibleOutboundIPAddresses`. Consulte [Búsqueda de las direcciones IP de salida](#find-outbound-ip-addresses).

#### <a name="forcing-an-outbound-ip-address-change"></a>Forzar el cambio de la dirección IP de salida

Use el procedimiento siguiente para forzar deliberadamente un cambio de dirección IP de salida en un plan dedicado (App Service):

1. Escale el plan de App Service o redúzcalo verticalmente entre los planes de tarifa Estándar y Premium v2.

2. Espere 10 minutos.

3. Vuelva a escalar al plan inicial.

## <a name="ip-address-restrictions"></a>Restricciones de las direcciones IP

Puede configurar una lista de direcciones IP a las que desea permitir o denegar el acceso a una aplicación de función. Para más información, consulte [Restricciones de IP estáticas de Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Direcciones IP dedicadas

Hay varias estrategias que puede examinar cuando la aplicación de funciones requiere direcciones IP dedicadas estáticas. 

### <a name="virtual-network-nat-gateway-for-outbound-static-ip"></a>Instancia de NAT Gateway de red virtual para IP estática de salida

Para controlar la dirección IP del tráfico de salida desde las funciones, puede usar una instancia de NAT Gateway de red virtual, a fin de dirigir el tráfico a través de una dirección IP pública estática. Puede usar esta topología cuando realiza la ejecución en un [plan Premium](functions-premium-plan.md) o en un [plan Dedicado (App Service)](dedicated-plan.md). Para más información, consulte [Tutorial: Control de la IP de salida de Azure Functions mediante un servicio NAT Gateway de Azure Virtual Network](functions-how-to-use-nat-gateway.md).

### <a name="app-service-environments"></a>Entornos de App Service

Para tener un control total sobre las direcciones IP, tanto de entrada como de salida, se recomienda usar instancias de [App Service Environment](../app-service/environment/intro.md) (el [nivel Aislado](https://azure.microsoft.com/pricing/details/app-service/) de los planes de App Service). Para más información, consulte [Direcciones IP de App Service Environment](../app-service/environment/network-info.md#ase-ip-addresses) y [Control del tráfico de entrada a App Service Environment](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Para averiguar si una aplicación de función se ejecuta en App Service Environment:

# <a name="azure-porta"></a>[Azure Portal](#tab/portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Vaya a la aplicación de función.
3. Seleccione la pestaña **Información general**.
4. El nivel del plan de App Service aparece en **Plan de App Service/plan de tarifa**. El plan de tarifa de App Service Environment es **Aislado**.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azurecli)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

---

El App Service Environment `sku` es `Isolated`.

## <a name="next-steps"></a>Pasos siguientes

Una causa común de los cambios IP son los cambios de escala de las aplicaciones de función. [Más información acerca del escalado de las aplicaciones de función](functions-scale.md).
