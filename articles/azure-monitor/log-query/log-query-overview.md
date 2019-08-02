---
title: Visão geral do log de consultas no Azure Monitor | Microsoft Docs
description: Respostas a perguntas comuns relacionadas ao fazer a consulta e permite começar a usá-los.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: 7605bf36c41c5b1276d29076173efd52409afaa9
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310334"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Visão geral das consultas de log no Azure Monitor
Log de consultas ajudam você a aproveitar totalmente o valor dos dados coletados no [Logs do Azure Monitor](../platform/data-platform-logs.md). Uma poderosa linguagem de consulta permite que você unir dados de várias tabelas, agregar grandes conjuntos de dados e executar operações complexas com o mínimo de código. Praticamente qualquer pergunta pode ser respondida e análise executadas desde que foram coletados dados de suporte, e você compreender como construir a consulta à direita.

Alguns recursos no Azure Monitor, como [insights](../insights/insights-overview.md) e [soluções](../insights/solutions-inventory.md) processar dados de log sem expor você às consultas subjacentes. Para aproveitar totalmente a outros recursos do Azure Monitor, você deve compreender como as consultas são construídas e como eles podem ser usados para analisar interativamente os dados em Logs do Azure Monitor.

Use este artigo como um ponto de partida para saber mais sobre consultas de log no Azure Monitor. Ele responde a perguntas comuns e fornece links para documentação adicional que fornece mais detalhes e as lições.

## <a name="how-can-i-learn-how-to-write-queries"></a>Como posso saber como escrever consultas?
Se desejar ir diretamente para as coisas, você pode começar com os tutoriais a seguir:

- [Introdução ao Log Analytics no Azure Monitor](get-started-portal.md).
- [Introdução às consultas de log no Azure Monitor](get-started-queries.md).

Quando você tiver as Noções básicas para baixo, percorra várias lições usando seus próprios dados ou dados de nosso ambiente de demonstração, iniciando com: 

