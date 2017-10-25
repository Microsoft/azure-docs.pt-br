---
title: "Log de diagnóstico de desempenho do Insights inteligentes – Banco de dados SQL do Azure|Microsoft Docs"
description: "O recurso Insights inteligentes fornece um log de diagnóstico de problemas de desempenho do banco de dados SQL do Azure"
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 4890baa4ead3323834a82b3f9340cf751bf0c755
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Use o log de diagnóstico de desempenho do Banco de dados SQL do Azure com Insights inteligentes

Esta página fornece informações sobre como usar o log de diagnóstico de desempenho de Banco de dados SQL do Azure gerados pelo [Insights inteligentes](sql-database-intelligent-insights.md), o formato e dados que ele contém para suas necessidades desenvolvimento personalizadas. Esse log de diagnóstico pode ser enviado para [Log Analytics do Azure](../log-analytics/log-analytics-azure-sql.md), [Hub de eventos do Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Armazenamento do Azure](sql-database-metrics-diag-logging.md#stream-into-azure-storage) ou uma solução de terceiros para recursos personalizados de alertas e relatórios de DevOps.

## <a name="log-header"></a>Cabeçalho do log

O log de diagnóstico usa o formato padrão JSON para gerar descobertas de insights inteligentes. A propriedade exata da categoria para acessar o log de Insights inteligentes é um valor fixo &#8220; **SQLInsights**&#8221;.

O cabeçalho do log é comum e consiste em carimbo de data/hora (TimeGenerated) quando uma entrada foi criada, incluindo uma ID de recurso (ResourceId) que faz referência a determinado Banco de dados SQL do Azure com o qual a entrada se relaciona. Categoria (Category), nível (Level) e nome da operação (OperationName) são propriedades corrigidas cujo valor não se altera – e indicam que a entrada de log é informativa e é proveniente de insights inteligentes (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>ID do problema e o banco de dados afetado

A propriedade de identificação do problema (issueId_d) fornece uma maneira de rastrear de forma exclusiva os problemas de desempenho até a solução. Insights inteligentes observa o ciclo de vida de cada problema como: Ativo, Verificando e Concluído. Por meio de cada uma dessas fases de status, o Insights inteligentes pode gravar vários registros de eventos no log e para cada uma dessas entradas, o número da ID do problema permanece exclusivo. O Insights inteligentes controla o problema por meio de seu ciclo de vida e gera um insight no log de diagnóstico a cada 15 minutos.

Quando um problema de desempenho é detectado e enquanto ele dura, é reportado como "Ativo" na propriedade status (Status). Depois que um problema detectado é atenuado, ele é verificado e relatado como "Verificando" na propriedade status (Status). Caso o problema não exista mais, a propriedade status (Status) relata esse problema como "Concluído".
Juntamente com a ID do problema, o log de diagnósticos informa o carimbo de data/hora do início (intervalStartTime_t) e término (intervalEndTme_t) do evento específico relacionado a um erro informado no log de diagnósticos.

Pool elástico da propriedade (elasticPoolName_s) indica a qual pool elástico de banco de dados um problema pertence. Se o banco de dados não for parte de um pool elástico, essa propriedade não terá valor. O banco de dados no qual o problema foi detectado é divulgado na propriedade nome (databaseName_s) do banco de dados.

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported timestamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported timestamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: Active, Verifying and Complete
```

## <a name="detected-issues"></a>Problemas detectados

A próxima seção de log de desempenho do Insights inteligentes contém problemas de desempenho detectados por meio de inteligência artificial interna. As detecções são divulgadas nas detecções da propriedade JSON e consistem na categoria de um problema, no impacto dos problemas, nas consultas afetadas e métricas. Observe que a propriedade de detecções pode conter vários problemas de desempenho detectados.

Problemas de desempenho detectados são relatados com a seguinte estrutura de propriedade de detecções:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // tperformance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Padrões de desempenho detectáveis e detalhes enviados ao log de diagnóstico são fornecidos na tabela a seguir.

### <a name="detection-category"></a>Categoria de detecção

A propriedade categoria (category) descreve a categoria de padrões de desempenho detectáveis. Consulte a tabela a seguir para todas as categorias possíveis de padrões de desempenho detectáveis. Mais detalhes estão disponíveis na página [Solucionar problemas de desempenho do banco de dados com Insights inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md).

Dependendo do problema de desempenho detectado, os detalhes gerados no arquivo de log de diagnósticos diferem de acordo.

| Padrões de desempenho detectáveis | Detalhes da saída |
| :------------------- | ------------------- |
| Atingindo os Limites do Recurso | <li>Recursos afetados</li><li>Hashes de consulta</li><li>Percentual do consumo de recurso</li> |
| Aumento da Carga de Trabalho | <li>Número de consultas cuja execução aumentou</li><li>Hashes de consulta das consultas com a maior contribuição para o aumento da carga de trabalho</li> |
| Demanda de Memória | <li>Administrador de memória</li> |
| Bloqueio | <li>Hashes de consulta afetados</li><li>Bloqueio de hashes de consulta</li> |
| Aumento de MAXDOP | <li>Hashes de consulta</li><li>Tempos de espera CXP</li><li>Tempos de espera</li> |
| Contenção de pagelatch | <li>Hashes de consulta das consultas que causam contenção</li> |
| Índice Ausente | <li>Hashes de consulta</li> |
| Nova Consulta | <li>Hash de consulta de novas consultas</li> |
| Estatística de Espera Incomum | <li>Tipos de espera incomum</li><li>Hashes de consulta</li><li>Tempos de espera da consulta</li> |
| Contenção de TempDB | <li>Hashes de consulta das consultas que causam contenção</li><li>Atribuição de consulta ao tempo total de espera da contenção de pagelatch do banco de dados [%]</li> |
| Insuficiência de DTU no pool elástico | <li>Pool elástico</li><li>Principal banco de dados de consumo de DTU</li><li>Porcentagem de DTU do pool usada pelo principal consumidor</li> |
| Regressão de Plano | <li>Hash de consulta</li><li>Boas IDs de plano</li><li>IDs de plano inválidas</li><li>Hashes de consulta</li> |
| Alteração do Valor de Configuração no Escopo do Banco de Dados | <li>Alterações de configuração no escopo do banco de dados em comparação com os valores padrão</li> |
| Cliente Lento | <li>Hashes de consulta</li><li>Tempos de espera</li> |
| Downgrade de Tipo de Preço | <li>Notificação de texto</li> |

### <a name="impact"></a>Impacto

A propriedade impacto (impact) descreve o quanto um comportamento detectado contribuiu para o banco de dados do problema, variando de 1 a 3, em que 3 é a contribuição mais alta, 2 é moderado e 1 é a contribuição mais baixa. O valor do impacto pode ser usado como entrada para a automação de alertas personalizada, dependendo de suas necessidades específicas. Consultas de propriedade afetadas (QueryHashes) fornecem a uma lista de hashes de consultas afetadas por determinada detecção.

### <a name="impacted-queries"></a>Consultas afetadas

A próxima seção do log de insights inteligentes fornece informações sobre cada consulta específica afetada pelos problemas de desempenho detectados. Essas informações são divulgadas como uma matriz de objetos incorporados à propriedade impact_s. A propriedade impacto consiste em entidades e métricas. Entidades referem-se a uma consulta específica (tipo: Query) e ao hash de consulta exclusivo divulgado no valor da propriedade (Value). Além disso, cada uma das consultas divulgadas é seguida por uma métrica e um valor, que indica um problema de desempenho detectado.

No exemplo de log a seguir pode ser observado que a consulta com o hash 0x9102EXZ4 foi detectada com um aumento da duração da execução (métrica: DurationIncreaseSeconds) com o valor de 110 segundos – indicando que essa consulta específica levou 110 segundos a mais para ser executada. Observe que várias consultas podem ser detectadas e por isso esta seção de log específica pode incluir várias entradas de consulta.

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

A unidade de medida para cada métrica relatada é fornecida na propriedade métrica (metric) com os valores possíveis: segundos, número e percentual. O valor de uma métrica de medida será relatado na propriedade valor (value).

A propriedade DurationIncreaseSeconds fornece unidade de medida em segundos e, para CriticalErrorCount, a unidade de medida é um número que representa uma contagem de erros.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Recomendações de melhoria e análise da causa raiz

A última parte do log de desempenho do Insights inteligentes pertence à análise da causa raiz automatizada do problema de degradação de desempenho identificado em uma argumentação humana amigável por meio da propriedade de análise da causa raiz (rootCauseAnalysis_s). Recomendações de melhoria são incluídas na argumentação registrada no log de casos em que isso é possível.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

O log de desempenho do Insights inteligentes pode ser usado com a [Log Analytics do Azure]( https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql) ou com uma solução de terceiros para recursos personalizados de alerta e relatórios de DevOps.

## <a name="next-steps"></a>Próximas etapas
- Aprenda os conceitos de [Insights inteligentes](sql-database-intelligent-insights.md)
- Saiba como [Solucionar problemas de desempenho do Banco de dados SQL do Azure com Insights inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md)
- Saiba como [monitorar o Banco de dados SQL do Azure usando a Análise de SQL do Azure](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql)
- [Colete e consuma dados de log de recursos do Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)



