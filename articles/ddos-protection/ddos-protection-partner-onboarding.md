---
title: Parceria com a proteção contra DDoS do Azure Standard
description: Entenda as oportunidades de parceria habilitadas pelo padrão de proteção contra DDoS do Azure.
ms.service: ddos-protection
documentationcenter: na
author: yitoh
mms.devlang: na
ms.topic: how-to
ms.date: 08/28/2020
ms.author: kumud
ms.openlocfilehash: a7a21dcaea22ed0d1c077d0c42ddb5b4bd50d24f
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539325"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Parceria com a proteção contra DDoS do Azure Standard
Este artigo descreve as oportunidades de parceria habilitadas pelo padrão de proteção contra DDoS do Azure. Este artigo foi projetado para ajudar gerentes de produto e funções de desenvolvimento de negócios a entender os caminhos de investimento e fornecer informações sobre as propostas de valor de parceria.

## <a name="background"></a>Tela de fundo
Ataques de DDoS (negação de serviço distribuído) são uma das principais preocupações de disponibilidade e segurança em voz por clientes que movem seus aplicativos para a nuvem. Com o extortion e o Hacktivism sendo as motivações comuns por trás dos ataques de DDoS, eles foram consistentemente aumentando em tipo, escala e frequência de ocorrência, pois são relativamente fáceis e baratos de iniciar.

A proteção contra DDoS do Azure fornece medidas defensivas contra as ameaças de DDoS mais sofisticadas, aproveitando a escala global da rede do Azure. O serviço fornece recursos aprimorados de mitigação de DDoS para aplicativos e recursos implantados em redes virtuais.

Os parceiros de tecnologia podem proteger os recursos dos clientes nativamente com a proteção contra DDoS do Azure Standard para resolver as preocupações de disponibilidade e confiabilidade devido a ataques de DDoS.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Introdução ao padrão de proteção contra DDoS do Azure
A proteção contra DDoS do Azure Standard fornece recursos aprimorados de mitigação de DDoS contra ataques de DDoS de camada 3 e camada 4. A seguir estão os principais recursos do serviço Standard de proteção contra DDoS.

### <a name="adaptive-real-time-tuning"></a>Ajuste adaptável em tempo real
Para cada aplicativo protegido, a proteção contra DDoS do Azure Standard ajusta automaticamente os limites da política de mitigação de DDoS com base nos padrões de perfil de tráfego do aplicativo. O serviço realiza essa personalização usando dois insights:

- Aprendizado automático de padrões de tráfego por cliente (por IP) das camadas 3 e 4.
- Minimizando falsos positivos, considerando que a escala do Azure permite que absorva uma quantidade significativa de tráfego.

