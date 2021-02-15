---
title: Práticas recomendadas fundamentais da proteção contra DDoS do Azure
description: Conheça as práticas recomendadas de segurança usando a proteção contra DDoS.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 66c1ab1cb5ed478aa34825fb6903e4d06f834097
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989466"
---
# <a name="fundamental-best-practices"></a>Práticas recomendadas fundamentais

A seção a seguir fornece diretrizes descritivas para criar serviços resilientes contra DDoS no Azure.

## <a name="design-for-security"></a>Design para segurança

Garanta que a segurança seja uma prioridade durante todo o ciclo de vida de um aplicativo, desde o design e implementação até a implantação e as operações. Os aplicativos podem ter bugs que permitem que um volume relativamente baixo de solicitações use uma quantidade excessiva de recursos, resultando em uma interrupção de serviço. 

Para ajudar a proteger um serviço em execução no Microsoft Azure, você deve ter uma boa compreensão da arquitetura de seus aplicativos e se concentrar nos [Cinco pilares de qualidade de software](/azure/architecture/guide/pillars).
Você deve conhecer os volumes de tráfego típicos, o modelo de conectividade entre o aplicativo e outros aplicativos e os pontos de extremidade de serviço expostos à Internet pública.

O mais importante é garantir que um aplicativo seja resiliente o suficiente para lidar com uma negação de serviço direcionados ao próprio aplicativo. A segurança e a privacidade estão incorporadas na plataforma do Azure, começando com o [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx). O SDL trata da segurança em cada fase do desenvolvimento e garante que o Azure seja atualizado continuamente para torná-lo ainda mais seguro.

## <a name="design-for-scalability"></a>Design para escalabilidade

A escalabilidade é como um sistema pode tratar aumentos de carga. projetar seus aplicativos para [escalar horizontalmente](/azure/architecture/guide/design-principles/scale-out) para atender à demanda de uma carga amplificada, especificamente em caso de ataque de DDoS. Se seu aplicativo depender de uma única instância de um serviço, ele criará um único ponto de falha. O provisionamento de várias instâncias torna o sistema mais resiliente e mais escalonável.

Para [Azure app serviço](../app-service/overview.md), selecione um [plano do serviço de aplicativo](../app-service/overview-hosting-plans.md) que ofereça várias instâncias. Para Serviços de Nuvem do Azure, configure cada uma das suas funções para usar [várias instâncias](../cloud-services/cloud-services-choose-me.md). Para [Máquinas Virtuais do Azure](../virtual-machines/index.yml), verifique se sua arquitetura de VM inclui mais de uma VM e se cada uma delas está incluída em um [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md). É recomendável usar [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/overview.md) para recursos de dimensionamento automático.

## <a name="defense-in-depth"></a>Proteção completa

A ideia por trás da defesa completa é gerenciar riscos ao usar estratégias de defesa diversificadas. Dispor as defesas de segurança em camadas em um aplicativo reduz a possibilidade de um ataque ser bem-sucedido. É recomendável que você implemente designs seguros para seus aplicativos ao utilizar recursos internos da plataforma Azure.

Por exemplo, o risco de ataque aumenta conforme o tamanho (*área da superfície*) do aplicativo. Você pode reduzir a área da superfície usando uma lista de aprovação para fechar o espaço de endereço IP exposto e portas de escuta que não são necessárias nos balanceadores de carga ([Azure Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md) e [aplicativo Azure gateway](../application-gateway/application-gateway-create-probe-portal.md)). [NSGs (grupos de segurança de rede)](../virtual-network/network-security-groups-overview.md) são outra maneira de reduzir a superfície de ataque.
Você pode usar [marcas de serviço](../virtual-network/network-security-groups-overview.md#service-tags) e [grupos de segurança de aplicativo](../virtual-network/network-security-groups-overview.md#application-security-groups) para minimizar a complexidade da criação de regras de segurança e a configuração da segurança de rede, como uma extensão natural da estrutura do aplicativo.

Você deve implantar os serviços do Azure em uma [rede virtual](../virtual-network/virtual-networks-overview.md) sempre que possível. Esta prática permite que os recursos de serviço se comuniquem por meio de endereços IP privados. O tráfego do serviço do Azure de uma rede virtual usa Endereços IP Públicos como endereços IP de origem por padrão. Usar [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) alterará o tráfego de serviço para usar endereços de rede virtual privados como endereços IP de origem ao acessar o serviço do Azure de uma rede virtual.

Muitos recursos locais dos clientes são atacados juntamente com seus recursos no Azure. Se você estiver conectando um ambiente local ao Azure, recomendamos que você minimize a exposição dos recursos locais à Internet pública. Use as funcionalidades de escala e de proteção contra DDoS avançada do Azure implantando suas entidades públicas conhecidas no Azure. Como essas entidades publicamente acessíveis muitas vezes são alvo de ataques de DDoS, colocá-las no Azure reduz o impacto sobre os recursos locais.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um plano de proteção contra DDoS](manage-ddos-protection.md).