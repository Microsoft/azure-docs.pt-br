---
title: A estrutura de Painéis do Azure | Microsoft Docs
description: Este artigo explica a estrutura JSON de um Painel do Azure
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: kfollis
ms.openlocfilehash: a7e9acbe78ffdca2e615873cc4c33f86b250a429
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551476"
---
# <a name="the-structure-of-azure-dashboards"></a>A estrutura de Painéis do Azure
Este documento explica a estrutura de um painel do Azure, usando o painel a seguir como exemplo:

![painel de amostra](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Como os [painéis compartilhados do Azure são recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), este painel pode ser representado como JSON.  O JSON a seguir representa o painel visualizado acima.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

## <a name="common-resource-properties"></a>Propriedades comuns do recurso

Vamos dividir as seções relevantes do JSON.  As propriedades de nível superior, __id__, __nome__, __tipo__, __local__ e __marcas__ são compartilhadas entre todos os tipos de recursos do Azure. Ou seja, elas não têm muita relação com o conteúdo do painel.

### <a name="the-id-property"></a>A propriedade id

A ID de recurso do Azure está sujeita às [convenções de nomenclatura dos recursos do Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Quando o portal cria um painel, ele, geralmente, escolhe uma ID na forma de um GUID, mas você é livre para usar qualquer nome válido ao criar programaticamente. 

### <a name="the-name-property"></a>A propriedade name
O nome é o segmento da ID de recurso que não inclui informações de assinatura, tipo de recurso ou grupo de recursos. Basicamente, é o último segmento da ID de recurso.

### <a name="the-type-property"></a>A propriedade type
Todos os painéis são do tipo __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>A propriedade location
Ao contrário de outros recursos, os painéis não têm um componente de tempo de execução.  Para os painéis, o local indica a localização geográfica principal que armazena a representação JSON do painel. O valor deve ser um dos códigos de local que podem ser buscados usando a [API de locais no recurso de assinaturas](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>A propriedade tags
Marcas são um recurso comum de recursos do Azure que lhe permitem organizar seus recursos por pares de valor de nome arbitrário. Para os painéis, há uma marca especial chamada __título oculto__. Se seu painel tem essa propriedade preenchida, ele é usado como o nome de exibição para o painel no portal. As IDs de recurso do Azure não podem ser renomeadas, mas as marcas podem. Esta marca dá uma maneira de ter um nome de exibição renomeável para seu painel.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>O objeto proprierties
O objeto de propriedades contém duas propriedades, __lenses__ e __metadata__. A propriedade __lenses__ contém informações sobre os blocos (também conhecidos como partes) no painel.  A propriedade __metadata__ está disponível para futuros recursos potenciais.

### <a name="the-lenses-property"></a>A propriedade lenses
A propriedade __lenses__ contém o painel. Observe que o objeto lenses neste exemplo contém uma única propriedade chamada "0". As lentes são um conceito de agrupamento que não está implementado atualmente nos painéis. Por enquanto, todos os seus painéis têm essa propriedade única no objeto lens, novamente, chamada "0".

### <a name="the-lens-object"></a>O objeto lens
O objeto sob o "0" contém duas propriedades, __order__ e __parts__.  Na versão atual de painéis, __ordem__ é sempre 0. A propriedade __parts__ contém um objeto que define as partes individuais (também conhecidas como blocos) no painel.

O objeto __parts__ contém uma propriedade para cada parte, no qual o nome da propriedade é um número. Esse número não é significativo. 

### <a name="the-part-object"></a>O objeto part
Cada objeto parte individual tem um __posição__ e __metadados__.

### <a name="the-position-object"></a>O objeto position
A propriedade __position__ contém as informações de tamanho e local para a parte expressa como __x__, __y__, __rowSpan__ e __colSpan__. Os valores estão em termos de unidades de grade. Essas unidades de grade são visíveis quando o painel está no modo de personalizar conforme mostrado aqui. Se você quiser que um bloco tenha uma largura de duas unidades de grade, a altura de unidade de uma grade e um local no canto superior esquerdo do painel, então, o objeto de posição terá esta aparência:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![unidades de grade](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>O objeto metadata
Cada parte tem uma propriedade de metadados, um objeto tem apenas uma propriedade necessária chamada __type__. Essa cadeia de caracteres informa o portal qual bloco mostrar. Nosso painel de exemplo usa esses tipos de blocos:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – usado para mostrar as métricas de monitoramento
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – usado para mostrar com texto ou imagens com formatação básica para listas, links, etc.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – usado para mostrar os vídeos do YouTube, Channel9 e qualquer outro tipo de vídeo que funciona em uma marca de vídeo de HTML.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – usado para mostrar o nome e o status de uma máquina virtual do Azure.

Cada tipo de parte tem sua própria configuração. As propriedades de configuração possíveis são chamadas de __entradas__, __configurações__ e __ativo__. 

### <a name="the-inputs-object"></a>O objeto inputs
O objeto inputs geralmente contém informações que associam um bloco a uma instância do recurso.  A parte da máquina virtual em nosso painel de exemplo contém uma única entrada que usa a ID de recurso do Azure para expressar a associação.  Esse formato de ID de recurso é consistente em todos os recursos do Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
A parte do gráfico de métricas tem uma única entrada que expressa o recurso para associação, bem como informações sobre as métricas que estão sendo exibidas. Aqui está a entrada para o bloco que mostra as métricas Saída de Rede e Entrada de Rede.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
                {
                    "name": "Network In",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                },
                {
                    "name": "Network Out",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                }
            ]
        }
    }
]

```

### <a name="the-settings-object"></a>O objeto settings
O objeto settings contém os elementos configuráveis de uma parte.  Em nosso painel de amostra, a parte Markdown usa configurações para armazenar o conteúdo markdown personalizado, bem como o título e o subtítulo configuráveis.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

Da mesma forma, o bloco de vídeo tem suas próprias configurações que contêm um ponteiro para que o vídeo seja reproduzido, uma configuração de reprodução automática e informações de título opcional.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>O objeto asset
Os blocos que estão associados aos objetos de portal gerenciáveis de primeira classe (chamados de ativos) têm essa relação expressa pelo objeto asset.  Em nosso painel de exemplo, o bloco de máquina virtual contém a descrição do ativo.  A propriedade __idInputName__ informa ao portal que a entrada da ID contém o identificador exclusivo para o ativo, neste caso, a ID de recurso. A maioria dos tipos de recursos do Azure tem ativos definidos no portal.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`
