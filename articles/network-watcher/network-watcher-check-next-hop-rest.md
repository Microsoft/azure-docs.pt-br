---
title: "Localizar próximo salto com o Próximo Salto do Observador de Rede do Azure - REST | Microsoft Docs"
description: "Este artigo descreve como você pode encontrar o que é o tipo do próximo salto e o endereço ip com o próximo salto usando a API REST do Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b541cd5cb7e49468af2c522b16c3a3b9fe75fd54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>Descubra qual o tipo do próximo salto é usando o recurso de próximo salto no Observador de Rede do Azure usando a API REST do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST do Azure](network-watcher-check-next-hop-rest.md)

Próximo salto é um recurso do Observador de Rede fornece o capacidade de obter o tipo do próximo salto e o endereço IP com base em uma máquina virtual especificada. Esse recurso é útil para determinar se o tráfego deixar uma máquina virtual atravessa um gateway, internet ou redes virtuais para chegar ao seu destino.

## <a name="before-you-begin"></a>Antes de começar

O ARMclient é usado para chamar a API REST usando o PowerShell. O ARMClient é encontrado no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo usa o próximo salto, um recurso do Observador de Rede que localiza o tipo do próximo salto e o endereço IP para um recurso. Para saber mais sobre o próximo nó, visite [Visão geral do próximo salto](network-watcher-next-hop-overview.md).

Neste cenário, você:

* Recuperará o próximo salto de uma máquina virtual.

## <a name="log-in-with-armclient"></a>Faça logon com o ARMClient

Faça logon no armclient com suas credenciais do Azure.

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Recuperar uma máquina virtual

Execute o script a seguir para retornar uma máquina virtual. Essas informações são necessárias para a execução do próximo salto.

O código a seguir precisa de valores para as variáveis a seguir:

- **subscriptionId** - A Id da assinatura a ser usada.
- **resourceGroupName** - o nome de um grupo de recursos que contém as máquinas virtuais.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Na saída a seguir, a id da máquina virtual é usada no exemplo a seguir:

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>Obter o próximo salto

Depois de criar o cabeçalho de autorização, o próximo salto de uma máquina virtual pode ser recuperado. Os valores a seguir devem ser substituídos para que o exemplo de código funcione.

> [!Important]
> Para chamadas da API REST do Observador de Rede o nome do grupo de recursos no URI da solicitação é o grupo de recursos que contém o Observador de Rede, não os recursos nos quais você está executando as ações de diagnóstico.

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> O próximo salto exige a alocação do recurso de VM para ser executado.

## <a name="results"></a>Resultados

O trecho a seguir é um exemplo da saída recebida. Os resultados contêm os valores a seguir:

* **nextHopType** - Esse valor pode ser um dos seguintes valores: Internet, VirtualAppliance, VirtualNetworkGateway, VnetLocal, HyperNetGateway ou Nenhum.
* **nextHopIpAddress** - o endereço IP do próximo salto.
* **routeTableId** - O valor é um uri para a tabela de rotas associada à rota, ou se nenhuma rota for definida pelo usuário o valor *Rota do Sistema* retornará.

Veja a seguir os resultados no formato json.

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>Próximas etapas

Depois que você conseguir descobrir o próximo salto para uma máquina virtual, exiba a segurança de seus recursos de rede visitando [Visão geral do modo de segurança](network-watcher-security-group-view-overview.md)














