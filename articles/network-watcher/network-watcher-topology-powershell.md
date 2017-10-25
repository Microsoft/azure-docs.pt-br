---
title: Exibir a topologia do Observador de rede do Azure - PowerShell | Microsoft Docs
description: "Este artigo descreverá como usar o PowerShell para consultar sua topologia de rede."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: bd0e882d-8011-45e8-a7ce-de231a69fb85
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 84e925b4461e55e570e9848bf03d3d352bfff898
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="view-network-watcher-topology-with-powershell"></a>Exibir a topologia do Observador de rede com o PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [API REST](network-watcher-topology-rest.md)

O recurso de topologia do Observador de rede fornece uma representação visual dos recursos de rede em uma assinatura. No portal, essa visualização é apresentada para você automaticamente. As informações que seguem a visualização de topologia no portal podem ser recuperadas por meio do PowerShell.
Esse recurso torna as informações de topologia mais versáteis, já que os dados podem ser consumidos por outras ferramentas para criar a visualização.

A interconexão é modelada em duas relações.

- **Confinamento** - exemplo: a VNet contém uma sub-rede que contém uma NIC
- **Associados** - exemplo: a NIC está associada a uma VM

A lista a seguir contém propriedades que são retornadas ao consultar a REST API de topologia.

* **nome** - o nome do recurso
* **ID** - o URI do recurso.
* **local** - o local onde o recurso existe.
* **associações** - uma lista de associações para o objeto referenciado.
    * **nome** - o nome do recurso referenciado.
    * **resourceId** -o ID do recurso é o URI do recurso referenciado na associação.
    * **associationType** - a relação entre o objeto filho e pai faz referência a esse valor. Os valores válidos são **Contém** ou **Associado**.

## <a name="before-you-begin"></a>Antes de começar

Nesse cenário, você deve usar o cmdlet `Get-AzureRmNetworkWatcherTopology` para recuperar as informações de topologia. Também há um artigo sobre [Como recuperar a topologia de rede com a REST API](network-watcher-topology-rest.md).

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo recupera a resposta de topologia para um grupo de recursos determinado.

## <a name="retrieve-network-watcher"></a>Recuperar o Observador de Rede

A primeira etapa é recuperar a instância do Observador de Rede. A variável `$networkWatcher` é passada para o cmdlet `Get-AzureRmNetworkWatcherTopology`.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="retrieve-topology"></a>Recuperar a topologia

O cmdlet `Get-AzureRmNetworkWatcherTopology` recupera a topologia para um grupo de recursos determinado.

```powershell
Get-AzureRmNetworkWatcherTopology -NetworkWatcher $networkWatcher -TargetResourceGroupName testrg
```

## <a name="results"></a>Resultados

Os resultados retornados têm uma propriedade de nome "Recursos", que contêm o corpo da resposta json para o cmdlet `Get-AzureRmNetworkWatcherTopology`.  A resposta contém os recursos no grupo de segurança de rede e suas associações (ou seja, Contém e Associado).

```json
Id              : 00000000-0000-0000-0000-000000000000
CreatedDateTime : 2/1/2017 7:52:21 PM
LastModified    : 2/1/2017 7:46:18 PM
Resources       : [
                    {
                      "Name": "testrg-vnet",
                      "Id":
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet/subnets/default"
                        }
                      ]
                    },
                    {
                      "Name": "default",
                      "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testr
                  g-vnet/subnets/default",
                      "Location": "westcentralus",
                      "Associations": []
                    },
                    {
                      "Name": "testrg-vnet2",
                      "Id": 
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet2",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/default"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "GatewaySubnet",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/GatewaySubnet"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "gateway2",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworkGateways/gateway2"
                        }
                      ]
                    },
                    ...
                  ]
```

## <a name="next-steps"></a>Próximas etapas

Para saber como visualizar os logs de fluxo NSG com o Power BI, veja [Como visualizar logs de fluxos NSG com Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)


