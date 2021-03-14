---
title: Limites de recursos para servidores lógicos no Azure
description: Este artigo fornece uma visão geral dos limites de recursos para o servidor lógico no Azure usado pelo banco de dados SQL do Azure e pelo Azure Synapse Analytics. Também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou excedidos.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 02/02/2021
ms.openlocfilehash: 34613633b6b27fc3387e6a9fa63caf4a194ba963
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691222"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Limites de recursos para servidores do banco de dados SQL do Azure e do Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo fornece uma visão geral dos limites de recursos para o servidor lógico usado pelo banco de dados SQL do Azure e pelo Azure Synapse Analytics. Ele fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou ultrapassados e descreve os mecanismos de governança de recursos usados para impor esses limites.

> [!NOTE]
> Para limites de Instância Gerenciada do SQL do Azure, consulte [limites de recursos do banco de dados SQL para instâncias gerenciadas](../managed-instance/resource-limits.md).

## <a name="maximum-resource-limits"></a>Limites máximos de recursos

| Recurso | Limite |
| :--- | :--- |
| Bancos de dados por servidor | 5.000 |
| Número padrão de servidores por assinatura por região | 20 |
| Número máximo padrão de servidores por assinatura por região | 200 |  
| DTU / cota de eDTU por servidor | 54.000 |  
| Cota de vCore por servidor/instância | 540 |
| Pools de máx por servidor | Limitado pelo número de DTUs ou vCores. Por exemplo, se cada pool tiver 1.000 DTUs, um servidor poderá dar suporte a 54 pools.|
|||

> [!IMPORTANT]
> Conforme o número de bancos de dados vai se aproximando do limite por servidor, pode ocorrer o seguinte:
>
> - Aumento de latência nas consultas em execução no banco de dados mestre.  Isso inclui modos de exibição de estatísticas de utilização de recursos, como sys.resource_stats.
> - Aumento de latência nas operações de gerenciamento e pontos de vista do portais de renderização que envolvem a enumeração de bancos de dados no servidor.

> [!NOTE]
> Para obter mais cota de DTU/eDTU, cota de vCore ou mais servidores do que o valor padrão, envie uma nova solicitação de suporte no portal do Azure. Para obter mais informações, consulte [aumentos de cota de solicitação para o banco de dados SQL do Azure](quota-increase-request.md).

### <a name="storage-size"></a>Tamanho de armazenamento

Para tamanhos de armazenamento de recursos de bancos de dados únicos, consulte [limites de recursos baseados em DTU](resource-limits-dtu-single-databases.md) ou limites de [recursos baseados em vCore](resource-limits-vcore-single-databases.md) para os limites de tamanho de armazenamento por tipo de preço.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>O que acontece quando os limites de recursos do banco de dados são atingidos

### <a name="compute-cpu"></a>CPU de computação

Quando a utilização de CPU de computação de banco de dados se torna alta, a latência de consulta aumenta e as consultas podem até atingir o tempo limite. Sob essas condições, as consultas podem ser enfileiradas pelo serviço e são fornecidas recursos para execução à medida que os recursos são liberados.
Ao encontrar uma utilização alta de computação, as opções de atenuação incluem:

