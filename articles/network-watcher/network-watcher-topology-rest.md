---
title: Exibir a topologia do Observador de Rede do Azure - API REST | Microsoft Docs
description: "Este artigo descreverá como usar a API REST para consultar sua topologia de rede."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: de9af643-aea1-4c4c-89c5-21f1bf334c06
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fefeae4e816994d3ee69d6ac1c1cbe6cf8bbd06e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="view-network-watcher-topology-with-rest-api"></a>Exibir a topologia do Observador de rede com a API REST

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

Nesse cenário, você recupera as informações de topologia. O ARMclient é usado para chamar a API REST usando o PowerShell. O ARMClient é encontrado no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo recupera a resposta de topologia para um grupo de recursos determinado.

## <a name="log-in-with-armclient"></a>Faça logon com o ARMClient

Faça logon no armclient com suas credenciais do Azure.

```PowerShell
armclient login
```

## <a name="retrieve-topology"></a>Recuperar a topologia

O exemplo a seguir solicita a topologia da API REST.  O exemplo é parametrizado para permitir flexibilidade ao criar um exemplo.  Substitua todos os valores por \< \> ao redor deles.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>" # Resource group name to run topology on
$NWresourceGroupName = "<resource group name>" # Network Watcher resource group name
$networkWatcherName = "<network watcher name>"
$requestBody = @"
{
    'targetResourceGroupName': '${resourceGroupName}'
}
"@

armclient POST "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/topology?api-version=2016-07-01" $requestBody
```

A resposta a seguir é um exemplo de uma resposta reduzida que retorna ao recuperar a topologia para um grupo de recursos:

```json
{
  "id": "ecd6c860-9cf5-411a-bdba-512f8df7799f",
  "createdDateTime": "2017-01-18T04:13:07.1974591Z",
  "lastModified": "2017-01-17T22:11:52.3527348Z",
  "resources": [
    {
      "name": "virtualNetwork1",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{virtualNetworkName}",
      "location": "westcentralus",
      "associations": [
        {
          "name": "{subnetName}",
          "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/(virtualNetworkName)/subnets
/{subnetName}",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "webtestnsg-wjplxls65qcto",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}
s65qcto",
      "associationType": "Associated"
    },
    ...
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber como visualizar os logs de fluxo NSG com o Power BI, veja [Como visualizar logs de fluxos NSG com Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

