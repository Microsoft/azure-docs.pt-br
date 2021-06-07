---
title: SKUs do Azure Load Balancer
description: Visão geral dos SKUs do Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: 874ecfc8c1c50816916fb0b04975477a1cbe0a71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94698080"
---
# <a name="azure-load-balancer-skus"></a>SKUs do Azure Load Balancer

O Azure Load Balancer tem duas SKUs.

## <a name="sku-comparison"></a><a name="skus"></a> Comparação de SKUs

O Load Balancer dá suporte a SKUs Standard e Basic. Há diferenças de SKU na escala, nos recursos e no preço do cenário. Qualquer cenário possível com o Basic Load Balancer pode ser criado com o Standard Load Balancer.

Veja a tabela a seguir para comparar e entender as diferenças. Para saber mais, confira [Visão geral do Standard Load Balancer](./load-balancer-overview.md).

>[!NOTE]
> A Microsoft recomenda o Standard Load Balancer.
VMs autônomas, conjuntos de disponibilidade e conjuntos de dimensionamento de máquina virtual podem estar conectados apenas a um SKU, nunca a ambos. Os SKUs do Load Balancer e do endereço IP público devem ser correspondentes quando você os usa com endereços IP públicos. Os SKUs do Load Balancer e do IP público não são mutáveis.

| | Standard Load Balancer | Load Balancer Básico |
| --- | --- | --- |
| **[Tamanho do pool de back-end](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)** | Dá suporte a até 1.000 instâncias. | Dá suporte a até 300 instâncias. |
| **Pontos de extremidade de pool de back-end** | Máquinas virtuais ou conjuntos de dimensionamento de máquinas virtuais em uma rede virtual única. | Máquinas virtuais em um conjunto de disponibilidade ou conjunto de dimensionamento da máquina virtual. |
| **[Investigações de integridade](./load-balancer-custom-probe-overview.md#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Comportamento de investigação de integridade inoperante](./load-balancer-custom-probe-overview.md#probedown)** | As conexões TCP permanecem ativas com uma instância de investigação inoperante __e__ todas as investigações inoperantes. | As conexões TCP permanecem ativas com uma instância de investigação inoperante. Todas as conexões TCP terminam quando todas as investigações estão inativas. |
| **Zonas de Disponibilidade** | Front-ends com zonas e redundância de zonas para tráfego de entrada e de saída. | Não disponível |
| **Diagnóstico** | [Métricas multidimensionais do Azure Monitor](./load-balancer-standard-diagnostics.md) | [Logs do Azure Monitor](./load-balancer-monitor-log.md) |
| **Portas de HA** | [Disponível para o Balanceador de Carga Interno](./load-balancer-ha-ports-overview.md) | Não disponível |
| **Seguro por padrão** | Fechado para fluxos de entrada, a menos que permitido por um grupo de segurança de rede. O tráfego interno da rede virtual para o balanceador de carga interno é permitido. | Abertos por padrão. Grupo de segurança de rede opcional. |
| **Regras de saída** | [Configuração de NAT de saída declarativa](./load-balancer-outbound-connections.md#outboundrules) | Não disponível |
| **Redefinição de TCP quando ocioso** | [Disponível em qualquer regra](./load-balancer-tcp-reset.md) | Não disponível |
| **[Vários front-ends](./load-balancer-multivip-overview.md)** | Entrada e [saída](./load-balancer-outbound-connections.md) | Somente entrada |
| **Operações de gerenciamento** | Maioria das operações < 30 segundos | 60-90+ segundos típicos |
| **SLA** | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Não disponível | 

Para saber mais, confira [Limites do balanceador de carga](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer). Para obter detalhes sobre o Load Balancer Standard, confira [visão geral](./load-balancer-overview.md), [preços](https://aka.ms/lbpricing) e [SLA](https://aka.ms/lbsla).

## <a name="limitations"></a>Limitações

- Os SKUs não são mutáveis. Você não pode alterar o SKU de um recurso existente.
- Um recurso de máquina virtual padrão, recurso de conjunto de disponibilidade ou recurso de conjunto de dimensionamento da máquina virtal podem referenciar um SKU, nunca ambos.
- [Operações de movimentação](../azure-resource-manager/management/move-resource-group-and-subscription.md):
  - **Há compatibilidade** com as operações de movimentação de grupo de recursos (na mesma assinatura) para o Standard Load Balancer e o IP Público Padrão. 
  - **Não** há compatibilidade com [operações de movimentação de grupo de subscrições](../azure-resource-manager/management/move-support-resources.md) nos recursos Standard Load Balancer e IP Público Padrão.

## <a name="next-steps"></a>Próximas etapas

- Confira [Criar um Standard Load Balancer público](quickstart-load-balancer-standard-public-portal.md) para começar a usar um Load Balancer.
- Saiba mais sobre usar o [Standard Load Balancer e Zonas de Disponibilidade](load-balancer-standard-availability-zones.md).
- Saiba mais sobre [Investigações de Integridade](load-balancer-custom-probe-overview.md).
- Saiba mais sobre como usar o [Azure Load Balancer para conexões de saída](load-balancer-outbound-connections.md).
- Saiba mais sobre [o Standard Load Balancer com as regras de balanceamento das Portas de Alta Disponibilidade](load-balancer-ha-ports-overview.md).
- Saiba mais sobre [Grupos de Segurança de Rede](../virtual-network/network-security-groups-overview.md).