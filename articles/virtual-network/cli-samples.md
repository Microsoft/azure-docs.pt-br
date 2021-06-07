---
title: Exemplos de CLI do Azure para rede virtual
description: Saiba mais sobre scripts de exemplo diversos que você pode usar para concluir tarefas na CLI do Azure, incluindo a criação de uma rede virtual para aplicativos de várias camadas.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 97d63598f6a9cca199779e56cc211b9622117301
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048826"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Exemplos de CLI do Azure para rede virtual

A tabela a seguir inclui links para bash scripts com comandos de CLI do Azure:

| Script | Descrição |
|----|----|
| [Criar uma rede virtual para aplicativos de várias camadas](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Cria uma rede virtual com sub-redes de front-end e back-end. O tráfego para a sub-rede de front-end é limitado a HTTP e SSH, enquanto o tráfego para a sub-rede de back-end é limitado a MySQL, porta 3306. |
| [Emparelhar duas redes virtuais](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Cria e conecta duas redes virtuais na mesma região. |
| [Rotear o tráfego por meio de uma solução de virtualização de rede](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Cria uma rede virtual com sub-redes de front-end e back-end e uma VM capaz de rotear o tráfego entre as duas sub-redes. |
| [Filtrar o tráfego de entrada e saída de rede da VM](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Cria uma rede virtual com sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede de front-end é limitado a HTTP, HTTPS e SSH. O tráfego de saída para a Internet da sub-rede de back-end não é permitido. |
|[Configurar rede virtual de pilha dupla IPv4 + IPv6 com o Basic Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Implanta a rede virtual de pilha dual (IPv4 + IPv6) com duas VMs e um Azure Load Balancer básico com endereços IP públicos do IPv4 e IPv6. |
|[Configurar rede virtual de pilha dupla IPv4 + IPv6 com o Standard Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Implanta a rede virtual de pilha dual (IPv4 + IPv6) com duas VMs e um Azure Standard Load Balancer com endereços IP públicos do IPv4 e IPv6. |
|[Tutorial: Criar e testar um Gateway da NAT – CLI do Azure](./tutorial-create-nat-gateway-cli.md)|Crie e valide um Gateway da NAT usando uma máquina virtual de origem e de destino. |