- [Trabalhar com cadeias de caracteres em consultas de log do Azure Monitor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Qual idioma faça uso de consultas?
Os Logs do Azure Monitor se baseia [Data Explorer do Azure](/azure/data-explorer), e consultas de log são gravadas usando a mesma linguagem de consulta do Kusto (KQL). Isso é uma linguagem avançada projetada para ser fácil de ler e autor e você poderá começar a usá-lo com orientação mínima.

Ver [documentação do Azure Data Explorer KQL](/azure/kusto/query) para a documentação completa sobre KQL e referência sobre as diferentes funções disponíveis.<br>
Ver [Introdução às consultas de log no Azure Monitor](get-started-queries.md) para uma rápida explicação passo a passo da linguagem usando os dados de Logs do Azure Monitor.
Ver [diferenças de linguagem de consulta de log do Azure Monitor](data-explorer-difference.md) para pequenas diferenças na versão do KQL usado pelo Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Quais dados estão disponíveis para consultas de log?
Todos os dados coletados nos Logs do Azure Monitor está disponível para recuperar e analisar em consultas de log. Fontes de dados diferentes gravará seus dados em tabelas diferentes, mas você pode incluir várias tabelas em uma única consulta para analisar dados de várias fontes. Quando você cria uma consulta, comece determinando quais tabelas têm os dados que você está procurando, portanto, você deve ter pelo menos um entendimento básico de como os dados em Logs do Azure Monitor estão estruturados.

Ver [fontes de Logs do Azure Monitor](../platform/data-platform-logs.md#sources-of-azure-monitor-logs), para uma lista de dados de diferentes fontes que preencher os Logs do Azure Monitor.<br>
Ver [estrutura de Logs do Azure Monitor](logs-structure.md) para obter uma explicação de como os dados são estruturados.

## <a name="what-does-a-log-query-look-like"></a>O que é a aparência de uma consulta de log?
Uma consulta pode ser tão simple quanto um nome de tabela simples para recuperar todos os registros da tabela:

```Kusto
Syslog
```

Ou pode filtrar os registros de determinado, resumi-los e visualizar os resultados em um gráfico:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Para uma análise mais complexa, você pode recuperar dados de várias tabelas usando uma junção para analisar os resultados juntos.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Mesmo se você não estiver familiarizado com KQL, você poderá descobrir pelo menos a lógica básica que está sendo usada por essas consultas. Eles começam com o nome de uma tabela e, em seguida, adicionar vários comandos para filtrar e processar os dados. Uma consulta pode usar qualquer número de comandos, e você pode escrever consultas mais complexas, como você se familiarizar com os diferentes comandos KQL.

Ver [Introdução às consultas de log no Azure Monitor](get-started-queries.md) para obter um tutorial que apresenta o idioma e as funções comuns, em consultas de log.<br>


## <a name="what-is-log-analytics"></a>O que é o Log Analytics?
Log Analytics é a principal ferramenta no portal do Azure para escrever consultas de log e analisando interativamente seus resultados. Mesmo se uma consulta de log é usada em outro lugar no Azure Monitor, você normalmente escrever e testar a consulta usando o Log Analytics.

Você pode iniciar o Log Analytics de diversos lugares no portal do Azure. O escopo dos dados disponíveis para o Log Analytics é determinado por como você iniciá-lo. Ver [escopo da consulta](scope.md) para obter mais detalhes.

- Selecione **Logs** da **do Azure Monitor** menu ou **espaços de trabalho do Log Analytics** menu.
- Selecione **Analytics** da **visão geral** página de um aplicativo de Application Insights.
- Selecione **Logs** no menu de um recurso do Azure.

![Log Analytics](media/log-query-overview/log-analytics.png)

Ver [Introdução ao Log Analytics no Azure Monitor](get-started-portal.md) para uma tutorial passo a passo do Log Analytics que apresenta vários dos seus recursos.

## <a name="where-else-are-log-queries-used"></a>Onde mais consultas de log são usadas?
Além de trabalhar interativamente com consultas de log e seus resultados no Log Analytics, áreas no Azure Monitor onde você usará consultas incluem o seguinte:

- **Regras de alerta** As [Regras de alerta](../platform/alerts-overview.md) identificam proativamente os problemas dos dados no workspace.  Cada regra de alerta é baseada em uma pesquisa de logs que é executada automaticamente em intervalos regulares.  Os resultados são inspecionados para determinar se um alerta deve ser criado.
- **Painéis.** Você pode fixar os resultados da consulta em um [painel do Azure](../learn/tutorial-logs-dashboards.md), que permite visualizar os dados de log e de métrica em conjunto e, opcionalmente, compartilhar com outros usuários do Azure.
- **Exibições.**  Você pode criar visualizações de dados a serem incluídas em painéis de usuários com [Designer de Exibição](../platform/view-designer.md).  As consultas de logs fornecem os dados usados por [blocos](../platform/view-designer-tiles.md) e [blocos de visualização](../platform/view-designer-parts.md) em cada exibição.  
- **Exportação.**  Ao importar dados de log do Azure Monitor para o Excel ou o [Power BI](../platform/powerbi.md), você cria uma consulta de logs para definir os dados a serem exportados.
- **PowerShell.** Você pode executar um script do PowerShell de uma linha de comando ou um runbook de automação do Azure que usa [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) para recuperar dados de log do Azure Monitor.  Esse cmdlet requer uma consulta para determinar os dados a serem recuperados.
- **API de Logs do Azure Monitor.**  A [API de Logs do Azure Monitor](../platform/alerts-overview.md) permite que qualquer cliente da API REST recupere dados de log do workspace.  A solicitação de API inclui uma consulta que é executada no Azure Monitor para determinar os dados a serem recuperados.


## <a name="next-steps"></a>Próximas etapas
- Percorrer um [tutorial sobre como usar o Log Analytics no portal do Azure](get-started-portal.md).
- Percorrer um [tutorial sobre como escrever consultas](get-started-queries.md).
