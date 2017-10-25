---
title: "Localizar próximo salto com o Próximo Salto do Observador de Rede do Azure – CLI 1.0 do Azure | Microsoft Docs"
description: "Este artigo descreve como você pode encontrar o que é o tipo do próximo salto e o endereço ip com o próximo salto usando a CLI do Azure."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: e849f7952962d177f40ce99307ef1c305e089827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-10"></a>Descubra qual o tipo do próximo salto é usando o recurso de próximo salto no Observador de Rede do Azure usando a CLI 1.0 do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST do Azure](network-watcher-check-next-hop-rest.md)

O Próximo salto é um recurso do Observador de Rede fornece o capacidade de obter o tipo do próximo salto e o endereço IP com base em uma máquina virtual especificada. Esse recurso é útil para determinar se o tráfego deixar uma máquina virtual atravessa um gateway, internet ou redes virtuais para chegar ao seu destino.

Este artigo usa a CLI 1.0 do Azure para plataforma cruzada, que está disponível para Windows, Mac e Linux.

## <a name="before-you-begin"></a>Antes de começar

Nesse cenário, você usará a CLI do Azure para localizar o tipo do próximo salto e o endereço IP.

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de rede](network-watcher-create.md) para criar um Observador de rede. O cenário também pressupõe que exista um grupo de recursos com uma máquina virtual válida a ser usada.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo usa o próximo salto, um recurso do Observador de Rede que localiza o tipo do próximo salto e o endereço IP para um recurso. Para saber mais sobre o próximo nó, visite [Visão geral do próximo salto](network-watcher-next-hop-overview.md).


## <a name="get-next-hop"></a>Obter o próximo salto

Para obter o próximo salto, chamamos o cmdlet `azure netowrk watcher next-hop`. Passamos o cmdlet do grupo de recursos do Observador de Rede, NetworkWatcher, a máquina virtual Id, endereço IP de origem e endereço IP de destino. Neste exemplo, o endereço IP de destino é uma VM em outra rede virtual. Há um gateway de rede virtual entre as duas redes virtuais. 

```azurecli
azure network watcher next-hop -g resourceGroupName -n networkWatcherName -t targetResourceId -a <source-ip> -d <destination-ip>
```

> [!NOTE]
Se a VM tiver várias NICs e encaminhamento de IP está habilitado em qualquer uma das NICs, em seguida, o parâmetro NIC (-i nic-id) deve ser especificado. Caso contrário, será opcional.

## <a name="review-results"></a>Analisar resultados

Ao concluir, os resultados são fornecidos. O próximo endereço IP for retornado, bem como o tipo de recurso é.

```
data:    Next Hop Ip Address             : 10.0.1.2
info:    network watcher next-hop command OK
```

A lista a seguir mostra os valores de NextHopType disponíveis no momento:

**Tipo do próximo salto**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Nenhum

## <a name="next-steps"></a>Próximas etapas

Aprenda a analisar as configurações de Grupo de Segurança de Rede por meio de programação visitando [NSG auditoria com o Observador de Rede](network-watcher-nsg-auditing-powershell.md)
