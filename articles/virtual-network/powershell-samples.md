---
title: Exemplos de Azure PowerShell para rede virtual
description: Saiba mais sobre as amostras do Azure PowerShell para gerenciar redes virtuais, incluindo uma amostra para criar uma rede virtual para aplicativos de várias camadas.
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
ms.openlocfilehash: b3107d521fb79e3ea6cfe190abd2ec3fb7859cb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288222"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Exemplos de Azure PowerShell para rede virtual

A tabela a seguir inclui links para scripts de Azure PowerShell:

| Script | Descrição |
|----|----|
| [Criar uma rede virtual para aplicativos de várias camadas](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Cria uma rede virtual com sub-redes de front-end e back-end. O tráfego para a sub-rede de front-end é limitado a HTTP, enquanto o tráfego para a sub-rede de back-end é limitado a SQL, porta 1433. |
| [Emparelhar duas redes virtuais](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Cria e conecta duas redes virtuais na mesma região. |
| [Rotear o tráfego por meio de uma solução de virtualização de rede](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Cria uma rede virtual com sub-redes de front-end e back-end e uma VM capaz de rotear o tráfego entre as duas sub-redes. |
| [Filtrar o tráfego de entrada e saída de rede da VM](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Cria uma rede virtual com sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede de front-end é limitado a HTTP e HTTPS. O tráfego de saída para a Internet da sub-rede de back-end não é permitido. |
|[Configurar rede virtual de pilha dupla IPv4 + IPv6 com o Basic Load Balancer](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Implanta a rede virtual de pilha dual (IPv4 + IPv6) com duas VMs e um Azure Load Balancer básico com endereços IP públicos do IPv4 e IPv6. |
|[Configurar rede virtual de pilha dupla IPv4 + IPv6 com o Standard Load Balancer](./scripts/virtual-network-powershell-sample-ipv6-dual-stack-standard-load-balancer.md)|Implanta a rede virtual de pilha dual (IPv4 + IPv6) com duas VMs e um Azure Standard Load Balancer com endereços IP públicos do IPv4 e IPv6. |
