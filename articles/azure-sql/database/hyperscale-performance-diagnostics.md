---
title: Diagnóstico de desempenho em hiperescala
description: Este artigo descreve como solucionar problemas de desempenho de hiperescala no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019 sqldbrb=1
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: ed31ff5d77b258d141a77fc174c2d5452adf7d01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92791708"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnóstico de solução de problemas de desempenho de hiperescala do SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Para solucionar problemas de desempenho em um banco de dados de hiperescala, as [metodologias gerais de ajuste de desempenho](monitor-tune-overview.md) no nó de computação do banco de dados SQL do Azure é o ponto inicial de uma investigação de desempenho. No entanto, considerando a [arquitetura distribuída](service-tier-hyperscale.md#distributed-functions-architecture) de hiperescala, diagnósticos adicionais foram adicionados para auxiliar. Este artigo descreve dados de diagnóstico específicos de hiperescala.

## <a name="log-rate-throttling-waits"></a>Esperas de limitação de taxa de log

Cada nível de serviço do banco de dados SQL do Azure tem limites de taxa de geração de log impostos por meio da [governança de taxa de log](resource-limits-logical-server.md#transaction-log-rate-governance). Em hiperescala, o limite de geração de log está definido atualmente como 100 MB/s, independentemente do nível de serviço. No entanto, há ocasiões em que a taxa de geração de log na réplica de computação primária deve ser limitada para manter os SLAs de recuperação. Essa limitação ocorre quando um [servidor de página ou outra réplica de computação](service-tier-hyperscale.md#distributed-functions-architecture) é significativamente por trás da aplicação de novos registros de log do serviço de log.

Os seguintes tipos de espera (em [Sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) descrevem os motivos pelos quais a taxa de log pode ser limitada na réplica de computação primária:

|Tipo de Espera    |Descrição                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Ocorre quando uma taxa de geração de log do nó de computação primário do banco de dados de hiperescala está sendo limitada devido ao consumo de log atrasado no (s) servidor (es) de página.         |
|RBIO_RG_DESTAGE        | Ocorre quando uma taxa de geração de log do nó de computação do banco de dados de hiperescala está sendo limitada devido ao consumo de log atrasado pelo armazenamento de log de longo prazo.         |
|RBIO_RG_REPLICA        | Ocorre quando uma taxa de geração de log do nó de computação do banco de dados de hiperescala está sendo limitada devido ao consumo de log atrasado pelas réplicas secundárias legíveis.         |
|RBIO_RG_LOCALDESTAGE   | Ocorre quando uma taxa de geração de log do nó de computação do banco de dados de hiperescala está sendo limitada devido ao consumo de log atrasado pelo serviço de log.         |

## <a name="page-server-reads"></a>Leituras de servidor de página

As réplicas de computação não armazenam em cache uma cópia completa do banco de dados localmente. Os dados locais para a réplica de computação são armazenados no pool de buffers (na memória) e no cache RBPEX (extensão de pool de buffers resilientes) que é um cache parcial (não abrangendo) de páginas de dados. Esse cache RBPEX local é dimensionado proporcionalmente ao tamanho da computação e é três vezes a memória da camada de computação. RBPEX é semelhante ao pool de buffers, pois ele tem os dados acessados com mais frequência. Por outro lado, cada servidor de página tem um cache RBPEX de cobertura para a parte do banco de dados que ele mantém.

Quando uma leitura é emitida em uma réplica de computação, se os dados não existirem no pool de buffers ou no cache RBPEX local, uma chamada de função GetPage (PageId, LSN) será emitida e a página será buscada no servidor de páginas correspondente. As leituras de servidores de páginas são leituras remotas e, portanto, são mais lentas do que as leituras do RBPEX local. Ao solucionar problemas de desempenho relacionados a e/s, precisamos saber quantos IOs foram feitos por meio de leituras de servidor de página remota relativamente mais lentas.

Várias DMVs (exibições gerenciadas dinâmicas) e eventos estendidos têm colunas e campos que especificam o número de leituras remotas de um servidor de páginas, que podem ser comparadas com o total de leituras. O repositório de consultas também captura as leituras remotas como parte das estatísticas de tempo de execução de consulta.

- As leituras das colunas para o servidor da página de relatório estão disponíveis em DMVs de execução e exibições de catálogo, como:

  - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
  - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
  - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
  - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
  - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- As leituras de servidor de página são adicionadas aos seguintes eventos estendidos:
  - sql_statement_completed
  - sp_statement_completed
  - sql_batch_completed
  - rpc_completed
  - scan_stopped
  - query_store_begin_persist_runtime_stat
  - store_execution_runtime_info de consulta
- ActualPageServerReads/ActualPageServerReadAheads são adicionados ao XML do plano de consulta para planos reais. Por exemplo:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Para exibir esses atributos na janela Propriedades do plano de consulta, é necessário o SSMS 18,3 ou posterior.

## <a name="virtual-file-stats-and-io-accounting"></a>Estatísticas de arquivo virtual e estatísticas de e/s

No banco de dados SQL do Azure, a Dmf de [Sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) é a principal maneira de monitorar a e/s do banco de dados SQL. As características de e/s em hiperescala são diferentes devido à sua [arquitetura distribuída](service-tier-hyperscale.md#distributed-functions-architecture). Nesta seção, nos concentramos na e/s (leituras e gravações) nos arquivos de dados, como visto nessa DMF. Em hiperescala, cada arquivo de dados visível nessa DMF corresponde a um servidor de página remoto. O cache RBPEX mencionado aqui é um cache local baseado em SSD, que é um cache sem cobertura na réplica de computação.

### <a name="local-rbpex-cache-usage"></a>Uso do cache RBPEX local

O cache RBPEX local existe na réplica de computação, no armazenamento SSD local. Portanto, a e/s nesse cache é mais rápida do que a e/s em servidores de página remota. Atualmente, [Sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) em um banco de dados de hiperescala tem uma linha especial relatando a e/s em relação ao cache RBPEX local na réplica de computação. Essa linha tem o valor de 0 para ambas `database_id` as `file_id` colunas e. Por exemplo, a consulta abaixo retorna estatísticas de uso de RBPEX desde a inicialização do banco de dados.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Uma taxa de leituras feitas em RBPEX para leituras agregadas feitas em todos os outros arquivos de dados fornece a taxa de acertos do cache RBPEX.

### <a name="data-reads"></a>Leituras de dados

- Quando as leituras são emitidas pelo mecanismo de banco de dados SQL Server em uma réplica de computação, elas podem ser servidas pelo cache RBPEX local ou por servidores de página remota, ou por uma combinação dos dois se estiver lendo várias páginas.
- Quando a réplica de computação lê algumas páginas de um arquivo específico, por exemplo file_id 1, se esses dados residem exclusivamente no cache RBPEX local, toda a e/s para essa leitura é contabilizada em file_id 0 (RBPEX). Se alguma parte desses dados estiver no cache RBPEX local e alguma parte estiver em um servidor de página remoto, a e/s será contabilizada em relação a file_id 0 para a parte servida de RBPEX, e a parte servida do servidor de página remoto será contabilizada em direção a file_id 1.
- Quando uma réplica de computação solicitar uma página em um determinado [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) de um servidor de página, se o servidor de página não tiver sido detectado até o LSN solicitado, a leitura na réplica de computação aguardará até que o servidor de página seja exibido antes que a página seja retornada para a réplica de computação. Para qualquer leitura de um servidor de página na réplica de computação, você verá o tipo de espera PAGEIOLATCH_ * se ele estiver aguardando nessa e/s. Em hiperescala, esse tempo de espera inclui o tempo para acompanhar a página solicitada no servidor de página para o LSN necessário e o tempo necessário para transferir a página do servidor de página para a réplica de computação.
- Leituras grandes, como Read-Ahead, geralmente são feitas usando [leituras de "dispersão"](/sql/relational-databases/reading-pages/). Isso permite leituras de até 4 MB de páginas por vez, consideradas uma única leitura no mecanismo de banco de dados SQL Server. No entanto, quando os dados que estão sendo lidos estão em RBPEX, essas leituras são contadas como várias leituras individuais de 8 KB, pois o pool de buffers e o RBPEX sempre usam páginas de 8 KB. Como resultado, o número de IOs de leitura visto em relação a RBPEX pode ser maior do que o número real de IOs executado pelo mecanismo.

### <a name="data-writes"></a>Gravações de dados

- A réplica de computação primária não grava diretamente em servidores de página. Em vez disso, os registros de log do serviço de log são reproduzidos nos servidores de página correspondentes.
- As gravações que acontecem na réplica de computação são, predominantemente, gravações no RBPEX local (file_id 0). Para gravações em arquivos lógicos maiores que 8 KB, em outras palavras aquelas feitas usando o [gather-Write](/sql/relational-databases/writing-pages/), cada operação de gravação é convertida em várias gravações individuais de 8 KB em RBPEX, já que o pool de buffers e RBPEX sempre usam páginas de 8 KB. Como resultado, o número de IOs de gravação visto em relação a RBPEX pode ser maior do que o número real de IOs executado pelo mecanismo.
- Arquivos não RBPEX ou arquivos de dados diferentes de file_id 0 que correspondem a servidores de página, também mostram gravações. Na camada de serviço de hiperescala, essas gravações são simuladas, pois as réplicas de computação nunca gravam diretamente em servidores de página. O IOPS de gravação e a taxa de transferência são contabilizados à medida que ocorrem na réplica de computação, mas a latência de arquivos de dados diferentes de file_id 0 não reflete a latência real das gravações do servidor de página.

### <a name="log-writes"></a>Gravações de log

- Na computação primária, uma gravação de log é contabilizada no file_id 2 de sys.dm_io_virtual_file_stats. Uma gravação de log na computação principal é uma gravação na zona de aterrissagem de log.
- Os registros de log não são protegidos na réplica secundária em uma confirmação. Em hiperescala, o log é aplicado pelo serviço de log para as réplicas secundárias de forma assíncrona. Como as gravações de log não ocorrem na verdade em réplicas secundárias, todas as estatísticas de log IOs nas réplicas secundárias são apenas para fins de acompanhamento.

## <a name="data-io-in-resource-utilization-statistics"></a>E/s de dados nas estatísticas de utilização de recursos

Em um banco de dados que não é de hiperescala, os IOPS de leitura e gravação combinados em relação aos arquivos de dado, em relação ao limite de IOPS de dados de [governança de recursos](./resource-limits-logical-server.md#resource-governance) , são relatados nas exibições [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [Sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) na `avg_data_io_percent` coluna. O mesmo valor é relatado no portal do Azure como _porcentagem de e/s de dados_.

Em um banco de dados de hiperescala, essa coluna relata a utilização de IOPS de dados em relação ao limite do armazenamento local somente na réplica de computação, especificamente a e/s em relação a RBPEX e `tempdb` . Um valor de 100% nessa coluna indica que a governança de recursos está limitando o IOPS de armazenamento local. Se isso estiver correlacionado a um problema de desempenho, ajuste a carga de trabalho para gerar menos e/s ou aumente o objetivo do serviço de banco de dados para aumentar o [limite](resource-limits-vcore-single-databases.md) _máximo de IOPS de data_ de governança de recursos. Para a governança de recursos de leituras e gravações de RBPEX, o sistema conta o IOs de 8 KB individual, em vez de um IOs maior que pode ser emitido pelo mecanismo de banco de dados SQL Server.

A e/s de dados em servidores de página remota não é relatada nas exibições de utilização de recursos ou no portal, mas é relatada na Dmf [Sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) , conforme observado anteriormente.

## <a name="additional-resources"></a>Recursos adicionais

- Para limites de recursos vCore para um banco de dados individual de hiperescala, consulte [limites vCore da camada de serviço de hiperescala](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)
- Para ajuste de desempenho do banco de dados SQL do Azure, consulte [desempenho de consulta no banco de dados SQL do Azure](performance-guidance.md)
- Para ajuste de desempenho usando Repositório de Consultas, consulte [monitoramento de desempenho usando o repositório de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- Para scripts de monitoramento DMV, consulte [monitorando o desempenho do banco de dados SQL do Azure usando exibições de gerenciamento dinâmico](monitoring-with-dmvs.md)