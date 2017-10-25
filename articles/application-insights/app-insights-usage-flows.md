---
title: "Analisar padrões de navegação do usuário com o Fluxos de Usuário no Azure Application Insights | Microsoft Docs"
description: "Analise como os usuários navegam entre as páginas e recursos de seu aplicativo Web."
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 08/02/2017
ms.author: cfreeman
ms.openlocfilehash: d17ed3dff08f00a1d6a2108608e42b29f95fbd84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analisar padrões de navegação do usuário com o Fluxos de Usuário no Azure Application Insights

![Ferramenta Fluxos de Usuário do Application Insights](./media/app-insights-usage-flows/flows.png)

A ferramenta Fluxos de Usuário visualiza como os usuários navegam entre as páginas e recursos de seu site. Isso é ótimo para responder a perguntas como:
* Como os usuários navegam para outra página em seu site?
* Em que os usuários clicam em uma página em seu site?
* Quais são os locais com maior rotatividade pelos usuários em seu site?
* Há locais em que os usuários repetem a mesma ação repetidamente?

A ferramenta Fluxos de Usuário começa de uma exibição de página ou um evento inicial que você especificar. Dada essa exibição de página ou evento personalizado, o Fluxos de Usuário mostra as exibições de página e eventos personalizados que os usuários enviaram imediatamente depois e durante uma sessão, duas etapas depois e assim por diante. Linhas de espessura variada mostram quantas vezes cada caminho foi seguido por usuários. Nós especiais de "Sessão finalizada" mostram quantos usuários não enviaram exibições de página nem eventos personalizados após o nó anterior, realçando o ponto em que os usuários provavelmente deixaram seu site.



> [!NOTE]
> O recurso do Application Insights deve conter exibições de página ou eventos personalizados para usar a ferramenta Fluxos de Usuário. [Saiba como configurar seu aplicativo para coletar exibições de página automaticamente com o SDK do JavaScript do Application Insights](app-insights-javascript.md).
> 
> 

## <a name="start-by-choosing-an-initial-page-view-or-custom-event"></a>Comece escolhendo uma exibição de página ou um evento personalizado inicial

![Escolha um evento inicial para o Fluxos de Usuário](./media/app-insights-usage-flows/flows-initial-event.png)

Para começar a responder perguntas com a ferramenta Fluxos de Usuário, escolha uma exibição de página ou um evento personalizado inicial para servir como o ponto de partida para a visualização:
1. Clique no link no título "O que os usuários fazem depois de...?" ou clique no botão Editar. 
2. Selecione uma exibição de página ou evento personalizado no menu suspenso "Evento inicial".
3. Clique em "Criar gráfico".

A coluna "Etapa 1" da visualização mostra o que os usuários as ações mais frequentes dos usuários logo após o evento inicial, ordenadas de cima para baixo, da mais para a menos frequente. A coluna "Etapa 2" e as colunas subsequentes mostram que os usuários fizeram depois disso, criando uma imagem de todas as maneiras em que os usuários navegam por meio de seu site.

Por padrão, a ferramenta Fluxos de Usuário faz uma amostragem aleatória apenas das últimas 24 horas de exibições de página e eventos personalizados do seu site. No menu Editar, você pode aumentar o intervalo de tempo e alterar o equilíbrio entre desempenho e precisão para amostragem aleatória.

Se alguns dos eventos personalizados e modos de exibição de página não são relevantes para você, clique no "X" nos nós que você deseja ocultar. Depois de selecionar os nós que você deseja ocultar, clique no botão "Criar gráfico" abaixo da visualização. Para ver todos os nós que você ocultou, clique no botão Editar e, em seguida, examine a seção "Eventos excluídos".

Se as exibições de página ou eventos personalizados que você espera ver na visualização estão ausentes:
* Verifique a seção "Eventos excluídos" no menu Editar.
* Use o controle de "Nível de detalhe" no menu Editar para incluir eventos menos frequentes na visualização.
* Se a exibição de página ou evento personalizado que você espera é enviado com pouca frequência pelos usuários, tente aumentar o intervalo de tempo da visualização no menu Editar.
* Verifique se a exibição de página ou evento personalizado que você espera está configurado para ser coletado pelo SDK do Application Insights no código-fonte do seu site. [Saiba mais sobre como coletar eventos personalizados.](app-insights-api-custom-events-metrics.md)

