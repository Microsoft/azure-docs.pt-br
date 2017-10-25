---
title: "Análise de retenção de usuários para aplicativos Web com o Azure Application Insights | Microsoft Docs"
description: "Quantos usuários retornam ao seu aplicativo?"
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: bwren
ms.openlocfilehash: 7f7ca19ab171278bbd82f68e3822bc650b25373d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Análise de retenção de usuários para aplicativos Web com o Application Insights

O recurso de retenção no [Azure Application Insights](app-insights-overview.md) ajuda a analisar quantos usuários retornam ao seu aplicativo e com que frequência eles executam determinadas tarefas ou atingem metas. Por exemplo, caso gerencie um site de jogos, você pode comparar o número de usuários que retornam ao site após perder um jogo com o número de usuários que retornam após vencer. Esse conhecimento pode ajudar a melhorar a experiência do usuário e sua estratégia de negócios.

## <a name="get-started"></a>Introdução

Caso ainda não veja dados na ferramenta de retenção do portal do Application Insights, [saiba como começar a usar as ferramentas de uso](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>A ferramenta de retenção

![Ferramenta de retenção](./media/app-insights-usage-retention/retention.png)

1. A barra de ferramentas permite aos usuários criar novos relatórios de retenção, abrir os relatórios de retenção existentes, salvar o relatório de retenção atual ou salvar como, reverter as alterações feitas nos relatórios salvos, atualizar dados no relatório, compartilhar relatórios por email ou link direto e acessar a página de documentação. 
2. Por padrão, a retenção mostra todos os usuários que fizeram algo e então voltaram e fizeram outra coisa em um período. Você pode selecionar diferentes combinações de eventos para estreitar o foco em atividades de usuário específico.
3. Adicione um ou mais filtros para as propriedades. Por exemplo, você pode se concentrar em usuários de um determinado país ou região. Clique em **Atualizar** depois de configurar os filtros. 
4. O gráfico de retenção geral mostra um resumo de retenção de usuário entre o período selecionado. 
5. A grade mostra o número de usuários retidos de acordo com o construtor de consultas no nº 2. Cada linha representa um coorte de usuários que realizaram qualquer evento no período mostrado. Cada célula na linha mostra quantas pessoas desse coorte retornaram pelo menos uma vez em um período posterior. Alguns usuários podem retornar em mais de um período. 
6. Os cartões de informações mostram os cinco principais eventos de início e os cinco eventos principais retornados para dar aos usuários uma ideia melhor sobre seus relatórios de retenção. 

![Foco de mouse de retenção](./media/app-insights-usage-retention/hover.png)

Os usuários podem focalize células sobre a ferramenta de retenção para acessar as dicas de ferramenta e o botão de análise explicando o que significa que a célula. Botão análise leva o usuário para a ferramenta de análise com uma consulta preenchida previamente para gerar os usuários da célula. 

## <a name="use-business-events-to-track-retention"></a>Usar eventos de negócios para monitorar a retenção

Para obter a análise de retenção mais útil, meça eventos que representam atividades de negócios significativas. 

Por exemplo, muitos usuários podem abrir uma página em seu aplicativo sem jogar o jogo que ela exibe. Monitorar apenas as exibições de página, portanto, forneceria uma estimativa imprecisa de quantas pessoas retornam para jogar o jogo após tê-lo jogado anteriormente. Para obter uma visão clara dos jogadores que retornam, seu aplicativo deve enviar um evento personalizado quando um usuário de fato jogar.  

É uma boa prática codificar eventos personalizados que representam ações de negócios essenciais e usá-los para sua análise de retenção. Para capturar o resultado do jogo, você precisa escrever uma linha de código para enviar um evento personalizado para o Application Insights. Se você a escrever no código da página da Web ou no Node.JS, ela fica assim:

```JavaScript
    appinsights.trackEvent("won game");
```

Ou no código de servidor do ASP.NET:

```C#
   telemetry.TrackEvent("won game");
```

[Saiba mais sobre como escrever eventos personalizados](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Próximas etapas
- Para habilitar as experiências de uso, comece enviando [eventos personalizados](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [exibições de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se você já envia eventos personalizados ou exibições de página, explore as ferramentas de uso para saber como os usuários utilizam o seu serviço.
    - [Usuários, Sessões, Eventos](app-insights-usage-segmentation.md)
    - [Funis](usage-funnels.md)
    - [Fluxos de Usuário](app-insights-usage-flows.md)
    - [Pastas de trabalho](app-insights-usage-workbooks.md)
    - [Adicionar contexto de usuário](app-insights-usage-send-user-context.md)


