---
title: Taxa de transferência de rede da máquina virtual do Azure | Microsoft Docs
description: Saiba mais sobre a taxa de transferência da rede de máquinas virtuais do Azure, incluindo como a largura de banda é alocada para uma máquina virtual
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: cb128f9269895f04d1e0dad8e0c8d06c481e86c6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576156"
---
# <a name="virtual-machine-network-bandwidth"></a>Largura de banda de rede da máquina virtual

O Azure oferece uma variedade de tamanhos e tipos de VM, cada um com uma combinação diferente de funcionalidades de desempenho. Uma funcionalidade é a taxa de transferência (ou largura de banda) de rede, medida em megabits por segundo (Mbps). Como as máquinas virtuais são hospedadas em um hardware compartilhado, a capacidade de rede deve ser compartilhada adequadamente entre as máquinas virtuais que compartilham o mesmo hardware. Máquinas virtuais maiores recebem relativamente mais largura de banda que máquinas virtuais menores.
 
A largura de banda de rede alocada a cada máquina virtual é limitada no tráfego de saída (saída) da máquina virtual. Todo o tráfego de rede que deixa a máquina virtual é contado para o limite alocado, independentemente do destino. Por exemplo, se uma máquina virtual tem um limite de 1.000 Mbps, esse limite se aplica se o tráfego de saída é destinado a outra máquina virtual na mesma rede virtual ou fora do Azure.
 
A entrada não é limitada diretamente. No entanto, há outros fatores, como limites de CPU e armazenamento, que podem afetar a capacidade de uma máquina virtual de processar dados de entrada.

Rede acelerada é um recurso projetado para melhorar o desempenho de rede, incluindo latência, taxa de transferência e utilização da CPU. Embora a rede acelerada possa melhorar a taxa de transferência de uma máquina virtual, ela poderá fazer isso somente até a largura de banda alocada da máquina virtual. Para saber mais sobre a Rede Acelerada, consulte Rede acelerada para máquinas virtuais do [Windows](create-vm-accelerated-networking-powershell.md) ou do [Linux](create-vm-accelerated-networking-cli.md).
 
As máquinas virtuais do Azure devem ter um, mas podem ter vários adaptadores de rede anexados a elas. A largura de banda alocada a uma máquina virtual é a soma de todo o tráfego de saída em todos os adaptadores de rede anexados a uma máquina virtual. Em outras palavras, a largura de banda alocada é por máquina virtual, independentemente de quantos adaptadores de rede estão anexados à máquina virtual. Para saber para quantos adaptadores de rede diferentes tamanhos de VM do Azure dão suporte, consulte Tamanhos de VM [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure. 

## <a name="expected-network-throughput"></a>Taxa de transferência de rede esperada

A taxa de transferência de saída esperada e o número de adaptadores de rede com suporte em cada tamanho de VM são detalhados em Tamanhos de VM [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure. Selecione um tipo, como Uso geral e, em seguida, uma série de tamanho na página resultante, como a série Dv2. Cada série tem uma tabela com as especificações de rede na última coluna intitulada **Máx. de NICs/Desempenho de rede esperado (Mbps)**. 

O limite de taxa de transferência se aplica à máquina virtual. A taxa de transferência não é afetada pelos seguintes fatores:
- **Número de adaptadores de rede**: o limite de largura de banda é cumulativo de todo o tráfego de saída da máquina virtual.
- **Rede acelerada**: embora o recurso possa ser útil para alcançar o limite publicado, ele não altera o limite.
- **Destino do tráfego**: todos os destinos contam para o limite de saída.
- **Protocolo**: todo o tráfego de saída em todos os protocolos contam para o limite.

## <a name="network-flow-limits"></a>Limites de fluxo de rede

Além da largura de banda, o número de conexões de rede presentes em uma VM em um determinado momento pode afetar o desempenho da rede. A pilha de rede do Azure mantém o estado para cada direção de uma conexão TCP/UDP em estruturas de dados chamadas ' flows '. Uma conexão TCP/UDP típica terá 2 fluxos criados, um para a entrada e outro para a direção de saída. 

A transferência de dados entre pontos de extremidade requer a criação de vários fluxos além daqueles que executam a transferência de dados. Alguns exemplos são fluxos criados para resolução de DNS e fluxos criados para investigações de integridade do balanceador de carga. Observe também que as NVAs (soluções de virtualização de rede), como gateways, proxies, firewalls, verão os fluxos que estão sendo criados para conexões encerradas no dispositivo e originadas pelo dispositivo. 

![Contagem de fluxo para conversa TCP por meio de um dispositivo de encaminhamento](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-active-connections-recommendations"></a>Limites de fluxo e recomendações de conexões ativas

Hoje, a pilha de rede do Azure dá suporte a 1mb total de fluxos (500 mil de entrada e 500 mil de saída) para uma VM. As conexões ativas totais que podem ser tratadas por uma VM em cenários diferentes são as seguintes.
- As VMs que pertencem à VNET podem manipular conexões 500 mil ***ativas** _ para todos os tamanhos de VM com fluxos de 500 mil _ *_ativos em cada direção_* *.  
- VMs com NVAs (soluções de virtualização de rede), como gateway, proxy, firewall, podem lidar com **conexões ativas** 250 mil * _ com *_fluxos de 500 mil _ ativos em cada direção_** devido ao encaminhamento e à criação de novo fluxo adicional na configuração de nova conexão para o próximo salto, conforme mostrado no diagrama acima. 

Quando esse limite for atingido, conexões adicionais serão descartadas. As taxas de estabelecimento e término da conexão também podem afetar o desempenho da rede, pois o estabelecimento da conexão e o encerramento compartilham a CPU com rotinas de processamento de pacotes É recomendável que você compare as cargas de trabalho com os padrões de tráfego esperados e escale horizontalmente as cargas de trabalho de acordo com suas necessidades de desempenho.

As métricas estão disponíveis em [Azure monitor](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines) para controlar o número de fluxos de rede e a taxa de criação de fluxo em suas instâncias de VM ou VMSS.

![Captura de tela mostra a página métricas de Azure Monitor com um gráfico de linhas e totais para fluxos de entrada e saída.](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

As taxas de estabelecimento e término da conexão também podem afetar o desempenho da rede, pois o estabelecimento da conexão e o encerramento compartilham a CPU com rotinas de processamento de pacotes É recomendável que você compare as cargas de trabalho com os padrões de tráfego esperados e escale horizontalmente as cargas de trabalho de acordo com suas necessidades de desempenho. 

## <a name="next-steps"></a>Próximas etapas

- [Otimizar a taxa de transferência de rede para um sistema operacional da máquina virtual](virtual-network-optimize-network-bandwidth.md)
- [Testar a taxa de transferência de rede](virtual-network-bandwidth-testing.md) de uma máquina virtual.