---
title: Exemplos de CLI do Azure – rede
description: Saiba mais sobre exemplos de CLI do Azure de rede, incluindo exemplos de conectividade entre recursos do Azure e exemplos para balanceamento de carga e direção de tráfego.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: c038f0d238646f43b93ba2a2c6a1120ab5feccee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87497283"
---
# <a name="azure-cli-samples-for-networking"></a>Exemplos de CLI do Azure para rede

A tabela a seguir inclui links para bash scripts criados usando a CLI do Azure.

| Script | Descrição |
|-|-|
|**Conectividade entre os recursos do Azure**||
| [Criar uma rede virtual para aplicativos de várias camadas](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com sub-redes de front-end e back-end. O tráfego para a sub-rede de front-end é limitado a HTTP e SSH, enquanto o tráfego para a sub-rede de back-end é limitado a MySQL, porta 3306. |
| [Emparelhar duas redes virtuais](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria e conecta duas redes virtuais na mesma região. |
| [Rotear o tráfego por meio de uma solução de virtualização de rede](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com sub-redes de front-end e back-end e uma VM capaz de rotear o tráfego entre as duas sub-redes. |
| [Filtrar o tráfego de entrada e saída de rede da VM](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede de front-end é limitado a SSH, HTTP e HTTPS. O tráfego de saída para a Internet da sub-rede de back-end não é permitido. |
|**Balanceamento de carga e direção do tráfego**||
| [Balanceamento de carga de vários sites em VMs](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria duas VMs com várias configurações de IP, associadas a um conjunto de disponibilidade do Azure, acessível por meio de um Azure Load Balancer. |
| [Direcionar o tráfego através de várias regiões para alta disponibilidade de aplicativos](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Cria dois planos de serviço de aplicativo, dois aplicativos web, um perfil do Gerenciador de tráfego e dois pontos de extremidade de Gerenciador de tráfego. |
| | |
