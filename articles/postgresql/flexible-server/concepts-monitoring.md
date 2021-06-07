---
title: Monitoramento e métricas-banco de dados do Azure para PostgreSQL-servidor flexível
description: Este artigo descreve os recursos de monitoramento e métricas no banco de dados do Azure para PostgreSQL – servidor flexível.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 3d26c585593161ccf4f8ec33f913cc7163531f89
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606650"
---
# <a name="monitor-metrics-on-azure-database-for-postgresql---flexible-server"></a>Monitorar métricas no banco de dados do Azure para PostgreSQL-servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Monitorar os dados dos seus servidores ajuda a solucionar problemas e otimizar sua carga de trabalho. O Banco de Dados do Azure para PostgreSQL oferece várias opções de monitoramento para fornecer insights sobre o comportamento do servidor.

## <a name="metrics"></a>Métricas
O Banco de Dados do Azure para PostgreSQL oferece várias métricas que fornecem informações sobre o comportamento dos recursos compatíveis com o servidor PostgreSQL. Cada métrica é emitida a uma frequência de um minuto e tem até [93 dias de histórico](../../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics). É possível configurar alertas nas métricas. Outras opções incluem a configuração de ações automatizadas, a execução de análises avançadas e o histórico de arquivamento. Para obter mais informações, consulte a [Visão geral das métricas no Microsoft Azure](../../azure-monitor/essentials/data-platform-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
As métricas a seguir estão disponíveis para o servidor flexível do PostgreSQL:


|Métrica|Nome de exibição da métrica|Unidade|Descrição|
|---|---|---|---|
| active_connections | Conexões ativas | Contagem | O número de conexões com o servidor. | 
| backup_storage_used | Backup do Microsoft Azure | Bytes | Quantidade de armazenamento de backup usada. Essa métrica representa a soma do armazenamento consumido por todos os backups de banco de dados completos, backups diferenciais e backups de log retidos com base no período de retenção de backup definido para o servidor. A frequência dos backups é gerenciada pelo serviço. Para o armazenamento com redundância geográfica, o uso de armazenamento de backup é o dobro do armazenamento com redundância local. |
| connections_failed | Conexões com falha | Contagem | Conexões com falha. |
| connections_succeeded | Conexões com êxito | Contagem | Conexões com êxito. |
| cpu_credits_consumed | Créditos de CPU Consumidos | Contagem | Número de créditos usados pelo servidor flexível. Aplicável à camada expansível. |
| cpu_credits_remaining | Créditos de CPU Restantes | Contagem | Número de créditos disponíveis para intermitência. Aplicável à camada expansível. |
| cpu_percent | Porcentagem de CPU | Porcentagem | Percentual de CPU em uso. | 
| disk_queue_depth | Profundidade da fila de disco | Contagem | Número de operações de e/s pendentes para o disco de dados. |
| iops | IOPS | Contagem | Número de operações de e/s em disco por segundo. |
| maximum_used_transactionIDs | Máximo de IDs de transação usadas | Contagem | ID de transação máxima em uso. |
| memory_percent | Porcentagem de memória | Porcentagem | Percentual de memória em uso. |
| network_bytes_egress | Saída da rede | Bytes | Quantidade de tráfego de rede de saída. |
| network_bytes_ingress | Entrada na rede | Bytes | Quantidade de tráfego de rede de entrada. |
| read_iops | IOPS de leitura | Contagem | Número de operações de leitura de e/s de disco de dados por segundo. |
| read_throughput | Taxa de transferência de leitura | Bytes | Bytes lidos por segundo do disco. |
| storage_free | Armazenamento livre | Bytes | A quantidade de espaço de armazenamento disponível. |
| storage_percent | Porcentagem de armazenamento | Percentual | Porcentagem de espaço de armazenamento usado. O armazenamento usado pelo serviço pode incluir os arquivos de banco de dados, os logs de transação e os logs do servidor.|
| storage_used | Armazenamento usado | Bytes | Porcentagem de espaço de armazenamento usado. O armazenamento usado pelo serviço pode incluir os arquivos de banco de dados, os logs de transação e os logs do servidor. |
| txlogs_storage_used | Armazenamento de log de transações usado | Bytes | Quantidade de espaço de armazenamento usada pelos logs de transação. | 
| write_throughput | Taxa de transferência de gravação | Bytes | Bytes gravados por segundo no disco. |
| write_iops | IOPS de gravação | Contagem | Número de operações de gravação de e/s de disco de dados por segundo. |

## <a name="server-logs"></a>Logs do servidor
O banco de dados do Azure para PostgreSQL permite configurar e acessar os logs padrão do Postgres. Para saber mais sobre os logs, visite o [documento conceitos de registro em log](concepts-logging.md).
