---
title: Gerenciamento de recursos em pools elásticos densos
description: Gerencie recursos de computação em pools elásticos do banco de dados SQL do Azure com muitos bancos.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 1398745a485b57c3a739c25dbca77d7b21f19758
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792150"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Gerenciamento de recursos em pools elásticos densos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Os [pools elásticos](./elastic-pool-overview.md) do banco de dados SQL do Azure são uma solução econômica para o gerenciamento de muitos banco de dados com diferentes usos de recursos. Todos os bancos de dados em um pool elástico compartilham a mesma alocação de recursos, como CPU, memória, threads de trabalho, espaço de armazenamento, tempdb, na hipótese de que **apenas um subconjunto de bancos de dados no pool usará recursos de computação em um determinado momento** . Essa suposição permite que os pools elásticos sejam econômicos. Em vez de pagar por todos os recursos que cada banco de dados individual poderia precisar, os clientes pagam um conjunto muito menor de recursos, compartilhados entre todos os bancos de dados no pool.

## <a name="resource-governance"></a>Governança de recursos

O compartilhamento de recursos exige que o sistema controle cuidadosamente o uso de recursos para minimizar o efeito de "vizinho ruidosa", no qual um banco de dados com alto consumo de recursos afeta outros bancos no mesmo pool elástico. Ao mesmo tempo, o sistema deve fornecer recursos suficientes para recursos como alta disponibilidade e recuperação de desastre (HADR), backup e restauração, monitoramento, Repositório de Consultas, ajuste automático, etc. para funcionar de forma confiável.

O banco de dados SQL do Azure atinge essas metas usando vários mecanismos de governança de recursos, incluindo [objetos de trabalho](/windows/win32/procthread/job-objects) do Windows para governança de recursos de nível de processo, [FSRM (Gerenciador de recursos de servidor de arquivos)](/windows-server/storage/fsrm/fsrm-overview) do Windows para gerenciamento de cota de armazenamento e uma versão modificada e estendida do SQL Server [resource governor](/sql/relational-databases/resource-governor/resource-governor) para implementar a governança de recursos no banco de dados SQL.

A meta de design principal dos pools elásticos é ser econômica. Por esse motivo, o sistema permite intencionalmente que os clientes criem pools _densos_ , que são os pools com o número de bancos de dados que se aproximam ou no máximo permitido, mas com uma alocação moderada de recursos de computação. Pelo mesmo motivo, o sistema não reserva todos os recursos potencialmente necessários para seus processos internos, mas permite o compartilhamento de recursos entre os processos internos e as cargas de trabalho do usuário.

Essa abordagem permite que os clientes usem pools elásticos densos para atingir o desempenho adequado e a maior economia de custos. No entanto, se a carga de trabalho em relação a muitos bancos de dados em um pool denso for suficientemente intensa, a contenção de recursos se tornará significativa. A contenção de recursos reduz o desempenho da carga de trabalho do usuário e pode afetar negativamente os processos internos.

> [!IMPORTANT]
> Em pools densos com muitos bancos de dados ativos, talvez não seja possível aumentar o número de bancos de dados no pool até os máximos documentados para os pools elásticos de [DTU](resource-limits-dtu-elastic-pools.md) e [vCore](resource-limits-vcore-elastic-pools.md) .
>
> O número de bancos de dados que podem ser colocados em pools densos sem causar problemas de desempenho e contenção de recursos depende do número de bancos de dados ativos simultaneamente e do consumo de recursos por cargas de trabalho de usuário em cada banco de dados. Esse número pode mudar ao longo do tempo conforme as cargas de trabalho do usuário são alteradas.

Quando a contenção de recursos ocorre em um pool com pacote denso, os clientes podem escolher uma ou mais das seguintes ações para atenuá-lo:

- Ajuste a carga de trabalho de consulta para reduzir o consumo de recursos ou distribuir o consumo de recursos entre vários bancos de dados ao longo do tempo.
- Reduza a densidade do pool movendo alguns bancos de dados para outro pool ou tornando-os bancos de dados autônomos.
- Escale verticalmente o pool para obter mais recursos.

