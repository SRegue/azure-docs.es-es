---
title: Integración de Azure Event Hubs con Azure Private Link
description: Aprenda a integrar Azure Event Hubs con Azure Private Link
ms.date: 05/10/2021
ms.topic: article
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 48688c9f16330830111aff5dd26292825370fcb9
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112415575"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-via-private-endpoints"></a>Permiso para acceder a los espacios de nombres de Azure Event Hubs a través de puntos de conexión privados 
Azure Private Link le permite acceder a los servicios de Azure (por ejemplo, Azure Event Hubs, Azure Storage y Azure Cosmos DB) y a los servicios de asociados o clientes hospedados de Azure mediante un **punto de conexión privado** de la red virtual.

Un punto de conexión privado es una interfaz de red que le conecta de forma privada y segura a un servicio con la tecnología de Azure Private Link. El punto de conexión privado usa una dirección IP privada de la red virtual para incorporar el servicio de manera eficaz a su red virtual. Todo el tráfico dirigido al servicio se puede enrutar mediante el punto de conexión privado, por lo que no se necesita ninguna puerta de enlace, dispositivos NAT, conexiones de ExpressRoute o VPN ni direcciones IP públicas. El tráfico entre la red virtual y el servicio atraviesa la red troncal de Microsoft, eliminando la exposición a la red pública de Internet. Puede conectarse a una instancia de un recurso de Azure, lo que le otorga el nivel más alto de granularidad en el control de acceso.

Para más información, consulte [¿Qué es Azure Private Link?](../private-link/private-link-overview.md)

