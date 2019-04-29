---
title: Diagnóstico de transação do Azure Application Insights | Microsoft Docs
description: Diagnóstico de transação de ponta a ponta do Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: c6c44525018e2115f1df8ed2d3f15432b95490c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783555"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Diagnóstico de transação entre componentes unificado

A experiência de diagnóstico unificado correlaciona automaticamente a telemetria no servidor de todos os seus componentes monitorados do Application Insights em uma única exibição. Não importa se você tem vários recursos com chaves de instrumentação separadas. O Application Insights detecta o relacionamento subjacente e permite diagnosticar facilmente o componente, a dependência ou a exceção do aplicativo que causou uma desaceleração ou falha na transação.

## <a name="what-is-a-component"></a>O que é um componente?

Os componentes são partes independentes dos aplicativos de microsserviços/distribuídos. As equipes de operações e desenvolvedores têm acesso ou visibilidade nível de código para telemetria gerada por esses componentes de aplicativos.

* Os componentes são diferentes das dependências externas "observadas", como SQL, EventHub etc., a qual sua organização/equipe pode não ter acesso (código ou telemetria).
* Os componentes são executados em qualquer número de instâncias de contêiner/função/servidor.
* Os componentes podem ser chaves de instrumentação do Application Insights separadas (mesmo se as assinaturas forem diferentes) ou diferentes funções relatando para uma única chave de instrumentação do Application Insights. A nova experiência mostra detalhes em todos os componentes, independentemente de como foram configurados.

> [!NOTE]
> * **Não encontra os links dos itens relacionados?** Toda a telemetria relacionada está nas seções [superior](#cross-component-transaction-chart) e [inferior](#all-telemetry-with-this-operation-id) do lado esquerdo. 

## <a name="transaction-diagnostics-experience"></a>Experiência de diagnóstico de transação
Essa exibição tem quatro partes principais: a lista de resultados, um gráfico de transações entre componentes, uma lista de sequência de tempo de toda a telemetria relacionada a essa operação e o painel de detalhes de qualquer item de telemetria selecionado à esquerda.

![Partes principais](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Gráfico de transações entre componentes

Esse gráfico fornece uma linha do tempo com barras horizontais para a duração das solicitações e dependências entre componentes. As exceções coletadas também são marcadas na linha do tempo.

* A linha superior nesse gráfico representa o ponto de entrada, a solicitação de entrada para o primeiro componente chamado nessa transação. A duração é o tempo total decorrido para a conclusão da transação.
* Todas as chamadas para dependências externas são linhas não recolhíveis simples, com ícones que representam o tipo de dependência.
* As chamadas para outros componentes são linhas recolhíveis. Cada linha corresponde a uma operação específica invocada no componente.
* Por padrão, a solicitação, dependência ou exceção selecionada é exibida no lado direito.
* Selecione qualquer linha para ver os [detalhes à direita](#details-of-the-selected-telemetry). 

> [!NOTE]
> As chamadas para outros componentes têm duas linhas: uma linha representa a chamada de saída (dependência) do componente do chamador e a outra linha corresponde à solicitação de entrada no componente chamado. O ícone principal e o estilo distinto das barras de duração ajudam a diferenciá-las.

## <a name="all-telemetry-with-this-operation-id"></a>Toda a telemetria com esta ID de operação

Esta seção mostra uma exibição de lista plana em uma sequência de tempo de toda a telemetria relacionada à transação. Ela também mostra os eventos personalizados e os rastreamentos que não são exibidos no gráfico de transações. Você pode filtrar a lista para ver a telemetria gerada por uma chamada/componente específico. É possível selecionar qualquer item de telemetria nessa lista para ver os [detalhes correspondentes à direita](#details-of-the-selected-telemetry).

![Sequência de tempo de toda a telemetria](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Detalhes da telemetria selecionada

Esse painel recolhível mostra os detalhes de qualquer item selecionado no gráfico de transações ou na lista. "Mostrar tudo" lista todos os atributos padrão que são coletados. Todos os atributos personalizados são listados separadamente abaixo do conjunto padrão. Clique em "..." abaixo da janela do rastreamento de pilha para ter a opção de copiar o rastreamento. "Abrir rastreamentos do profiler" ou "Abrir instantâneo de depuração" mostra diagnósticos de nível de código nos painéis de detalhes correspondentes.

![Detalhes de exceção](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Resultados da Pesquisa

Esse painel recolhível mostra os outros resultados que atendem aos critérios de filtro. Clique em qualquer resultado para atualizar os respectivos detalhes listados nas três seções acima. Tentamos encontrar exemplos que têm mais probabilidade de ter detalhes disponíveis de todos os componentes, mesmo que a amostragem esteja em vigor em qualquer um deles. Eles são mostrados como exemplos "sugeridos".

![Resultados da Pesquisa](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler e depurador de instantâneos

O [Application Insights Profiler](../../azure-monitor/app/profiler.md) ou o [depurador de instantâneos](snapshot-debugger.md) ajuda com diagnósticos de nível de código de problemas de falha e desempenho. Com essa experiência, você pode ver Rastreamentos do Profiler ou instantâneos de qualquer componente com um único clique.

Se você não foi possível obter o Profiler trabalhando, entre em contato **serviceprofilerhelp\@microsoft.com**

Se você não foi possível obter o funcionamento do depurador de instantâneos, entre em contato **snapshothelp\@microsoft.com**

![Integração do Profiler](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Perguntas frequentes

*Eu vejo um único componente no gráfico e os outros somente estão mostrando como dependências externas sem qualquer detalhe do que aconteceu dentro desses componentes.*

Motivos possíveis:

* Os outros componentes são instrumentados com Application Insights?
* Eles estão usando o SDK estável mais recente do Application Insights?
* Se esses componentes são recursos separados do Application Insights, você precisou ter acesso à telemetria?

Se você tiver acesso e os componentes estiverem instrumentados com os SDKs mais recentes do Application Insights, avise-nos através do canal de comentários superior direito.

*Eu vejo linhas duplicadas para as dependências. Isso é esperado?*

Nesse momento, mostramos a chamada de dependência de saída separada da solicitação de entrada. Normalmente, as duas chamadas parecem idênticas, apenas o valor da duração sendo diferente devido à viagem de ida e volta da rede. O ícone principal e o estilo distinto das barras de duração ajudam a diferenciá-las. Essa apresentação dos dados está confusa? Envie-nos seus comentários!

*O que significa o relógio distorcer entre instâncias do componente diferentes?*

As linhas do tempo são ajustadas para as distorções do relógio no gráfico de transações. É possível visualizar os carimbos de data/hora exatos no painel de detalhes ou usando o Analytics.

*Por que na nova experiência está faltando a maioria das consultas de itens relacionados?*

Esse comportamento é intencional. Todos os itens relacionados, em todos os componentes, já estão disponíveis no lado esquerdo (seções superior e inferior). A nova experiência tem dois itens relacionados que o lado esquerdo não abrange: toda a telemetria de cinco minutos antes e depois desse evento e a linha do tempo do usuário.
