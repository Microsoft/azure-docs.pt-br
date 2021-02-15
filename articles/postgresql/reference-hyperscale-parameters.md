---
title: Parâmetros do servidor – Citus (hiperescala) – banco de dados do Azure para PostgreSQL
description: Parâmetros na API do SQL de hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 07f966c7b0be542f848f1a0a4eaf2b5549735b4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336234"
---
# <a name="server-parameters"></a>Parâmetros do Servidor

Há vários parâmetros de servidor que afetam o comportamento de Citus (hiperescala), tanto do PostgreSQL padrão, quanto do Citus (alscale).
Esses parâmetros podem ser definidos no portal do Azure para um grupo de servidores Citus (hiperescala). Na categoria **configurações** , escolha **parâmetros de nó de trabalho** ou parâmetros de nó de **Coordenador**. Essas páginas permitem que você defina parâmetros para todos os nós de trabalho ou apenas para o nó de coordenador.

## <a name="hyperscale-citus-parameters"></a>Parâmetros de hiperescala (Citus)

> [!NOTE]
>
> Os grupos de servidores de hiperescala (Citus) que executam versões mais antigas do mecanismo de Citus podem não oferecer todos os parâmetros listados abaixo.

### <a name="general-configuration"></a>Configuração geral

#### <a name="citususe_secondary_nodes-enum"></a>citus. use \_ os \_ nós secundários (enum)

