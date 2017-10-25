---
title: Investigar e compartilhar dados de uso com Workbooks interativas no Azure Application Insights | Microsoft Docs
description: "Análise demográfica dos usuários de seu aplicativo Web."
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: bwren
ms.openlocfilehash: 75028b4fbda43d90f56690a33c7eb624fce049c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Investigar e compartilhar dados de uso com pastas de trabalho interativas no Application Insights

As pastas de trabalho combinam as visualizações de dados do [Azure Application Insights](app-insights-overview.md), as [consultas do Analytics](app-insights-analytics.md) e textos em documentos interativos. As pastas de trabalho são editáveis por outros membros da equipe com acesso ao mesmo recurso do Azure. Isso significa que as consultas e os controles usados para criar uma pasta de trabalho estão disponíveis para outras pessoas que estão lendo a pasta de trabalho, facilitando a exploração, extensão e verificação de erros.

As pastas de trabalho são úteis para cenários como:

* Exploração do uso do aplicativo quando você não sabe as métricas de interesse antecipadamente: números de usuários, taxas de retenção, taxas de conversão, etc. Ao contrário de outras ferramentas de análise de uso do Application Insights, as pastas de trabalho permitem combinar vários tipos de visualizações e análises, tornando-as excelentes para esse tipo de exploração de forma livre.
* Explicar para sua equipe sobre o desempenho de um recurso recém-liberado, mostrando contagens de usuário para as principais interações e outras métricas.
* Compartilhar os resultados de um experimento A/B no aplicativo com outros membros de sua equipe. Você pode explicar as metas do experimento com um texto e, depois, mostrar cada métrica de uso e consulta do Analytics usada para avaliar o experimento, junto com textos explicativos claros que indicam se cada métrica estava acima ou abaixo da meta.
* Relatar o impacto de uma interrupção no uso do aplicativo, combinando dados, explicação de texto e uma discussão das próximas etapas para prevenir interrupções no futuro.

