---
title: Balanceador de carga entre regiões (visualização)
titleSuffix: Azure Load Balancer
description: Visão geral da camada do balanceador de carga entre regiões para Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: 8e14b22895c4734f1efd8688a5b20c946422a080
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225618"
---
# <a name="cross-region-load-balancer-preview"></a>Balanceador de carga entre regiões (visualização)

Azure Load Balancer distribui o tráfego de entrada que chega ao front-end do balanceador de carga para instâncias do pool de back-end.

O Azure Standard Load Balancer dá suporte ao balanceamento de carga entre regiões habilitando cenários de HA com redundância geográfica, como:

* Tráfego de entrada proveniente de várias regiões.
* [Failover global instantâneo](#regional-redundancy) para a próxima implantação regional ideal.
* Carregar a distribuição entre regiões para a região do Azure mais próxima com [latência extremamente baixa](#ultra-low-latency).
* Capacidade de [escalar](#ability-to-scale-updown-behind-a-single-endpoint) verticalmente por trás de um único ponto de extremidade.
* [IP Estático](#static-ip)
* [Preservação de IP do cliente](#client-ip-preservation)
* [Criar uma solução de balanceador de carga existente](#build-cross-region-solution-on-existing-azure-load-balancer) sem curva de aprendizado

> [!IMPORTANT]
> O balanceador de carga entre regiões está atualmente em visualização.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O balanceamento de carga entre regiões oferece os mesmos benefícios de alto desempenho e baixa latência como balanceador de carga Standard regional. 

A configuração de IP de front-end do balanceador de carga entre regiões é estática e anunciada na [maioria das regiões do Azure](#participating-regions).

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="Diagrama de balanceador de carga entre regiões." border="true":::

> [!NOTE]
> A porta de back-end de sua regra de balanceamento de carga no balanceador de carga entre regiões deve corresponder à porta de front-end da regra de NAT de entrada/regra de balanceamento de carga no balanceador de carga Standard regional. 

### <a name="regional-redundancy"></a>Redundância regional

Configure a redundância regional adicionando um endereço IP público de front-end global para os balanceadores de carga existentes. 

Se uma região falhar, o tráfego será roteado para o balanceador de carga regional íntegro mais próximo.  

A investigação de integridade do balanceador de carga entre regiões coleta informações sobre a disponibilidade a cada 20 segundos. Se um balanceador de carga regional descartar sua disponibilidade para 0, o balanceador de carga entre regiões detectará a falha. O balanceador de carga regional é então retirado da rotação. 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="Diagrama da exibição de tráfego de região global." border="true":::

### <a name="ultra-low-latency"></a>Latência ultra baixa

O algoritmo de balanceamento de carga de proximidade geográfica baseia-se na localização geográfica de seus usuários e em suas implantações regionais. 

O tráfego iniciado a partir de um cliente atingirá a região participante mais próxima e viajará pelo backbone da rede global da Microsoft para chegar à implantação regional mais próxima. 

Por exemplo, você tem um balanceador de carga entre regiões com balanceadores de carga padrão em regiões do Azure:

* Oeste dos EUA
* Norte da Europa

Se um fluxo for iniciado a partir de Seattle, o tráfego entrará no oeste dos EUA. Essa região é a região participante mais próxima de Seattle. O tráfego é roteado para o balanceador de carga de região mais próximo, que é o oeste dos EUA.

O balanceador de carga entre regiões do Azure usa o algoritmo de balanceamento de carga de proximidade geográfica para a decisão de roteamento. 

O modo de distribuição de carga configurado dos balanceadores de carga regionais é usado para fazer a decisão de roteamento final quando vários balanceadores de carga regionais são usados para a proximidade geográfica.

Para saber mais, confira [Configurar o modo de distribuição para o Azure Load Balancer](./load-balancer-distribution-mode.md).


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>Capacidade de escalar verticalmente por trás de um único ponto de extremidade

Ao expor o ponto de extremidade global de um balanceador de carga entre regiões para os clientes, você pode adicionar ou remover implantações regionais por trás do ponto de extremidade global sem interrupção. 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>IP Estático
O balanceador de carga entre regiões é fornecido com um IP público estático, o que garante que o endereço IP permaneça o mesmo. Para saber mais sobre o IP estático, leia mais [aqui](../virtual-network/public-ip-addresses.md#allocation-method)

### <a name="client-ip-preservation"></a>Preservação de IP do cliente
O balanceador de carga entre regiões é um balanceador de carga de rede de passagem de camada 4. Essa passagem preserva o IP original do pacote.  O IP original está disponível para o código em execução na máquina virtual. Essa preservação permite que você aplique lógica específica a um endereço IP.

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>Criar solução entre regiões no Azure Load Balancer existente
O pool de back-end do balanceador de carga entre regiões contém um ou mais balanceadores de carga regionais. 

Adicione suas implantações de balanceador de carga existentes a um balanceador de carga entre regiões para uma implantação de alta disponibilidade entre regiões.

A **região de residência** é onde o balanceador de carga entre regiões ou o endereço IP público da camada global é implantado. Essa região não afeta como o tráfego será roteado. Se uma região de residência falhar, o fluxo de tráfego não será afetado.

### <a name="home-regions"></a>Regiões residenciais
* Leste dos EUA 2
* Oeste dos EUA
* Europa Ocidental
* Sudeste Asiático
* Centro dos EUA
* Norte da Europa
* Leste da Ásia

> [!NOTE]
> Você só pode implantar o balanceador de carga entre regiões ou o IP público na camada global em uma das sete regiões acima.

Uma **região participante** é onde o IP público global do balanceador de carga está disponível. 

O tráfego iniciado pelo usuário irá viajar para a região participante mais próxima por meio da rede Microsoft Core. 

O balanceador de carga entre regiões roteia o tráfego para o balanceador de carga regional apropriado.

### <a name="participating-regions"></a>Regiões participantes
* Leste dos EUA 
* Europa Ocidental 
* Centro dos EUA 
* Leste dos EUA 2 
* Oeste dos EUA 
* Norte da Europa 
* Centro-Sul dos Estados Unidos 
* Oeste dos EUA 2 
* Sul do Reino Unido 
* Sudeste Asiático 
* Centro-Norte dos EUA 
* Japan East 
* Leste da Ásia 
* Centro-Oeste dos EUA 
* Australia Southeast 
* Leste da Austrália 
* Índia Central 

## <a name="limitations"></a>Limitações

* As configurações de IP de front-end entre regiões são apenas públicas. No momento, não há suporte para um front-end interno.

* O balanceador de carga interno ou privado não pode ser adicionado ao pool de back-end de um balanceador de carga entre regiões 

* Não há suporte para configurações de IP de front-end IPv6 entre regiões. 

* Uma investigação de integridade não pode ser configurada no momento. Uma investigação de integridade padrão coleta automaticamente informações de disponibilidade sobre o balanceador de carga regional a cada 20 segundos. 

* A integração com o serviço de kubernetes do Azure (AKS) não está disponível no momento. A perda de conectividade ocorrerá ao implantar um balanceador de carga entre regiões com o balanceador de carga padrão com o cluster AKS implantado no back-end.

## <a name="pricing-and-sla"></a>Preço e SLA
O balanceador de carga entre regiões compartilha o [SLA](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) do balanceador de carga padrão.

 
## <a name="next-steps"></a>Próximas etapas

- Consulte [tutorial: criar um balanceador de carga entre regiões usando o portal do Azure](tutorial-cross-region-portal.md) para criar um balanceador de carga entre regiões.
- Consulte [criar um balanceador de carga padrão público](quickstart-load-balancer-standard-public-portal.md) para criar um balanceador de carga regional padrão.
- Saiba mais sobre o [Azure Load Balancer](load-balancer-overview.md).
