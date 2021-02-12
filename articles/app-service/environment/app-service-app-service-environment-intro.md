---
title: Introdução ao ASE v1
description: Saiba mais sobre os recursos do Ambiente do Serviço de Aplicativo v1. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 878cef39ade0487c03dba83c9e3631fe3c72088f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008610"
---
# <a name="introduction-to-app-service-environment-v1"></a>Introdução ao Ambiente do Serviço de Aplicativo v1

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1.  Há uma versão mais recente do Ambiente do Serviço de Aplicativo que é mais fácil de usar e é executada em infraestrutura mais avançada. Para saber mais sobre a nova versão, comece com a [introdução ao ambiente do serviço de aplicativo](intro.md).

## <a name="overview"></a>Visão geral

Um Ambiente do Serviço de Aplicativo é uma opção de plano de serviço [Premium][PremiumTier] do [serviço de Azure app](../overview.md) que fornece um ambiente totalmente isolado e dedicado para execução segura de aplicativos de serviço do Azure app em alta escala, incluindo aplicativos Web, aplicativos móveis e aplicativos de API.  

Os Ambientes de Serviço de Aplicativo são ideais para cargas de trabalho de aplicativos que exigem:

* Escala muito alta
* Isolamento e acesso seguro à rede

Os clientes podem criar vários Ambientes de Serviço de Aplicativo dentro de uma única região do Azure, bem como entre várias regiões do Azure.  Isso faz dos Ambientes de Serviço de Aplicativo ideais para dimensionar horizontalmente camadas de aplicativo sem estado para dar suporte a cargas de trabalho RPS altas.

Ambientes de Serviço de Aplicativo são isolados para executar somente aplicativos de um único cliente, e sempre são implantados em uma rede virtual.  Os clientes têm um controle refinado sobre o tráfego de entrada e saída da rede de aplicativos, e os aplicativos podem estabelecer conexões seguras de alta velocidade por meio de redes virtuais com recursos corporativos locais.

Para obter uma visão geral de como os ambientes do serviço de aplicativo permitem o acesso de alta escala e segurança à rede, consulte o aprofundamento [AzureCon][AzureConDeepDive] nos ambientes do serviço de aplicativo!

Para uma análise aprofundada sobre o dimensionamento horizontal usando vários Ambientes de Serviço de Aplicativo, veja o artigo sobre como configurar uma [área de aplicativo distribuído geograficamente][GeodistributedAppFootprint].

Para ver como a arquitetura de segurança exibida no AzureCon Deep Dive foi configurada, consulte o artigo sobre a implementação de uma [arquitetura de segurança em camadas](app-service-app-service-environment-layered-security.md) com os Ambientes do Serviço de Aplicativo.

Os aplicativos executados nos Ambientes do Serviço de Aplicativo podem ter seu acesso restrito por dispositivos upstream como WAF (firewalls do aplicativo Web).  O artigo sobre como [configurar um WAF para Ambientes do Serviço de Aplicativo](app-service-app-service-environment-web-application-firewall.md) aborda esse cenário.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Recursos de computação dedicados

Todos os recursos de computação em um Ambiente de Serviço de Aplicativo são dedicados exclusivamente a uma única assinatura, e um Ambiente de Serviço de Aplicativo pode ser configurado com até 50 (cinquenta) recursos de computação para uso exclusivo por um único aplicativo.

Um Ambiente de Serviço de Aplicativo é composto de um pool de recursos de computação de front-end, bem como um a três pools de recursos de computação de trabalho.

O pool de front-end contém recursos de computação responsáveis pela terminação de TLS, bem como balanceamento de carga automático de solicitações de aplicativo dentro de um Ambiente do Serviço de Aplicativo.

