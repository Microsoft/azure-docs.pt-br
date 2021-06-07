---
title: Monitoramento-banco de dados do Azure para MySQL
description: Este artigo descreve as métricas de monitoramento e alertas para o Banco de Dados do Azure para MySQL, incluindo CPU, armazenamento e estatísticas de conexão.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/21/2020
ms.openlocfilehash: 8d6aa4de7c3b75747c6e2f103e66e39dfa288ba7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576241"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitoramento no Banco de Dados do Azure para MySQL
Monitorar os dados dos seus servidores ajuda a solucionar problemas e otimizar sua carga de trabalho. O Banco de Dados do Azure para MySQL oferece várias métricas que fornecem insights sobre o comportamento do seu servidor.

## <a name="metrics"></a>Métricas
Todas as métricas do Azure têm uma frequência de um minuto e cada uma delas fornece 30 dias de histórico. É possível configurar alertas nas métricas. Para obter diretrizes passo a passo, consulte [How to set up alerts](howto-alert-on-metric.md) (Como configurar alertas). Outras tarefas incluem a configuração de ações automatizadas, execução de análises avançadas e arquivamento de histórico. Para obter mais informações, consulte a [Visão geral das métricas no Microsoft Azure](../azure-monitor/data-platform.md).

### <a name="list-of-metrics"></a>Lista de métricas
Essas métricas estão disponíveis para o Banco de Dados do Azure para MySQL:

|Métrica|Nome de exibição da métrica|Unidade|Descrição|
|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|O percentual de CPU em uso.|
|memory_percent|Porcentagem de memória|Porcentagem|O percentual de memória em uso.|
|io_consumption_percent|Porcentagem de E/S|Porcentagem|O percentual de E/S em uso. (Não aplicável para servidores de camada básica)|
|storage_percent|Porcentagem de armazenamento|Porcentagem|O percentual de armazenamento usado fora do máximo do servidor.|
|storage_used|Armazenamento usado|Bytes|A quantidade de armazenamento em uso. O armazenamento usado pelo serviço pode incluir os arquivos de banco de dados, os logs de transação e os logs do servidor.|
|serverlog_storage_percent|Porcentagem de armazenamento do Log do Servidor|Porcentagem|A porcentagem de armazenamento de log do servidor usada fora do armazenamento de log máximo do servidor.|
|serverlog_storage_usage|Armazenamento do Log do Servidor usado|Bytes|A quantidade de armazenamento de log do servidor em uso.|
|serverlog_storage_limit|Limite de armazenamento do Log do Servidor|Bytes|O armazenamento de log do servidor de máximo para esse servidor.|
|storage_limit|Limite de armazenamento|Bytes|O armazenamento máximo para esse servidor.|
|active_connections|Conexões ativas|Contagem|O número de conexões ativas com o servidor.|
|connections_failed|Conexões com falha|Contagem|O número de conexões com falha com o servidor.|
|seconds_behind_master|Retardo de replicação em segundos|Contagem|O número de segundos que o servidor de réplica está atrasado no servidor de origem. (Não aplicável para servidores de camada básica)|
|network_bytes_egress|Saída da rede|Bytes|Rede-Out em conexões ativas.|
|network_bytes_ingress|Entrada na rede|Bytes|Entrada de rede em conexões ativas.|
|backup_storage_used|Backup do Microsoft Azure|Bytes|A quantidade de armazenamento de backup usado. Essa métrica representa a soma do armazenamento consumido por todos os backups de banco de dados completos, backups diferenciais e backups de log retidos com base no período de retenção de backup definido para o servidor. A frequência dos backups é gerenciada pelo serviço e explicada no [artigo conceitos](concepts-backup.md). Para o armazenamento com redundância geográfica, o uso de armazenamento de backup é o dobro do armazenamento com redundância local.|

## <a name="server-logs"></a>Logs do servidor
Você pode habilitar a consulta lenta e o log de auditoria em seu servidor. Esses logs também estão disponíveis por meio dos logs de diagnóstico do Azure em logs de Azure Monitor, hubs de eventos e conta de armazenamento. Para saber mais sobre registro em log, visite os artigos [logs de auditoria](concepts-audit-logs.md) e [logs de consulta lentos](concepts-server-logs.md) .

## <a name="query-store"></a>Repositório de Consultas
[Repositório de consultas](concepts-query-store.md) é um recurso que controla o desempenho da consulta ao longo do tempo, incluindo estatísticas de tempo de execução de consulta e eventos de espera. O recurso persiste as informações de desempenho de tempo de execução de consulta no esquema **MySQL** . Você pode controlar a coleta e o armazenamento de dados por meio de vários botões de configuração.

## <a name="query-performance-insight"></a>Análise de Desempenho de Consultas
[Análise de Desempenho de Consultas](concepts-query-performance-insight.md) funciona em conjunto com o Repositório de Consultas para fornecer visualizações acessíveis do portal do Azure. Esses gráficos permitem que você identifique as principais consultas que afetam o desempenho. Análise de Desempenho de Consultas é acessível na seção de **desempenho inteligente** da página do portal do banco de dados do Azure para o servidor MySQL.

## <a name="performance-recommendations"></a>Recomendações de desempenho
O recurso [Recomendações de Desempenho](concepts-performance-recommendations.md) identifica as oportunidades de melhorar o desempenho da carga de trabalho. As recomendações de desempenho fornecem recomendações para a criação de novos índices que têm o potencial de melhorar o desempenho de suas cargas de trabalho. Para produzir recomendações de índice, o recurso leva em consideração várias características do banco de dados, inclusive seu esquema e a carga de trabalho, conforme relatado pelo Repositório de Consultas. Depois de implementar qualquer recomendação de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações.

## <a name="planned-maintenance-notification"></a>Notificação de manutenção planejada

As [notificações de manutenção planejada](./concepts-planned-maintenance-notification.md) permitem que você receba alertas para a próxima manutenção planejada para o banco de dados do Azure para MySQL. Essas notificações são integradas à manutenção planejada [da integridade do serviço](../service-health/overview.md) e permitem que você exiba toda a manutenção agendada para suas assinaturas em um único local. Ele também ajuda a dimensionar a notificação para os públicos certos para grupos de recursos diferentes, pois você pode ter contatos diferentes responsáveis por diferentes recursos. Você receberá a notificação sobre as próximas 72 horas de manutenção antes do evento.

Saiba mais sobre como configurar notificações no documento de [notificações de manutenção planejada](./concepts-planned-maintenance-notification.md) .

## <a name="next-steps"></a>Próximas etapas
- Consulte [Como configurar alertas](howto-alert-on-metric.md) para obter orientação sobre como criar um alerta em uma métrica.
- Para obter mais informações sobre como acessar e exportar métricas usando o Portal do Azure, a API REST ou a CLI, consulte a [Visão geral das métricas no Microsoft Azure](../azure-monitor/data-platform.md).
- Leia nosso blog sobre [práticas recomendadas para monitorar seu servidor](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
- Saiba mais sobre as [notificações de manutenção planejada](./concepts-planned-maintenance-notification.md) no banco de dados do Azure para MySQL-servidor único