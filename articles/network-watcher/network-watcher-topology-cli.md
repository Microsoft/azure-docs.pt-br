---
title: "Exibição da topologia do Observador de rede do Azure - CLI do Azure | Microsoft Docs"
description: Este artigo descreve como usar a CLI do Azure para consultar a sua topologia de rede.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 654c23e567d81bff1cb0c3091ba3d8f96f0a3eda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="view-network-watcher-topology-with-azure-cli"></a>Como exibir a topologia do Observador de rede com a CLI do Azure

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [API REST](network-watcher-topology-rest.md)

O recurso de topologia do Observador de rede fornece uma representação visual dos recursos de rede em uma assinatura. No portal, essa visualização é apresentada para você automaticamente. As informações que seguem a visualização de topologia no portal podem ser recuperadas por meio do PowerShell.
Esse recurso torna as informações de topologia mais versáteis, já que os dados podem ser consumidos por outras ferramentas para criar a visualização.

Este artigo usa a CLI 1.0 do Azure para plataforma cruzada, que está disponível para Windows, Mac e Linux. Atualmente, o Observador de Rede usa a CLI 1.0 do Azure para dar suporte à CLI.

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

Nesse cenário, você deve usar o cmdlet `network watcher topology` para recuperar as informações de topologia. Também há um artigo sobre [Como recuperar a topologia de rede com a REST API](network-watcher-topology-rest.md).

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo recupera a resposta de topologia para um grupo de recursos determinado.

## <a name="retrieve-topology"></a>Como recuperar a topologia

O cmdlet `network watcher topology` recupera a topologia para um grupo de recursos determinado. Adicione o argumento "--json" para exibir a saída no formato json

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>Resultados

Os resultados retornados têm uma propriedade de nome "Recursos", que contêm o corpo da resposta json para o cmdlet `network watcher topology`.  A resposta contém os recursos no grupo de segurança de rede e suas associações (ou seja, Contém e Associado).

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as regras de segurança que são aplicadas aos recursos de rede no artigo [Visão geral de exibição do grupo de segurança](network-watcher-security-group-view-overview.md)
