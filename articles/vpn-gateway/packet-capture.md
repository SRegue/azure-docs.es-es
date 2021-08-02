---
title: Configuración de la captura de paquetes para VPN Gateway
titleSuffix: Azure VPN Gateway
description: Obtenga información sobre la funcionalidad de captura de paquetes que puede usar en las instancias de VPN Gateway para ayudar a suavizar la causa de un problema.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alzam
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c7a781a497ce6b3e4511eff3ba5818bab1a1a2b8
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2021
ms.locfileid: "110681631"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>Configuración de captura de paquetes para instancias de VPN Gateway

Los problemas relacionados con la conectividad y el rendimiento suelen ser complejos. Puede tardar mucho tiempo y esfuerzo en suavizar la causa del problema. La captura de paquetes puede ayudarle a limitar el ámbito de un problema a algunas partes de la red. Puede ayudarle a determinar si el problema está en el lado del cliente de la red, en el lado de Azure de la red o en algún punto entre ambos. Después de limitar el problema, la depuración y las medidas correctivas son más eficaces.

Existen algunas herramientas de capturas de paquetes disponibles comúnmente. La obtención de capturas de paquetes apropiadas con estas herramientas puede resultar complicada, especialmente cuando se trabaja en escenarios con un gran volumen de tráfico. Las capacidades de filtrado que proporciona una captura de paquetes de Azure VPN Gateway son diferenciador importante. Puede usar una captura de paquetes de VPN Gateway junto con las herramientas de captura de paquetes disponibles habitualmente.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Capacidades de filtrado de captura de paquetes de VPN Gateway

Según sus necesidades, la captura de paquetes de VPN Gateway se puede ejecutar en la puerta de enlace o en una conexión específica. También puede ejecutar la captura de paquetes en varios túneles al mismo tiempo. Puede capturar el tráfico unidireccional o bidireccional, el tráfico de IKE y ESP, y los paquetes internos junto con el filtrado en una instancia de VPN Gateway.

El uso de un filtro de tupla de cinco elementos (subred de origen, subred de destino, puerto de origen, puerto de destino y protocolo) y de marcas TCP (SYN, ACK, FIN, URG, PSH y RST) resulta útil al aislar problemas cuando hay mucho volumen de tráfico.

Los ejemplos siguientes de JSON y un esquema JSON proporcionan explicaciones de cada propiedad. Estas son algunas limitaciones a tener en cuenta al ejecutar capturas de paquetes:

- En el esquema que se muestra aquí, el filtro es una matriz, pero en la actualidad no se pueden usar varios filtros simultáneamente.
- No se permiten varias capturas de paquetes en toda la puerta de enlace al mismo tiempo.
- No se permiten varias capturas de paquetes en la misma conexión al mismo tiempo. Puede ejecutar varias capturas de paquetes en diferentes conexiones a la vez.
- Se puede ejecutar un máximo de cinco capturas de paquetes en paralelo por puerta de enlace. Estas capturas de paquetes pueden ser una combinación de capturas de paquetes en toda la puerta de enlace y capturas de paquetes por conexión.
- La unidad de MaxPacketBufferSize es bytes, mientras que la de MaxFileSize es megabytes

### <a name="example-json"></a>Ejemplo de JSON
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>Esquema JSON
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="packet-capture---portal"></a>Captura de paquetes: portal

Puede configurar la captura de paquetes en Azure Portal.

:::image type="content" source="./media/packet-capture/portal.jpg" alt-text="Captura de pantalla de la captura de paquetes en el portal." lightbox="./media/packet-capture/portal.jpg":::

## <a name="packet-capture---powershell"></a>Captura de paquetes: PowerShell

En los siguientes ejemplos se muestran comandos de PowerShell que inician y detienen capturas de paquetes. Para obtener más información sobre las opciones de parámetros, consulte [Start-AzVirtualnetworkGatewayPacketCapture](/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture).

>
### <a name="prerequisite"></a>Requisito previo

* Los datos de captura de paquetes tendrán que registrarse en una cuenta de almacenamiento de la suscripción. Consulte [Creación de una cuenta de almacenamiento](../storage/common/storage-account-create.md).
* Para detener la captura de paquetes, deberá generar el elemento `SASUrl` para la cuenta de almacenamiento. Consulte [Creación de una SAS de delegación de usuarios](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Iniciar la captura de paquetes para una VPN Gateway

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Puede usar el parámetro opcional `-FilterData` para aplicar un filtro.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Detener la captura de paquetes para una VPN Gateway

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

Para más información sobre las opciones de parámetros, consulte [Stop-AzVirtualnetworkGatewayPacketCapture](/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture).

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Iniciar la captura de paquetes para una conexión de VPN Gateway

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Puede usar el parámetro opcional `-FilterData` para aplicar un filtro.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Detener la captura de paquetes en una conexión de VPN Gateway

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

Para más información sobre las opciones de parámetros, consulte [Stop-AzVirtualNetworkGatewayConnectionPacketCapture](/powershell/module/az.network/stop-azvirtualnetworkgatewayconnectionpacketcapture).

## <a name="key-considerations"></a>Consideraciones clave

- La ejecución de capturas de paquetes puede afectar al rendimiento. Recuerde detener la captura de paquetes cuando no sea necesaria.
- La duración de la captura de paquetes mínima sugerida es de 600 segundos. Debido a los problemas de sincronización entre varios componentes de la ruta de acceso, es posible que las capturas de paquetes más cortas no proporcionen datos completos.
- Los archivos de datos de captura de paquetes se generan en formato PCAP. Use Wireshark u otras aplicaciones disponibles habitualmente para abrir archivos PCAP.
- Las capturas de paquetes no se admiten en las puertas de enlace basadas en directivas.
- Si el parámetro `SASurl` no está configurado correctamente, es posible que se produzcan errores de almacenamiento en el seguimiento. Para obtener ejemplos sobre cómo generar correctamente un parámetro `SASurl`, consulte [Stop-AzVirtualNetworkGatewayPacketCapture](/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture).



## <a name="next-steps"></a>Pasos siguientes

Para más información sobre VPN Gateway, consulte [¿Qué es VPN Gateway?](vpn-gateway-about-vpngateways.md)