![Ajuste de tempo real adaptável](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Análise de ataques, telemetria, monitoramento e alertas
A proteção contra DDoS do Azure identifica e atenua ataques de DDoS sem nenhuma intervenção do usuário.

- Se o recurso protegido estiver na assinatura coberta pela central de segurança do Azure, a proteção contra DDoS Standard enviará automaticamente um alerta para a central de segurança sempre que um ataque de DDoS for detectado e atenuado em relação ao aplicativo protegido.
- Como alternativa, para ser notificado quando houver uma mitigação ativa para um IP público protegido, você pode [configurar um alerta](alerts.md) na métrica sob ataque de DDoS ou não.
- Além disso, você pode optar por criar alertas para outras métricas de DDoS e [Configurar a telemetria de ataque](telemetry.md) para entender a escala do ataque, o tráfego que está sendo eliminado, vetores de ataque, principais colaboradores e outros detalhes.

![Métricas de DDoS](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>Resposta rápida a DDoS (DRR)
Os clientes padrão de proteção contra DDoS têm acesso à [equipe de resposta rápida](ddos-rapid-response.md) durante um ataque ativo. O DRR pode ajudar na investigação de ataques durante um ataque, bem como a análise após o ataque.

### <a name="sla-guarantee-and-cost-protection"></a>Garantia de SLA e proteção de custo
O serviço padrão de proteção contra DDoS é coberto por um SLA de 99,99% e a proteção de custo fornece créditos de recursos para escalar horizontalmente durante um ataque documentado. Para obter mais informações, consulte [SLA para proteção contra DDoS do Azure](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Cenários de parceiros em destaque
Veja a seguir os principais benefícios que você pode derivar integrando-se com o padrão de proteção contra DDoS do Azure:

- Os serviços oferecidos pelos parceiros (balanceador de carga, firewall do aplicativo Web, firewall, etc.) para seus clientes são automaticamente protegidos (com rótulos de branco) pela proteção contra DDoS do Azure Standard no back-end.
- Os parceiros têm acesso à análise de ataque padrão da proteção contra DDoS do Azure e à telemetria que eles podem integrar com seus próprios produtos, oferecendo uma experiência de cliente unificada.  
- Os parceiros têm acesso ao suporte de resposta rápida de DDoS mesmo na ausência da resposta rápida do Azure, para problemas relacionados a DDoS.
- Os aplicativos protegidos pelos parceiros são apoiados por uma garantia de SLA de DDoS e proteção de custos no caso de ataques de DDoS.

## <a name="technical-integration-overview"></a>Visão geral da integração técnica
As oportunidades de parceiro padrão da proteção contra DDoS do Azure são disponibilizadas por meio de portal do Azure, APIs e CLI/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Integrar com a proteção contra DDoS Standard
As etapas a seguir são necessárias para que os parceiros configurem a integração com a proteção contra DDoS do Azure Standard:
1. Crie um plano de proteção contra DDoS em sua assinatura desejada (de parceiro). Para obter as instruções passo a passo, consulte [criar um plano de proteção padrão de DDoS](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Somente 1 plano de proteção contra DDoS precisa ser criado para um determinado locatário. 
2. Implante um serviço com um ponto de extremidade público em suas assinaturas do (parceiro), como balanceador de carga, firewalls e firewall do aplicativo Web. 
3. Habilite a proteção contra DDoS do Azure Standard na rede virtual do serviço que tem pontos de extremidade públicos usando o plano de proteção contra DDoS criado na primeira etapa. Para obter instruções STPE, consulte [habilitar o plano de proteção padrão DDoS](manage-ddos-protection.md#enable-ddos-protection-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > Depois que a proteção contra DDoS do Azure Standard estiver habilitada em uma rede virtual, todos os IPs públicos dentro dessa rede virtual serão protegidos automaticamente. A origem desses IPs públicos pode estar no Azure (assinatura do cliente) ou fora do Azure. 
4. Opcionalmente, integre a telemetria padrão da proteção contra DDoS do Azure e a análise de ataques no painel voltado para o cliente específico do aplicativo. Para obter mais informações sobre como usar a telemetria, consulte [Exibir e configurar a telemetria de proteção contra DDoS](telemetry.md). 

### <a name="onboarding-guides-and-technical-documentation"></a>Guias de integração e documentação técnica

- [Página do produto proteção contra DDoS do Azure](https://azure.microsoft.com/services/ddos-protection/)
- [Documentação da proteção contra DDoS do Azure](ddos-protection-overview.md)
- [Referência da API de proteção contra DDoS do Azure](/rest/api/virtualnetwork/ddosprotectionplans)
- [Referência da API de rede virtual do Azure](/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Obter ajuda

- Se você tiver dúvidas sobre integrações de aplicativos, serviços ou produtos com a proteção contra DDoS do Azure Standard, entre em contato com a [comunidade de segurança do Azure](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security).
- Siga as discussões em [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Acesse o mercado

- O principal programa para a parceria com a Microsoft é a [Microsoft Partner Network](https://partner.microsoft.com/). – Microsoft Graph integrações de segurança se enquadram na faixa [ISV (fornecedor independente de software) MPN](https://partner.microsoft.com/saas-solution-guide) .
- A [Associação de segurança inteligente da Microsoft](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) é o programa especificamente para parceiros de segurança da Microsoft para ajudar a enriquecer seus produtos de segurança e a melhorar a descoberta do cliente de suas integrações para produtos de segurança da Microsoft.

## <a name="next-steps"></a>Próximas etapas
Exibir integrações de parceiros existentes:

- [Barracuda WAF como serviço](https://www.barracuda.com/waf-as-a-service)
- [Azure cloud WAF da RADWARE](https://www.radware.com/resources/microsoft-azure/)
