---
title: 'Gateway de VPN do Azure: configurar a captura de pacotes'
description: Saiba mais sobre a funcionalidade de captura de pacote que você pode usar em gateways de VPN para ajudar a reduzir a causa de um problema.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alzam
ms.openlocfilehash: 0983139d1c9af235eba4c9f99da7bc9dea3f231b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726607"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>Configurar a captura de pacotes para gateways de VPN

Problemas relacionados à conectividade e ao desempenho são geralmente complexos. Pode levar muito tempo e esforço para restringir a causa do problema. A captura de pacotes pode ajudá-lo a reduzir o escopo de um problema para determinadas partes da rede. Ele pode ajudá-lo a determinar se o problema está no lado do cliente da rede, no lado do Azure da rede ou em algum lugar entre eles. Depois de restringir o problema, é mais eficiente depurar e tomar medidas corretivas.

Há algumas ferramentas de captura de pacote comumente disponíveis. Obter capturas de pacotes relevantes com essas ferramentas pode ser complicado, especialmente em cenários de tráfego de alto volume. Os recursos de filtragem fornecidos pela captura de pacotes do gateway de VPN do Azure são um dos principais diferenciais. Você pode usar a captura de pacotes do gateway de VPN junto com as ferramentas de captura de pacote normalmente disponíveis.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Recursos de filtragem de captura de pacotes do gateway de VPN

Você pode executar a captura de pacotes do gateway de VPN no gateway ou em uma conexão específica, dependendo de suas necessidades. Você também pode executar a captura de pacotes em vários túneis ao mesmo tempo. Você pode capturar o tráfego unidirecional ou bidirecional, o tráfego IKE e ESP e os pacotes internos, juntamente com a filtragem em um gateway de VPN.

É útil usar um filtro de cinco tuplas (sub-rede de origem, sub-rede de destino, porta de origem, porta de destino, protocolo) e sinalizadores TCP (SYN, ACK, FIN, URG, PSH, RST) quando você estiver isolando problemas no tráfego de alto volume.

Os exemplos a seguir de JSON e um esquema JSON fornecem explicações de cada propriedade. Aqui estão algumas limitações para ter em mente ao executar capturas de pacote:

- No esquema mostrado aqui, o filtro é uma matriz, mas no momento apenas um filtro pode ser usado de cada vez.
- Você não pode executar várias capturas de pacote de todo o gateway ao mesmo tempo.
- Você não pode executar várias capturas de pacote em uma única conexão ao mesmo tempo. Você pode executar várias capturas de pacote em diferentes conexões ao mesmo tempo.
- Um máximo de cinco capturas de pacote pode ser executado em paralelo por gateway. Essas capturas de pacote podem ser uma combinação de capturas de pacotes em todo o gateway e capturas de pacotes por conexão.
- A unidade para MaxPacketBufferSize é bytes e MaxFileSize é megabytes

### <a name="example-json"></a>JSON de exemplo
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
### <a name="json-schema"></a>JSON schema
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

## <a name="packet-capture---portal"></a>Captura de pacote-Portal

Você pode configurar a captura de pacotes no portal do Azure.

:::image type="content" source="./media/packet-capture/portal.jpg" alt-text="Captura de tela da captura de pacote no Portal." lightbox="./media/packet-capture/portal.jpg":::

## <a name="packet-capture---powershell"></a>Captura de pacote-PowerShell

Os exemplos a seguir mostram os comandos do PowerShell que iniciam e param as capturas de pacote. Para obter mais informações sobre as opções de parâmetro, consulte [Start-AzVirtualnetworkGatewayPacketCapture](/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Iniciar captura de pacote para um gateway de VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Você pode usar o parâmetro opcional `-FilterData` para aplicar um filtro.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Parar captura de pacote para um gateway de VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Iniciar captura de pacote para uma conexão de gateway de VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Você pode usar o parâmetro opcional `-FilterData` para aplicar um filtro.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Parar captura de pacote em uma conexão de gateway de VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Principais considerações

- A execução da captura de pacotes pode afetar o desempenho. Lembre-se de interromper a captura de pacotes quando você não precisar dela.
- A duração mínima da captura de pacotes sugerida é de 600 segundos. Devido a problemas de sincronização entre vários componentes no caminho, as capturas de pacotes mais curtas podem não fornecer dados completos.
- Os arquivos de dados de captura de pacote são gerados no formato PCAP. Use o Wireshark ou outros aplicativos comumente disponíveis para abrir arquivos PCAP.
- Não há suporte para capturas de pacote em gateways baseados em políticas.
- Se o `SASurl` parâmetro não estiver configurado corretamente, o rastreamento poderá falhar com erros de armazenamento. Para obter exemplos de como gerar um `SASurl` parâmetro corretamente, consulte [Stop-AzVirtualNetworkGatewayPacketCapture](/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture).



## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o gateway de VPN, consulte [o que é o gateway de VPN?](vpn-gateway-about-vpngateways.md).
