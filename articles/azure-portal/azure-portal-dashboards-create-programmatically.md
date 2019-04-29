---
title: Criar de maneira programática Painéis do Azure | Microsoft Docs
description: Este artigo explica como criar programaticamente os Painéis do Azure.
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
ms.openlocfilehash: b24a0397a1365479907fedc6348caa54508dbbb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552094"
---
# <a name="programmatically-create-azure-dashboards"></a>Criar de maneira programática Painéis do Azure

Este documento explica o processo de criação e publicação de forma programática de painéis do Azure. O painel abaixo é referenciado em todo o documento.

![painel de amostra](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Visão geral

Os painéis compartilhados no Azure são [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) assim como máquinas virtuais e contas de armazenamento.  Portanto, eles podem ser gerenciados programaticamente por meio de [APIs REST do Azure Resource Manager](/rest/api/), [CLI do Azure](https://docs.microsoft.com/cli/azure), [comandos do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) e muitos recursos do [Portal do Azure](https://portal.azure.com) compilados no topo dessas APIs para facilitar o gerenciamento de recursos.  

Cada uma dessas APIs e ferramentas oferece maneiras de criar, listar, recuperar, modificar e excluir recursos.  Como painéis são recursos, você pode escolher sua API/ferramenta favorita para usar.

Independentemente de qual ferramenta usar, você precisa construir uma representação JSON do seu objeto de painel antes de chamar qualquer API de criação de recursos. Este objeto contém informações sobre as partes (também conhecidas como blocos) no painel. Inclui tamanhos, posições, recursos aos quais estão associados e todas as personalizações de usuário.

A maneira mais prática para criar este documento JSON é usar [o portal](https://portal.azure.com/) para adicionar e posicionar seus blocos de forma interativa. Então, exporte o JSON. Por fim, você deve criar um modelo do resultado para uso posterior em scripts, programas e ferramentas de implantação.

## <a name="create-a-dashboard"></a>Criar um painel

Para criar um novo painel, use o comando Novo painel na tela principal do portal.

![Comando Novo painel](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Você pode usar a galeria de blocos para localizar e adicionar blocos. Os blocos são adicionados ao serem arrastados e soltados. Alguns blocos dão suporte ao redimensionamento por meio de um identificador de arrastar, enquanto outros dão suporte a tamanhos de correções podem ser vistos no seu menu de contexto.

### <a name="drag-handle"></a>Identificador de arrastar
![identificador de arrastar](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Tamanhos fixos por meio do menu de contexto
![Menu de contexto de tamanhos](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Compartilhar o painel

Depois de configurar o painel de sua preferência, as próximas etapas são publicar o painel (usando o comando Compartilhamento) e, em seguida, usar o gerenciador de recursos para buscar o JSON.

![Comando de compartilhamento](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Clicar no comando Compartilhamento mostra uma caixa de diálogo que solicita a escolha de a qual grupo de recursos e assinatura publicar. Tenha em mente que você [deve ter acesso de gravação](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) à assinatura e ao grupo de recursos que escolher.

![compartilhamento e acesso](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Buscar a representação JSON do painel

A publicação leva apenas alguns segundos.  Quando tiver terminado, a próxima etapa será ir para o [Gerenciador de Recursos](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) para buscar o JSON.

![Procurar Gerenciador de Recursos](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

No Gerenciador de Recursos, navegue até a assinatura e o grupo de recursos que você escolheu. Em seguida, clique no recurso do painel publicado recentemente para revelar o JSON.

![Gerenciador de Recursos json](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Criar um modelo do JSON

A próxima etapa é criar um modelo deste JSON para que ele possa ser reutilizado por meio de programação com as APIs de gerenciamento de recursos apropriadas, bem como com ferramentas de linha de comando ou no portal.

Não é necessário entender completamente a estrutura JSON do painel para criar um modelo. Na maioria dos casos, você deseja preservar a estrutura e a configuração de cada bloco e parametrizar o conjunto de recursos do Azure está para o qual estão apontando. Examine seu painel JSON exportado e localize todas as ocorrências de IDs de recurso do Azure. Nosso painel de exemplo tem vários blocos que apontam em uma única máquina virtual do Azure. Isso ocorre porque o nosso painel olha apenas esse recurso único. Se pesquisar o json de exemplo (incluído no final do documento) para “/subscriptions”, você encontrará várias ocorrências dessa ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

No futuro, para publicar esse painel para qualquer máquina virtual, você precisará parametrizar cada ocorrência dessa cadeia de caracteres no JSON. 

Há dois tipos de APIs que criam recursos no Azure. [APIs fundamentais](https://docs.microsoft.com/rest/api/resources/resources) que criam um recurso de cada vez e um sistema de [implantação baseada em modelo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) que pode coordenar a criação de vários recursos dependentes com uma única chamada à API. O último, dá suporte de modo nativo à parametrização e modelagem para que possamos usar em nosso exemplo.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Criar programaticamente um painel de seu modelo usando uma implantação de modelo

O Azure oferece a capacidade de coordenar a implantação de vários recursos. Você cria um modelo de implantação que expressa o conjunto de recursos para implantar, bem como as relações entre eles.  O formato JSON de cada recurso é o mesmo como se você estivesse criando um de cada vez. A diferença é que a [linguagem do modelo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) adiciona alguns conceitos como variáveis, parâmetros, funções básicas e muito mais. Essa sintaxe estendida só tem suporte no contexto de uma implantação de modelo e não funciona se usada com as APIs fundamentais discutidas anteriormente.

Se estiver indo por essa rota, a parametrização deverá ser feita usando a sintaxe do parâmetro do modelo.  Você substitui todas as instâncias da ID de recurso que encontramos anteriormente, conforme mostrado aqui.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Exemplo de propriedade JSON com a ID de recurso embutida em código
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Exemplo de propriedade JSON convertida para uma versão com parâmetros com base nos parâmetros de modelo

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

Você também precisa declarar alguns metadados de modelo necessários e os parâmetros na parte superior do modelo json, como segue:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__Você pode ver o modelo de trabalho completo no final deste documento.__

Depois de ter criado seu modelo, é possível implantá-lo usando as [APIs REST](https://docs.microsoft.com/rest/api/resources/deployments), o [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), a [CLI do Azure](https://docs.microsoft.com/cli/azure/group/deployment#az-group-deployment-create) ou a [página de implantação de modelo do portal](https://portal.azure.com/#create/Microsoft.Template).

Aqui estão duas versões do nosso painel JSON de exemplo. O primeiro é a versão que exportamos do portal que já estava associada a um recurso. O segundo é a versão do modelo que pode ser vinculada por meio de programação para qualquer VM e implantada usando o Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>A representação JSON do nosso painel de exemplo (antes da aplicação de modelos)

Isso é o que você pode esperar ver se seguir as instruções anteriores para buscar a representação JSON de um painel que já esteja implantado. Observe os identificadores de recurso embutido em código que mostram que este painel está apontando para uma máquina virtual específica do Azure.

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
        "metadata": { }
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

### <a name="template-representation-of-our-example-dashboard"></a>A representação de modelo do nosso painel de exemplo

A versão do modelo do painel tem três parâmetros definidos chamados __virtualMachineName__, __virtualMachineResourceGroup__ e __dashboardName__.  Os parâmetros permitem que você aponte este painel em uma máquina virtual do Azure diferente toda vez que implantar. As IDs com parâmetros são realçadas para mostrar que esse painel pode ser programaticamente configurado e implantado para apontar para qualquer máquina virtual do Azure. A maneira mais fácil de testar esse recurso é copiar o modelo a seguir e colar na [página de implantação de modelo do Portal do Azure](https://portal.azure.com/#create/Microsoft.Template). 

Este exemplo implanta um painel por si só, mas a linguagem do modelo permite que você implante vários recursos e agrupe um ou mais painéis ao lado deles. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```