- Aumentar o tamanho da computação do banco de dados ou do pool elástico a fim de fornecer ao banco de dados mais recursos de computação. Consulte [limites de recursos do banco de dados individual em escala](single-database-scale.md) e [limites de recursos do pool elástico](elastic-pool-scale.md).
- Otimização de consultas para reduzir a utilização de recursos de CPU de cada consulta. Para saber mais, consulte [Ajuste/Dicas de consulta](performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Armazenamento

Quando o espaço usado do banco de dados atingir o limite de tamanho máximo, inserções e atualizações do banco de dados que aumentam o tamanho dos dados falharão, e os clientes receberão uma [mensagem de erro](troubleshoot-common-errors-issues.md). As instruções SELECT e DELETE continuam a ter sucesso.

Ao encontrar uma utilização alta de espaço, as opções de atenuação incluem:

- Aumentar o tamanho máximo do banco de dados ou do pool elástico ou adicionar mais armazenamento. Consulte [limites de recursos do banco de dados individual em escala](single-database-scale.md) e [limites de recursos do pool elástico](elastic-pool-scale.md).
- Se o banco de dados estiver em um pool elástico, como alternativa, o banco de dados poderá ser movido para fora do pool para que seu espaço de armazenamento não seja compartilhado com outros bancos de dados.
- Encolher um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](file-space-manage.md).
- Verifique se a utilização de espaço alta é devido a um pico no tamanho do repositório de versão persistente (PVS). O PVS é uma parte de cada banco de dados e é usado para implementar a  [recuperação de banco de dados acelerada](../accelerated-database-recovery.md). Para determinar o tamanho atual do PVS, consulte [solução de problemas do PVS](/sql/relational-databases/accelerated-database-recovery-management#troubleshooting). Um motivo comum para o tamanho de PVS grande é uma transação aberta por um longo tempo (horas), impedindo a limpeza de versões mais antigas em PVS.

### <a name="sessions-and-workers-requests"></a>Sessões e trabalhos (solicitações)

Os números máximos de sessões e trabalhos são determinados pela camada de serviço e pelo tamanho da computação (DTUs/eDTUs ou vCores). Novas solicitações serão rejeitadas quando os limites de sessão ou de trabalho forem atingidos, e os clientes receberão uma mensagem de erro. Embora o número de conexões disponíveis possa ser controlado pelo aplicativo, o número de trabalhos simultâneos costuma ser mais difícil de ser estimado e controlado. Isso é especialmente verdadeiro durante períodos de pico de carga quando os limites de recursos do banco de dados são atingidos e os trabalhadores se acumulam devido a consultas em execução mais longas, cadeias de bloqueio grandes ou paralelismo de consulta excessivo.

Ao encontrar uma utilização alta de sessão ou trabalho, as opções de atenuação incluem:

- Aumentar a camada de serviço ou o tamanho da computação do banco de dados ou do pool elástico. Consulte [limites de recursos do banco de dados individual em escala](single-database-scale.md) e [limites de recursos do pool elástico](elastic-pool-scale.md).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta se a causa do aumento da utilização de trabalho for devido à contenção de recursos de computação. Para saber mais, consulte [Ajuste/Dicas de consulta](performance-guidance.md#query-tuning-and-hinting).
- Reduzindo a configuração [MAXDOP](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (grau máximo de paralelismo).
- Otimizar a carga de trabalho de consulta para reduzir o número de ocorrências e a duração do bloqueio de consulta. Para obter mais informações, consulte [entender e resolver problemas de bloqueio do SQL do Azure](understand-resolve-blocking.md).

### <a name="memory"></a>Memória

Ao contrário de outros recursos (CPU, trabalhos, armazenamento), atingir o limite de memória não afeta negativamente o desempenho da consulta e não causa erros e falhas. Conforme descrito em detalhes no [Guia de arquitetura de gerenciamento de memória](/sql/relational-databases/memory-management-architecture-guide), o mecanismo de banco de dados do SQL Server geralmente usa toda a memória disponível, por design. A memória é usada principalmente para armazenar dados em cache, para evitar acesso mais caro ao armazenamento. Portanto, a utilização de memória mais alta geralmente melhora o desempenho da consulta devido a leituras mais rápidas da memória, em vez de leituras mais lentas do armazenamento.

Após a inicialização do mecanismo de banco de dados, à medida que a carga de trabalho começa a ler dados do armazenamento, o mecanismo de banco de dados armazena agressivamente o cache em memória. Após esse período inicial de rampa, é comum e espera-se que as `avg_memory_usage_percent` `avg_instance_memory_percent` colunas e no [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) sejam próximas ou iguais a 100%, especialmente para bancos de dados que não estão ociosos e não se encaixam totalmente na memória.

Além do cache de dados, a memória é usada em outros componentes do mecanismo de banco de dados. Quando houver demanda de memória e toda a memória disponível for usada pelo cache de dados, o mecanismo de banco de dados reduzirá dinamicamente o tamanho do cache para disponibilizar a memória para outros componentes e aumentará dinamicamente o cache de dados quando outros componentes liberarem memória.

Em casos raros, uma carga de trabalho suficientemente exigente pode causar uma condição de memória insuficiente, levando a erros de falta de memória. Isso pode acontecer em qualquer nível de utilização de memória entre 0% e 100%. É mais provável que isso ocorra em tamanhos de computação menores que tenham limites de memória proporcionalmente menores e/ou com cargas de trabalho usando mais memória para processamento de consulta, como em [pools elásticos densos](elastic-pool-resource-management.md).

Ao encontrar erros de memória insuficiente, as opções de mitigação incluem:
- Aumentar a camada de serviço ou o tamanho da computação do banco de dados ou do pool elástico. Consulte [limites de recursos do banco de dados individual em escala](single-database-scale.md) e [limites de recursos do pool elástico](elastic-pool-scale.md).
- Otimização de consultas e configuração para reduzir a utilização de memória. Soluções comuns são descritas na tabela a seguir.

|Solução|Descrição|
| :----- | :----- |
|Reduzir o tamanho das concessões de memória|Para obter mais informações sobre concessões de memória, consulte a postagem do blog [Understanding SQL Server Memory Grant](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595) . Uma solução comum para evitar concessões de memória excessivamente grandes é manter as [estatísticas](/sql/relational-databases/statistics/statistics) atualizadas. Isso resulta em estimativas mais precisas de consumo de memória pelo mecanismo de consulta, evitando concessões de memória desnecessariamente grandes.</br></br>Em bancos de dados que usam o nível de compatibilidade 140 e posteriores, o mecanismo de Database pode ajustar automaticamente o tamanho de concessão de memória usando os [comentários de concessão de memória do modo de lote](/sql/relational-databases/performance/intelligent-query-processing#batch-mode-memory-grant-feedback). Em bancos de dados usando o nível de compatibilidade 150 e posterior, o mecanismo de banco de dados usa de forma semelhante os [comentários de concessão de memória de modo de linha](/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback), para consultas de modo de linha mais comuns. Essa funcionalidade interna ajuda a evitar erros de memória insuficiente devido a concessões de memória desnecessariamente grandes.|
|Reduzir o tamanho do cache do plano de consulta|O mecanismo de banco de dados armazena em cache os planos de consulta na memória, para evitar a compilação de um plano de consulta para cada execução de consulta. Para evitar o inchar de cache do plano de consulta causado por planos de cache que são usados apenas uma vez, habilite a configuração OPTIMIZE_FOR_AD_HOC_WORKLOADS no [escopo do banco de dados](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).|
|Reduzir o tamanho da memória de bloqueio|O mecanismo de banco de dados usa memória para [bloqueios](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine). Quando possível, evite transações grandes que possam adquirir um grande número de bloqueios e causar alto consumo de memória de bloqueio.|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Consumo de recursos por cargas de trabalho do usuário e processos internos

O consumo de CPU e de memória por cargas de trabalho de usuário em cada banco de dados é relatado nas exibições de [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [Sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) , nas `avg_cpu_percent` `avg_memory_usage_percent` colunas e. Para pools elásticos, o consumo de recursos no nível do pool é relatado na exibição [Sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) . O consumo de CPU da carga de trabalho do usuário também é relatado por meio da `cpu_percent` métrica de Azure monitor, para [bancos de dados individuais](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) e [pools elásticos](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) no nível do pool.

O banco de dados SQL do Azure requer recursos de computação para implementar recursos de serviço principais, como alta disponibilidade e recuperação de desastre, backup e restauração de banco de dados, monitoramento, Repositório de Consultas, ajuste automático, etc. O sistema separa uma determinada parte limitada dos recursos gerais para esses processos internos usando mecanismos de [governança de recursos](#resource-governance) , tornando os demais recursos disponíveis para cargas de trabalho do usuário. Às vezes, quando os processos internos não estão usando recursos de computação, o sistema os torna disponíveis para cargas de trabalho do usuário.

O consumo total de CPU e memória por cargas de trabalho do usuário e processos internos é relatado nas exibições [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [Sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) , nas `avg_instance_cpu_percent` `avg_instance_memory_percent` colunas e. Esses dados também são relatados por meio das `sqlserver_process_core_percent` `sqlserver_process_memory_percent` métricas e Azure monitor, para [bancos de dados individuais](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) e [pools elásticos](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) no nível do pool.

Uma análise mais detalhada do consumo de recursos recente por cargas de trabalho do usuário e processos internos é relatada nas exibições [Sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) e [Sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) . Para obter detalhes sobre pools de recursos e grupos de carga de trabalho referenciados nessas exibições, consulte [governança de recursos](#resource-governance). Essas exibições relatam sobre a utilização de recursos por cargas de trabalho do usuário e processos internos específicos nos pools de recursos e grupos de carga de trabalho associados.

No contexto do monitoramento de desempenho e solução de problemas, é importante considerar o **consumo de CPU do usuário** ( `avg_cpu_percent` , `cpu_percent` ) e o **consumo total de CPU** por cargas de trabalho do usuário e processos internos ( `avg_instance_cpu_percent` , `sqlserver_process_core_percent` ).

O **consumo de CPU do usuário** é calculado como uma porcentagem dos limites de carga de trabalho do usuário em cada objetivo de serviço. A **utilização de CPU do usuário** em 100% indica que a carga de trabalho do usuário atingiu o limite do objetivo do serviço. No entanto, quando o **consumo total da CPU** atinge o intervalo de 70-100%, é possível ver o aumento da taxa de transferência de carga de trabalho do usuário e aumentar a latência de consulta, mesmo que o **consumo de CPU do usuário** relatado permaneça significativamente abaixo de 100%. Isso é mais provável de ocorrer ao usar objetivos de serviço menores com uma alocação moderada de recursos de computação, mas cargas de trabalho de usuário relativamente intensa, como em [pools elásticos denso](elastic-pool-resource-management.md). Isso também pode ocorrer com objetivos de serviço menores quando processos internos exigem recursos adicionais temporariamente, por exemplo, ao criar uma nova réplica do banco de dados.

Quando o **consumo total de CPU** é alto, as opções de mitigação são as mesmas mencionadas anteriormente e incluem aumento de objetivo de serviço e/ou otimização de carga de trabalho do usuário.

## <a name="resource-governance"></a>Governança de recursos

Para impor limites de recursos, o banco de dados SQL do Azure usa uma implementação de governança de recursos baseada em SQL Server [resource governor](/sql/relational-databases/resource-governor/resource-governor), modificado e estendido para execução no banco de dados SQL do Azure. No banco de dados SQL, vários [pools de recursos](/sql/relational-databases/resource-governor/resource-governor-resource-pool) e [grupos de carga de trabalho](/sql/relational-databases/resource-governor/resource-governor-workload-group), com limites de recursos definidos no pool e nos níveis de grupo, fornecem um banco de [dados como serviço equilibrado](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). Carga de trabalho do usuário e cargas de trabalho internas são classificadas em pools de recursos e grupos de carga de trabalho separados. A carga de trabalho do usuário nas réplicas secundárias primárias e legíveis, incluindo réplicas geográficas, é classificada no `SloSharedPool1` pool de recursos e no `UserPrimaryGroup.DBId[N]` grupo de carga de trabalho, em que `N` significa o valor da ID do banco de dados. Além disso, há vários pools de recursos e grupos de carga de trabalho para várias cargas de trabalho internas.

Além de usar Resource Governor para controlar os recursos no processo SQL, o banco de dados SQL do Azure também usa [objetos de trabalho](/windows/win32/procthread/job-objects) do Windows para a governança de recursos de nível de processo e o [FSRM (Gerenciador de recursos de servidor de arquivos)](/windows-server/storage/fsrm/fsrm-overview) do Windows para gerenciamento de cota de armazenamento.

A governança de recursos do banco de dados SQL do Azure é hierárquica por natureza. De cima para baixo, os limites são impostos no nível do sistema operacional e no nível do volume de armazenamento usando mecanismos de governança de recursos do sistema operacional e Resource Governor, em seguida, no nível do pool de recursos usando Resource Governor e, em seguida, no nível do grupo de carga de trabalho usando Resource Governor. Os limites de governança de recursos em vigor para o banco de dados atual ou pool elástico são exibidos na exibição de [Sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) .

### <a name="data-io-governance"></a>Governança de e/s de dados

A governança de e/s de dados é um processo no banco de dados SQL do Azure usado para limitar a e/s física de leitura e gravação em arquivos de data de um banco. Os limites de IOPS são definidos para cada nível de serviço para minimizar o efeito de "vizinho ruidosa", para fornecer a integridade de alocação de recursos no serviço multilocatário e para permanecer dentro dos recursos do hardware e do armazenamento subjacentes.

Para bancos de dados individuais, os limites de grupo de carga de trabalho são aplicados a todas as e/s de armazenamento em relação ao banco de dados, enquanto os limites do pool de recursos se aplicam a todas as e/s de armazenamento em todos os bancos no mesmo pool SQL dedicado, `tempdb` incluindo o Para pools elásticos, os limites de grupo de carga de trabalho se aplicam a cada banco de dados no pool, enquanto o limite do pool de recursos se aplica a todo o pool elástico, incluindo o `tempdb` banco de dados, que é compartilhado entre todos os bancos de dados no pool. Em geral, os limites do pool de recursos podem não ser obtidos pela carga de trabalho em relação a um banco de dados (único ou em pool), pois os limites do grupo de carga de trabalho são menores do que os limites do pool de recursos e limitam o IOPS/taxa No entanto, os limites de pool podem ser alcançados pela carga de trabalho combinada em vários bancos de dados no mesmo pool.

Por exemplo, se uma consulta gerar 1000 IOPS sem nenhuma governança de recursos de e/s, mas o limite máximo de IOPS do grupo de carga de trabalho for definido como 900 IOPS, a consulta não poderá gerar mais de 900 IOPS. No entanto, se o limite máximo de IOPS do pool de recursos for definido como 1500 IOPS e a e/s total de todos os grupos de carga de trabalho associados ao pool de recursos exceder 1500 IOPS, a e/s da mesma consulta poderá ser reduzida abaixo do limite do grupo de trabalhos de 900 IOPS.

Os valores mínimo/máximo de IOPS e taxa de transferência retornados pela exibição de [Sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) atuam como limites/Caps, não como garantias. Além disso, a governança de recursos não garante nenhuma latência de armazenamento específica. A melhor latência, IOPS e taxa de transferência atingíveis para uma determinada carga de trabalho de usuário dependem não apenas dos limites de governança de recursos de e/s, mas também da combinação de tamanhos de e/s usados e dos recursos do armazenamento subjacente. O banco de dados SQL usa IOs que varia de tamanho entre 512 KB e 4 MB. Para fins de imposição de limites de IOPS, cada e/s é contabilizada independentemente de seu tamanho, com a exceção de bancos de dados com arquivos de data no armazenamento do Azure. Nesse caso, IOs com mais de 256 KB são contabilizados como vários IOs de 256 KB, para alinhar com a contabilidade de e/s do armazenamento do Azure.

Para bancos de dados Basic, Standard e Uso Geral, que usam arquivos de dado no armazenamento do Azure, o `primary_group_max_io` valor pode não ser atingível se um banco de dados não tiver arquivos de dado suficientes para fornecer esse número de IOPS, ou se os dados não forem distribuídos uniformemente entre arquivos ou se o nível de desempenho dos BLOBs subjacentes limitar IOPS/taxa de transferência abaixo dos limites de governança de recursos Da mesma forma, com o IOs de log pequeno gerado por confirmações de transação frequentes, o `primary_max_log_rate` valor pode não ser atingível por uma carga de trabalho devido ao limite de IOPS no blob de armazenamento do Azure subjacente. Para bancos de dados que usam o armazenamento Premium do Azure, o banco de dados SQL do Azure usa blobs de armazenamento suficientemente grandes para obter IOPS/taxa de transferência necessários, independentemente do tamanho do banco de dados. Para bancos de dados maiores, vários arquivos de data são criados para aumentar a capacidade total de IOPS/taxa de transferência.

Os valores de utilização de recursos, como `avg_data_io_percent` e `avg_log_write_percent` , relatados nas exibições [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database),  [Sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)e [Sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) , são calculados como porcentagens de limites máximos de governança de recursos. Portanto, quando fatores diferentes da governança de recursos limitam o IOPS/taxa de transferência, é possível ver o nivelamento de IOPS/taxa de transferência e as latências aumentando à medida que a carga de trabalho aumenta, embora a utilização de recursos relatada permaneça abaixo de 100%.

Para ver a leitura e gravação de IOPS, taxa de transferência e latência por arquivo de banco de dados, use a função [Sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) . Essa função mostra todas as e/s no banco de dados, incluindo a e/s de segundo plano que não é contabilizada `avg_data_io_percent` , mas usa IOPS e taxa de transferência do armazenamento subjacente e pode afetar a latência de armazenamento observada. A função superfícies latência adicional que pode ser introduzida pela governança de recursos de e/s para leituras e gravações, nas `io_stall_queued_read_ms` colunas e, `io_stall_queued_write_ms` respectivamente.

### <a name="transaction-log-rate-governance"></a>Governança de taxa de log de transações

A governança de taxa do log de transações é um processo no banco de dados SQL do Azure usado para limitar altas taxas de ingestão para cargas de trabalho, como inserção em massa, seleção INTO e compilações de índice. Esses limites são rastreados e aplicados no nível de subsegundos à taxa de geração de registro de log, limitando a taxa de transferência, independentemente de quantos IOs podem ser emitidos em relação aos arquivos de dados.  As taxas de geração de log de transações atualmente são dimensionadas linearmente até um ponto que é dependente de hardware, com a taxa de log máxima permitida de 96 MB/s com o modelo de compra vCore.

> [!NOTE]
> O IOs físico real para os arquivos de log de transações não são governados ou limitados.

As taxas de log são definidas de modo que elas possam ser alcançadas e mantidas em vários cenários, enquanto o sistema geral pode manter sua funcionalidade com impacto minimizado na carga do usuário. A governança de taxa de log garante que os backups de log de transações permaneçam dentro dos SLAs de recuperação publicados.  Essa governança também impede uma pendência excessiva em réplicas secundárias.

À medida que os registros de log são gerados, cada operação é avaliada e avaliada se deve ser atrasada para manter uma taxa máxima de log desejada (MB/s por segundo). Os atrasos não são adicionados quando os registros de log são liberados para armazenamento, em vez disso, a governança de taxa de log é aplicada durante a própria geração de taxa de log.

As taxas de geração de log reais impostas em tempo de execução também podem ser influenciadas por mecanismos de comentários, reduzindo temporariamente as taxas de log permitidas para que o sistema possa se estabilizar. Gerenciamento de espaço de arquivo de log, evitando a execução de condições de espaço de log e os mecanismos de replicação de grupo de disponibilidade podem diminuir temporariamente os limites gerais do sistema.

A modelagem de tráfego do administrador da taxa de log é apresentada por meio dos seguintes tipos de espera (expostos nas exibições [Sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [Sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) ):

| Tipo de Espera | Observações |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitação de banco de dados |
| POOL_LOG_RATE_GOVERNOR | Limitação de pool |
| INSTANCE_LOG_RATE_GOVERNOR | Limitação de nível de instância |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Controle de comentários, replicação física do grupo de disponibilidade em Premium/Comercialmente Crítico não está acompanhando |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Controle de comentários, limitando as taxas para evitar uma condição de espaço de log insuficiente |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Controle de comentários de replicação geográfica, limitando a taxa de log para evitar alta latência de dados e indisponibilidade de secundários geográficos|
|||

Ao encontrar um limite de taxa de log que está atrasando a escalabilidade desejada, considere as seguintes opções:

- Escale verticalmente para um nível de serviço superior para obter a taxa máxima de logs de 96 MB/s ou alterne para uma camada de serviço diferente. A camada de serviço de [hiperescala](service-tier-hyperscale.md) fornece a taxa de log de 100 MB/s, independentemente do nível de serviço escolhido.
- Se os dados que estão sendo carregados forem transitórios, como dados de preparo em um processo de ETL, eles poderão ser carregados em tempdb (que é minimamente registrado).
- Para cenários analíticos, carregue em uma tabela coberta por columnstore clusterizado. Isso reduz a taxa de log necessária devido à compactação. Essa técnica aumenta a utilização da CPU e só é aplicável a conjuntos de dados que se beneficiam de índices columnstore clusterizados.

### <a name="storage-space-governance"></a>Governança de espaço de armazenamento

Em camadas de serviço Premium e Comercialmente Crítico, os arquivos de log de dados e de transações são armazenados no volume SSD local do computador que hospeda o banco de dados ou pool elástico. Isso fornece alta IOPS e taxa de transferência e baixa latência de e/s. O tamanho desse volume local depende dos recursos de hardware e é finito. Em um determinado computador, o espaço do volume local é consumido por bancos de dados de clientes, incluindo `tempdb` o sistema operacional, o software de gerenciamento, o monitoramento de data, os logs etc. À medida que os bancos de dados são criados, excluídos e aumentam/diminuem o uso do espaço, o consumo de espaço local em um computador flutua ao longo do tempo. 

Se o sistema detectar que o espaço livre disponível em um computador está baixo e um banco de dados ou pool elástico estiver em risco de ficar sem espaço, ele moverá o banco de dados ou o pool elástico para um computador diferente com espaço livre suficiente, permitindo o crescimento até os limites de tamanho máximo do objetivo de serviço configurado. Essa mudança ocorre de modo online, de forma semelhante a uma operação de dimensionamento de banco de dados, e tem um [impacto](single-database-scale.md#impact)semelhante, incluindo um failover curto (segundos) no final da operação. Esse failover encerra as conexões abertas e reverte as transações, potencialmente afetando os aplicativos que usam o banco de dados naquele momento.

Como os dados são fisicamente copiados em um computador diferente, mover bancos de dados maiores pode exigir uma quantidade significativa de tempo. Durante esse tempo, se o consumo de espaço local por um grande banco de dados de usuário ou pool elástico, ou pelo `tempdb` banco de dados aumentar muito rapidamente, o risco de ficar sem espaço aumenta. O sistema inicia a movimentação do banco de dados de forma balanceada para evitar erros de indisponibilidade de espaço e para evitar failovers desnecessários.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Para saber mais sobre DTUs e eDTUs, confira [DTUs e eDTUs](purchasing-models.md#dtu-based-purchasing-model).
- Para obter informações sobre limites de tamanho de tempdb, consulte [TempDB no Banco de Dados SQL do Azure](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).