> [!NOTE]
> O recurso do Application Insights deve conter exibições de página ou eventos personalizados para usar as pastas de trabalho. [Saiba como configurar seu aplicativo para coletar exibições de página automaticamente com o SDK do JavaScript do Application Insights](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Editando, reorganizando, clonando e excluindo seções da pasta de trabalho

Uma pasta de trabalho é composta por seções: visualizações de uso editáveis de forma independente, gráficos, tabelas, textos ou resultados de consultas do Analytics.

Para editar o conteúdo de uma seção da pasta de trabalho, clique no botão **Editar** abaixo e à direita da seção da pasta de trabalho.

![Controles de edição da seção Pastas de Trabalho do Application Insights](./media/app-insights-usage-workbooks/editing-controls.png)

1. Quando terminar de editar uma seção, clique em **Edição Concluída** no canto inferior esquerdo da seção.

2. Para criar uma duplicata de uma seção, clique no ícone **Clonar esta seção**. Criar seções duplicadas é uma ótima forma de iterar em uma consulta sem perder as iterações anteriores.

3. Para mover uma seção para cima em uma pasta de trabalho, clique no ícone **Mover para cima** ou **Mover para baixo**.

4. Para remover uma seção permanentemente, clique no ícone **Remover**.

## <a name="adding-usage-data-visualization-sections"></a>Adicionando seções de visualização de dados de uso

As pastas de trabalho oferecem quatro tipos de visualizações internas de análise de uso. Cada uma delas responde a uma pergunta comum sobre o uso do aplicativo. Para adicionar tabelas e gráficos além dessas quatro seções, adicione seções de consulta do Analytics (veja abaixo).

Para adicionar uma seção Usuários, Sessões, Eventos ou Retenção à pasta de trabalho, use o botão **Adicionar Usuários** ou outro botão correspondente na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

![Seção Usuários no Workbooks](./media/app-insights-usage-workbooks/users-section.png)

As seções **Usuários** respondem à pergunta “Quantos usuários exibiram alguma página ou usaram algum recurso de meu site?”

As seções **Sessões** respondem à pergunta “Quantas sessões os usuários gastaram exibindo alguma página ou usando algum recurso de meu site?”

As seções **Eventos** respondem à pergunta “Quantas vezes os usuários exibiram alguma página ou usaram algum recurso de meu site?”

Cada um desses três tipos de seção oferece os mesmos conjuntos de controles e visualizações:

* [Saiba mais sobre como editar as seções Usuários, Sessões e Eventos](app-insights-usage-segmentation.md)
* Ative/desative o gráfico principal, as grades de histograma, as informações automáticas e as visualizações de usuários de exemplo usando as caixas de seleção **Mostrar Gráfico**, **Mostrar Grade**, **Mostrar Informações** e **Amostra Destes Usuários** na parte superior de cada seção.

![Seção Retenção no Workbooks](./media/app-insights-usage-workbooks/retention-section.png)

As seções **Retenção** respondem à pergunta: “Das pessoas que exibiram alguma página ou usaram algum recurso em um dia ou uma semana, quantas voltaram no dia ou na semana seguinte?”

* [Saiba mais sobre como editar as seções Retenção](app-insights-usage-retention.md)
* Ative/desative o gráfico opcional Retenção Geral usando a caixa de seleção **Mostrar gráfico de retenção geral** na parte superior da seção.

## <a name="adding-application-insights-analytics-sections"></a>Adicionando as seções do Analytics do Application Insights

![Seção do Analytics no Workbooks](./media/app-insights-usage-workbooks/analytics-section.png)

Para adicionar uma seção de consulta do Analytics do Application Insights à pasta de trabalho, use o botão **Adicionar consulta do Analytics** na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

As seções de consulta do Analytics permitem que você adicione consultas arbitrárias dos dados do Application Insights a pastas de trabalho. Essa flexibilidade indica que as seções de consulta do Analytics devem ser a opção para a qual você deve ir para responder a qualquer pergunta sobre seu site que não seja as quatro listadas acima de Usuários, Sessões, Eventos e Retenção, como:

* Quantas exceções seu site gerou durante o mesmo período como um declínio no uso?
* Qual foi a distribuição dos tempos de carregamento de página para os usuários que exibiram alguma página?
* Quantos usuários exibiram algum conjunto de páginas no site, mas não algum outro conjunto de páginas? Isso poderá ser útil para entender se você tiver clusters de usuários que usam diferentes subconjuntos da funcionalidade do site (use o operador `join` com o modificador `kind=leftanti` na linguagem de consulta do Log Analytics).

Use a [referência de linguagem de consulta do Log Analytics](https://docs.loganalytics.io/) para saber mais sobre como escrever consultas.

## <a name="adding-text-and-markdown-sections"></a>Adicionando texto e seções de Markdown

Adicionar cabeçalhos, explicações e comentários às pastas de trabalho ajuda a transformar um conjunto de tabelas e gráficos em uma narrativa. As seções de texto nas pastas de trabalho dão suporte à [sintaxe de Markdown](https://daringfireball.net/projects/markdown/) na formatação de texto, como cabeçalhos, negrito, itálico e listas com marcadores.

Para adicionar uma seção de texto à pasta de trabalho, use o botão **Adicionar texto** na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Salvando e compartilhando pastas de trabalho com sua equipe

As pastas de trabalho são salvas em um recurso do Application Insights, na seção **Meus Relatórios**, que é particular a você ou na seção **Relatórios Compartilhados**, que é acessível a todos com acesso ao recurso do Application Insights. Para exibir todas as pastas de trabalho no recurso, clique no botão **Abrir** na barra de ações.

Para compartilhar uma pasta de trabalho que atualmente está em **Meus Relatórios**:

1. Clique em **Abrir** na barra de ações
2. Clique no botão “...” ao lado da pasta de trabalho que você deseja compartilhar
3. Clique em **Mover para Relatórios Compartilhados**.

Para compartilhar uma pasta de trabalho com um link ou por email, clique em **Compartilhar** na barra de ações. Tenha em mente que os destinatários do link precisam ter acesso a esse recurso no portal do Azure para exibir a pasta de trabalho. Para fazer edições, os destinatários precisam de, pelo menos, permissões de Colaborador ao recurso.

Para fixar um link em uma pasta de trabalho em um Painel do Azure:

1. Clique em **Abrir** na barra de ações
2. Clique no botão “...” ao lado da pasta de trabalho que você deseja fixar
3. Clique em **Fixar no painel**.

## <a name="next-steps"></a>Próximas etapas

## <a name="next-steps"></a>Próximas etapas
- Para habilitar as experiências de uso, comece enviando [eventos personalizados](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [exibições de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se você já envia eventos personalizados ou exibições de página, explore as ferramentas de uso para saber como os usuários utilizam o seu serviço.
    - [Usuários, Sessões, Eventos](app-insights-usage-segmentation.md)
    - [Funis](usage-funnels.md)
    - [Retenção](app-insights-usage-retention.md)
    - [Fluxos de Usuário](app-insights-usage-flows.md)
    - [Adicionar contexto de usuário](app-insights-usage-send-user-context.md)
    
