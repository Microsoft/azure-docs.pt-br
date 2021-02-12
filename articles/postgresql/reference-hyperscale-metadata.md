---
title: Tabelas do sistema – Citus (hiperescala) – banco de dados do Azure para PostgreSQL
description: Metadados para execução de consulta distribuída
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 74403365fe48584fa5d1db0e349c9dfc3772d874
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652835"
---
# <a name="system-tables-and-views"></a>Tabelas e exibições do sistema

O Citus (hiperscale) cria e mantém tabelas especiais que contêm informações sobre dados distribuídos no grupo de servidores. O nó de coordenador consulta essas tabelas ao planejar como executar consultas nos nós de trabalho.

## <a name="coordinator-metadata"></a>Metadados do coordenador

O Citus (subscale) divide cada tabela distribuída em vários fragmentos lógicos com base na coluna de distribuição. O coordenador mantém as tabelas de metadados para acompanhar as estatísticas e as informações sobre a integridade e o local desses fragmentos.

Nesta seção, descrevemos cada uma dessas tabelas de metadados e seu esquema.
Você pode exibir e consultar essas tabelas usando o SQL depois de fazer logon no nó de coordenador.

> [!NOTE]
>
> Os grupos de servidores de hiperescala (Citus) que executam versões mais antigas do mecanismo do Citus podem não oferecer todas as tabelas listadas abaixo.

### <a name="partition-table"></a>Tabela de partição

A tabela de partição do PG \_ dist \_ armazena metadados sobre quais tabelas no banco de dados são distribuídas. Para cada tabela distribuída, ela também armazena informações sobre o método de distribuição e informações detalhadas sobre a coluna de distribuição.

| Nome         | Tipo     | Descrição                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | Tabela distribuída à qual essa linha corresponde. Esse valor faz referência à coluna relfilenode na tabela pg_class catálogo do sistema.                                                                                                                   |
| partmethod   | char     | O método usado para particionamento/distribuição. Os valores desta coluna correspondentes a métodos de distribuição diferentes são Append: ' a ', hash: ' h ', tabela de referência: ' n'                                                                          |
| partkey      | texto     | Informações detalhadas sobre a coluna de distribuição, incluindo número de coluna, tipo e outras informações relevantes.                                                                                                                                      |
| colocationid | inteiro  | Grupo de colocalidades ao qual esta tabela pertence. As tabelas no mesmo grupo permitem junções colocalizadas e rollups distribuídos entre outras otimizações. Esse valor faz referência à coluna colocationid na tabela pg_dist_colocation.                      |
| repmodel     | char     | O método usado para replicação de dados. Os valores desta coluna correspondentes a métodos de replicação diferentes são: replicação baseada em instrução Citus: ' C', replicação de streaming PostgreSQL: ' s, confirmação de duas fases (para tabelas de referência): ' T' |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>Tabela de fragmentos

A tabela de fragmento do PG \_ dist \_ armazena metadados sobre fragmentos individuais de uma tabela. Pg_dist_shard tem informações sobre a quais fragmentos de tabela distribuída pertencem e estatísticas sobre a coluna de distribuição para fragmentos.
Para acrescentar tabelas distribuídas, essas estatísticas correspondem aos valores mín./máx. da coluna de distribuição. Para tabelas distribuídas por hash, elas são intervalos de token de hash atribuídos a esse fragmento. Essas estatísticas são usadas para remover fragmentos não relacionados durante consultas SELECT.

| Nome          | Tipo     | Descrição                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | Tabela distribuída à qual essa linha corresponde. Esse valor faz referência à coluna relfilenode na tabela pg_class catálogo do sistema.                                                          |
| fragmentoid       | BIGINT   | Identificador global exclusivo atribuído a este fragmento.                                                                                                                                           |
| shardstorage  | char     | Tipo de armazenamento usado para este fragmento. Tipos de armazenamento diferentes são discutidos na tabela a seguir.                                                                                               |
| shardminvalue | texto     | Para acrescentar tabelas distribuídas, o valor mínimo da coluna de distribuição neste fragmento (inclusivo). Para tabelas distribuídas de hash, valor de token de hash mínimo atribuído a esse fragmento (inclusivo). |
| shardmaxvalue | texto     | Para acrescentar tabelas distribuídas, o valor máximo da coluna de distribuição neste fragmento (inclusivo). Para tabelas distribuídas de hash, valor de token de hash máximo atribuído a esse fragmento (inclusivo). |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>Tipos de armazenamento de fragmento

A coluna shardstorage no \_ fragmento de dist \_ do PG indica o tipo de armazenamento usado para o fragmento. Abaixo está uma breve visão geral dos diferentes tipos de armazenamento de fragmento e sua representação.

