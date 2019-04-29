---
title: Log de diagnóstico de desempenho do Insights inteligentes – Banco de dados SQL do Azure|Microsoft Docs
description: O recurso Insights inteligentes fornece um log de diagnóstico de problemas de desempenho do banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 264d4cfc6b09813f34501a0e51d3100f4d2bce78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703159"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Use o log de diagnóstico de desempenho do Banco de Dados SQL do Azure com Intelligent Insights

Esta página apresenta informações sobre como usar o log de diagnóstico de desempenho do Banco de Dados SQL do Azure gerado pelo [Intelligent Insights](sql-database-intelligent-insights.md), seu formato e os dados que ele contém para suas necessidades desenvolvimento personalizadas. Você pode enviar esse log de diagnóstico para [registra em log do Azure Monitor](../azure-monitor/insights/azure-sql.md), [Hubs de eventos](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md), [armazenamento do Azure](sql-database-metrics-diag-logging.md#stream-into-storage), ou uma solução de terceiros para personalizados de alertas e relatórios de DevOps recursos.

## <a name="log-header"></a>Cabeçalho do log

O log de diagnóstico usa o formato padrão JSON para gerar descobertas do Intelligent Insights. A propriedade exata da categoria para acessar o log de Intelligent Insights é um valor fixo “SQLInsights”.

O cabeçalho do log é comum e consiste no carimbo de data e hora (TimeGenerated) que mostra quando uma entrada foi criada. Ele também inclui uma ID de recurso (ResourceId) que se refere ao Banco de Dados SQL específico ao qual a entrada está relacionada. A categoria (Category), o nível (Level) e o nome da operação (OperationName) são propriedades fixas cujos valores não mudam. Elas indicam que a entrada de log é informativa e vêm do Intelligent Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>ID do problema e o banco de dados afetado

A propriedade de identificação do problema (issueId_d) fornece uma maneira de rastrear de forma exclusiva os problemas de desempenho até a solução. Vários registros de eventos no status de relatórios de log do mesmo problema compartilharão a mesma ID do problema.

Juntamente com a ID do problema, o log de diagnóstico relata os carimbos de data/hora do início (intervalStartTime_t) e do término (intervalEndTme_t) do evento específico relacionado a um erro informado no log de diagnóstico.

A propriedade pool elástico (elasticPoolName_s) indica a qual pool elástico o banco de dados com um problema pertence. Se o banco de dados não fizer parte de um pool elástico, ela não terá valor. O banco de dados no qual o problema foi detectado é divulgado na propriedade nome do banco de dados (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Problemas detectados

A próxima seção do log de desempenho do Intelligent Insights contém problemas de desempenho detectados por meio de inteligência artificial interna. As detecções são divulgadas em propriedades dentro do log de diagnóstico JSON. Essas detecções consistem na categoria de um problema, no impacto do problema, nas consultas afetadas e nas métricas. As propriedades de detecções podem conter vários problemas de desempenho detectados.

Problemas de desempenho detectados são relatados com a seguinte estrutura de propriedade de detecções:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Padrões de desempenho detectáveis e os detalhes que são gerados no log de diagnóstico são fornecidos na tabela a seguir.

### <a name="detection-category"></a>Categoria de detecção

A propriedade categoria (category) descreve a categoria de padrões de desempenho detectáveis. Consulte a tabela a seguir para todas as categorias possíveis de padrões de desempenho detectáveis. Para obter mais informações, consulte [Solucionar problemas de desempenho do banco de dados com o Insights inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md).

Dependendo do problema de desempenho detectado, os detalhes gerados no arquivo de log de diagnóstico diferirão de acordo.

| Padrões de desempenho detectáveis | Detalhes da saída |
| :------------------- | ------------------- |
| Atingindo os limites do recurso | <li>Recursos afetados</li><li>Hashes de consulta</li><li>Percentual do consumo de recurso</li> |
| Aumento da Carga de Trabalho | <li>Número de consultas cuja execução aumentou</li><li>Hashes de consulta das consultas com a maior contribuição para o aumento da carga de trabalho</li> |
| Demanda de Memória | <li>Administrador de memória</li> |
| Bloqueio | <li>Hashes de consulta afetados</li><li>Bloqueio de hashes de consulta</li> |
| Aumento de MAXDOP | <li>Hashes de consulta</li><li>Tempos de espera CXP</li><li>Tempos de espera</li> |
| Contenção de pagelatch | <li>Hashes de consulta das consultas que causam contenção</li> |
| Índice Ausente | <li>Hashes de consulta</li> |
| Nova Consulta | <li>Hash de consulta de novas consultas</li> |
| Estatística de Espera Incomum | <li>Tipos de espera incomum</li><li>Hashes de consulta</li><li>Tempos de espera da consulta</li> |
| Contenção de TempDB | <li>Hashes de consulta das consultas que causam contenção</li><li>Atribuição de consulta ao tempo total de espera da contenção de pagelatch do banco de dados [%]</li> |
| Insuficiência de DTU no pool elástico | <li>Pool elástico</li><li>Principal banco de dados consumidor de DTU</li><li>Porcentagem de DTU do pool usada pelo principal consumidor</li> |
| Regressão de Plano | <li>Hashes de consulta</li><li>Boas IDs de plano</li><li>IDs de plano inválidas</li> |
| Alteração do Valor de Configuração no Escopo do Banco de Dados | <li>Alterações de configuração no escopo do banco de dados em comparação aos valores padrão</li> |
| Cliente Lento | <li>Hashes de consulta</li><li>Tempos de espera</li> |
| Downgrade de Tipo de Preço | <li>Notificação de texto</li> |

### <a name="impact"></a>Impacto

A propriedade de impacto (impact) descreve quanto um comportamento detectado contribuiu para o problema do banco de dados. O impacto varia de 1 a 3, sendo 3 a contribuição mais alta, 2 como moderada e 1 a contribuição mais baixa. O valor do impacto pode ser usado como entrada para a automação de alertas personalizada, dependendo de suas necessidades específicas. As consultas de propriedade afetadas (QueryHashes) fornecem uma lista de hashes de consultas afetadas por determinada detecção.

### <a name="impacted-queries"></a>Consultas afetadas

A próxima seção do log do Intelligent Insights fornece informações sobre cada consulta específica afetada pelos problemas de desempenho detectados. Essas informações são divulgadas como uma matriz de objetos incorporados à propriedade impact_s. A propriedade impacto consiste em entidades e métricas. Entidades referem-se a uma consulta específica (Tipo: Consulta). O hash de consulta exclusivo é divulgado sob a propriedade de valor (Value). Além disso, cada uma das consultas divulgadas é seguida por uma métrica e um valor, que indica um problema de desempenho detectado.

No exemplo de log a seguir, a consulta com o hash 0x9102EXZ4 foi detectada como tendo uma maior duração de execução (métrica: DurationIncreaseSeconds). O valor de 110 segundos indica que essa consulta específica levou mais de 110 segundos para ser executada. Como várias consultas podem ser detectadas, esta seção de log específico pode incluir várias entradas de consulta.

```json
"impact" : [{
"entity" : { 
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Métricas

A unidade de medida para cada métrica relatada é fornecida na propriedade de métrica (metric) com os valores possíveis de segundos, número e percentual. O valor de uma métrica de medida é relatado na propriedade de valor (value).

A propriedade DurationIncreaseSeconds fornece a unidade de medida em segundos. A unidade de medida CriticalErrorCount é um número que representa uma contagem de erro.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Recomendações de melhoria e análise da causa raiz

A última parte do log de desempenho do Intelligent Insights refere-se à análise da causa raiz automatizada do problema de degradação de desempenho identificado. As informações são exibidas na argumentação amigável a humanos na propriedade de análise de causa raiz (rootCauseAnalysis_s). Recomendações de aperfeiçoamento são incluídas no log sempre que possível.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Você pode usar o log de desempenho com Insights inteligentes [registra em log do Azure Monitor]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) ou uma solução de terceiros para alertas e recursos de relatórios de DevOps personalizado.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre conceitos de [Intelligent Insights](sql-database-intelligent-insights.md).
- Saiba como [Solucionar problemas de desempenho do Banco de Dados SQL do Azure com Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Saiba como [monitorar o Banco de dados SQL do Azure usando a Análise de SQL do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Saiba como [coletar e consumir dados de log dos recursos do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



