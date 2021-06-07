---
title: Gerenciador de Tráfego do Azure| Microsoft Docs
description: Este artigo fornece uma visão geral do Gerenciador de Tráfego do Azure. Descubra se ele é a escolha certa para balancear a carga do tráfego de usuário para seu aplicativo.
services: traffic-manager
author: duongau
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2021
ms.author: duau
ms.openlocfilehash: 8c8897218b153c8584c89abab98934268ccd555d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182155"
---
# <a name="what-is-traffic-manager"></a>O que é o Gerenciador de Tráfego?
O Gerenciador de Tráfego do Azure é um balanceador de carga de tráfego baseado no DNS. Esse serviço permite distribuir o tráfego para seus aplicativos voltados ao público nas regiões globais do Azure. O Gerenciador de Tráfego também fornece alta disponibilidade e rápida capacidade de resposta aos seus pontos de extremidade públicos.

O Gerenciador de Tráfego usa o DNS para direcionar as solicitações do cliente para o ponto de extremidade de serviço apropriado com base em um método de roteamento de tráfego. O Gerenciador de Tráfego também fornece monitoramento de integridade para cada ponto de extremidade. O ponto de extremidade pode ser qualquer serviço para a Internet hospedado dentro ou fora do Azure. O Gerenciador de Tráfego oferece uma variedade de [métodos de roteamento de tráfego](traffic-manager-routing-methods.md) e [opções de monitoramento de ponto de extremidade](traffic-manager-monitoring.md) para atender às diferentes necessidades dos aplicativos e modelos de failover automático. O Gerenciador de Tráfego é resistente a falhas, incluindo a falha de toda a região do Azure.

>[!NOTE]
> O Azure fornece um conjunto de soluções de balanceamento de carga totalmente gerenciadas para seus cenários. 
> * Se você quiser balancear a carga entre os servidores em uma região na camada de aplicativo, examine o [Gateway de Aplicativo](../application-gateway/overview.md).
> * Se você precisar otimizar o roteamento global do seu tráfego da Web e otimizar o desempenho e a confiabilidade do usuário final de nível superior por meio de um failover global rápido, confira [Front Door](../frontdoor/front-door-overview.md).
> * Para fazer o balanceamento de carga de camada de rede, examine [Load Balancer](../load-balancer/load-balancer-overview.md). 
> 
> Os cenários de ponta a ponta podem se beneficiar da combinação dessas soluções, conforme necessário.
> Para obter uma comparação das opções de balanceamento de carga do Azure, confira [Visão geral das opções de balanceamento de carga no Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

O Gerenciador de Tráfego oferece os seguintes recursos:

## <a name="increase-application-availability"></a>Aumentar a disponibilidade de aplicativo

O Gerenciador de Tráfego fornece alta disponibilidade para seus aplicativos cruciais ao monitorar seus pontos de extremidade e fornecer failover automático quando um ponto de extremidade fica inativo.
    
## <a name="improve-application-performance"></a>Melhorar o desempenho de aplicativos

O Azure permite executar serviços de nuvem e sites em datacenters localizados no mundo todo. O Gerenciador de Tráfego pode aprimorar a capacidade de resposta do seu site direcionando o tráfego para o ponto de extremidade com a menor latência.

## <a name="service-maintenance-without-downtime"></a>Manutenção de serviço sem tempo de inatividade

Você pode executar a manutenção planejada nos seus aplicativos sem tempo de inatividade. O Gerenciador de Tráfego pode direcionar tráfego para pontos de extremidade alternativos enquanto a manutenção está em andamento.

## <a name="combine-hybrid-applications"></a>Combinar aplicativos híbridos

O Gerenciador de Tráfego tem suporte para pontos de extremidade externos não do Azure habilitando seu uso com implantações locais e de nuvem híbrida, incluindo os cenários de [intermitência para a nuvem](https://azure.microsoft.com/overview/what-is-cloud-bursting/), “migrar para a nuvem” e “failover para a nuvem”.

## <a name="distribute-traffic-for-complex-deployments"></a>Distribuir o tráfego para implantações complexas

Usando [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md), vários métodos de roteamento de tráfego podem ser combinados para criar regras sofisticadas e flexíveis para escalar a necessidades de implantações maiores e mais complexas.

## <a name="pricing"></a>Preços

Para obter informações sobre preço, consulte [Preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um perfil do Gerenciador de Tráfego](./quickstart-create-traffic-manager-profile.md).
- Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-how-it-works.md).
- Exibir [perguntas frequentes](traffic-manager-FAQs.md) sobre o Gerenciador de Tráfego.