| Tipo de armazenamento | Valor de Shardstorage | Descrição                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| TABLE        | T'                | Indica que o fragmento armazena dados pertencentes a uma tabela distribuída regular.         |
| COLUNAR     | &                | Indica que o fragmento armazena dados de coluna. (Usado por tabelas cstore_fdw distribuídas) |
| FOREIGN      | fixo                | Indica que o fragmento armazena dados externos. (Usado por tabelas file_fdw distribuídas)    |

### <a name="shard-placement-table"></a>Tabela de posicionamento de fragmento

A tabela de posicionamento do PG \_ dist \_ controla o local das réplicas de fragmento em nós de trabalho. Cada réplica de um fragmento atribuído a um nó específico é chamada de posicionamento de fragmento. Esta tabela armazena informações sobre a integridade e o local de cada posicionamento do fragmento.

| Nome        | Tipo   | Descrição                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| fragmentoid     | BIGINT | Identificador de fragmento associado a este posicionamento. Esse valor faz referência à coluna fragmentid na tabela pg_dist_shard Catalog.             |
| fragmentostate  | INT    | Descreve o estado desse posicionamento. Diferentes Estados de fragmento são discutidos na seção abaixo.                                         |
| shardlength | BIGINT | Para acrescentar tabelas distribuídas, o tamanho do posicionamento do fragmento no nó de trabalho em bytes. Para tabelas distribuídas por hash, zero.            |
| placementid | BIGINT | Identificador exclusivo gerado automaticamente para cada posicionamento individual.                                                                           |
| groupid     | INT    | Denota um grupo de um servidor primário e zero ou mais servidores secundários quando o modelo de replicação de streaming é usado. |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>Estados de posicionamento do fragmento

O Citus (hiperscale) gerencia a integridade do fragmento em uma base por posicionamento. Se um posicionamento colocar o sistema em um estado inconsistente, o Citus o marcará automaticamente como indisponível. O estado de posicionamento é registrado na tabela pg_dist_shard_placement, dentro da coluna FragmentState. Aqui está uma breve visão geral dos diferentes Estados de posicionamento do fragmento:

| Nome do estado | Valor de FragmentState | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| FINALIZADO  | 1                | Os novos fragmentos de estado são criados no. Os posicionamentos de fragmentos nesse estado são considerados atualizados e usados no planejamento e na execução de consultas.                                                                                                                                                                                                                                                                                 |
| INACTIVE   | 3                | Os posicionamentos de fragmentos nesse estado são considerados inativos porque estão fora de sincronia com outras réplicas do mesmo fragmento. O estado pode ocorrer quando uma operação de acréscimo, modificação (inserção, atualização, exclusão) ou DDL falha para esse posicionamento. O planejador de consulta ignorará posicionamentos nesse estado durante o planejamento e a execução. Os usuários podem sincronizar os dados nesses fragmentos com uma réplica finalizada como uma atividade em segundo plano. |
| TO_DELETE  | 4                | Se o Citus tentar descartar um posicionamento de fragmento em resposta a uma chamada de master_apply_delete_command e falhar, o posicionamento será movido para esse estado. Os usuários podem excluir esses fragmentos como uma atividade de segundo plano subsequente.                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>Tabela de nó de trabalho

A \_ tabela do \_ nó dist. do PG contém informações sobre os nós de trabalho no cluster.

| Nome             | Tipo    | Descrição                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NodeId           | INT     | Identificador gerado automaticamente para um nó individual.                                                                                                                                          |
| groupid          | INT     | Identificador usado para indicar um grupo de um servidor primário e zero ou mais servidores secundários, quando o modelo de replicação de streaming é usado. Por padrão, é o mesmo que NodeId.         |
| NodeName         | texto    | Nome do host ou endereço IP do nó de trabalho do PostgreSQL.                                                                                                                                     |
| nodeport         | INT     | Número da porta na qual o nó de trabalho do PostgreSQL está escutando.                                                                                                                              |
| noderack         | texto    | Adicional Informações de posicionamento do rack para o nó de trabalho.                                                                                                                                 |
| hasmetadata      | booleano | Reservado para uso interno.                                                                                                                                                                 |
| IsActive         | booleano | Se o nó está ativa aceitando posicionamentos de fragmentos.                                                                                                                                     |
| noderole         | texto    | Se o nó é um primário ou secundário                                                                                                                                                 |
| nodecluster      | texto    | O nome do cluster que contém este nó                                                                                                                                               |
| shouldhaveshards | booleano | Se for false, os fragmentos serão movidos para fora do nó (drenado) durante o rebalanceamento, nem os fragmentos de novas tabelas distribuídas serão colocados no nó, a menos que estejam colocalizados com fragmentos já existentes |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>Tabela de objeto distribuído

