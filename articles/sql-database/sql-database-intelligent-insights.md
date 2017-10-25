---
title: "Monitorar o uso do banco de dados com o Intelligent Insights – Banco de dados SQL do Azure| Microsoft Docs"
description: "O Intelligent Insights do Banco de Dados SQL do Azure usa uma inteligência interna para monitorar continuamente o uso de banco de dados por meio de inteligência artificial e detecta os eventos de interrupção que causam baixo desempenho."
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
ms.openlocfilehash: 8e55fa02d5edda21fbf547397d63f9d1cf987a60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="intelligent-insights"></a>Insights inteligentes

O recurso ***&#8220;Insights inteligentes permite que você saiba o que está acontecendo com o desempenho do banco de dados.&#8221;***

O Intelligent Insights do Banco de Dados SQL do Azure usa uma inteligência interna para monitorar continuamente o uso de banco de dados por meio de inteligência artificial e detecta os eventos de interrupção que causam baixo desempenho. Quando detectados, uma análise detalhada é executada gerando um log de diagnóstico com avaliação inteligente do problema. Essa avaliação consiste em uma análise da causa raiz do problema de desempenho do banco de dados e possíveis recomendações para melhorias de desempenho – ou seja, ***&#8220; insights inteligentes. &#8221;.*** 

## <a name="what-can-intelligent-insights-do-for-you"></a>O que o recurso Insights inteligentes pode fazer por você?

Inteligentes Insights é uma capacidade exclusiva de inteligência interna do Azure e fornece o seguinte valor:

- Monitoramento proativo
- Insights de desempenho personalizado
- Detecção prévia de degradação de desempenho do banco de dados
- RCA (Análise da Causa Raiz) dos problemas detectados
- Recomendações de melhoria de desempenho
- Capacidade de expansão em centenas de milhares de bancos de dados
- Impacto positivo nos recursos de DevOps e no custo total de propriedade

## <a name="how-does-intelligent-insights-work"></a>Como funciona o recurso Insights inteligentes?

O recurso Insights inteligentes analisa o desempenho do banco de dados SQL do Azure comparando a carga de trabalho do banco de dados da última 1 hora com a carga de trabalho de linha de base dos últimos 7 dias. A carga de trabalho do banco de dados é composta por consultas determinadas a serem mais significativas ao desempenho do banco de dados – como, por exemplo, as consultas mais repetidas e maiores. Cada banco de dados é exclusivo com base em estrutura, dados, uso e aplicativo e por isso cada linha de base da carga de trabalho gerada é específica e exclusiva a uma instância individual. O recurso Insights inteligentes, independentemente da linha de base da carga de trabalho, também monitora limites operacionais absolutos e detecta problemas com tempos de espera excessivos, exceções críticas e problemas com parametrizações de consulta que podem afetar o desempenho.

Quando um problema de degradação de desempenho é detectado de várias métricas observadas usando inteligência artificial, a análise é executada gerando um log de diagnóstico com um insight inteligente sobre o que está acontecendo no banco de dados. O Insights inteligentes facilita o acompanhamento do problema de desempenho do banco de dados, da primeira ocorrência à solução. Isso é alcançado por meio de estados de acompanhamento de cada problema detectado em todo o ciclo de vida, desde a detecção inicial do problema, verificação de melhorias do desempenho até a conclusão. São fornecidas atualizações no log de diagnóstico a cada 15 minutos. 

<center>![Servidor](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)</center>