Define a política a ser usada ao escolher nós para consultas SELECT. Se estiver definido como ' Always ', o planejador consultará somente os nós marcados como ' secundários ' noderole em [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

Os valores com suporte para essa enumeração são:

-   **nunca:** (padrão) todas as leituras acontecem em nós primários.
-   **sempre:** As leituras são executadas em nós secundários e, em vez disso, as instruções INSERT/UPDATE são desabilitadas.

#### <a name="cituscluster_name-text"></a>citus. \_ nome do cluster (texto)

Informa ao planejador de nós de coordenador qual cluster ele coordena. Depois que \_ o nome do cluster for definido, o planejador consultará os nós de trabalho nesse cluster sozinha.

#### <a name="citusenable_version_checks-boolean"></a>citus. habilitar \_ \_ verificações de versão (booliano)

Atualizar a versão do Citus (hiperescala) requer uma reinicialização do servidor (para selecionar a nova biblioteca compartilhada), seguida pelo comando ALTER EXTENSION UPDATE.  A falha na execução de ambas as etapas pode causar erros ou falhas.
O Citus (hiperescala) valida, assim, a versão do código e a correspondência da extensão e os erros se não tiverem \' .

Esse valor padrão é true e é efetivo no coordenador. Em casos raros, processos complexos de atualização podem exigir a definição desse parâmetro como false, desabilitando assim a verificação.

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus. log \_ Distributed \_ deadlock \_ Detection (booliano)

Se é para registrar em log o processamento relacionado à detecção de deadlocks no log do servidor. O padrão é false.

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus. \_ \_ \_ fator de detecção de deadlock distribuído (ponto flutuante)

Define o tempo de espera antes de verificar se há deadlocks distribuídos. Em particular, o tempo de espera será esse valor multiplicado pela \' configuração de [ \_ tempo limite de deadlock](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) de PostgreSQL s. O valor padrão é `2`. Um valor `-1` desabilita A detecção de deadlock distribuído.

#### <a name="citusnode_connection_timeout-integer"></a>\_ \_ tempo limite de conexão de citus. Node (inteiro)

O `citus.node_connection_timeout` GUC define a duração máxima (em milissegundos) para aguardar o estabelecimento da conexão. O Citus (hiperescala) gerará um erro se o tempo limite expirar antes que pelo menos uma conexão de trabalho seja estabelecida. Esse GUC afeta as conexões do coordenador para os trabalhadores e os trabalhadores entre si.

-   Padrão: cinco segundos
-   Mínimo: 10 milissegundos
-   Máximo: uma hora

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>Estatísticas de Consulta

#### <a name="citusstat_statements_purge_interval-integer"></a>\_ \_ intervalo de limpeza de instruções citus. stat \_ (Integer)

Define a frequência na qual o daemon de manutenção remove registros de [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) que não correspondem no `pg_stat_statements` . Esse valor de configuração define o intervalo de tempo entre as limpezas em segundos, com um valor padrão de 10. Um valor de 0 desabilita as limpezas.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Esse parâmetro é efetivo no coordenador e pode ser alterado em tempo de execução.

### <a name="data-loading"></a>Carregamento de dados

#### <a name="citusmulti_shard_commit_protocol-enum"></a>citus. \_ \_ \_ protocolo de confirmação de vários fragmentos (enum)

Define o protocolo de confirmação a ser usado ao executar a cópia em uma tabela distribuída por hash. Em cada posicionamento de fragmento individual, a cópia é executada em um bloco de transação para garantir que nenhum dado seja ingerido se ocorrer um erro durante a cópia. No entanto, há um caso de falha específico em que a cópia é realizada com êxito em todos os posicionamentos, mas uma falha (de hardware) ocorre antes de todas as transações serem confirmadas. Esse parâmetro pode ser usado para evitar a perda de dados nesse caso escolhendo entre os seguintes protocolos de confirmação:

-   **2pc:** (padrão) as transações nas quais a cópia é executada nos posicionamentos de fragmentos são primeiro preparadas usando \' [a confirmação de duas fases](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) do PostgreSQL e, em seguida, confirmadas. As confirmações com falha podem ser recuperadas ou anuladas manualmente usando COMMIT preparado ou reversão preparada, respectivamente.
    Ao usar 2pc, [ \_ \_ as transações](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) preparadas máximas devem ser aumentadas em todos os trabalhos, normalmente para o mesmo valor que as conexões máximas \_ .
-   **1pc:** As transações em que a cópia é executada nos posicionamentos de fragmentos são confirmadas em uma única rodada. Os dados poderão ser perdidos se uma confirmação falhar depois que a cópia for bem-sucedida em todos os posicionamentos (raras).

#### <a name="citusshard_replication_factor-integer"></a>\_fator de replicação citus. Fragment \_ (Integer)

Define o fator de replicação para fragmentos que é, o número de nós nos quais os fragmentos serão colocados e o padrão é 1. Esse parâmetro pode ser definido em tempo de execução e em vigor no coordenador. O valor ideal para esse parâmetro depende do tamanho do cluster e da taxa de falha do nó.  Por exemplo, talvez você queira aumentar esse fator de replicação se executar clusters grandes e observar falhas de nó com mais frequência.

#### <a name="citusshard_count-integer"></a>citus. \_ contagem de fragmentos (Integer)

Define a contagem de fragmentos para tabelas com particionamento de hash e usa como padrão o 32.  Esse valor é usado pelo [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF ao criar tabelas particionadas por hash. Esse parâmetro pode ser definido em tempo de execução e em vigor no coordenador.

#### <a name="citusshard_max_size-integer"></a>\_tamanho máximo de citus. fragmento \_ (Integer)

Define o tamanho máximo para o qual um fragmento será aumentado antes de ser dividido e o padrão é 1 GB. Quando o tamanho do arquivo de origem \' (que é usado para preparo) para um fragmento excede esse valor de configuração, o banco de dados garante que um novo fragmento seja criado. Esse parâmetro pode ser definido em tempo de execução e em vigor no coordenador.

### <a name="planner-configuration"></a>Configuração do Planner

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>\_ \_ \_ contagem de busca de linha de cláusula citus. Limit \_ (inteiro)

Define o número de linhas a serem buscadas por tarefa para otimização da cláusula de limite.
Em alguns casos, selecione consultas com cláusulas de limite pode ser necessário buscar todas as linhas de cada tarefa para gerar resultados. Nesses casos, e onde uma aproximação produziria resultados significativos, esse valor de configuração define o número de linhas a serem buscadas de cada fragmento. As aproximações de limite são desabilitadas por padrão e esse parâmetro é definido como-1. Esse valor pode ser definido em tempo de execução e em vigor no coordenador.

#### <a name="cituscount_distinct_error_rate-floating-point"></a>\_ \_ taxa de erro distinta de citus. Count \_ (ponto flutuante)

O Citus (hiperscale) pode calcular a contagem (DISTINCT) aproximada usando a extensão PostgreSQL-HLL. Essa entrada de configuração define a taxa de erro desejada ao calcular a contagem (DISTINCT). 0,0, que é o padrão, desabilita as aproximações da contagem (DISTINCT); e o 1,0 não fornece nenhuma garantia sobre a precisão dos resultados. É recomendável definir esse parâmetro como 0, 5 para obter melhores resultados. Esse valor pode ser definido em tempo de execução e em vigor no coordenador.

#### <a name="citustask_assignment_policy-enum"></a>citus. \_ \_ política de atribuição de tarefa (enum)

> [!NOTE]
> Esse GUC é aplicável somente quando [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) é maior que um, ou para consultas em relação a [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Define a política a ser usada ao atribuir tarefas a trabalhadores. O coordenador atribui tarefas a trabalhadores com base em locais de fragmento. Esse valor de configuração especifica a política a ser usada ao fazer essas atribuições.
Atualmente, há três políticas de atribuição de tarefas possíveis que podem ser usadas.

-   **ávido:** A política de ávido é o padrão e pretende distribuir uniformemente as tarefas entre os trabalhadores.
-   **Round Robin:** A política Round Robin atribui tarefas a trabalhadores em um modo Round Robin alternando entre réplicas diferentes. Essa política permite uma melhor utilização do cluster quando a contagem de fragmentos de uma tabela é baixa em comparação com o número de trabalhadores.
-   **primeira réplica:** A política de primeira réplica atribui tarefas com base na ordem de inserção de posicionamentos (réplicas) para os fragmentos. Em outras palavras, a consulta de fragmento para um fragmento é atribuída ao trabalho que tem a primeira réplica desse fragmento.
    Esse método permite que você tenha garantias fortes sobre quais fragmentos serão usados em quais nós (ou seja, garantias de residência de memória mais fortes).

Esse parâmetro pode ser definido em tempo de execução e em vigor no coordenador.

### <a name="intermediate-data-transfer"></a>Transferência de Dados intermediários

#### <a name="citusbinary_worker_copy_format-boolean"></a>\_ \_ formato de cópia do operador citus. Binary \_ (booliano)

Use o formato de cópia binária para transferir dados intermediários entre os trabalhadores.
Durante junções de tabelas grandes, o Citus (hiperescala) pode ter que reparticionar e embaralhar dados dinamicamente entre diferentes trabalhadores. Por padrão, esses dados são transferidos em formato de texto. Habilitar esse parâmetro instrui o banco de dados a usar o formato de serialização binária do PostgreSQL para transferir esses dados. Esse parâmetro é eficaz nos trabalhos e precisa ser alterado no arquivo PostgreSQL. conf. Depois de editar o arquivo de configuração, os usuários podem enviar um sinal SIGHUP ou reiniciar o servidor para que essa alteração entre em vigor.

#### <a name="citusbinary_master_copy_format-boolean"></a>\_formato de cópia mestra citus. Binary \_ \_ (booliano)

Use o formato de cópia binária para transferir dados entre o coordenador e os trabalhadores. Ao executar consultas distribuídas, os trabalhadores transferem seus resultados intermediários para o coordenador para agregação final. Por padrão, esses dados são transferidos em formato de texto. Habilitar esse parâmetro instrui o banco de dados a usar o formato de serialização binária do PostgreSQL para transferir esses dados.
Esse parâmetro pode ser definido em tempo de execução e é eficaz no coordenador.

#### <a name="citusmax_intermediate_result_size-integer"></a>\_tamanho do resultado intermediário citus. Max \_ \_ (Integer)

O tamanho máximo em KB de resultados intermediários para CTEs que não podem ser empurrados para nós de trabalho para execução e para subconsultas complexas. O padrão é 1 GB, e um valor de-1 significa sem limite.
As consultas que excederem o limite serão canceladas e produzirão uma mensagem de erro.

### <a name="ddl"></a>DDL

#### <a name="citusenable_ddl_propagation-boolean"></a>citus. habilitar \_ a \_ propagação de DDL (booliano)

Especifica se as alterações DDL devem ser propagadas automaticamente do coordenador para todos os trabalhadores. O valor padrão é true. Como algumas alterações de esquema exigem um bloqueio exclusivo de acesso em tabelas, e como a propagação automática se aplica a todos os trabalhadores sequencialmente, ela pode tornar um cluster de hiperescala (Citus) temporariamente menos responsivo. Você pode optar por desabilitar essa configuração e propagar as alterações manualmente.

### <a name="executor-configuration"></a>Configuração do executor

#### <a name="general"></a>Geral

##### <a name="citusall_modifications_commutative"></a>citus. todas as \_ modificações são \_ comutativas

O Citus (hiperscale) impõe as regras de intercambialidade e adquire os bloqueios apropriados para operações de modificação a fim de garantir a exatidão do comportamento. Por exemplo, ele pressupõe que uma instrução INSERT faz o mudo com outra instrução INSERT, mas não com uma instrução UPDATE ou DELETE. Da mesma forma, ele pressupõe que uma instrução UPDATE ou DELETE não faz o desative com outra instrução UPDATE ou DELETE. Essa precaução significa que as atualizações e exclusões exigem hiperescala (Citus) para adquirir bloqueios mais fortes.

Se você tiver instruções UPDATE que sejam comutadas com suas inserções ou outras atualizações, poderá relaxar essas suposições intercambialidade definindo esse parâmetro como true. Quando esse parâmetro é definido como true, todos os comandos são considerados como comutadores e alegam um bloqueio compartilhado, o que pode melhorar a produtividade geral. Esse parâmetro pode ser definido em tempo de execução e é eficaz no coordenador.

##### <a name="citusremote_task_check_interval-integer"></a>citus. \_ \_ \_ intervalo de verificação de tarefa remota (Integer)

Define a frequência na qual o Citus (hiperescala) verifica os status dos trabalhos gerenciados pelo executor do rastreador de tarefas. O padrão é 10 ms. O coordenador atribui tarefas a trabalhadores e, em seguida, verifica regularmente com elas sobre o progresso de cada tarefa \' . Esse valor de configuração define o intervalo de tempo entre duas verificações de resultante. Esse parâmetro é efetivo no coordenador e pode ser definido em tempo de execução.

##### <a name="citustask_executor_type-enum"></a>\_tipo de executor de citus. Task \_ (enum)

O Citus (hiperscale) tem três tipos de executor para executar consultas de seleção distribuídas.  O executor desejado pode ser selecionado definindo esse parâmetro de configuração. Os valores aceitos para esse parâmetro são:

-   **adaptável:** O padrão. É ideal para respostas rápidas a consultas que envolvem agregações e junções colocalizadas que abrangem vários fragmentos.
-   **controlador de tarefas:** O executor de controlador de tarefas é adequado para consultas complexas e de longa execução que exigem embaralhamento de dados em nós de trabalho e gerenciamento eficiente de recursos.
-   **em tempo real:** (preterido) atende a uma finalidade semelhante à do executor adaptável, mas é menos flexível e pode causar mais pressão de conexão em nós de trabalho.

Esse parâmetro pode ser definido em tempo de execução e em vigor no coordenador.

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus. \_ \_ \_ \_ nível de log de consulta de várias tarefas (enum) {#multi_task_logging}

Define um nível de log para qualquer consulta que gere mais de uma tarefa (isto é, que atinge mais de um fragmento). O registro em log é útil durante uma migração de aplicativo multilocatário, pois você pode escolher um erro ou avisar para essas consultas, para encontrá-las e adicionar um \_ filtro de ID de locatário a elas. Esse parâmetro pode ser definido em tempo de execução e é eficaz no coordenador. O valor padrão para esse parâmetro é \' off \' .

Os valores com suporte para essa enumeração são:

-   **desativada:** Desative o registro em log de todas as consultas que geram várias tarefas (ou seja, abrangem vários fragmentos)
-   **Depurar:** Registra a instrução no nível de severidade de depuração.
-   **log:** Registra a instrução no nível de severidade do LOG. A linha de log incluirá a consulta SQL que foi executada.
-   **AVISO:** Registra a instrução em nível de severidade de aviso.
-   **AVISO:** Registra a instrução no nível de severidade de aviso.
-   **erro:** Registra a instrução no nível de severidade do erro.

Pode ser útil usar `error` durante o teste de desenvolvimento e um nível de log inferior, como `log` durante a implantação de produção real.
Escolher fará `log` com que as consultas de várias tarefas apareçam nos logs de banco de dados com a própria consulta mostrada após a \" instrução.\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

##### <a name="citusenable_repartition_joins-boolean"></a>citus. habilitar \_ \_ junções de repartição (booliano)

Normalmente, a tentativa de executar junções de repartição com o executor adaptável falhará com uma mensagem de erro.  No entanto, definir `citus.enable_repartition_joins` como true permite que o Citus (hiperescala) alterne temporariamente para o executor de controlador de tarefas para executar a junção.  O valor padrão é false.

#### <a name="task-tracker-executor-configuration"></a>Configuração do executor do rastreador de tarefas

##### <a name="citustask_tracker_delay-integer"></a>citus. \_ \_ atraso do rastreador de tarefa (inteiro)

Esse parâmetro define o tempo de suspensão do controlador de tarefas entre os arredondas de gerenciamento de tarefas e os padrões para 200 ms. O processo do rastreador de tarefas é ativado regularmente, percorre todas as tarefas atribuídas a ele e agenda e executa essas tarefas.  Em seguida, o controlador de tarefas é suspenso por um período de tempo antes de passar essas tarefas novamente.
Esse valor de configuração determina o comprimento desse período de repouso. Esse parâmetro é eficaz nos trabalhos e precisa ser alterado no arquivo PostgreSQL. conf. Depois de editar o arquivo de configuração, os usuários podem enviar um sinal SIGHUP ou reiniciar o servidor para que a alteração entre em vigor.

Esse parâmetro pode ser reduzido para cortar o atraso causado devido ao executor do rastreador de tarefas, reduzindo a lacuna de tempo entre os arredondas de gerenciamento.
A diminuição do atraso é útil em casos em que as consultas de fragmento são curtas e, portanto, atualizam seu status regularmente.

##### <a name="citusmax_assign_task_batch_size-integer"></a>citus. Max \_ atribuir \_ \_ \_ tamanho do lote de tarefa (Integer)

O executor do controlador de tarefas no coordenador atribui as tarefas em lotes ao daemon nos trabalhadores de forma síncrona. Esse parâmetro define o número máximo de tarefas a serem atribuídas em um único lote. Escolher um tamanho de lote maior permite uma atribuição de tarefa mais rápida. No entanto, se o número de trabalhos for grande, poderá levar mais tempo para que todos os trabalhos obtenham tarefas.  Esse parâmetro pode ser definido em tempo de execução e é eficaz no coordenador.

##### <a name="citusmax_running_tasks_per_node-integer"></a>citus. Max \_ running \_ Tasks \_ por \_ nó (Integer)

O processo do controlador de tarefas agenda e executa as tarefas atribuídas a ele conforme apropriado. Esse valor de configuração define o número máximo de tarefas a serem executadas simultaneamente em um nó em um determinado momento e o padrão é 8.

O limite garante que você não \' tenha muitas tarefas atingindo o disco ao mesmo tempo e ajuda a evitar contenção de e/s de disco. Se suas consultas forem servidas da memória ou do SSDs, você poderá aumentar o máximo de \_ tarefas em execução \_ \_ por \_ nó sem muita preocupação.

##### <a name="cituspartition_buffer_size-integer"></a>citus. \_ \_ tamanho do buffer de partição (Integer)

Define o tamanho do buffer a ser usado para operações de partição e usa como padrão 8 MB.
O Citus (hiperescala) permite que os dados da tabela sejam reparticionados em vários arquivos quando duas tabelas grandes estão sendo Unidas. Depois que esse buffer de partição for preenchido, os dados reparticionados serão liberados para arquivos no disco.  Essa entrada de configuração pode ser definida em tempo de execução e entra em vigor nos trabalhos.

#### <a name="explain-output"></a>Explicar a saída

##### <a name="citusexplain_all_tasks-boolean"></a>citus. explicar \_ todas as \_ tarefas (booliano)

Por padrão, o Citus (subscale) mostra a saída de uma única tarefa arbitrária ao executar [explicar](http://www.postgresql.org/docs/current/static/sql-explain.html) em uma consulta distribuída. Na maioria dos casos, a saída de explicação será semelhante entre as tarefas. Ocasionalmente, algumas das tarefas serão planejadas de maneira diferente ou terão tempos de execução muito maiores. Nesses casos, pode ser útil habilitar esse parâmetro, após o qual a saída de explicação incluirá todas as tarefas. A explicação de todas as tarefas pode fazer com que o explique demore mais.

## <a name="postgresql-parameters"></a>Parâmetros do PostgreSQL

* [Datastyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) – define o formato de exibição para valores de data e hora
* [Intervalstyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) – define o formato de exibição para valores de intervalo
* [Fuso](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) horário – define o fuso horário para exibir e interpretar carimbos de data/hora
* [APPLICATION_NAME](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) -define o nome do aplicativo a ser relatado em estatísticas e logs
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) -habilita a entrada de elementos nulos em matrizes
* [autovácuo](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) – inicia o subprocesso de vácuo
* [autovacuum_analyze_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) -número de inserções, atualizações ou exclusões de tupla antes de analisar como uma fração de reltuples
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) -número mínimo de inserções, atualizações ou exclusões de tupla antes da análise
* [autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) -tempo de suspensão entre as execuções do autovácuo
* [autovacuum_vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY) -o atraso de custo de vácuo em milissegundos, para o autovácuo
* [autovacuum_vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT) -valor de custo de vácuo disponível antes de napping, para o autovácuo
* [autovacuum_vacuum_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) -número de atualizações ou exclusões de tupla antes de aspirar como uma fração de reltuples
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) -número mínimo de atualizações de tupla ou exclusões antes do vácuo
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) -define a memória máxima a ser usada por cada processo de trabalho do autovácuo
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) -número de páginas após as quais as gravações executadas anteriormente são liberadas para o disco
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) – define se " \' " é permitido em literais de cadeia de caracteres
* [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) -tempo de suspensão do gravador em segundo plano entre arredondamentos
* [bgwriter_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) -número de páginas após as quais as gravações executadas anteriormente são liberadas para o disco
* [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES) -o gravador em segundo plano número máximo de páginas LRU a serem liberadas por rodada
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) -múltiplo da média de uso do buffer para livre por rodada
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) -define o formato de saída para bytea
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) -verifica corpos de função durante a função de criação
* [checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET) -tempo gasto na liberação de buffers sujos durante o ponto de verificação, como fração do intervalo do ponto de verificação
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) -define o tempo máximo entre pontos de verificação de Wal automáticos
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) -habilita avisos se os segmentos do ponto de verificação são preenchidos com mais frequência do que isso
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) -define a codificação do conjunto de caracteres do cliente
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) – define os níveis de mensagem que são enviados ao cliente
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) -define o atraso em microssegundos entre a confirmação da transação e a liberação de Wal para o disco
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) -define as transações abertas mínimas simultâneas antes de executar commit_delay
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) -permite que o planejador use restrições para otimizar consultas
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) -define a estimativa do planejador do custo de processamento de cada entrada de índice durante uma verificação de índice
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) -define a estimativa do planejador do custo de processamento de cada operador ou chamada de função
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) -define a estimativa do planejador do custo de processamento de cada tupla (linha)
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) -define a estimativa do planejador da fração das linhas de um cursor que serão recuperadas
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) – define a quantidade de tempo, em milissegundos, para aguardar um bloqueio antes de verificar o deadlock
* [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3) -os recuos de análise e árvore de plano
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -registra a árvore de análise de cada consulta
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -registra o plano de execução de cada consulta
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -registra a árvore de análise reescrita de cada consulta
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) -define o destino de estatísticas padrão
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) -define o espaço de tabela padrão para criar tabelas e índices em
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) -define a configuração de pesquisa de texto padrão
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) -define o status de adiamento padrão de novas transações
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) – define o nível de isolamento da transação de cada nova transação
* [default_transaction_read_only](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) -define o status padrão somente leitura de novas transações
* default_with_oids-cria novas tabelas com OIDs por padrão
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) -define a suposição do planejador sobre o tamanho do cache de disco
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) – habilita o uso do planejador de planos de bitmap-Scan
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) -permite o uso do planejador de reunir planos de mesclagem
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) -habilita o uso de planos de agregação com hash pelo planejador
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) -habilita o uso de planos de junção hash pelo planejador
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) – habilita o uso do planejador de planos de verificação somente de índice
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) – habilita o uso do planejador de planos de verificação de índice
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) -habilita o uso de materialização do planejador
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) -habilita o uso de planos de junção de mesclagem do planejador
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) – habilita o uso do planejador de planos de junção de loops aninhados
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) -habilita o uso de planos de verificação sequencial do planejador
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) -habilita o uso de etapas de classificação explícitas do planejador
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) -permite o uso do planejador de planos de verificação de tid
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) -avisa sobre escapes de barra invertida em literais de cadeia de caracteres comuns
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) -encerra a sessão em qualquer erro
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) -define o número de dígitos exibidos para valores de ponto flutuante
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) -força o uso de recursos de consulta paralela
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) -define o tamanho da lista além do qual as subconsultas não são recolhidas
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) -habilita a otimização de consulta genética
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) -geqo: o esforço é usado para definir o padrão para outros parâmetros de geqo
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) -geqo: número de iterações do algoritmo
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) -geqo: número de indivíduos na população
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) -geqo: semente para seleção de caminho aleatório
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) -geqo: pressão seletiva dentro da população
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) -define o limite de itens além dos quais geqo é usado
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) -define o resultado máximo permitido para a pesquisa exata por iniciar
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) -define o tamanho máximo da lista pendente para o índice iniciar
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) -define a duração máxima permitida de qualquer transação deixar
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) -define o tamanho da lista acima do qual as construções de junção não são achatadas
* [LC_MONETARY](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) -define a localidade para a formatação de valores monetários
* [LC_NUMERIC](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) -define a localidade para formatar números
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) – habilita o modo de compatibilidade com versões anteriores para verificações de privilégio em objetos grandes
* [LOCK_TIMEOUT](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) -define a duração máxima permitida (em milissegundos) de qualquer espera por um bloqueio. 0 desativa isso
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) -define o tempo mínimo de execução acima do qual as ações de vácuo serão registradas
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) -registra cada ponto de verificação
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) -registra cada conexão bem-sucedida
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) -define o destino para a saída de log do servidor
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) -fim dos logs de uma sessão, incluindo a duração
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) -registra a duração de cada instrução SQL concluída
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) -define o detalhamento de mensagens registradas
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) -espera de bloqueios longos de logs
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) -define o tempo de execução mínimo (em milissegundos) acima de quais instruções serão registradas em log. -1 desabilita as durações da instrução de registro em log
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) -faz com que todas as instruções gerando erro neste nível ou acima dele sejam registrados
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) – define os níveis de mensagem que são registrados
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) -registra cada comando de replicação
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) -define o tipo de instruções registradas
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) -para cada consulta, o grava estatísticas de desempenho cumulativas no log do servidor
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) -registra o uso de arquivos temporários maiores que este número de kilobytes
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) -define a memória máxima a ser usada para operações de manutenção
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) -define o número máximo de trabalhadores paralelos do que o pode estar ativo ao mesmo tempo
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) -define o número máximo de processos paralelos por nó de executor
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) -define o número máximo de tuplas bloqueadas por predicado por página
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) -define o número máximo de páginas bloqueadas por predicado e tuplas por relação
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) -define o atraso máximo antes de cancelar consultas quando um servidor em espera ativo está processando dados de Wal arquivados
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) -define o atraso máximo antes de cancelar consultas quando um servidor em espera ativo está processando dados de Wal em fluxo
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) -número máximo de trabalhos de sincronização de tabela por assinatura
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) -define o tamanho Wal que dispara um ponto de verificação
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) -define a quantidade mínima de dados de índice para uma verificação paralela
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) -define o tamanho mínimo para reduzir o Wal para
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) -emite um aviso para construções que alteraram o significado desde o PostgreSQL 9,4
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) -define a estimativa do planejador do custo de inicialização de processos de trabalho para consulta paralela
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) -define a estimativa do planejador do custo de passar cada tupla (linha) do trabalhador para o back-end mestre
* [pg_stat_statements. Save](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) – salva pg_stat_statements estatísticas entre os desligamentos do servidor
* [pg_stat_statements. Track](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) – seleciona quais instruções são controladas por pg_stat_statements
* [pg_stat_statements. track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) -seleciona se os comandos do utilitário são controlados por pg_stat_statements
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) -ao gerar fragmentos SQL, todos os identificadores
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) – define a estimativa do planejador do custo de uma página de disco buscada de forma não sequencial
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) -habilita a segurança de linha
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) – define a ordem de pesquisa de esquema para nomes que não são qualificados pelo esquema
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) – define a estimativa do planejador do custo de uma página de disco buscada sequencialmente
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) -define o comportamento da sessão para gatilhos e regras de reescrita
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) -causa '... ' cadeias de caracteres para tratar barras invertidas literalmente
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) -define a duração máxima permitida (em milissegundos) de qualquer instrução. 0 desativa isso
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) -habilita verificações sequenciais sincronizadas
* [synchronous_commit](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) – define o nível de sincronização da transação atual
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) -número máximo de retransmissãos de KeepAlive TCP
* [tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE) -tempo entre a emissão de KEEPALIVEs TCP
* tempo [tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL) entre reretransmissões de TCP KeepAlive
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) -define o número máximo de buffers temporários usados por cada sessão de banco de dados
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) -define os espaços de tabela a serem usados para tabelas temporárias e arquivos de classificação
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) -coleta informações sobre a execução de comandos
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) -coleta estatísticas sobre a atividade do banco de dados
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) -coleta estatísticas de nível de função na atividade do banco de dados
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) -coleta estatísticas de tempo para atividade de e/s de banco de dados
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) -trata "expr = NULL" como "expr é nulo"
* [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) -atraso no custo de vácuo em milissegundos
* [vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT) -valor de custo de vácuo disponível antes de napping
* [vacuum_cost_page_dirty](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) – custo de vácuo para uma página sujos por vácuo
* [vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT) -custo de vácuo para uma página encontrada no cache do buffer
* [vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS) -custo de vácuo para uma página não encontrada no cache do buffer
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) -número de transações pelas quais a vácuo e a limpeza a quente devem ser adiadas, se houver
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) -idade mínima na qual o vácuo deve congelar uma linha de tabela
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) -age em que a vácuo deve examinar toda a tabela para congelar tuplas
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) -idade mínima na qual o vácuo deve congelar um MultiXactId em uma linha de tabela
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) -multixact age em que a vácuo deve examinar toda a tabela para congelar tuplas
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) -define o intervalo máximo entre os relatórios de status do receptor Wal para o primário
* [wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY) -tempo entre as liberações de Wal executadas no gravador de Wal
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) -quantidade de Wal gravada pelo gravador de Wal que dispara uma liberação
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) -define a quantidade de memória a ser usada pelas operações de classificação interna e tabelas de hash antes de gravar em arquivos de disco temporários
* [xmlbinary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) – define como os valores binários devem ser codificados em XML
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) – define se os dados XML em operações implícitas de análise e serialização devem ser considerados como documentos ou fragmentos de conteúdo

## <a name="next-steps"></a>Próximas etapas

* Outra forma de configuração, além dos parâmetros de servidor, são as [Opções de configuração](concepts-hyperscale-configuration-options.md) de recurso em um grupo de servidores de hiperescala (Citus).
* A base de dados PostgreSQL subjacente também tem [parâmetros de configuração](http://www.postgresql.org/docs/current/static/runtime-config.html).
