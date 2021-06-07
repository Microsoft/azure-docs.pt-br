---
title: Recuperação de desastre geográfico do Azure Spring Cloud | Microsoft Docs
description: Saiba como proteger o aplicativo Spring Cloud contra interrupções regionais
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: e18193b40bb7c59a21a279f451673dc7d11140e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92092895"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Recuperação de desastre no Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

Este artigo explica algumas estratégias para proteger seus aplicativos do Azure Spring Cloud contra tempo de inatividade.  Qualquer região ou data center pode enfrentar tempo de inatividade causado por desastres regionais, mas o planejamento cuidadoso pode reduzir o impacto sobre seus clientes.

## <a name="plan-your-application-deployment"></a>Planejar a implantação do seu aplicativo

Os aplicativos do Azure Spring Cloud são executados em uma região específica.  O Azure opera em várias regiões geográficas em todo o mundo. Uma geografia do Azure é uma área definida do mundo que contém, pelo menos, uma Região do Azure. Uma região do Azure é uma área dentro de uma região geográfica que contém um ou mais datacenters.  Cada região do Azure é emparelhada com outra região na mesma área geográfica, formando juntas um par regional. O Azure atualiza as plataformas em série (manutenção planejada) para garantir que apenas uma região em cada par seja atualizada por vez. Em caso de interrupção que afete várias regiões, pelo menos uma região em cada par será priorizada para recuperação.

Para garantir a HA e a proteção contra desastres, você precisa implantar seus aplicativos Spring Cloud em várias regiões.  O Azure oferece uma lista de [regiões emparelhadas](../best-practices-availability-paired-regions.md) para que você possa planejar suas implantações do Spring Cloud levando isso em consideração.  Recomendamos considerar três fatores principais ao projetar sua arquitetura de microsserviço: disponibilidade de região, regiões emparelhadas do Azure e disponibilidade de serviço.

*  Disponibilidade de região:  Escolha uma área geográfica perto dos seus usuários para minimizar a latência da rede e o tempo de transmissão.
*  Regiões emparelhadas do Azure:  Escolha regiões emparelhadas dentro da área geográfica escolhida para garantir atualizações de plataforma coordenadas e prioridade nos esforços de recuperação, se necessário.
*  Disponibilidade do serviço:   Decida se suas regiões emparelhadas devem ser executadas em hot/hot, hot/warm ou hot/cold.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usar o Gerenciador de Tráfego do Azure para rotear o tráfego

O [Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md) oferece balanceamento de carga de tráfego baseado em DNS e pode distribuir tráfego de rede em várias regiões.  Use o Gerenciador de Tráfego do Azure para direcionar os clientes à instância de serviço do Azure Spring Cloud mais próxima deles.  Para obter melhor desempenho e redundância, direcione todo o tráfego do aplicativo por meio do Gerenciador de Tráfego do Azure antes de enviá-lo para seu serviço Azure Spring Cloud.

Se você tiver aplicativos do Azure Spring Cloud em várias regiões, use o Gerenciador de Tráfego do Azure para controlar o fluxo de tráfego para seus aplicativos em cada região.  Defina um ponto de extremidade do Gerenciador de Tráfego do Azure para cada serviço usando o IP do serviço. Os clientes devem se conectar a um nome DNS do Gerenciador de Tráfego do Azure que aponta para o serviço Azure Spring Cloud.  O Gerenciador de Tráfego do Azure equilibra o tráfego entre os pontos de extremidade definidos.  Se um desastre ocorrer um data center, o Gerenciador de Tráfego do Azure direcionará o tráfego dessa região para seu par, o que garantirá a continuidade do serviço.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Criar Gerenciador de Tráfego do Azure para Azure Spring Cloud

1. Crie o Azure Spring Cloud em duas regiões diferentes.
Você precisará implantar duas instâncias de serviço do Azure Spring Cloud em duas regiões diferentes (Leste dos EUA e Oeste da Europa). Inicie um aplicativo existente do Azure Spring Cloud com o portal do Azure para criar duas instâncias de serviço. Cada uma servirá como ponto de extremidade primário e de failover para o tráfego. 

**Informações das duas instâncias de serviço:**

| Nome do Serviço | Location | Aplicativo |
|--|--|--|
| service-sample-a | Leste dos EUA | gateway / auth-service / account-service |
| service-sample-b | Europa Ocidental | gateway / auth-service / account-service |

2. Configure o domínio personalizado para o serviço. Siga o [Documento de domínio personalizado](spring-cloud-tutorial-custom-domain.md) para configurar o domínio personalizado para essas duas instâncias de serviço. Após serem configuradas corretamente, ambas as instâncias de serviço serão associadas ao domínio personalizado: bcdr-test.contoso.com

3. Crie um gerenciador de tráfego e dois pontos de extremidade: [Crie um perfil do Gerenciador de Tráfego com o portal do Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md).

Este é o perfil do gerenciador de tráfego:
* Nome do DNS do Gerenciador de Tráfego: `http://asc-bcdr.trafficmanager.net`
* Perfis de ponto de extremidade: 

| Perfil | Type | Destino | Prioridade | Configurações personalizadas de cabeçalho |
|--|--|--|--|--|
| Perfil do ponto de extremidade A | Ponto de extremidade externo | service-sample-a.asc-test.net | 1 | host: bcdr-test.contoso.com |
| Perfil do ponto de extremidade B | Ponto de extremidade externo | service-sample-b.asc-test.net | 2 | host: bcdr-test.contoso.com |

4. Crie um registro CNAME na zona de DNS: bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net. 

5. Agora, o ambiente está completamente configurado. Os clientes devem conseguir acessar o aplicativo via: bcdr-test.contoso.com

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Implantar seu primeiro aplicativo do Azure Spring Cloud](spring-cloud-quickstart.md)
