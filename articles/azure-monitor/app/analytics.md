---
title: Analytics – a ferramenta de pesquisa e consulta avançada do Azure Application Insights | Microsoft Docs
description: 'Visão geral da Análise, a ferramenta de pesquisa avançada do Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: mbullwin
ms.openlocfilehash: 4c3ecdd01106cc8d305764206bc75535fa4dac3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691882"
---
# <a name="analytics-in-application-insights"></a>Análise no Application Insights
O Analytics é a ferramenta de pesquisa e consulta avançada do [Application Insights](app-insights-overview.md). O Analytics é uma ferramenta de Web, portanto, nenhuma configuração é necessária.
Se já tiver configurado o Application Insights para um de seus aplicativos, você poderá analisar os dados do aplicativo abrindo o Analytics na [folha de visão geral](app-insights-dashboards.md) desse aplicativo.

![Abra o portal.azure.com, abra o recurso do Application Insights e clique em Análise.](./media/analytics/001.png)

Também é possível usar o [playground do Analytics](https://go.microsoft.com/fwlink/?linkid=859557), que é um ambiente de demonstração livre com uma grande quantidade de dados de exemplo.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="relation-to-azure-monitor-logs"></a>Relação com os logs do Azure Monitor
A análise do Application Insights se baseia no [Azure Data Explorer](/azure/data-explorer) como logs do Azure Monitor e também usa a [linguagem de consulta do Kusto](/azure/kusto/query). Ela usa o mesmo [portal da análise de logs](../log-query/get-started-portal.md) que os logs do Azure Monitor, embora seus dados sejam armazenados em uma partição separada.

Você não pode acessar dados em um espaço de trabalho do Log Analytics diretamente pela análise do Application Insights, nem pode acessar dados do aplicativo diretamente da análise de logs. Para consultar os dois conjuntos de dados juntos, escreva uma [consulta na análise de logs](../log-query/log-query-overview.md) e use a [expressão app()](../log-query/app-expression.md) para acessar os dados do aplicativo.


## <a name="query-data-in-analytics"></a>Consultar dados no Analytics
Uma consulta comum começa com um nome de tabela, seguido por uma série de *operadores* separados por `|`.
Por exemplo, vamos descobrir quantas solicitações nosso aplicativo recebeu de diferentes países nas últimas três horas:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Começamos com o nome de tabela *requests* e adicionamos elementos conectados, conforme necessário.  Primeiro, definimos um filtro de tempo para analisar apenas registros das últimas três horas.
Em seguida, contamos o número de registros por país (esses dados se encontram na coluna *client_CountryOrRegion*). Por fim, renderizamos os resultados em um gráfico de pizza.
<br>

![Resultados da consulta](./media/analytics/030.png)

A linguagem tem muitos recursos atrativos:

* [Filtre](/azure/kusto/query/whereoperator) a telemetria bruta de aplicativos por qualquer campo, incluindo suas métricas e propriedades personalizadas.
* [Una](/azure/kusto/query/joinoperator) várias tabelas – correlacione as solicitações com exibições de página, as chamadas de dependência, as exceções e os rastreamentos de log.
* [Agregações](/azure/kusto/query/summarizeoperator)estatísticas poderosas.
* Visualizações imediatas e eficientes.
* Uma [API REST](https://dev.applicationinsights.io/) que você pode usar para executar consultas de modo programático; no PowerShell, por exemplo.

A [referência completa de linguagens](https://go.microsoft.com/fwlink/?linkid=856079) fornece detalhes sobre cada comando com suporte e é atualizada regularmente.

## <a name="next-steps"></a>Próximas etapas
* Introdução ao [Portal do Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* Introdução à [escrita de consultas](https://go.microsoft.com/fwlink/?linkid=856078)
* Analise o [roteiro para usuários de SQL](https://aka.ms/sql-analytics) para ver traduções das expressões mais comuns.
* Faça um test drive do Analytics com nosso [playground](https://analytics.applicationinsights.io/demo) se seu aplicativo ainda não estiver enviando dados para o Application Insights.
* Assista ao [vídeo introdutório](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).