Se você quiser ver mais etapas na visualização, use o controle deslizante de "Número de etapas" no menu Editar.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Depois de visitar uma página ou um recurso, para onde os usuários vão e no que eles clicam?

![Usar Fluxos de Usuário para entender em que os usuários clicam](./media/app-insights-usage-flows/flows-one-step.png)

Se o evento inicial é uma exibição de página, a primeira coluna ("Etapa 1") da visualização é uma maneira rápida de entender o que os usuários fizeram imediatamente após visitarem a página. Tente abrir o site em uma janela ao lado da visualização do Fluxos de Usuário. Compare suas expectativas de como os usuários interagem com a página à lista de eventos na coluna "Etapa 1". Geralmente, um elemento de interface do usuário na página que parece insignificante para a sua equipe pode estar entre os mais usados na página. Isso pode ser um excelente ponto de partida para aprimoramentos do design de seu site.

Se o evento inicial é um evento personalizado, a primeira coluna mostra o que os usuários fizeram logo após executarem essa ação. Assim como acontece com as exibições de página, considere se o comportamento dos usuários observado corresponde às expectativas e metas da sua equipe. Se o evento inicial selecionado for "Adicionar Item ao Carrinho de Compras", por exemplo, verifique se "Ir para Check-out" e "Compra Concluída" aparecem logo depois na visualização. Se o comportamento do usuário é muito diferente de suas expectativas, use a visualização para entender como os usuários estão sendo "capturados" pelo design atual do seu site.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Quais são os locais com maior rotatividade pelos usuários em seu site?

Fique atento aos nós "Sessão finalizada" que aparecem no alto de uma coluna na visualização, especialmente no início de um fluxo. Isso significa que muitos usuários provavelmente saíram do seu site depois de seguir o caminho precedente de páginas e interações de interface do usuário. Algumas vezes a rotatividade é esperada – depois de concluir uma compra em um site de comércio eletrônico, por exemplo – mas geralmente a rotatividade é um sinal de problemas de design, baixo desempenho ou outros problemas com seu site, os quais podem ser melhorados.

Tenha em mente que nós "Sessão finalizada" são baseados apenas na telemetria coletada por esse recurso do Application Insights. Se o Application Insights não receber a telemetria para determinadas interações do usuário, os usuários poderão ainda ter interagido com seu site dessas maneiras depois que a ferramenta Fluxos de Usuário diz que a sessão foi finalizada.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Há locais em que os usuários repetem a mesma ação repetidamente?

Procure uma exibição de página ou um evento personalizado que é repetido por muitos usuários em etapas subsequentes na visualização. Geralmente, isso significa que os usuários estão executando ações repetitivas no seu site. Se você encontrar a repetição, considere alterar o design de seu site ou adicionar novas funcionalidades para reduzir a repetição. Por exemplo, adicione a funcionalidade de edição em massa se você encontrar usuários executando ações repetitivas em cada linha de um elemento de tabela.

## <a name="common-questions"></a>Perguntas comuns

### <a name="why-do-steps-appear-disconnected"></a>Por que as etapas aparecem desconectadas?

![Fluxos de Usuário com etapas desconectadas](./media/app-insights-usage-flows/flows-disconnected.png)

Se as etapas (colunas) em uma visualização do Fluxos de Usuário estão desconectadas, isso significa que nenhum dos caminhos executados por usuários entre as etapas foi frequente o bastante para ser mostrado. Para mostrar eventos menos frequentes na visualização de modo que as etapas apareçam conectadas, ajuste o controle deslizante de "Nível de detalhe" no menu Editar.

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>O evento inicial representa a primeira vez em que o evento aparece em uma sessão ou qualquer momento em que ele aparece em uma sessão?

O evento inicial em visualização representa apenas a primeira vez que um usuário enviou essa exibição de página ou evento personalizado durante uma sessão. Se os usuários podem enviar o evento inicial várias vezes em uma sessão, a coluna "Etapa 1" mostra apenas como os usuários se comportam após a *primeira* instância do evento inicial e não de todas as instâncias.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do uso](app-insights-usage-overview.md)
* [Usuários, Sessões e Eventos](app-insights-usage-segmentation.md)
* [Retenção](app-insights-usage-retention.md)
* [Adicionando eventos personalizados ao aplicativo](app-insights-api-custom-events-metrics.md)