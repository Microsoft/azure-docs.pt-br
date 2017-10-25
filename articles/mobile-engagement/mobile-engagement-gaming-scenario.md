---
title: "Implementação do Azure Mobile Engagement para Aplicativo de Jogo"
description: "Cenário de aplicativo de jogo para implementação do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2cafc044-4902-4058-8037-49399bf6bf7f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0ca35a3d634db8eb5c63afacba046a35b8a3e7ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="implement-mobile-engagement-with-gaming-app"></a>Implementar o Mobile Engagement com o Aplicativo de Jogos
## <a name="overview"></a>Visão geral
Uma empresa de jogos em fase inicial lançou um novo aplicativo de jogo de pescar baseado em role-play/estratégia. O jogo está em execução por 6 meses. Este jogo é um enorme sucesso e tem milhões de downloads e a retenção é muito alta em comparação com outros aplicativos de jogos de empresas em fase inicial. Na reunião de análise trimestral, os participantes concordam que precisam aumentar a receita média por usuário (ARPU). Pacotes de jogo premium estão disponíveis como ofertas especiais. Esses pacotes de jogos permitem aos usuários atualizar a aparência e o desempenho de suas linhas e iscas ou equipamentos de pesca no jogo. No entanto, as vendas dos pacotes são muito baixas. Então, eles decidem primeiro analisar a experiência do cliente com uma ferramenta de análise e desenvolver um programa de envolvimento para aumentar as vendas usando segmentação avançada.

Com base no [Azure Mobile Engagement - Guia de Introdução com Práticas Recomendadas](mobile-engagement-getting-started-best-practices.md) eles compilam uma estratégia de envolvimento.

## <a name="objectives-and-kpis"></a>Objetivos e KPIs
Principais participantes dos jogos. Todos concordam em um objetivo principal - aumentar as vendas de pacotes premium em 15%. Eles criam Indicadores Chave de Desempenho (KPIs) de Negócios para medir e impulsionar esse objetivo

* Em que nível do jogo esses pacotes são comprados?
* Qual é a receita por usuário, por sessão, semanal e mensal?
* Quais são os tipos de compra favoritos?

A Parte 1 do [Guia de Introdução](mobile-engagement-getting-started-best-practices.md) explica como definir os KPIs e os objetivos. 

Com os KPIs de Negócios já definidos, o Gerente de Produtos Móveis cria KPIs de Envolvimento para determinar a retenção e novas tendências de usuários.

* Monitorar a retenção e usá-la nos seguintes intervalos: diariamente, a cada 2 dias, semanalmente, mensalmente e a cada 3 meses
* Contagens de usuários ativos
* A classificação do aplicativo na loja

Com base nas recomendações da equipe de TI, os seguintes KPIs técnicos foram adicionados para responder às seguintes perguntas:

* Qual é o caminho do meu usuário (qual página é visitada, quanto tempo os usuários passam nela)
* Número de falhas ou erros encontrados por sessão
* Quais versões de sistema operacional meus usuários estão executando?
* Qual é o tamanho médio da tela dos meus usuários?
* Que tipo de conectividade com a Internet meus usuários têm?

Para cada KPI, a Gerente de Produtos Móveis especifica os dados necessários e onde eles estão localizados em seu guia estratégico.

## <a name="engagement-program-and-integration"></a>Integração e programa de envolvimento
Antes de criar um programa de envolvimento avançado, o Diretor de Projetos Móveis responsável pelo projeto deve ter uma compreensão profunda de como e quando os produtos são consumidos pelos usuários.

Depois de três meses, o Diretor de Projetos Móveis coletou dados suficientes para aprimorar suas vendas de notificação por push no aplicativo. Ele descobre que:

* A primeira compra geralmente ocorre no nível 14. Em 90% dos casos, a compra é de novas armas lendárias por US$ 3.
* Em 80% dos casos, os usuários que fizeram uma compra, mantêm o produto e fazem mais compras.
* Os usuários que passaram do nível 20, começam a gastar mais de US$ 10 por semana.
* Os usuários tendem a comprar pacotes premium no nível 16, 24 e 32.

Graças a essa análise, o Diretor de Projetos Móveis decide criar sequências específicas de notificação por push para aumentar as vendas no aplicativo. Ele cria três sequências de push que ele chama de: Programa de Boas-vindas, Programa de Vendas e o Programa Inativo. Para saber mais, confira as [Cartilhas](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