## <a name="important-points"></a>Observaciones importantes
- Esta característica no se admite en el nivel **Básico**.
- La habilitación de los puntos de conexión privados puede evitar que otros servicios de Azure interactúen con Event Hubs.  Las solicitudes que bloquean incluyen aquellas de otros servicios de Azure, desde Azure Portal, desde los servicios de registro y de métricas, etc. Como excepción, puede permitir el acceso a los recursos de Event Hubs desde determinados **servicios de confianza**, incluso cuando los puntos de conexión privados no están habilitados. Para ver una lista de servicios de confianza, consulte [Servicios de confianza](#trusted-microsoft-services).
- Especifique **al menos una regla de IP o una regla de red virtual** para que el espacio de nombres permita el tráfico solo desde las direcciones IP o la subred especificadas de una red virtual. Si no hay ninguna regla de red virtual y de IP, se puede acceder al espacio de nombres a través de la red pública de Internet (mediante la clave de acceso). 

## <a name="add-a-private-endpoint-using-azure-portal"></a>Incorporación de un punto de conexión privado mediante Azure Portal

### <a name="prerequisites"></a>Requisitos previos

Para integrar un espacio de nombres de Event Hubs con Azure Private Link, necesitará las siguientes entidades o permisos:

- Un espacio de nombres de Event Hubs.
- Una red virtual de Azure.
- Una subred en la red virtual. Puede usar la subred **predeterminada**. 
- Permisos de propietario o colaborador para el espacio de nombres y la red virtual.

El punto de conexión privado y la red virtual deben estar en la misma región. Al seleccionar una región para el punto de conexión privado mediante el portal, solo se filtran automáticamente las redes virtuales que se encuentran en dicha región. El espacio de nombres puede estar en una región diferente.

El punto de conexión privado usa una dirección IP privada en la red virtual.

### <a name="steps"></a>Pasos
Si ya tiene un espacio de nombres de Event Hubs, puede crear una conexión de vínculo privado siguiendo estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 
2. En la barra de búsqueda, escriba **Event Hubs**.
3. En la lista, seleccione el **espacio de nombres** al que desea agregar un punto de conexión privado.
4. Seleccione **Redes** en **Configuración** en el menú de la izquierda.

    :::image type="content" source="./media/private-link-service/selected-networks-page.png" alt-text="Pestaña Redes: opción redes seleccionadas" lightbox="./media/private-link-service/selected-networks-page.png":::    

    > [!WARNING]
    > De forma predeterminada, está seleccionada la opción **Redes seleccionadas**. Si no se especifica una regla de firewall de IP ni se agrega una red virtual, se puede acceder al espacio de nombres desde la red pública de Internet (mediante la clave de acceso). 
1. Seleccione la pestaña **Conexiones de puntos de conexión privadas** en la parte superior de la página. 
1. Seleccione el botón **+ Punto de conexión privado** en la parte superior de la página.

    :::image type="content" source="./media/private-link-service/private-link-service-3.png" alt-text="Página Redes - Pestaña Conexiones de puntos de conexión privadas - Vínculo Agregar punto de conexión privado":::
7. En la página **Conceptos básicos**, siga estos pasos: 
    1. Seleccione la **suscripción de Azure** donde desea crear el punto de conexión privado. 
    2. Seleccione el **grupo de recursos** para el recurso de punto de conexión privado.
    3. Escriba el **Nombre** del punto de conexión privado. 
    5. Seleccione la **región** del punto de conexión privado. El punto de conexión privado debe estar en la misma región que la red virtual, que puede no ser la misma que la del recurso de Private Link al que se está conectando. 
    6. Seleccione **Siguiente: Recurso >** en la parte inferior de la página.

        ![Creación de un punto de conexión privado: página Conceptos básicos](./media/private-link-service/create-private-endpoint-basics-page.png)
8. En la página **Recurso**, siga estos pasos:
    1. Como método de conexión, si selecciona **Conectarse a un recurso de Azure en mi directorio.** , siga estos pasos: 
        1. Seleccione la **suscripción de Azure** en la que existe el **espacio de nombres de Event Hubs**. 
        2. En **Tipo de recurso**, seleccione **Microsoft.EventHub/namespaces** para el **tipo de recurso**.
        3. En **Recurso**, seleccione un espacio de nombres de Event Hubs de la lista desplegable. 
        4. Confirme que **Subrecurso de destino** está establecido en **espacio de nombres**.
        5. Seleccione **Siguiente: Configuración >** situado en la parte inferior de la página. 
        
            ![Creación de un punto de conexión privado: página Recurso](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Si selecciona **Conéctese a un recurso de Azure por identificador de recurso o alias.** , siga estos pasos:
        1. Escriba el **identificador de recurso** o **alias**. Puede ser el identificador de recurso o el alias que alguien haya compartido con usted. La forma más fácil de obtener el identificador de recurso es desplazarse hasta el espacio de nombres de Event Hubs de Azure Portal y copiar la parte de URI a partir de `/subscriptions/`. Vea la imagen siguiente como ejemplo. 
        2. En **Subrecurso de destino**, escriba **espacio de nombres**. Este es el tipo de subrecurso al que puede acceder el punto de conexión privado.
        3. (Opcional) Escriba un **mensaje de solicitud**. El propietario del recurso ve este mensaje mientras administra la conexión del punto de conexión privado.
        4. Después, seleccione **Next (Siguiente): Configuración >** situado en la parte inferior de la página.

            ![Creación de un punto de conexión privado: conexión mediante el identificador de recurso](./media/private-link-service/connect-resource-id.png)
9. En la página **Configuración**, seleccione la subred de una red virtual en la que desee implementar el punto de conexión privado. 
    1. Seleccione una **red virtual**. En la lista desplegable, solo se muestran las redes virtuales de la suscripción y la ubicación seleccionadas actualmente. 
    2. Seleccione una **subred** de la red virtual que seleccionó. 
    3. Seleccione **Siguiente: Etiquetas >** situado en la parte inferior de la página. 

        ![Creación de un punto de conexión privado: página Configuración](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. En la página **Etiquetas**, cree cualquier etiqueta (nombres y valores) que desee asociar al recurso de punto de conexión privado. Después, en la parte inferior de la página, seleccione el botón **Revisar y crear**. 
11. En **Revisar y crear**, revise toda la configuración y seleccione **Crear** para crear el punto de conexión privado.
    
    ![Creación de un punto de conexión privado: página Revisar y crear](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Confirme que la conexión de punto de conexión privado que ha creado aparece en la lista de puntos de conexión. En este ejemplo, el punto de conexión privado se aprueba automáticamente porque se conectó a un recurso de Azure de su directorio y tiene permisos suficientes. 

    ![Punto de conexión privado creado](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [event-hubs-trusted-services](./includes/event-hubs-trusted-services.md)]

Para permitir que los servicios de confianza accedan a su espacio de nombres, cambie a la pestaña **Firewalls y redes virtuales** de la página **Redes** y seleccione **Sí** para **¿Quiere permitir que los servicios de confianza de Microsoft puedan omitir este firewall?** 

## <a name="add-a-private-endpoint-using-powershell"></a>Incorporación de un punto de conexión privado mediante PowerShell
En el ejemplo siguiente se muestra cómo usar Azure PowerShell para crear una conexión de punto de conexión privado. No crea un clúster dedicado. Siga los pasos de [este artículo](event-hubs-dedicated-cluster-create-portal.md) para crear un clúster de Event Hubs dedicado. 

```azurepowershell-interactive
$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
New-AzResourceGroup -Name $rgName -Location $vnetLocation 

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>Configuración de la zona DNS privada
Cree una zona DNS privada para el dominio de Event Hubs y cree un vínculo de asociación con la red virtual:

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Administración de puntos de conexión privados desde Azure Portal

Cuando se crea un punto de conexión privado, se debe aprobar la conexión. Si el recurso para el que va a crear un punto de conexión privado está en el directorio, puede aprobar la solicitud de conexión siempre que tenga permisos suficientes. Si se va a conectar a un recurso de Azure en otro directorio, debe esperar a que el propietario de ese recurso apruebe la solicitud de conexión.

Hay cuatro estados de aprovisionamiento:

| Acción del servicio | Estado de punto de conexión privado del consumidor del servicio | Descripción |
|--|--|--|
| None | Pending | La conexión se crea manualmente y está pendiente de aprobación por parte del propietario del recurso de Private Link. |
| Aprobación | Aprobado | La conexión se aprobó de forma automática o manual y está lista para usarse. |
| Reject | Rechazada | El propietario del recurso de vínculo privado rechazó la conexión. |
| Remove | Escenario desconectado | El propietario del recurso del vínculo privado quitó la conexión, el punto de conexión privado se vuelve informativo y debe eliminarse para la limpieza. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Aprobación, rechazo o eliminación de una conexión de punto de conexión privado

1. Inicie sesión en Azure Portal.
2. En la barra de búsqueda, escriba **Event Hubs**.
3. Seleccione el **espacio de nombres** que desea administrar.
4. Seleccione la pestaña **Redes**.
5. Vaya a la sección correspondiente a continuación según la operación que desee: aprobar, rechazar o quitar.

### <a name="approve-a-private-endpoint-connection"></a>Aprobación de una conexión de punto de conexión privado
1. Si hay alguna conexión pendiente, verá una conexión que aparece con el estado **Pendiente** como estado de aprovisionamiento. 
2. Seleccione el **punto de conexión privado** que desea aprobar.
3. Seleccione el botón **Aprobar**.

    ![Aprobación de un punto de conexión privado](./media/private-link-service/approve-private-endpoint.png)
4. En la página **Aprobación de la conexión** agregue un comentario (opcional), y seleccione **Sí**. Si selecciona **No**, no ocurrirá nada. 
5. Ahora puede ver que el estado de la conexión de punto de conexión privado de la lista ha cambiado a **Aprobado**. 

### <a name="reject-a-private-endpoint-connection"></a>Rechazo de una conexión de punto de conexión privado

1. Si hay conexiones de punto de conexión privado que quiere rechazar, ya sea una solicitud pendiente o una conexión existente, seleccione la conexión y haga clic en el botón **Rechazar**.

    ![Rechazo de un punto de conexión privado](./media/private-link-service/private-endpoint-reject-button.png)
2. En la página **Rechazo de la conexión**, escriba un comentario (opcional), y seleccione **Sí**. Si selecciona **No**, no ocurrirá nada. 
3. Ahora puede ver que el estado de la conexión de punto de conexión privado de la lista ha cambiado a **Rechazado**. 

### <a name="remove-a-private-endpoint-connection"></a>Eliminación de una conexión de punto de conexión privado

1. Para eliminar una conexión de punto de conexión privado, selecciónela en la lista y seleccione **Eliminar** en la barra de herramientas.
2. En la página **Eliminar conexión**, seleccione **Sí** para confirmar la eliminación del punto de conexión privado. Si selecciona **No**, no ocurrirá nada.
3. Ahora puede ver que el estado ha cambiado a **Desconectado**. A continuación, verá que el punto de conexión desaparece de la lista.

## <a name="validate-that-the-private-link-connection-works"></a>Validación de que la conexión de vínculo privado funciona

Debe comprobar que los recursos de la red virtual del punto de conexión privado se conectan al espacio de nombres de Event Hubs mediante una dirección IP privada y que tienen la integración correcta de la zona DNS privada.

En primer lugar, cree una máquina virtual siguiendo los pasos que encontrará en [Creación de una máquina virtual Windows en Azure Portal](../virtual-machines/windows/quick-create-portal.md).

Haga clic en la pestaña **Redes**: 

1. Especifique **Red virtual** y **Subred**. Debe seleccionar la instancia de Virtual Network en la que implementó el punto de conexión privado.
2. Especifique un recurso de **dirección IP pública**.
3. En **Grupo de seguridad de red de NIC**, seleccione **Ninguno**.
4. En **Equilibrio de carga**, seleccione **No**.

Conéctese a la máquina virtual, abra la línea de comandos y ejecute el siguiente comando:

```console
nslookup <event-hubs-namespace-name>.servicebus.windows.net
```

Debería ver un resultado con el siguiente aspecto. 

```console
Non-authoritative answer:
Name:    <event-hubs-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <event-hubs-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitaciones y consideraciones de diseño

**Precios**: Para más información sobre los precios, consulte [Precios de Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

**Limitaciones**:  Esta característica está disponible en todas las regiones públicas de Azure.

**Número máximo de puntos de conexión privados por espacio de nombres de Event Hubs**: 120.

Para más información, consulte [Servicio Azure Private Link: Limitaciones](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Azure Private Link](../private-link/private-link-service-overview.md).
- Más información acerca de [Azure Event Hubs](event-hubs-about.md)