A \_ tabela de objeto citus.PG dist \_ contém uma lista de objetos, como tipos e funções que foram criados no nó de coordenador e propagados para nós de trabalho. Quando um administrador adiciona novos nós de trabalho ao cluster, o Citus (hiperescala) cria automaticamente cópias dos objetos distribuídos nos novos nós (na ordem correta para satisfazer as dependências de objeto).

| Nome                        | Tipo    | Descrição                                          |
|-----------------------------|---------|------------------------------------------------------|
| ClassID                     | oid     | Classe do objeto distribuído                      |
| objid                       | oid     | ID de objeto do objeto distribuído                  |
| objsubid                    | inteiro | Subid do objeto do objeto distribuído, por exemplo, Attn |
| tipo                        | texto    | Parte do endereço estável usado durante as atualizações de PG   |
| object_names                | text []  | Parte do endereço estável usado durante as atualizações de PG   |
| object_args                 | text []  | Parte do endereço estável usado durante as atualizações de PG   |
| distribution_argument_index | inteiro | Válido somente para funções/procedimentos distribuídos      |
| colocationid                | inteiro | Válido somente para funções/procedimentos distribuídos      |

\"Os endereços estáveis \" identificam exclusivamente objetos de forma independente de um servidor específico. O Citus (hiperscale) rastreia objetos durante uma atualização do PostgreSQL usando endereços estáveis criados com a função [PG \_ Identify \_ Object \_ as \_ Address ()](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE) .

Aqui está \' um exemplo de como o `create_distributed_function()` adiciona entradas à `citus.pg_dist_object` tabela:

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>Tabela de grupos de colocalização

A \_ tabela de \_ colocalização do PG dist contém informações sobre quais \' fragmentos de tabelas devem ser colocadas em conjunto ou [colocalizados](concepts-hyperscale-colocation.md).
Quando duas tabelas estão no mesmo grupo de colocalização, o Citus (hiperescala) garante que os fragmentos com os mesmos valores de partição serão colocados nos mesmos nós de trabalho.
A colocação permite otimizações de junção, determinados rollups distribuídos e suporte de chave estrangeira. A colocação de fragmento é inferida quando as contagens de fragmentos, fatores de replicação e tipos de coluna de partição correspondem a todas as duas tabelas; no entanto, um grupo de colocalização personalizado pode ser especificado ao criar uma tabela distribuída, se desejado.

| Nome                   | Tipo | Descrição                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| colocationid           | INT  | Identificador exclusivo do grupo de colocal para o qual esta linha corresponde.          |
| shardcount             | INT  | Contagem de fragmentos para todas as tabelas neste grupo de colocalização                          |
| replicationfactor      | INT  | Fator de replicação para todas as tabelas neste grupo de colocalização.                  |
| distributioncolumntype | oid  | O tipo da coluna de distribuição para todas as tabelas neste grupo de colocalização. |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>Tabela de estratégias de rebalanceador

Esta tabela define estratégias que [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) podem usar para determinar onde mover fragmentos.

| Nome                           | Tipo    | Descrição                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | booleano | Se rebalance_table_shards deve escolher essa estratégia por padrão. Usar citus_set_default_rebalance_strategy para atualizar esta coluna             |
| shard_cost_function            | regproc | Identificador para uma função de custo, que deve usar um fragmentid como bigint e retornar sua noção de um custo, como o tipo real                                |
| node_capacity_function         | regproc | Identificador para uma função de capacidade, que deve usar NodeId como int e retornar sua noção de capacidade de nó como tipo real                          |
| shard_allowed_on_node_function | regproc | O identificador para uma função que recebeu o fragmentoid bigint e nodeidarg int, retorna booliano para se o Citus pode armazenar o fragmento no nó |
| default_threshold              | float4  | Limite para considerar um nó muito cheio ou vazio, o que determina quando o rebalance_table_shards deve tentar mover fragmentos                    |
| minimum_threshold              | float4  | Uma proteção para impedir que o argumento de limite de rebalance_table_shards () seja definido muito baixo                                                  |

Uma instalação de hiperescala (Citus) é fornecida com essas estratégias na tabela:

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

A estratégia padrão, `by_shard_count` , atribui a cada fragmento o mesmo custo. Seu efeito é igualar a contagem de fragmentos entre nós. A outra estratégia predefinida, `by_disk_size` , atribui um custo a cada fragmento que corresponde a seu tamanho de disco em bytes, além dos fragmentos que estão colocalizados com ele. O tamanho do disco é calculado usando `pg_total_relation_size` , portanto, inclui índices. Essa estratégia tenta atingir o mesmo espaço em disco em cada nó. Observe o limite de 0,1--impede a movimentação desnecessária de fragmentos causada por diferenças insignificantes no espaço em disco.

