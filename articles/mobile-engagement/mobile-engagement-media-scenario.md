---
title: "Implementação do Azure Mobile Engagement para Aplicativos de Mídia"
description: "Cenário de aplicativo de mídia para implementação do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 48201cc8-4e04-485c-a8dc-d6406d23f3ed
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c1591c3e436981e621830916cf0cdc4b7f395d7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="implement-mobile-engagement-with-media-app"></a>Implementar o Mobile Engagement com os Aplicativos de Mídia
## <a name="overview"></a>Visão geral
Pedro é gerente de projetos móveis de uma grande empresa de mídia. Recentemente, ele lançou um novo aplicativo com uma contagem de downloads muito alta. Ele atingiu seus objetivos de download, mas ainda assim seu ROI (Retorno sobre o Investimento) por usuário não atendeu aos seus requisitos. 

Pedro já identificou a causa de seu ROI muito baixo. Com frequência, os usuários param de usar o aplicativo depois de apenas duas semanas e a maioria não volta mais. Ele deseja aumentar a retenção do aplicativo.

Depois de alguns testes iniciais, ele descobriu que quando os usuários são envolvidos por notificações por push, eles tendem a continuar usando seu aplicativo. Além disso, os usuários inativos quase sempre retornarão ao aplicativo, dependendo das notificações enviadas. Pedro decide investir em algum tipo de Programa de Envolvimento para seu aplicativo que use o direcionamento avançado com notificações por push.

Recentemente, Pedro leu [Azure Mobile Engagement - Guia de introdução com as práticas recomendadas](mobile-engagement-getting-started-best-practices.md) e decidiu implementar as recomendações do guia.

## <a name="objectives-and-kpis"></a>Objetivos e KPIs
Há uma reunião com os principais participantes no aplicativo de Pedro. Os negócios são gerados de anúncios à medida que os usuários consomem a mídia. Aumentando o conteúdo consumido por usuário, Pedro aumenta sua receita. Todos concordam com um objetivo principal: aumentar as vendas de anúncios em 25%. Eles criam Indicadores de Desempenho Principais (KPIs) de Negócios para medir e direcionar esse objetivo

* Número de anúncios clicados por usuário
* Quantas páginas de artigo visitadas (por usuário/por sessão/por semana/por mês…)
* Quais são as categorias favoritas

Com base na reunião de Pedro com os participantes principais, ele definiu os KPIs de Negócios. Ele segue a Parte 1 do [Azure Mobile Engagement - Guia de introdução com práticas recomendadas](mobile-engagement-getting-started-best-practices.md). 

Em seguida, cria os seguintes KPIs de Envolvimento para garantir que os objetivos sejam atingidos:

* Monitorar a retenção nos seguintes intervalos: diário, semanal, quinzenal e mensal.
* Contagens de usuários ativos
* A classificação do aplicativo nas lojas de aplicativos

Com base nas recomendações da equipe de TI, os seguintes KPIs Técnicos foram adicionados para responder às seguintes perguntas:

* Qual é o caminho do meu usuário (qual página é visitada, quanto tempo os usuários passam nela)
* Número de falhas ou erros encontrados por sessão?
* Quais versões de sistema operacional meus usuários estão executando?
* Qual é o tamanho médio da tela dos meus usuários?
* Que tipo de conexões com a Internet meus usuários têm?

Para cada KPI, ele classifica os dados necessários e os registra no local apropriado da sua cartilha.

## <a name="engagement-program-and-integration"></a>Programa de envolvimento e integração
Agora que Pedro terminou de definir os KPIs, ele iniciará a fase de estratégia de Envolvimento definindo quatro programas de envolvimento e seus objetivos: ![][1]

Em seguida, Pedro vai mais além, detalhando as notificações por push para cada programa. As notificações por push são definidas por cinco elementos:

1. Objetivo: qual é o objetivo da notificação
2. Como o objetivo será atingido
3. Destino: quem receberá a notificação?
4. Conteúdo: como será o texto e o formato da notificação (no aplicativo/fora do aplicativo)
5. Quando: qual é o melhor momento para enviar a notificação por push
   
    ![][2]

Para saber mais, confira as [Cartilhas](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

De acordo com a segunda parte do white paper, Pedro usa a seção de destino para definir quais dados deseja coletar e escreve seu Plano de Marcas em conjunto com a equipe de TI para implementar a solução. Depois de uma semana de implementação e de testes de aceitação de usuário, Pedro pode finalmente lançar seus programas.

## <a name="program-results"></a>Resultados do programa
Quatro meses depois, Pedro examina o desempenho dos programas. O Programa de Boas-vindas e o Programa Semanal estão atendendo às suas metas. O número de usuários com apenas uma sessão diminui, mais recursos do aplicativo estão sendo usados e o número de conexões por semana dobrou.

O **Programa Inativo** está ajudando Pedro a compreender as tendências de usuário. Parece que 15% dos usuários inativos voltam para o aplicativo. No entanto, a maioria deles não permanece ativa mais de um mês. Pedro prevê uma potencial otimização dessa sequência com notificações adicionais e a expansão de suas opções de conteúdo.

O **Programa Descobrir** não funciona bem. Ele aumenta a venda cruzada mas não o suficiente para atingir seus objetivos. Pedro identifica que ele não tem dados suficientes para fazer um direcionamento relevante e propor o conteúdo apropriado. Ele interrompe o programa e se concentra em "notificações por push editoriais" com o Azure Mobile Engagement. Seus jornalistas já têm uma solução CMS para enviar notificações por push e não desejam alterá-la.

Pedro decide usar a API Reach, que é uma API REST HTTP que permite o gerenciamento de campanhas Reach sem o uso da interface da Web AZME. Com essa abordagem, Pedro pode coletar os dados de que precisa e permitir que seus autores continuem usando a solução CMS.

Para garantir que esse recurso funcione corretamente, Pedro solicita que a equipe de TI esteja atenta aos seguintes pontos:

1. **Sistemas operacionais** : todos eles têm suas próprias regras para administrar as notificações por push e, portanto, Pedro decide listar todos os casos e verificar se as APIs conseguem lidar com eles.
   Por exemplo: o sistema de push do Android permite a visão global, o que não é o caso com o iOS.
2. **Período**: Pedro quer uma API que defina o período e defina um final para as campanhas. Ele deseja preservar os usuários de qualquer bombardeio de notificações que possa gerar interrupções.
3. **Categorias**: a equipe de marketing prepara o modelo de cada tipo de alerta. Pedro solicita que a equipe de TI defina categorias dentro da API.

Depois de alguns testes, Pedro está satisfeito. Graças a essa API, os jornalistas ainda podem enviar notificações por push com o CMS e o Azure Mobile Engagement coleta todos os dados de comportamentos para eles

Depois dos quatro primeiros meses, os resultados refletem um bom desempenho geral e oferece a confiança para Pedro e sua diretoria, o ROI por usuário aumentou em 15% e as vendas móveis representam 17,5% do total de vendas, um aumento de 7,5% em apenas quatro meses.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
