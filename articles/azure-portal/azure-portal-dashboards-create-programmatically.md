---
title: Criar de maneira programática Painéis do Azure
description: Use um Dashboard no portal do Azure como um modelo para criar programaticamente painéis do Azure. Inclui referência JSON.
ms.topic: how-to
ms.date: 12/4/2020
ms.openlocfilehash: bd56dc1c729c5aa7a77e79aa3af3366166fdcfea
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095173"
---
# <a name="programmatically-create-azure-dashboards"></a>Criar de maneira programática Painéis do Azure

Este artigo orienta você pelo processo de criação e publicação programaticamente de painéis do Azure. O painel abaixo é referenciado em todo o documento.

![painel de amostra](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Visão geral

Os painéis compartilhados no [portal do Azure](https://portal.azure.com) são [recursos](../azure-resource-manager/management/overview.md) assim como as máquinas virtuais e as contas de armazenamento. Você pode gerenciar recursos programaticamente usando as [APIs REST do Azure Resource Manager](/rest/api/), os comandos [CLI do Azure](/cli/azure)e [Azure PowerShell](/powershell/azure/get-started-azureps).

Muitos recursos se baseiam nessas APIs para facilitar o gerenciamento de recursos. Cada uma dessas APIs e ferramentas oferece maneiras de criar, listar, recuperar, modificar e excluir recursos. Como os painéis são recursos, você pode escolher sua API ou ferramenta favorita a ser usada.

Independentemente das ferramentas usadas, para criar um Dashboard programaticamente, você constrói uma representação JSON do seu objeto Dashboard. Este objeto contém informações sobre os blocos no painel. Ele inclui tamanhos, posições, recursos aos quais eles estão vinculados e personalizações de usuário.

A maneira mais prática de criar esse documento JSON é usar o portal do Azure. Você pode adicionar e posicionar seus blocos interativamente. Em seguida, exporte o JSON e crie um modelo do resultado para uso posterior em scripts, programas e ferramentas de implantação.

## <a name="create-a-dashboard"></a>Criar um painel

Para criar um painel, selecione **painel** no menu [portal do Azure](https://portal.azure.com) e, em seguida, selecione **novo painel**.

![Comando Novo painel](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Use a Galeria de blocos para localizar e adicionar blocos. Os blocos são adicionados ao serem arrastados e soltados. Alguns blocos dão suporte ao redimensionamento usando uma alça de arrastar.

![Arraste a alça para alterar o tamanho](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Outros têm tamanhos fixos para escolher em seu menu de contexto.

![menu de contexto de tamanhos para alterar o tamanho](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Compartilhar o painel

Depois de configurar o painel, a próxima etapa é publicar o painel usando o comando **compartilhar** .

![compartilhando um painel](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Selecionar **compartilhar** solicita que você escolha em qual assinatura e grupo de recursos publicar. Você deve ter acesso de gravação à assinatura e ao grupo de recursos que escolher. Para obter mais informações, consulte [atribuir funções do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md).

![fazer alterações no compartilhamento e no acesso](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Buscar a representação JSON do painel

A publicação leva apenas alguns segundos. Quando terminar, a próxima etapa será buscar o JSON usando o comando de **Download** .

![baixar representação JSON](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Criar um modelo do JSON

A próxima etapa é criar um modelo a partir desse JSON. Use esse modelo programaticamente com as APIs de gerenciamento de recursos apropriadas, ferramentas de linha de comando ou dentro do Portal.

Você não precisa entender totalmente a estrutura JSON do Dashboard para criar um modelo. Na maioria dos casos, você deseja preservar a estrutura e a configuração de cada bloco. Em seguida, parametriza o conjunto de recursos do Azure ao qual os blocos apontam. Examine seu painel JSON exportado e localize todas as ocorrências de IDs de recursos do Azure. Nosso painel de exemplo tem vários blocos que apontam em uma única máquina virtual do Azure. Isso porque nosso painel só examina esse único recurso. Se você pesquisar o JSON de exemplo, incluído no final do documento, para "/subscriptions", encontrará várias ocorrências dessa ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Para publicar esse painel para qualquer máquina virtual no futuro, parametrizar todas as ocorrências dessa cadeia de caracteres dentro do JSON.

Há duas abordagens para APIs que criam recursos no Azure:

* APIs imperativas criam um recurso por vez. Para saber mais, confira [Recursos](/rest/api/resources/resources).
* Um sistema de implantação baseado em modelo que cria vários recursos dependentes com uma única chamada à API. Para obter mais informações, consulte  [implantar recursos com modelos e Azure PowerShell do Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

A implantação baseada em modelo dá suporte à parametrização e modelagem. Usamos essa abordagem neste artigo.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Criar programaticamente um painel de seu modelo usando uma implantação de modelo

O Azure oferece a capacidade de coordenar a implantação de vários recursos. Você cria um modelo de implantação que expressa o conjunto de recursos a serem implantados e as relações entre eles.  O formato JSON de cada recurso é o mesmo como se você estivesse criando um de cada vez. A diferença é que a linguagem do modelo adiciona alguns conceitos como variáveis, parâmetros, funções básicas e muito mais. Essa sintaxe estendida só tem suporte no contexto de uma implantação de modelo. Ele não funcionará se for usado com as APIs imperativas discutidas anteriormente. Para obter mais informações, consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

A parametrização deve ser feita usando a sintaxe do parâmetro do modelo.  Você substitui todas as instâncias da ID de recurso que encontramos anteriormente, conforme mostrado aqui.

Exemplo de propriedade JSON com ID de recurso embutido em código:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Exemplo de propriedade JSON convertida para uma versão com parâmetros com base nos parâmetros de modelo

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Declare os metadados de modelo necessários e os parâmetros na parte superior do modelo JSON como este:

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
Depois de configurar o modelo, implante-o usando qualquer um dos seguintes métodos:

* [APIs REST](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [CLI do Azure](/cli/azure/group/deployment#az-group-deployment-create)
* [A página de implantação do modelo de portal do Azure](https://portal.azure.com/#create/Microsoft.Template)

Em seguida, você verá duas versões do nosso Dashboard de exemplo JSON. O primeiro é a versão que exportamos do portal que já estava associada a um recurso. A segunda é a versão de modelo que pode ser vinculada de forma programática a qualquer máquina virtual e implantada usando Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>Representação JSON do nosso painel de exemplo antes do Templating

Este exemplo mostra o que você pode esperar para ver se você seguiu este artigo. As instruções exportaram a representação JSON de um dashboard que já está implantado. Os identificadores de recursos embutidos em código mostram que esse painel está apontando para uma máquina virtual específica do Azure.

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

A versão de modelo do painel definiu três parâmetros chamados `virtualMachineName` , `virtualMachineResourceGroup` e `dashboardName` .  Os parâmetros permitem que você aponte este painel em uma máquina virtual do Azure diferente toda vez que implantar. Esse painel pode ser configurado e implantado programaticamente para apontar para qualquer máquina virtual do Azure. Para testar esse recurso, copie o modelo a seguir e cole-o na [página de implantação do modelo de portal do Azure](https://portal.azure.com/#create/Microsoft.Template).

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

Agora que você viu um exemplo de como usar um modelo com parâmetros para implantar um painel, você pode tentar implantar o modelo usando as [APIs REST do Azure Resource Manager](/rest/api/), os comandos [CLI do Azure](/cli/azure)ou [Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Criar programaticamente um painel usando CLI do Azure

Prepare seu ambiente para a CLI do Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Esses exemplos usam o seguinte painel: [portal-dashboard-template-testvm.jsem](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json). Substitua o conteúdo por colchetes angulares pelos seus valores.

Execute o comando [AZ portal Dashboard Create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) para criar um painel:

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Você pode atualizar um painel usando o comando [az portal dashboard update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update):

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Consulte os detalhes de um painel executando o comando [AZ portal Dashboard show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) :

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

Para ver todos os painéis para a assinatura atual, use [az portal dashboard list](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Você também pode ver todos os painéis de um grupo de recursos:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre áreas de trabalho, consulte [Manage portal do Azure Settings and Preferences](set-preferences.md).

Para obter mais informações sobre o suporte à CLI do Azure para painéis, confira [az portal dashboard](/cli/azure/ext/portal/portal/dashboard).