Métricas usadas para medir e detectar problemas de desempenho do banco de dados baseiam-se na duração da consulta, nas solicitações de tempo limite, nos tempos de espera excessivos e em solicitação com erro e são elaboradas com mais detalhes na seção [Métricas de detecção](sql-database-intelligent-insights.md#detection-metrics) deste documento.

## <a name="degradations-detected"></a>Degradações detectadas

As degradações de desempenho do Banco de dados SQL do Azure identificadas são registradas no log de diagnóstico com entradas inteligentes que consistem nas seguintes propriedades:

| Propriedade             | Detalhes              |
| :------------------- | ------------------- |
| Informações de banco de dados  | Metadados sobre um banco de dados no qual foi detectado um insight, como o URI do recurso |
| Intervalo de tempo observado | Hora de início e término do período do insight detectado |
| Métricas afetadas | Métricas que causam a geração de um insight: <ul><li>Aumento da duração da consulta [segundos]</li><li>Espera excessiva [segundos]</li><li>Solicitações com tempo limite [percentual]</li><li>Solicitações com erro [percentual]</li></ul>|
| Valor do impacto | Valor de uma métrica medida |
| Consultas afetadas e códigos de erro | Hash de consulta ou código de erro. Podem ser usados para correlacionar facilmente com as consultas afetadas. São fornecidas métricas que consistem em aumento de duração da consulta, tempo de espera, contagens de tempo limite ou códigos de erro. |
| Detecções | Detecção identificada no banco de dados durante o tempo de um evento. Há 15 padrões de detecção. Consulte [Solucionar problemas de desempenho do banco de dados com o Insights inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Análise da causa raiz | RCA (Análise da Causa Raiz) do problema identificado em um formato humanamente legível. Alguns insights podem conter recomendação de melhoria de desempenho sempre que possível. |
|||

## <a name="issues-state-lifecycle-active-verifying-and-completed"></a>Ciclo de vida de estado dos problemas: Ativo, Verificando e Concluído

Problemas de desempenho registrados no log de diagnóstico são marcados com um dos três estados do ciclo de vida do problema: Ativo, Verificando e Concluído. Quando um problema de desempenho é detectado e enquanto for considerado pela inteligência interna do SQL Azure como presente, o problema é sinalizado como &#8220;***Ativo***&#8221;. Quando o problema é considerado como atenuado, ele é verificado e o status alterado para &#8220; ***Verificando***&#8221;. Quando a inteligência interna de SQL do Azure considera o problema resolvido, ele é marcado como &#8220;***Concluído***&#8221;.

## <a name="using-intelligent-insights"></a>Usando Insights inteligentes

O log de diagnóstico do Insights inteligentes pode ser enviado para o Log Analytics do Azure, Hub de eventos do Azure e Armazenamento do Azure conforme descrito em [Registro em log de métricas e diagnósticos do Banco de dados SQL do Azure](sql-database-metrics-diag-logging.md). Quando o log é enviado a um esses destinos, pode ser usado para alertas personalizados e desenvolvimento de monitoramento usando o Microsoft ou ferramentas de terceiros. Para instruções de solução de problemas de desempenho do Banco de dados SQL do Azure usando Insights inteligentes, consulte [Solucionar problemas de desempenho do Banco de dados SQL do Azure com Insights inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="built-in-intelligent-insights-analytics-with-azure-log-analytics"></a>Análise de Insights inteligentes internos com Log Analytics do Azure 

A solução de Log Analytics do Azure fornece recursos de relatórios e alertas, além dos dados de log de diagnóstico de Insights inteligentes. Abaixo está um exemplo de relatório do Insights inteligentes na Análise de SQL do Azure.

![Servidor](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

Depois que o log de diagnóstico do Insights inteligentes é configurado para transmitir dados para Análise de SQL do Azure, você pode [monitorar o Banco de dados SQL do Azure usando a Análise de SQL do Azure](../log-analytics/log-analytics-azure-sql.md).

## <a name="custom-integrations-of-intelligent-insights-log"></a>Integrações personalizadas de log do Insights inteligentes

Para alertas personalizados e monitoramento do desenvolvimento, usando o Microsoft ou ferramentas de terceiros, consulte [Usar o log de diagnóstico de desempenho de banco de dados do Insights inteligentes](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="how-to-set-up-intelligent-insights-with-azure-event-hub"></a>Como configurar o Insights inteligentes com o Hub de eventos do Azure

- Configure o Insights inteligentes para transmitir eventos de log para o Hub de eventos do Azure por meio de [Transmitir logs de diagnóstico do Azure para Hubs de evento](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).
- Use o Hub de eventos do Azure para monitoramento e alertas personalizados por meio de [O que fazer com métricas e logs de diagnóstico no Hub de eventos](sql-database-metrics-diag-logging.md#stream-into-azure-storage). 

## <a name="how-to-set-up-intelligent-insights-with-azure-storage"></a>Como configurar o Insights inteligentes com o Armazenamento do Azure

- Configure insights inteligentes para serem armazenados com o Armazenamento do Azure por meio de [Transmitir para o armazenamento do Azure](sql-database-metrics-diag-logging.md#stream-into-azure-storage).

## <a name="detection-metrics"></a>Métricas de detecção

Métricas usadas para modelos de detecção que geram insights inteligentes são baseadas em monitoramento:

- Duração da consulta
- Solicitações de tempo limite
- Tempo de espera excessivo 
- Solicitações com erro

Solicitações de duração e tempo limite de consulta são usadas como modelos principais na detecção de problemas de desempenho de carga de trabalho de banco de dados. Isso ocorre porque o que acontece com a carga de trabalho é medido diretamente. Para detectar todos os casos possíveis de degradação do desempenho de carga de trabalho, tempo de espera excessivo e solicitações com erros são usados como modelos adicionais para indicar problemas que afetam o desempenho da carga de trabalho.

O sistema automaticamente leva em consideração alterações na carga de trabalho e no número de solicitações de consulta feitas ao banco de dados para determinar dinamicamente os limites normais e fora do comum de desempenho do banco de dados.

Todas as métricas são levadas em consideração em conjunto em vários relacionamentos, por meio de um modelo de dados derivado cientificamente, categorizando cada problema de desempenho detectado. As informações fornecidas por meio de um insight inteligente incluem detalhes do problema de desempenho detectado, uma análise da causa raiz da ocorrência do problema e recomendações sobre como melhorar o desempenho do banco de dados SQL do Azure monitorado em casos onde possível.

## <a name="query-duration"></a>Duração da consulta

O modelo de degradação da duração da consulta analisa consultas individuais e detecta aumento no tempo necessário para compilar e executar uma consulta, em comparação com a linha de base de desempenho.

Quando a inteligência interna do banco de dados SQL do Azure detecta um aumento significativo no tempo de execução ou na compilação da consulta que afeta o desempenho da carga de trabalho, essas consultas são sinalizadas para apresentar o problema de degradação de desempenho de duração da consulta. 

O log de diagnóstico do Insights inteligentes gera o hash de consulta da consulta degradada no desempenho, a indicação se a degradação do desempenho foi relacionada à compilação ou tempo de execução da consulta e maior tempo de duração da consulta.

## <a name="timeout-requests"></a>Solicitações de tempo limite

O modelo de degradação das solicitações de tempo limite analisa consultas individuais e detecta aumento nos tempos limite no nível de execução da consulta e nos limites de solicitação geral no nível do banco de dados em comparação com o período de linha de base de desempenho.

Como algumas das consultas podem atingir o tempo limite mesmo antes de alcançar o estágio de execução, a inteligência interna do Banco de dados SQL do Azure também mede e analisa, por meio de trabalhadores anulados versus solicitações feitas, todas as consultas que chegaram ao banco de dados, independentemente de terem alcançado o estágio de execução ou não. 

Depois que o número de tempos limite das consultas executadas ou o número de trabalhadores com solicitação anuladas excede o limite gerenciado do sistema, um log de diagnóstico é populado com insights inteligentes.

As informações geradas contêm o número de solicitações com tempo limite, o número de consultas com tempo limite e indicação se a degradação do desempenho estava relacionada ao aumento do tempo limite no estágio de execução ou no nível do banco de dados em geral. Quando o aumento nos tempos limite é considerado como significativo ao desempenho do banco de dados, essas consultas são sinalizadas para apresentar um problema de degradação do desempenho de tempo limite. 

## <a name="excessive-wait-times"></a>Tempos excessivos de espera

O modelo de tempo excessivo de espera monitora as consultas de banco de dados individual e detecta estatísticas de espera de consulta altamente incomum, acima dos limites absolutos gerenciados pelo sistema. As seguintes métricas de tempo de espera excessivo são observadas utilizando o novo recurso de Estatísticas de espera de repositório de consultas do SQL Server (sys.query_store_wait_stats) de recurso do SQL Server:

- Atingindo os limites do recurso
- Atingindo os limites de recurso do pool elástico
- Número excessivo de threads de trabalho ou sessão
- Bloqueio excessivo do banco de dados
- Demanda de memória
- Outras estatísticas de espera

O alcance dos limites do recurso ou dos limites do recurso de pool elástico indica que o consumo de recursos disponíveis em uma assinatura ou no pool elástico excedeu os limites absolutos, indicando degradação de desempenho da carga de trabalho. O número excessivo de threads de sessão ou trabalho indica uma condição em que o número de threads de trabalho ou sessões iniciado excedeu os limites absolutos, indicando degradação de desempenho da carga de trabalho.

O bloqueio excessivo do banco de dados indica uma condição em que a contagem de bloqueios em um banco de dados excedeu um limite absoluto, indicando degradação de desempenho da carga de trabalho. A demanda de memória é uma condição em que o número de threads que solicitam concessões de memória excede um limite absoluto, indicando degradação de desempenho da carga de trabalho.

A detecção de outras estatísticas de espera indicam uma condição em que diversas métricas medidas por meio das estatísticas de espera de repositório de consultas estão atingindo acima de um limite absoluto, indicando degradação de desempenho da carga de trabalho.

Quando são detectados tempos de espera excessivo, o Insights inteligentes faz um diagnóstico das saídas de log, dependendo dos dados disponíveis, dos hashes de consultas afetadas e que afetam o sistema degradadas no desempenho, dos detalhes das métricas que causam a espera das consultas em execução e do tempo de espera medido.

## <a name="errored-requests"></a>Solicitações com erros

O modelo de degradação de solicitações com erros monitora consultas individuais e detecta um aumento no número de consultas que apresentaram erros em comparação com o período da linha de base. Esse modelo também monitora exceções críticas que alcançaram limites absolutos gerenciados pela inteligência interna do Banco de dados do Azure. O sistema automaticamente leva em consideração o número de solicitações de consulta feitas ao banco de dados e considera quaisquer alterações de carga de trabalho no período monitorado.

Quando o aumento medido em solicitações com erros em relação ao número total de solicitações feitas é considerado como significativo ao desempenho da carga de trabalho, as consultas afetadas são sinalizadas para apresentar o problema de degradação de desempenho de solicitações com erros.

O log do Insights inteligentes gera a contagem de solicitações com erros, indicação se a degradação do desempenho estava relacionada com o aumento das solicitações com erros ou com o alcance de um dos limites de exceção críticos monitorados e tempo medido da degradação de desempenho. 

No caso de alguma exceção crítica monitorada ir acima dos limites absolutos gerenciados pelo sistema, um insight inteligente será gerado com detalhes da exceção crítica.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [Solucionar problemas de desempenho do Banco de dados SQL do Azure com Insights inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md)
* Use o [log de diagnóstico de desempenho do Banco de dados SQL do Azure com Insights inteligentes](sql-database-intelligent-insights-use-diagnostics-log.md)
* Saiba como [monitorar o Banco de dados SQL do Azure usando a Análise de SQL do Azure](../log-analytics/log-analytics-azure-sql.md)
* Saiba como [coletar e consumir dados de log dos recursos do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)