Cada pool de trabalho contém recursos de computação alocados para [Planos do Serviço de Aplicativo][AppServicePlan] que, por sua vez, contêm um ou mais aplicativos de Serviço de Aplicativo do Azure.  Como pode haver até três pools de trabalho diferentes em um ambiente de serviço de aplicativo, você tem a flexibilidade de escolher os recursos de computação diferentes para cada pool de trabalho.  

Por exemplo, isso permite a criação de um pool de trabalho com menos recursos de computação poderosos para Plano do Serviço de Aplicativo destinados a aplicativos de desenvolvimento ou teste.  Um segundo (ou até mesmo terceiro) pool de trabalho de pode usar recursos de computação mais poderosos para planos de serviço de aplicativo executando aplicativos de produção.

Para obter mais detalhes sobre a quantidade de recursos de computação disponíveis para os pools de front-end e de trabalho, confira [Como configurar um Ambiente do Serviço de Aplicativo][HowToConfigureanAppServiceEnvironment].  

Para obter detalhes sobre os tamanhos de recursos de computação disponíveis com suporte em um Ambiente do Serviço de Aplicativo, veja a página [Preço do Serviço de Aplicativo][AppServicePricing] e revise as opções disponíveis para Ambientes do Serviço de Aplicativo no tipo de preço Premium.

## <a name="virtual-network-support"></a>Suporte a Rede Virtual

Um Ambiente do Serviço de Aplicativo pode ser criado **ou** em uma rede virtual do Azure Resource Manager, **ou** em uma rede virtual do modelo de implantação clássico ([mais informações sobre redes virtuais][MoreInfoOnVirtualNetworks]).  Como um Ambiente de Serviço de Aplicativo sempre existe em uma rede virtual, mais precisamente em uma sub-rede de uma rede virtual, você pode aproveitar os recursos de segurança de redes virtuais para controlar tanto a comunicação de rede de entrada quanto a de saída.  

Um Ambiente de Serviço de Aplicativo pode ser voltado para a Internet com um endereço IP público ou voltado para o interior com apenas um endereço ILB (Balanceador de Carga Interno).

Você pode usar [grupos de segurança de rede][NetworkSecurityGroups] para restringir a comunicação de rede de entrada à sub-rede na qual reside um Ambiente do Serviço de Aplicativo.  Isso permite que você execute aplicativos por trás de dispositivos e serviços upstream, como firewalls de aplicativo Web e provedores SaaS de rede.

Aplicativos frequentemente precisam acessar recursos corporativos, como bancos de dados internos e serviços da Web.  Uma abordagem comum é disponibilizar esses pontos de extremidade apenas ao tráfego de rede interno que flui dentro de uma rede virtual do Azure.  Depois que um Ambiente do Serviço de Aplicativo é adicionado à mesma rede virtual que os serviços internos, os aplicativos que são executados no ambiente podem acessá-los, incluindo pontos de extremidade acessíveis via conexões [Site a Site][SiteToSite] e [Azure ExpressRoute][ExpressRoute].

Para obter mais detalhes sobre como os Ambientes do Serviço de Aplicativo funcionam com redes virtuais e redes locais, confira os artigos a seguir sobre [Arquitetura de rede][NetworkArchitectureOverview], [Controle do tráfego de entrada][ControllingInboundTraffic] e [Conexão segura a back-ends][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Introdução

Para se familiarizar com os Ambientes do Serviço de Aplicativo, confira [Como criar um Ambiente do Serviço de Aplicativo][HowToCreateAnAppServiceEnvironment]

Para obter uma visão geral da arquitetura de rede do Ambiente do Serviço de Aplicativo, veja o artigo [Visão geral da arquitetura de rede][NetworkArchitectureOverview].

Para obter detalhes sobre como usar um Ambiente do Serviço de Aplicativo com o ExpressRoute, confira o artigo a seguir sobre [Ambientes do ExpressRoute e Serviço de Aplicativo][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: ../../virtual-network/virtual-networks-faq.md
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: ../../logic-apps/logic-apps-overview.md
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->