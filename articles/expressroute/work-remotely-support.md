---
title: Usando o Azure ExpressRoute para dar suporte a usuários remotos
description: Esta página descreve como você pode aproveitar o Azure ExpressRoute para habilitar o trabalho remotamente devido ao pandemia COVID-19.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/22/2020
ms.author: duau
ms.openlocfilehash: d51f47b73fe572ce81d3e7b54f902f94fcd11b5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89567666"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Usando o Azure ExpressRoute para criar conectividade híbrida para dar suporte a usuários remotos

Este artigo descreve como você pode aproveitar o ExpressRoute, o Azure, o Microsoft Network e o ecossistema de parceiros do Azure para trabalhar remotamente.

## <a name="connecting-to-azure-services-with-expressroute"></a>Conectando aos serviços do Azure com o ExpressRoute

>[!NOTE]
>Este artigo descreve como você pode aproveitar o ExpressRoute, o Azure, o Microsoft Network e o ecossistema de parceiros do Azure para trabalhar remotamente e atenuar os problemas de rede que você está enfrentando devido a crises COVID-19.
>

O [ExpressRoute](expressroute-introduction.md) é um serviço do Azure que permite a conectividade privada entre data centers da Microsoft e a infraestrutura que está em suas instalações ou em uma instalação de colocalização. As conexões de ExpressRoute não passam pela Internet pública. Eles oferecem conectividade segura, confiabilidade e velocidades, com latências menores e consistentes do que conexões típicas pela Internet.

## <a name="useful-links"></a>Links úteis

* [Como aumentar a largura de banda para o circuito de ExpressRoute existente](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [Monitoramento, métricas e alertas do ExpressRoute](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Otimização de rota no ExpressRoute](expressroute-optimize-routing.md)
* [Azure ExpressRoute para Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* [Considerações de roteamento assimétrico](expressroute-asymmetric-routing.md)
* [Como abrir uma solicitação de suporte no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Solucionar problemas

* [Verificar conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* Obtendo tabela ARP no [Gerenciador de recursos](expressroute-troubleshooting-arp-resource-manager.md) e [clássico](expressroute-troubleshooting-arp-classic.md)
* [Redefinir um circuito com falha](reset-circuit.md)
* [Solucionar problemas de desempenho de rede](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os modelos de conectividade do ExpressRoute](expressroute-connectivity-models.md)
* Saiba mais sobre conexões e domínios de roteamento do ExpressRoute. Consulte [circuitos e domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md)
* Encontrar um provedor de serviços. Consulte [parceiros e locais de emparelhamento do ExpressRoute](expressroute-locations.md)
* Certifique-se que todos os pré-requisitos foram atendidos. Consulte [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* [Criar e modificar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
* [Criar e modificar o emparelhamento de um circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Conectar uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