Para obter sugestões sobre como implementar as duas últimas ações, consulte [recomendações operacionais](#operational-recommendations) mais adiante neste artigo. Reduzir a contenção de recursos beneficia as cargas de trabalho do usuário e os processos internos e permite que o sistema mantenha o nível esperado de serviço de forma confiável.

## <a name="monitoring-resource-consumption"></a>Monitorando o consumo de recursos

Para evitar a degradação do desempenho devido à contenção de recursos, os clientes que usam pools elásticos denso devem monitorar proativamente o consumo de recursos e tomar uma ação oportuna se o aumento da contenção de recursos começar a afetar as cargas de trabalho. O monitoramento contínuo é importante porque o uso de recursos em um pool é alterado ao longo do tempo, devido a alterações na carga de trabalho do usuário, alterações nos volumes de dados e na distribuição, alterações na densidade do pool e alterações no serviço do Azure SQL Database.

O banco de dados SQL do Azure fornece várias métricas que são relevantes para esse tipo de monitoramento. Exceder o valor médio recomendado para cada métrica indica a contenção de recursos no pool e deve ser endereçado usando uma das ações mencionadas anteriormente.

|Nome da métrica|Descrição|Valor médio recomendado|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|Utilização da CPU do processo SQL associado a um pool elástico, conforme medido pelo sistema operacional subjacente. Disponível na exibição de [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) em cada banco de dados e na exibição de [Sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) no `master` banco de dados. Essa métrica também é emitida para Azure Monitor, onde é [nomeada](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `sqlserver_process_core_percent` e pode ser exibida em portal do Azure. Esse valor é o mesmo para todos os bancos de dados no mesmo pool elástico.|Abaixo de 70%. Picos ocasionais de até 90% podem ser aceitáveis.|
|`max_worker_percent`|Utilização de [thread de trabalho]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) . Fornecido para cada banco de dados no pool, bem como para o próprio pool. Há limites diferentes no número de threads de trabalho no nível do banco de dados e no nível do pool, portanto, é recomendável monitorar essa métrica em ambos os níveis. Disponível na exibição de [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) em cada banco de dados e na exibição de [Sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) no `master` banco de dados. Essa métrica também é emitida para Azure Monitor, onde é [nomeada](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `workers_percent` e pode ser exibida em portal do Azure.|Abaixo de 80%. Picos de até 100% causarão tentativas de conexão e consultas para falharem.|
|`avg_data_io_percent`|Utilização de IOPS para e/s física de leitura e gravação. Fornecido para cada banco de dados no pool, bem como para o próprio pool. Há diferentes limites no número de IOPS no nível do banco de dados e no nível do pool, portanto, é recomendável monitorar essa métrica em ambos os níveis. Disponível na exibição de [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) em cada banco de dados e na exibição de [Sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) no `master` banco de dados. Essa métrica também é emitida para Azure Monitor, onde é [nomeada](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `physical_data_read_percent` e pode ser exibida em portal do Azure.|Abaixo de 80%. Picos ocasionais de até 100% podem ser aceitáveis.|
|`avg_log_write_percent`|Utilizações de taxa de transferência para IO de gravação de log de transações. Fornecido para cada banco de dados no pool, bem como para o próprio pool. Há limites diferentes na taxa de transferência do log no nível do banco de dados e no nível do pool, portanto, é recomendável monitorar essa métrica em ambos os níveis. Disponível na exibição de [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) em cada banco de dados e na exibição de [Sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) no `master` banco de dados. Essa métrica também é emitida para Azure Monitor, onde é [nomeada](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `log_write_percent` e pode ser exibida em portal do Azure. Quando essa métrica está próxima de 100%, todas as modificações de banco de dados (instruções INSERT, UPDATE, DELETE, MERGE, SELECT... EM, BULK INSERT, etc.) será mais lento.|Abaixo de 90%. Picos ocasionais de até 100% podem ser aceitáveis.|
|`oom_per_second`|A taxa de erros de OOM (memória insuficiente) em um pool elástico, que é um indicador de pressão de memória. Disponível na exibição [Sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) . Veja [exemplos](#examples) de uma consulta de exemplo para calcular essa métrica.|0|
|`avg_storage_percent`|Espaço de armazenamento total usado pelos dados em todos os bancos de dado em um pool elástico. Não inclui espaço vazio em arquivos de banco de dados. Disponível no modo de exibição de [Sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) no `master` banco de dados. Essa métrica também é emitida para Azure Monitor, onde é [nomeada](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `storage_percent` e pode ser exibida em portal do Azure.|Abaixo de 80%. Pode se aproximar de 100% para pools sem crescimento de dados.|
|`avg_allocated_storage_percent`|Espaço de armazenamento total usado por arquivos de banco de dados no armazenamento em todos os bancos de dados em um pool elástico. Inclui espaço vazio em arquivos de banco de dados. Disponível no modo de exibição de [Sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) no `master` banco de dados. Essa métrica também é emitida para Azure Monitor, onde é [nomeada](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `allocated_data_storage_percent` e pode ser exibida em portal do Azure.|Abaixo de 90%. Pode se aproximar de 100% para pools sem crescimento de dados.|
|`tempdb_log_used_percent`|Utilização do espaço do log de transações no `tempdb` banco de dados. Embora os objetos temporários criados em um banco de dados não fiquem visíveis em outros bancos no mesmo pool elástico, o `tempdb` é um recurso compartilhado para todos os bancos de dados no mesmo pool. Uma transação de longa execução ou órfã no `tempdb` início de um banco de dados no pool pode consumir uma grande parte do log de transações e causar falhas para consultas em outros bancos de dados no mesmo pool. Derivado de exibições [Sys.dm_db_log_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) e [Sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) . Essa métrica também é emitida para Azure Monitor e pode ser exibida em portal do Azure. Veja [exemplos](#examples) de uma consulta de exemplo para retornar o valor atual dessa métrica.|Abaixo de 50%. Picos ocasionais de até 80% são aceitáveis.|
|||

Além dessas métricas, o banco de dados SQL do Azure fornece uma exibição que retorna os limites de governança de recursos reais, bem como exibições adicionais que retornam estatísticas de utilização de recursos no nível do pool de recursos e no nível do grupo de carga de trabalho.

|Nome da exibição|Descrição|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Retorna as configurações de capacidade e configuração reais usadas pelos mecanismos de governança de recursos no banco de dados atual ou no pool elástico.|
|[sys.dm_resource_governor_resource_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Retorna informações sobre o estado atual do pool de recursos, a configuração atual de pools de recursos e estatísticas cumulativas do pool de recursos.|
|[sys.dm_resource_governor_workload_groups](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Retorna estatísticas de grupo de carga de trabalho cumulativa e a configuração atual do grupo de cargas de trabalho. Este modo de exibição pode ser Unido a sys.dm_resource_governor_resource_pools na `pool_id` coluna para obter informações do pool de recursos.|
|[sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Retorna estatísticas de utilização do pool de recursos para os últimos 32 minutos. Cada linha representa um intervalo de 20 segundos. As `delta_` colunas retornam a alteração em cada estatística durante o intervalo.|
|[sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Retorna estatísticas de utilização do grupo de carga de trabalho para os últimos 32 minutos. Cada linha representa um intervalo de 20 segundos. As `delta_` colunas retornam a alteração em cada estatística durante o intervalo.|
|||

Essas exibições podem ser usadas para monitorar a utilização de recursos e solucionar problemas de contenção de recursos quase em tempo real. A carga de trabalho do usuário nas réplicas secundárias primárias e legíveis, incluindo réplicas geográficas, é classificada no `SloSharedPool1` pool de recursos e no `UserPrimaryGroup.DBId[N]` grupo de carga de trabalho, em que `N` significa o valor da ID do banco de dados.

Além de monitorar a utilização de recursos atual, os clientes que usam pools densas podem manter dados históricos de utilização de recursos em um armazenamento de dados separado. Esses dados podem ser usados na análise preditiva para gerenciar proativamente a utilização de recursos com base em tendências históricas e sazonais.

## <a name="operational-recommendations"></a>Recomendações operacionais

**Deixe espaço suficiente no recurso** . Se ocorrer a degradação de recursos e o desempenho, a mitigação poderá envolver a movimentação de alguns bancos de dados do pool elástico afetado ou o dimensionamento do pool, conforme observado anteriormente. No entanto, essas ações exigem recursos de computação adicionais para serem concluídas. Em particular, para os pools Premium e Comercialmente Crítico, essas ações exigem a transferência de todos os dados para os bancos que estão sendo movidos ou para todos os bancos de dado no pool elástico, se o pool for dimensionado verticalmente. A transferência de dados é uma operação demorada e com uso intensivo de recursos. Se o pool já estiver sob alta pressão de recursos, a própria operação de mitigação diminuirá o desempenho ainda mais. Em casos extremos, talvez não seja possível resolver a contenção de recursos por meio da movimentação do banco de dados ou da expansão do pool, pois os recursos necessários não estão disponíveis. Nesse caso, reduzir temporariamente a carga de trabalho de consulta no pool elástico afetado pode ser a única solução.

Os clientes que usam pools densas devem monitorar de maneira minuciosa as tendências de utilização de recursos, conforme descrito anteriormente, e realizar ações de mitigação enquanto as métricas permanecem dentro dos intervalos recomendados e ainda há recursos suficientes no pool elástico.

A utilização de recursos depende de vários fatores que mudam ao longo do tempo para cada banco de dados e cada pool elástico. Obter a taxa ideal de preço/desempenho em pools densos requer monitoramento e rebalanceamento contínuos, que está movendo bancos de dados de pools mais utilizados para pools menos utilizados e criando novos pools conforme necessário para acomodar maior carga de trabalho.

Não **mova bancos de dados "quentes"** . Se a contenção de recursos no nível do pool for causada principalmente por um pequeno número de bancos de dados altamente utilizados, poderá ser tentador mover esses bancos de dados para um pool menos utilizado ou torná-los bancos de dados autônomos. No entanto, fazer isso enquanto um banco de dados permanece altamente utilizado não é recomendado, pois a operação de movimentação diminuirá ainda mais o desempenho, tanto para o banco de dados que está sendo movido quanto para o pool inteiro. Em vez disso, aguarde até que a alta utilização seja sublado ou mova bancos de dados menos utilizados em vez de reduzir a pressão de recursos no nível do pool. Mas mover bancos de dados com pouca utilização não oferece nenhum benefício nesse caso, pois ele não reduz materialmente a utilização de recursos no nível do pool.

**Crie novos bancos de dados em um pool de "quarentena"** . Em cenários em que novos bancos de dados são criados com frequência, como aplicativos que usam o modelo locatário-per-Database, há risco de que um novo banco de dados colocado em um pool elástico existente inesperadamente consumirá recursos significativos e afete outros bancos dados e processos internos no pool. Para atenuar esse risco, crie um pool separado de "quarentena" com uma ampla alocação de recursos. Use este pool para novos bancos de dados com padrões de consumo de recursos ainda desconhecidos. Quando um banco de dados permaneceu nesse pool por um ciclo comercial, como uma semana ou um mês, e seu consumo de recursos é conhecido, ele pode ser movido para um pool com capacidade suficiente para acomodar esse uso de recurso adicional.

**Monitore ambos os espaços usados e alocados** . Quando o espaço do pool alocado (o tamanho total de todos os arquivos de banco de dados no armazenamento de todos os bancos em um pool) atinge o tamanho máximo do pool, podem ocorrer erros de espaço insuficiente. Se as tendências de espaço alocadas estiverem altas e estiver na faixa para alcançar o tamanho máximo do pool, as opções de mitigação incluem:
- Mover alguns bancos de dados para fora do pool para reduzir o espaço alocado total
- [Reduzir arquivos de banco de](file-space-manage.md) dados para reduzir o espaço alocado vazio em arquivos
- Escalar verticalmente o pool para um objetivo de serviço com um tamanho de pool máximo maior

Se o espaço do pool usado (tamanho total dos dados em todos os bancos de dado em um pool, não incluindo o espaço vazio nos arquivos) tiver alta tendência e estiver em faixa para alcançar o tamanho máximo do pool, as opções de mitigação incluem:
- Mover alguns bancos de dados para fora do pool para reduzir o espaço total usado
- Mova (arquive) os dados fora do banco de dados ou exclua os mais não necessários
- Implementar [compactação de dados](/sql/relational-databases/data-compression/data-compression)
- Escalar verticalmente o pool para um objetivo de serviço com um tamanho de pool máximo maior

**Evite servidores excessivamente densos** . O banco de dados SQL do Azure [dá suporte](./resource-limits-logical-server.md) a até 5000 bancos de dados por servidor. Os clientes que usam pools elásticos com milhares de bancos de dados podem considerar a colocação de vários pools elásticos em um único servidor, com o número total de bancos de dados até o limite com suporte. No entanto, os servidores com muitos milhares de bancos de dados criam desafios operacionais. As operações que exigem a enumeração de todos os bancos de dados em um servidor, por exemplo, a exibição de bancos de dados no portal, serão mais lentas. Erros operacionais, como a modificação incorreta de logons no nível do servidor ou regras de firewall, afetarão um número maior de bancos de dados. A exclusão acidental do servidor exigirá ajuda de Suporte da Microsoft recuperar bancos de dados no servidor excluído e causará uma interrupção prolongada para todos os bancos de dados afetados.

É recomendável limitar o número de bancos de dados por servidor a um número menor do que o máximo permitido. Em muitos cenários, o uso de até 1000-2000 bancos de dados por servidor é ideal. Para reduzir a probabilidade de exclusão acidental de servidor, é recomendável colocar um [bloqueio de exclusão](../../azure-resource-manager/management/lock-resources.md) no servidor ou em seu grupo de recursos.

No passado, determinados cenários que envolvem mover bancos de dados para dentro, para fora ou entre pools elásticos no mesmo servidor eram mais rápidos do que ao mover bancos de dados entre servidores. Atualmente, todas as movimentações de banco de dados são executadas na mesma velocidade, independentemente do servidor de origem e de destino.

## <a name="examples"></a>Exemplos

### <a name="monitoring-memory-utilization"></a>Monitorando a utilização de memória

Essa consulta calcula a `oom_per_second` métrica para cada pool de recursos, nos últimos 32 minutos. Essa consulta pode ser executada em qualquer banco de dados em um pool elástico.

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>Monitoramento da `tempdb` utilização do espaço de log

Essa consulta retorna o valor atual da `tempdb_log_used_percent` métrica, mostrando a utilização relativa do log de `tempdb` transações em relação ao tamanho máximo permitido. Essa consulta pode ser executada em qualquer banco de dados em um pool elástico.

```sql
SELECT (lsu.used_log_space_in_bytes / df.log_max_size_bytes) * 100 AS tempdb_log_space_used_percent
FROM tempdb.sys.dm_db_log_space_usage AS lsu
CROSS JOIN (
           SELECT SUM(CAST(max_size AS bigint)) * 8 * 1024. AS log_max_size_bytes
           FROM tempdb.sys.database_files
           WHERE type_desc = N'LOG'
           ) AS df
;
```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução aos pools elásticos, consulte [pools elásticos ajudam você a gerenciar e dimensionar vários bancos de dados no banco de dados SQL do Azure](./elastic-pool-overview.md).
- Para obter informações sobre como ajustar cargas de trabalho de consulta para reduzir a utilização de recursos, consulte [monitoramento e ajuste]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index), [monitoramento e ajuste de desempenho](./monitor-tune-overview.md).