#### <a name="creating-custom-rebalancer-strategies"></a>Criando estratégias de rebalanceamento personalizado

Aqui estão exemplos de funções que podem ser usadas em novas estratégias de rebalanceador de fragmento e registradas no [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) com a função [citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy) .

-   Definindo uma exceção de capacidade de nó por padrão de nome de host:

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   Rebalanceamento por número de consultas que vão para um fragmento, conforme medido pelo [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table):

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   Isolando um fragmento específico (10000) em um nó (endereço \' 10.0.0.1 \' ):

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>Tabela de estatísticas de consulta

O Citus (hiperscale) fornece `citus_stat_statements` estatísticas sobre como as consultas estão sendo executadas e para quem. É \' análogo a (e pode ser unida) a exibição de [ \_ \_ instruções do PG stat](https://www.postgresql.org/docs/current/static/pgstatstatements.html) no PostgreSQL, que controla estatísticas sobre a velocidade da consulta.

Esse modo de exibição pode rastrear consultas para locatários de origem em um aplicativo multilocatário, que ajuda a decidir quando fazer o isolamento do locatário.

| Nome          | Tipo   | Descrição                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| QueryId       | BIGINT | identificador (bom para junções de pg_stat_statements)                                   |
| userid        | oid    | usuário que executou a consulta                                                           |
| dbid          | oid    | instância de banco de dados do coordenador                                                 |
| Consulta         | texto   | Cadeia de consulta anônima                                                          |
| execu      | texto   | Executor Citus usado: adaptável, em tempo real, controlador de tarefas, roteador ou inserção-seleção |
| partition_key | texto   | valor da coluna de distribuição em consultas executadas pelo roteador, senão nulo               |
| chamadas         | BIGINT | número de vezes que a consulta foi executada                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

Resultados:

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

Restrições:

-   Os dados de estatísticas não são replicados e não \' sobrevivem a falhas ou failover do banco de dado
-   Rastreia um número limitado de consultas, definidas pelo `pg_stat_statements.max` GUC (padrão 5000)
-   Para truncar a tabela, use a `citus_stat_statements_reset()` função

### <a name="distributed-query-activity"></a>Atividade de consulta distribuída

O Citus (hiperescala) fornece exibições especiais para observar consultas e bloqueios em todo o cluster, incluindo consultas específicas de fragmentos usadas internamente para criar resultados para consultas distribuídas.

-   **\_ atividade de \_ estatísticas \_ de dist de citus**: mostra as consultas distribuídas que estão sendo executadas em todos os nós. Um superconjunto de `pg_stat_activity` , utilizável onde for o último.
-   **\_ \_ \_ atividade stat do citus Worker**: mostra consultas em trabalhadores, incluindo consultas de fragmento em fragmentos individuais.
-   **\_ \_ esperas de bloqueio citus**: consultas bloqueadas em todo o cluster.

As duas primeiras exibições incluem todas as colunas da [ \_ \_ atividade PG stat](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) mais o host/porta do host do trabalho que iniciou a consulta e o host/porta do nó de coordenador do cluster.

Por exemplo, considere a possibilidade de contar as linhas em uma tabela distribuída:

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

Podemos ver que a consulta aparece em `citus_dist_stat_activity` :

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

Essa consulta requer informações de todos os fragmentos. Algumas das informações estão no fragmento `users_table_102038` , que são armazenadas no `localhost:9700` . Podemos ver uma consulta acessando o fragmento examinando o `citus_worker_stat_activity` modo de exibição:

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

O `query` campo mostra os dados sendo copiados para fora do fragmento para serem contados.

> [!NOTE]
> Se uma consulta de roteador (por exemplo, um único locatário em um aplicativo multilocatário, ' SELECT "
> * DA tabela em que tenant_id = X ') é executado sem um bloco de transação, \_ as \_ colunas nome do host da consulta mestra \_ e porta do \_ \_ host de consulta mestre \_ serão nulas na \_ atividade stat do trabalhador citus \_ \_ .

Para ver como o `citus_lock_waits` funciona, podemos gerar uma situação de bloqueio manualmente. Primeiro, vamos \' Configurar uma tabela de teste do Coordenador:

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

Em seguida, usando duas sessões no coordenador, podemos executar esta sequência de instruções:

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

A `citus_lock_waits` exibição mostra a situação.

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

Neste exemplo, as consultas foram originadas no coordenador, mas a exibição também pode listar os bloqueios entre as consultas provenientes de trabalhadores (executados com o Citus) MX para a instância).

## <a name="next-steps"></a>Próximas etapas

* Saiba como algumas [funções de hiperescala (Citus)](reference-hyperscale-functions.md) alteram tabelas do sistema
* Examinar os conceitos de [nós e tabelas](concepts-hyperscale-nodes.md)
