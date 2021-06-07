---
title: Componentes de uma estratégia de resposta a DDoS
description: Saiba como usar a proteção contra DDoS do Azure padrão para responder a ataques de DDoS.
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
ms.openlocfilehash: 2b31a8aa8b126c228ac7e9c3ca182300c710b098
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97814050"
---
# <a name="components-of-a-ddos-response-strategy"></a>Componentes de uma estratégia de resposta a DDoS

Um ataque de DDoS que tem como alvo recursos do Azure normalmente requer intervenção mínima do ponto de vista do usuário. Ainda assim, incorporar a mitigação de DDoS como parte da estratégia de resposta a incidentes ajudará a minimizar o impacto na continuidade de negócios.

## <a name="microsoft-threat-intelligence"></a>Inteligência contra ameaças da Microsoft

A Microsoft tem uma ampla rede de inteligência sobre ameaças. Essa rede usa o conhecimento coletivo de uma comunidade de segurança estendida que dá suporte aos serviços online da Microsoft, parceiros da Microsoft e relações da comunidade de segurança da Internet. 

Como um provedor de infraestrutura crítico, a Microsoft recebe avisos antecipados sobre ameaças. A Microsoft reúne inteligência sobre ameaças de seus serviços online e de sua base de clientes global. A Microsoft incorpora toda essa inteligência contra ameaças em seus produtos de Proteção contra DDoS do Azure.

Além disso, a DCU (Unidade de Crimes Digitais da Microsoft) executa estratégias ofensivas contra botnets. Botnets são uma fonte comum de comando e controle de ataques de DDoS.

## <a name="risk-evaluation-of-your-azure-resources"></a>Avaliação de risco dos seus recursos do Azure

É fundamental compreender o escopo do seu risco a ataques de DDoS continuamente. Periodicamente, pergunte-se:

- Quais novos recursos do Azure disponíveis publicamente precisam de proteção?

- Há um ponto único de falha no serviço? 

- Como os serviços podem ser isolados para limitar o impacto de um ataque e ainda disponibilizá-los para os clientes válidos?

- Há redes virtuais em que a Proteção contra DDoS Standard deve ser habilitada, mas ainda não está? 

- Meu serviços são ativo/ativo com failover em várias regiões?

É essencial que você compreenda o comportamento normal de um aplicativo e se prepare para agir se o aplicativo não estiver se comportando conforme o esperado durante um ataque de DDoS. Ter monitores configurados para seus aplicativos críticos para os negócios que imitam o comportamento do cliente e notificá-lo quando anomalias relevantes forem detectadas. Consulte as [práticas recomendadas de monitoramento e diagnóstico](/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios) para obter informações sobre a integridade do seu aplicativo.

O [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) é um serviço de gerenciamento de desempenho de aplicativo (APM) extensível para desenvolvedores da Web em várias plataformas. Use o Application Insights para monitorar seu aplicativo Web em tempo real. Ele detecta anomalias de desempenho automaticamente. Ele inclui ferramentas de análise para ajudá-lo a diagnosticar problemas e a entender o que os usuários fazem com seu aplicativo. Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo.

## <a name="customer-ddos-response-team"></a>Equipe de resposta a DDoS do cliente

Criar uma equipe de resposta a DDoS é uma etapa importante para responder a um ataque de forma rápida e eficiente. Identifique vários contatos na organização que supervisionarão o planejamento e a execução. Essa equipe de resposta de DDoS deve compreender totalmente o serviço de Proteção contra DDoS do Azure Standard. Certifique-se de que a equipe possa identificar e eliminar um ataque através da coordenação com clientes internos e externos, incluindo a equipe de Suporte da Microsoft. 

Recomendamos que você use os exercícios de simulação como uma parte normal da disponibilidade do seu serviço e do planejamento de continuidade, e esses exercícios devem incluir testes de escala. Consulte [testar por meio de simulações](test-through-simulations.md) para saber como simular o tráfego de teste de DDoS em seus pontos de extremidade públicos do Azure.

## <a name="alerts-during-an-attack"></a>Alertas durante um ataque

A Proteção contra DDoS do Azure Standard identificará e mitigará os ataques de DDoS sem qualquer intervenção do usuário. Para ser notificado quando houver uma mitigação ativa para um IP público protegido, você poderá [configurar alertas](alerts.md).

### <a name="when-to-contact-microsoft-support"></a>Quanto entrar em contato com o Suporte da Microsoft

Os clientes padrão da proteção contra DDoS do Azure têm acesso à equipe de reação rápida a DDoS (DRR), que pode ajudar na investigação de ataques durante um ataque, bem como a análise após o ataque. Consulte [resposta rápida de DDoS](ddos-rapid-response.md) para obter mais detalhes, incluindo quando você deve envolver a equipe DRR.

## <a name="post-attack-steps"></a>Etapas de pós-ataques

Sempre é uma boa estratégia realizar uma análise posterior ao ataque e reajustar a estratégia de resposta a DDoS conforme necessário. Itens a serem considerados:

- Ocorreu alguma interrupção no serviço ou na experiência do usuário devido à falta de arquitetura escalonável?

- Quais aplicativos ou serviços mais sofreram?

- O quão eficaz foi a estratégia de resposta a DDoS e como ela pode ser aprimorada?

Se você suspeitar que está sofrendo um ataque de DDoS, escale a questão por meio dos canais de suporte normais do Azure.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um plano de proteção contra DDoS](manage-ddos-protection.md).