---
title: Monitoramento e ajuste no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve os recursos de monitoramento e ajuste no Banco de Dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 0d5508fe6b07bd5664bd0708960559537d8f6be1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456216"
---
# <a name="monitor-and-tune"></a>Monitorar e ajustar
Monitorar os dados dos seus servidores ajuda a solucionar problemas e otimizar sua carga de trabalho. O Banco de Dados do Azure para PostgreSQL oferece várias opções de monitoramento para fornecer insights sobre o comportamento do servidor.

## <a name="metrics"></a>Métricas
O Banco de Dados do Azure para PostgreSQL oferece várias métricas que fornecem informações sobre o comportamento dos recursos compatíveis com o servidor PostgreSQL. Cada métrica é emitida em uma frequência de um minuto e tem até 30 dias de histórico. É possível configurar alertas nas métricas. Para obter diretrizes passo a passo, consulte [How to set up alerts](howto-alert-on-metric.md) (Como configurar alertas). Outras tarefas incluem a configuração de ações automatizadas, execução de análises avançadas e arquivamento de histórico. Para obter mais informações, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
Essas métricas estão disponíveis para o Banco de Dados do Azure para PostgreSQL:

|Métrica|Nome de exibição da métrica|Unidade|DESCRIÇÃO|
|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|O percentual de CPU em uso.|
|memory_percent|Porcentagem de memória|Porcentagem|O percentual de memória em uso.|
|io_consumption_percent|Porcentagem de E/S|Porcentagem|O percentual de E/S em uso.|
|storage_percent|Porcentagem de armazenamento|Porcentagem|O percentual de armazenamento usado fora do máximo do servidor.|
|storage_used|Armazenamento usado|Bytes|A quantidade de armazenamento em uso. O armazenamento usado pelo serviço pode incluir os arquivos de banco de dados, os logs de transação e os logs do servidor.|
|storage_limit|Limite de armazenamento|Bytes|O armazenamento máximo para esse servidor.|
|serverlog_storage_percent|Porcentagem de armazenamento do Log do Servidor|Porcentagem|A porcentagem de armazenamento de log do servidor usada fora do armazenamento de log máximo do servidor.|
|serverlog_storage_usage|Armazenamento do Log do Servidor usado|Bytes|A quantidade de armazenamento de log do servidor em uso.|
|serverlog_storage_limit|Limite de armazenamento do Log do Servidor|Bytes|O armazenamento de log do servidor de máximo para esse servidor.|
|active_connections|Conexões ativas|Contagem|O número de conexões ativas com o servidor.|
|connections_failed|Conexões com falha|Contagem|O número de conexões com falha com o servidor.|
|network_bytes_egress|Saída da rede|Bytes|Rede-Out em conexões ativas.|
|network_bytes_ingress|Entrada na rede|Bytes|Entrada de rede em conexões ativas.|
|backup_storage_used|Backup do Microsoft Azure|Bytes|A quantidade de armazenamento de backup usado.|

## <a name="server-logs"></a>Logs do servidor
Você pode habilitar o registro no servidor. Esses logs também estão disponíveis por meio de Logs de diagnóstico do Azure no [registra em log do Azure Monitor](../azure-monitor/log-query/log-query-overview.md), Hubs de eventos e a conta de armazenamento. Para saber mais sobre o registro em log, visite a página [logs de servidor](concepts-server-logs.md).

## <a name="query-store"></a>Repositório de Consultas
[Repositório de Consultas](concepts-query-store.md) é uma versão prévia pública do recurso que mantém o controle do desempenho da consulta ao longo do tempo incluindo eventos de espera e estatísticas de tempo de execução de consulta. O recurso mantém as informações de desempenho de tempo de execução de consulta em um banco de dados do sistema chamado **azure_sys** sob o esquema query_store. Você pode controlar a coleta e o armazenamento de dados por meio de vários botões de configuração.

## <a name="query-performance-insight"></a>Análise de Desempenho de Consultas
[Análise de Desempenho de Consultas](concepts-query-performance-insight.md) funciona em conjunto com o Repositório de Consultas para fornecer visualizações acessíveis do portal do Azure. Esses gráficos permitem que você identifique as principais consultas que afetam o desempenho. A Análise de Desempenho de Consultas está em versão prévia pública e é acessível pela seção **Suporte + solução de problemas** da página do portal do servidor do Banco de Dados do Azure para PostgreSQL.

## <a name="performance-recommendations"></a>Recomendações de desempenho
O recurso [Recomendações de Desempenho](concepts-performance-recommendations.md) identifica as oportunidades de melhorar o desempenho da carga de trabalho. A versão prévia pública das Recomendações de Desempenho fornece recomendações para a criação de novos índices que têm o potencial de melhorar o desempenho de suas cargas de trabalho. Para produzir recomendações de índice, o recurso leva em consideração várias características do banco de dados, inclusive seu esquema e a carga de trabalho, conforme relatado pelo Repositório de Consultas. Depois de implementar qualquer recomendação de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="next-steps"></a>Próximas etapas
- Confira [como configurar alertas](howto-alert-on-metric.md) para obter orientação sobre como criar um alerta em uma métrica.
- Para obter mais informações sobre como acessar e exportar métricas usando o Portal do Azure, a API REST ou a CLI, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Leia nosso blog sobre [práticas recomendadas para monitorar seu servidor](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
