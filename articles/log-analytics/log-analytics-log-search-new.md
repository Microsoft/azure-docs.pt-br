---
title: Pesquisa de logs no Log Analytics do Azure | Microsoft Docs
description: "Você precisará de uma pesquisa de logs para recuperar quaisquer dados do Log Analytics.  Este artigo descreve como novas pesquisas de logs são utilizadas no Log Analytics e fornece conceitos necessários para serem compreendidos antes de criar uma."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: bwren
ms.openlocfilehash: db271f5157fee29a5cc0c4534768bdb3c769ba74
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-log-searches-in-log-analytics"></a>Compreendendo as pesquisas de logs no Log Analytics

> [!NOTE]
> Este artigo descreve pesquisas de logs no Log Analytics utilizando a nova linguagem de consulta.  Você pode saber mais sobre a nova linguagem e obter o procedimento para fazer upgrade do espaço de trabalho em [Fazer upgrade do espaço de trabalho do Azure Log Analytics para uma nova pesquisa de logs](log-analytics-log-search-upgrade.md).  
>
> Se o seu espaço de trabalho não foi atualizado para a nova linguagem de consulta, você deverá consultar [Localizar dados usando pesquisas de logs no Log Analytics](log-analytics-log-searches.md).

Você precisará de uma pesquisa de logs para recuperar quaisquer dados do Log Analytics.  Se você estiver analisando dados no portal, configurando uma regra de alerta para ser notificada de uma condição específica ou recuperando dados utilizando a API do Log Analytics, você usará uma pesquisa de logs para especificar os dados desejados.  Este artigo descreve como novas pesquisas de logs são utilizadas no Log Analytics e fornece conceitos que deverão ser compreendidos antes de criar uma. Consulte a seção [Próximas etapas](#next-steps) para obter detalhes sobre como criar e editar pesquisas de logs e referências na linguagem de consulta.

## <a name="where-log-searches-are-used"></a>Onde as pesquisas de logs são utilizadas

As diferentes maneiras que serão utilizadas nas pesquisas de logs no Log Analytics incluem o seguinte:

- **Portais.** É possível realizar análises de dados interativas no repositório com o Portal do Azure ou no [Portal de Análise Avançada](https://go.microsoft.com/fwlink/?linkid=856587).  Isso permite que você edite sua consulta e analise os resultados em uma variedade de formatos e visualizações.  A maioria das consultas criadas será iniciada em um dos portais e, depois, copiada quando você verificar se ela está funcionando conforme esperado.
- **Regras de alerta** As [Regras de alerta](log-analytics-alerts.md) identificam proativamente os problemas dos dados no espaço de trabalho.  Cada regra de alerta é baseada em uma pesquisa de logs que é executada automaticamente em intervalos regulares.  Os resultados são inspecionados para determinar se um alerta deve ser criado.
- **Exibições.**  Você pode criar visualizações de dados a serem incluídas em painéis de usuários com [Designer de Exibição](log-analytics-view-designer.md).  As pequisas de logs fornecem os dados utilizados por [blocos](log-analytics-view-designer-tiles.md) e [blocos de visualização](log-analytics-view-designer-parts.md) em cada exibição.  É possível fazer busca detalhada dos blocos de visualização na página de Pesquisa de Logs para realizar análises adicionais nos dados.
- **Exportação.**  Ao exportar dados do espaço de trabalho do Log Analytics para o Excel ou [Power BI](log-analytics-powerbi.md), você cria uma pesquisa de logs para definir os dados a serem exportados.
- **PowerShell.** É possível executar um script do PowerShell a partir de uma linha de comando ou um runbook de Automação do Azure que utiliza [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) para recuperar dados do Log Analytics.  Esse cmdlet requer uma consulta para determinar os dados a serem recuperados.
- **API do Log Analytics.**  A [API da pesquisa de logs do Log Analytics](log-analytics-log-search-api.md) permite que qualquer cliente de API REST obtenha dados do espaço de trabalho.  A solicitação de API inclui uma consulta que é executada no Log Analytics para determinar os dados a serem recuperados.

![Pesquisas de log](media/log-analytics-log-search-new/log-search-overview.png)

## <a name="how-log-analytics-data-is-organized"></a>Como os dados do Log Analytics são organizados
Ao criar uma consulta, você começa determinando quais tabelas possuem os dados procurados. Cada [fonte de dados](log-analytics-data-sources.md) e [solução](../operations-management-suite/operations-management-suite-solutions.md) armazena seus dados em tabelas dedicadas no espaço de trabalho do Log Analytics.  A documentação para cada fonte de dados e solução inclui o nome do tipo de dados que ele cria e uma descrição de cada uma de suas propriedades.  Muitas consultas somente requerem dados de uma única tabela, mas outras podem utilizar uma variedade de opções para incluir dados de várias tabelas.

![Tabelas](media/log-analytics-log-search-new/queries-tables.png)


## <a name="writing-a-query"></a>Escrevendo uma consulta
No núcleo das pesquisas de logs no Log Analytics há [uma linguagem de consulta extensa](https://docs.loganalytics.io/) que permite recuperar e analisar dados do repositório de várias formas.  Este mesmo idioma de consulta é usado para [Application Insights](../application-insights/app-insights-analytics.md).  Aprender a gravar uma consulta é fundamental para criar pesquisas de logs no Log Analytics.  Normalmente, você começa com consultas básicas e, depois, progride para utilizar funções mais avançadas na medida em que seus requisitos se tornam mais complexos.

A estrutura básica de uma consulta é uma tabela de origem seguida de uma série de operadores separados por um caractere de pipe `|`.  É possível encadear várias operadores para refinar os dados e executar funções avançadas.

Por exemplo, suponha que você deseja localizar os dez melhores computadores com mais eventos de erro no último dia.

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

Ou talvez você queira localizar computadores que não tiveram pulsação no último dia.

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

E quanto a um gráfico de linhas com a utilização do processador para cada computador da semana passada?

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

É possível consultar essas amostras rápidas que, independentemente do tipo de dados com o qual você está trabalhando, a estrutura da consulta é similar.  Você pode dividir em etapas distintas onde os dados resultantes de um comando são enviados através do pipeline para o próximo comando.

Você também pode consultar dados em espaços de trabalho do Log Analytics dentro de sua assinatura.

    union Update, workspace("contoso-workspace").Update
    | where TimeGenerated >= ago(1h)
    | summarize dcount(Computer) by Classification 


Para obter a documentação completa sobre a linguagem de consulta do Azure Log Analytics, incluindo tutoriais e referência de linguagem, consulte a [Documentação da linguagem de consulta do Azure Log Analytics](https://docs.loganalytics.io/).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [portais utilizados para criar e editar pesquisas de logs](log-analytics-log-search-portals.md).
- Confira um [tutorial sobre como escrever consultas](log-analytics-tutorial-viewdata.md) utilizando a nova linguagem de consulta.
