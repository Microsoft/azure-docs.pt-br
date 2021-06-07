---
title: Funções do SQL – Citus (hiperescala) – banco de dados do Azure para PostgreSQL
description: Funções na API do SQL de hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: f324ef44d002f50bf27c08072e904c1d92b5512f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026226"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>Funções na API do SQL de hiperescala (Citus)

Esta seção contém informações de referência para as funções definidas pelo usuário fornecidas pelo Citus (subscale). Essas funções ajudam no fornecimento de funcionalidade distribuída adicional a Citus (hiperescala) diferente dos comandos SQL padrão.

> [!NOTE]
>
> Os grupos de servidores de hiperescala (Citus) que executam versões mais antigas do mecanismo do Citus podem não oferecer todas as funções listadas abaixo.

## <a name="table-and-shard-ddl"></a>DDL de tabela e fragmento

### <a name="create_distributed_table"></a>criar \_ tabela distribuída \_

A \_ função CREATE Distributed \_ Table () é usada para definir uma tabela distribuída e criar seus fragmentos se ela for uma tabela distribuída por hash. Essa função usa um nome de tabela, a coluna de distribuição e um método de distribuição opcional e insere os metadados apropriados para marcar a tabela como distribuída. A função usa como padrão a distribuição ' hash ' se nenhum método de distribuição for especificado. Se a tabela for de hash distribuído, a função também criará fragmentos de trabalho com base nos valores de configuração de fator de replicação de fragmento e contagem de fragmentos. Se a tabela contiver linhas, elas serão distribuídas automaticamente aos nós de trabalho.

Essa função substitui o uso da \_ \_ tabela distribuída Create mestre \_ () seguida por Master \_ criar \_ \_ fragmentos de trabalho ().

#### <a name="arguments"></a>Argumentos

**nome da tabela \_ :** o nome da tabela que precisa ser distribuída.

**coluna de distribuição \_ :** a coluna na qual a tabela deve ser distribuída.

**tipo de distribuição \_ :** (opcional) o método de acordo com o qual a tabela deve ser distribuída. Os valores permitidos são Append ou hash, com um valor padrão de ' hash '.

**colocar \_ com:** (opcional) incluir tabela atual no grupo de colocalização de outra tabela. Por padrão, as tabelas são colocalizadas quando são distribuídas por colunas do mesmo tipo, têm a mesma contagem de fragmentos e têm o mesmo fator de replicação. Os valores possíveis para `colocate_with` são `default` , `none` para iniciar um novo grupo de colocalização ou o nome de outra tabela a ser colocalizado com essa tabela.  (Consulte a colocação da [tabela](concepts-hyperscale-colocation.md).)

Tenha em mente que o valor padrão de `colocate_with` é a colocalização implícita. A [colocação pode ser](concepts-hyperscale-colocation.md) uma ótima coisa quando as tabelas estão relacionadas ou serão Unidas.  No entanto, quando duas tabelas não estão relacionadas, mas acontecem para usar o mesmo tipo de dados para suas colunas de distribuição, a colocação acidental delas pode diminuir o desempenho durante o [rebalanceamento do fragmento](howto-hyperscale-scale-rebalance.md).  Os fragmentos de tabela serão movidos juntos desnecessariamente em \" cascata.\"

Se uma nova tabela distribuída não estiver relacionada a outras tabelas, será melhor especificar `colocate_with => 'none'` .

#### <a name="return-value"></a>Valor Retornado

N/D

#### <a name="example"></a>Exemplo

Este exemplo informa ao banco de dados que a tabela de eventos do GitHub \_ deve ser distribuída por hash na \_ coluna ID do repositório.

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>criar \_ tabela de referência \_

A \_ função criar tabela de referência \_ () é usada para definir uma pequena referência ou tabela de dimensões. Essa função usa um nome de tabela e cria uma tabela distribuída com apenas um fragmento, replicada para cada nó de trabalho.

#### <a name="arguments"></a>Argumentos

**nome da tabela \_ :** o nome da dimensão pequena ou da tabela de referência que precisa ser distribuída.

#### <a name="return-value"></a>Valor Retornado

N/D

#### <a name="example"></a>Exemplo

Este exemplo informa ao banco de dados que a tabela Nation deve ser definida como uma tabela de referência

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>atualizar \_ para \_ tabela de referência \_

A \_ função atualizar para a \_ \_ tabela de referência () usa uma tabela distribuída existente que tem uma contagem de fragmentos de uma e a atualiza para ser uma tabela de referência reconhecida. Depois de chamar essa função, a tabela será como se ela tivesse sido criada com [create_reference_table](#create_reference_table).

#### <a name="arguments"></a>Argumentos

**nome da tabela \_ :** o nome da tabela distribuída (com contagem de fragmentos = 1) que será distribuído como uma tabela de referência.

#### <a name="return-value"></a>Valor Retornado

N/D

#### <a name="example"></a>Exemplo

Este exemplo informa ao banco de dados que a tabela Nation deve ser definida como uma tabela de referência

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>marcar \_ tabelas \_ colocalizadas

A \_ função marcar tabelas \_ colocalizadas () usa uma tabela distribuída (a origem) e uma lista de outras (os destinos) e coloca os destinos no mesmo grupo de colocalização que a origem. Se a origem ainda não estiver em um grupo, essa função criará uma e atribuirá a origem e os destinos a ela.

A colocação de tabelas deve ser feita no tempo de distribuição da tabela por meio do `colocate_with` parâmetro de [create_distributed_table](#create_distributed_table), mas `mark_tables_colocated` pode cuidar disso posteriormente, se necessário.

#### <a name="arguments"></a>Argumentos

**nome da tabela de origem \_ \_ :** o nome da tabela distribuída cujo grupo de alocações os destinos serão atribuídos para corresponder.

**nomes de tabela de destino \_ \_ :** a matriz de nomes das tabelas de destino distribuída deve ser não vazia. Essas tabelas distribuídas devem corresponder à tabela de origem em:

> -   Método distribution
> -   tipo de coluna de distribuição
> -   tipo de replicação
> -   contagem de fragmentos

Se nenhuma das alternativas acima for aplicável, o Citus (hiperescala) gerará um erro. Por exemplo, a tentativa de colocar tabelas `apples` e `oranges` cujos tipos de coluna de distribuição diferem resulta em:

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>Valor Retornado

N/D

#### <a name="example"></a>Exemplo

Este exemplo coloca `products` e `line_items` no mesmo grupo de colocalização que `stores` . O exemplo supõe que essas tabelas sejam todas distribuídas em uma coluna com tipo correspondente, provavelmente uma \" ID de loja.\"

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>criar \_ função distribuída \_

Propaga uma função do nó de coordenador para trabalhadores e marca-a para execução distribuída. Quando uma função distribuída é chamada no coordenador, o Citus (subscale) usa o valor do \" argumento Distribution \" para escolher um nó de trabalho para executar a função. A execução da função em funcionários aumenta o paralelismo e pode trazer o código mais próximo aos dados em fragmentos para uma latência mais baixa.

O caminho de pesquisa do postgres não é propagado do coordenador para trabalhadores durante a execução da função distribuída, portanto, o código de função distribuído deve qualificar totalmente os nomes dos objetos de banco de dados. Além disso, os avisos emitidos pelas funções não serão exibidos para o usuário.

#### <a name="arguments"></a>Argumentos

**nome da função \_ :** o nome da função a ser distribuída. O nome deve incluir os tipos de parâmetro da função entre parênteses, pois várias funções podem ter o mesmo nome no PostgreSQL. Por exemplo, `'foo(int)'` é diferente de `'foo(int, text)'` .

**nome do ARG de distribuição \_ \_ :** (opcional) o nome do argumento pelo qual distribuir. Para sua conveniência (ou se os argumentos de função não tiverem nomes), um espaço reservado posicional é permitido, como `'$1'` . Se esse parâmetro não for especificado, a função chamada por `function_name` será simplesmente criada nos trabalhadores. Se os nós de trabalho forem adicionados no futuro, a função será automaticamente criada lá.

**colocar \_ com:** (opcional) quando a função distribuída lê ou grava em uma tabela distribuída (ou, mais geralmente, grupo de colocalização), certifique-se de nomear essa tabela usando o `colocate_with` parâmetro. Em seguida, cada invocação da função será executada no nó de trabalho que contém os fragmentos relevantes.

#### <a name="return-value"></a>Valor Retornado

N/D

#### <a name="example"></a>Exemplo

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

## <a name="metadata--configuration-information"></a>Informações de metadados/configuração

### <a name="master_get_table_metadata"></a>\_metadados da \_ tabela de obtenção mestra \_

A \_ função mestra obter \_ metadados da tabela \_ () pode ser usada para retornar metadados relacionados à distribuição para uma tabela distribuída. Esses metadados incluem a ID da relação, o tipo de armazenamento, o método de distribuição, a coluna de distribuição, a contagem de replicação, o tamanho máximo do fragmento e a política de posicionamento do fragmento para a tabela. Nos bastidores, essa função consulta as tabelas de metadados de hiperescala (Citus) para obter as informações necessárias e concatena-as em uma tupla antes de retorná-la ao usuário.

#### <a name="arguments"></a>Argumentos

**nome da tabela \_ :** o nome da tabela distribuída para a qual você deseja buscar metadados.

#### <a name="return-value"></a>Valor Retornado

Uma tupla que contém as seguintes informações:

**\_ relid lógico:** OID da tabela distribuída. Ele faz referência à coluna relfilenode na \_ tabela de catálogo do sistema da classe PG.

**tipo de armazenamento de parte \_ \_ :** tipo de armazenamento usado para a tabela. Pode ser ' T' (tabela padrão), ' f ' (tabela estrangeira) ou ' C' (tabela de coluna).

**método de parte \_ :** método de distribuição usado para a tabela. Pode ser ' a ' (append) ou ' h ' (hash).

**chave de parte \_ :** coluna de distribuição da tabela.

**\_ \_ contagem de réplicas de parte:** contagem de replicação de fragmento atual.

**\_ \_ tamanho máximo da parte:** tamanho máximo atual do fragmento em bytes.

**política de posicionamento de parte \_ \_ :** política de posicionamento de fragmento usada para colocar os fragmentos da tabela. Pode ser 1 (primeiro nó-local) ou 2 (Round-Robin).

#### <a name="example"></a>Exemplo

O exemplo a seguir busca e exibe os metadados da tabela para a \_ tabela de eventos do github.

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>obter \_ \_ ID \_ de fragmento para a \_ coluna de distribuição \_

O Citus (subscale) atribui todas as linhas de uma tabela distribuída a um fragmento com base no valor da coluna de distribuição da linha e no método de distribuição da tabela. Na maioria dos casos, o mapeamento preciso é um detalhe de baixo nível que o administrador de banco de dados pode ignorar. No entanto, pode ser útil determinar o fragmento de uma linha para tarefas manuais de manutenção de banco de dados ou apenas para satisfazer a curiosidade. A `get_shard_id_for_distribution_column` função fornece essas informações para tabelas distribuídas por hash e intervalo, bem como tabelas de referência. Ele não funciona para a distribuição de acréscimo.

#### <a name="arguments"></a>Argumentos

**nome da tabela \_ :** a tabela distribuída.

**valor de distribuição \_ :** o valor da coluna de distribuição.

#### <a name="return-value"></a>Valor Retornado

O Citus (ID de fragmento) associa o valor da coluna de distribuição para a tabela especificada.

#### <a name="example"></a>Exemplo

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>coluna \_ para \_ nome da coluna \_

Traduz a `partkey` coluna de `pg_dist_partition` em um nome de coluna textual. A conversão é útil para determinar a coluna de distribuição de uma tabela distribuída.

Para obter uma discussão mais detalhada, consulte [escolhendo uma coluna de distribuição](concepts-hyperscale-choose-distribution-column.md).

#### <a name="arguments"></a>Argumentos

**nome da tabela \_ :** a tabela distribuída.

**texto de variável de coluna \_ \_ :** o valor de `partkey` na `pg_dist_partition` tabela.

#### <a name="return-value"></a>Valor Retornado

O nome da `table_name` coluna de distribuição.

#### <a name="example"></a>Exemplo

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Saída:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>\_tamanho da relação de citus \_

Obtenha o espaço em disco usado por todos os fragmentos da tabela distribuída especificada.
O espaço em disco inclui o tamanho da \" bifurcação principal, \" mas exclui o mapa de visibilidade e o mapa de espaço livre para os fragmentos.

#### <a name="arguments"></a>Argumentos

**logicalrelid:** o nome de uma tabela distribuída.

#### <a name="return-value"></a>Valor Retornado

Tamanho em bytes como um bigint.

#### <a name="example"></a>Exemplo

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>\_tamanho da tabela citus \_

Obtenha o espaço em disco usado por todos os fragmentos da tabela distribuída especificada, excluindo índices (mas incluindo o sistema de notificação, o mapa de espaço livre e o mapa de visibilidade).

#### <a name="arguments"></a>Argumentos

**logicalrelid:** o nome de uma tabela distribuída.

#### <a name="return-value"></a>Valor Retornado

Tamanho em bytes como um bigint.

#### <a name="example"></a>Exemplo

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>\_tamanho da \_ relação \_ total de citus

Obtenha o total de espaço em disco usado por todos os fragmentos da tabela distribuída especificada, incluindo todos os índices e dados de notificação.

#### <a name="arguments"></a>Argumentos

**logicalrelid:** o nome de uma tabela distribuída.

#### <a name="return-value"></a>Valor Retornado

Tamanho em bytes como um bigint.

#### <a name="example"></a>Exemplo

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>redefinição de \_ instruções citus stat \_ \_

Remove todas as linhas de [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table).
Essa função funciona independentemente do `pg_stat_statements_reset()` . Para redefinir todas as estatísticas, chame ambas as funções.

#### <a name="arguments"></a>Argumentos

N/D

#### <a name="return-value"></a>Valor Retornado

Nenhum

## <a name="server-group-management-and-repair"></a>Gerenciamento e reparo do grupo de servidores

### <a name="master_copy_shard_placement"></a>\_posicionamento do \_ fragmento de cópia mestra \_

Se um posicionamento de fragmento não for atualizado durante um comando de modificação ou uma operação DDL, ele será marcado como inativo. A \_ função de posicionamento de fragmento de cópia mestre \_ \_ pode ser chamada para reparar um posicionamento de fragmento inativo usando dados de um posicionamento íntegro.

Para reparar um fragmento, a função primeiro descarta o posicionamento do fragmento não íntegro e o recria usando o esquema no coordenador. Depois que o posicionamento do fragmento é criado, a função copia dados do posicionamento íntegro e atualiza os metadados para marcar o novo posicionamento do fragmento como íntegro. Essa função garante que o fragmento será protegido de quaisquer modificações simultâneas durante o reparo.

#### <a name="arguments"></a>Argumentos

**ID do fragmento \_ :** a ID do fragmento a ser reparada.

**nome do nó de origem \_ \_ :** nome DNS do nó no qual o posicionamento do fragmento íntegro está presente ( \" nó de origem \" ).

**porta do nó de origem \_ \_ :** a porta no nó de trabalho de origem no qual o servidor de banco de dados está escutando.

**nome do nó de destino \_ \_ :** nome DNS do nó no qual o posicionamento de fragmento inválido está presente ( \" nó de destino \" ).

**porta do nó de destino \_ \_ :** a porta no nó de trabalho de destino no qual o servidor de banco de dados está escutando.

#### <a name="return-value"></a>Valor Retornado

N/D

#### <a name="example"></a>Exemplo

O exemplo a seguir reparará um posicionamento de fragmento inativo do fragmento 12345, que está presente no servidor de banco de dados que está sendo executado em ' host inadequado \_ ' na porta 5432. Para repará-lo, ele usará dados de um posicionamento de fragmento íntegro presente no servidor que está sendo executado em ' bom \_ host ' na porta
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>\_posicionamento do \_ fragmento de movimentação mestre \_

Essa função move um determinado fragmento (e os fragmentos colocalizados com ele) de um nó para outro. Normalmente, ele é usado indiretamente durante o rebalanceamento de fragmento em vez de ser chamado diretamente por um administrador de banco de dados.

Há duas maneiras de mover os dados: bloqueio ou não bloqueio. A abordagem de bloqueio significa que, durante a movimentação, todas as modificações no fragmento são pausadas.
A segunda maneira, que evita o bloqueio de gravações de fragmento, depende da replicação lógica do postgres 10.

Após uma operação de movimentação bem-sucedida, os fragmentos no nó de origem são excluídos. Se a movimentação falhar a qualquer momento, essa função gera um erro e deixa os nós de origem e de destino inalterados.

#### <a name="arguments"></a>Argumentos

**ID do fragmento \_ :** a ID do fragmento a ser movido.

**nome do nó de origem \_ \_ :** nome DNS do nó no qual o posicionamento do fragmento íntegro está presente ( \" nó de origem \" ).

**porta do nó de origem \_ \_ :** a porta no nó de trabalho de origem no qual o servidor de banco de dados está escutando.

**nome do nó de destino \_ \_ :** nome DNS do nó no qual o posicionamento de fragmento inválido está presente ( \" nó de destino \" ).

**porta do nó de destino \_ \_ :** a porta no nó de trabalho de destino no qual o servidor de banco de dados está escutando.

**modo de transferência de fragmento \_ \_ :** (opcional) especifique o método de replicação, se deseja usar a replicação lógica PostgreSQL ou um comando de cópia entre operadores. Os valores possíveis são:

> -   `auto`: Exigir identidade de réplica se a replicação lógica for possível; caso contrário, use o comportamento herdado (por exemplo, para reparo de fragmentos, PostgreSQL 9,6). Esse é o valor padrão.
> -   `force_logical`: Use a replicação lógica, mesmo se a tabela não tiver uma identidade de réplica. Quaisquer instruções de atualização/exclusão simultâneas à tabela falharão durante a replicação.
> -   `block_writes`: Use cópia (bloqueio de gravações) para tabelas que não têm a chave primária ou a identidade da réplica.

#### <a name="return-value"></a>Valor Retornado

N/D

#### <a name="example"></a>Exemplo

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>reequilibrar \_ fragmentos de tabela \_

A função rebalancear \_ \_ fragmentos de tabela () move fragmentos da tabela fornecida para torná-los uniformemente distribuídos entre os trabalhadores. A função primeiro calcula a lista de movimentações que precisa fazer para garantir que o grupo de servidores seja balanceado dentro do limite fornecido. Em seguida, ele move os posicionamentos de fragmentos um por um do nó de origem para o nó de destino e atualiza os metadados de fragmento correspondentes para refletir a movimentação.

Cada fragmento recebe um custo ao determinar se os fragmentos são \" distribuídos uniformemente. \" Por padrão, cada fragmento tem o mesmo custo (um valor de 1), portanto, a distribuição para equalizar o custo entre os trabalhadores é igual à equalização do número de fragmentos em cada um. A estratégia de custo constante é chamada \" por \_ contagem de fragmentos \_ \" e é a estratégia de rebalanceamento padrão.

A estratégia padrão é apropriada nessas circunstâncias:

*  Os fragmentos são aproximadamente do mesmo tamanho
*  Os fragmentos obtêm aproximadamente a mesma quantidade de tráfego
*  Os nós de trabalho são todos do mesmo tamanho/tipo
*  Os fragmentos não foram fixados para determinados funcionários

Se qualquer uma dessas suposições não mantiver, o rebalanceamento padrão poderá resultar em um plano inadequado. Nesse caso, você pode personalizar a estratégia usando o `rebalance_strategy` parâmetro.

É aconselhável chamar [get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan) antes de executar a redistribuição \_ de fragmentos de tabela \_ , para ver e verificar as ações a serem executadas.

#### <a name="arguments"></a>Argumentos

**nome da tabela \_ :** (opcional) o nome da tabela cujos fragmentos precisam ser rebalanceados. Se for NULL, balanceie novamente todos os grupos de colocalização existentes.

**Threshold:** (opcional) um número flutuante entre 0,0 e 1,0 que indica a taxa de diferença máxima de utilização de nó da utilização média. Por exemplo, especificar 0,1 fará com que o balanceador de fragmentos tente balancear todos os nós para manter o mesmo número de fragmentos ± de 10%.
Especificamente, o rebalanceador de fragmentos tentará convergir a utilização de todos os nós de trabalho para a utilização média (1-limite) \* \_ \. . (1
+ limite) \* intervalo médio de \_ utilização.

**máximo \_ de \_ movimentações de fragmentos:** (opcional) o número máximo de fragmentos a serem movidos.

**\_ lista de fragmentos excluídos \_ :** (opcional) identificadores de fragmentos que não devem ser movidos durante a operação de rebalanceamento.

**modo de transferência de fragmento \_ \_ :** (opcional) especifique o método de replicação, se deseja usar a replicação lógica PostgreSQL ou um comando de cópia entre operadores. Os valores possíveis são:

> -   `auto`: Exigir identidade de réplica se a replicação lógica for possível; caso contrário, use o comportamento herdado (por exemplo, para reparo de fragmentos, PostgreSQL 9,6). Esse é o valor padrão.
> -   `force_logical`: Use a replicação lógica, mesmo se a tabela não tiver uma identidade de réplica. Quaisquer instruções de atualização/exclusão simultâneas à tabela falharão durante a replicação.
> -   `block_writes`: Use cópia (bloqueio de gravações) para tabelas que não têm a chave primária ou a identidade da réplica.

**drenagem \_ apenas:** (opcional) quando true, mover fragmentos de nós de trabalho que foram `shouldhaveshards` definidos como false no [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table); não mova nenhum outro fragmento.

**estratégia de rebalanceamento \_ :** (opcional) o nome de uma estratégia em [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Se esse argumento for omitido, a função escolherá a estratégia padrão, conforme indicado na tabela.

#### <a name="return-value"></a>Valor Retornado

N/D

#### <a name="example"></a>Exemplo

O exemplo a seguir tentará reequilibrar os fragmentos da tabela de \_ eventos do GitHub dentro do limite padrão.

```postgresql
SELECT rebalance_table_shards('github_events');
```

Este exemplo de uso tentará reequilibrar a \_ tabela de eventos do GitHub sem mover fragmentos com a ID 1 e 2.

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>obter \_ reequilibrar plano de fragmentos de \_ tabela \_ \_

Gere os movimentos do fragmento planejado de [rebalance_table_shards](#rebalance_table_shards) sem executá-los.
Embora seja improvável, \_ \_ o plano obter os fragmentos de tabela de rebalanceamento \_ \_ pode gerar um plano um pouco diferente do que uma chamada de fragmentos de tabela de rebalanceamento \_ \_ com os mesmos argumentos. Eles não são executados ao mesmo tempo, portanto, os fatos sobre o grupo de servidores, \- por exemplo, espaço em disco, \- podem diferir entre as chamadas.

#### <a name="arguments"></a>Argumentos

Os mesmos argumentos que reequilibram \_ fragmentos de tabela \_ : relação, limite, máximo de \_ \_ movimentações de fragmentos, \_ lista de fragmentos excluídos \_ e \_ somente dreno. Consulte a documentação dessa função para obter o significado dos argumentos.

#### <a name="return-value"></a>Valor Retornado

Tuplas que contêm estas colunas:

-   **\_ nome da tabela**: a tabela cujos fragmentos seriam movidos
-   **fragmentid: o fragmento em** questão
-   **\_ tamanho do fragmento**: tamanho em bytes
-   **SourceName**: nome do host do nó de origem
-   **sourceport**: porta do nó de origem
-   **TargetName**: nome do host do nó de destino
-   **TARGETPORT**: porta do nó de destino

### <a name="get_rebalance_progress"></a>obter o \_ progresso do rebalanceamento \_

Quando um rebalanceamento de fragmentos começa, a `get_rebalance_progress()` função lista o progresso de cada fragmento envolvido. Ele monitora as movimentações planejadas e executadas pelo `rebalance_table_shards()` .

#### <a name="arguments"></a>Argumentos

N/D

#### <a name="return-value"></a>Valor Retornado

Tuplas que contêm estas colunas:

-   **SessionID**: Postgres PID do monitor de rebalanceamento
-   **\_ nome da tabela**: a tabela cujos fragmentos estão sendo movidos
-   **fragmentid: o fragmento em** questão
-   **\_ tamanho do fragmento**: tamanho em bytes
-   **SourceName**: nome do host do nó de origem
-   **sourceport**: porta do nó de origem
-   **TargetName**: nome do host do nó de destino
-   **TARGETPORT**: porta do nó de destino
-   **progresso**: 0 = aguardando para ser movido; 1 = movendo; 2 = concluído

#### <a name="example"></a>Exemplo

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>citus \_ Adicionar \_ estratégia de rebalanceamento \_

Acrescentar uma linha a [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) .

#### <a name="arguments"></a>Argumentos

Para obter mais informações sobre esses argumentos, consulte os valores de coluna correspondentes em `pg_dist_rebalance_strategy` .

**nome:** identificador para a nova estratégia

**função de custo de fragmento \_ \_ :** identifica a função usada para determinar o \" custo \" de cada fragmento

**função de capacidade de nó \_ \_ :** identifica a função para medir a capacidade do nó

**fragmento \_ permitido \_ na \_ função de nó \_ :** identifica a função que determina quais fragmentos podem ser colocados em quais nós

**\_ limite padrão:** um limite de ponto flutuante que ajusta o quão precisamente o custo de fragmento cumulativo deve ser balanceado entre os nós

**\_ limite mínimo:** (opcional) uma coluna de salvaguarda que contém o valor mínimo permitido para o argumento de limite de reequilibrar \_ fragmentos de tabela \_ (). Seu valor padrão é 0

#### <a name="return-value"></a>Valor Retornado

N/D

### <a name="citus_set_default_rebalance_strategy"></a>citus \_ definir \_ \_ estratégia de rebalanceamento padrão \_

Atualize a tabela [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) , alterando a estratégia nomeada por seu argumento para ser o padrão escolhido ao reequilibrar fragmentos.

#### <a name="arguments"></a>Argumentos

**nome:** o nome da estratégia na estratégia de \_ rebalanceamento de dist do PG \_ \_

#### <a name="return-value"></a>Valor Retornado

N/D

#### <a name="example"></a>Exemplo

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>\_Estatísticas de \_ conexão \_ remota citus

A \_ função citus Remote \_ Connection \_ stats () mostra o número de conexões ativas para cada nó remoto.

#### <a name="arguments"></a>Argumentos

N/D

#### <a name="example"></a>Exemplo

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>\_nó de dreno mestre \_

A \_ função de \_ nó () de dreno mestre move fragmentos para fora do nó designado e para outros nós que tenham `shouldhaveshards` definido como true no [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table). Chame a função antes de remover um nó do grupo de servidores e desligar o servidor físico do nó.

#### <a name="arguments"></a>Argumentos

**NodeName:** O nome do host do nó a ser drenado.

**nodeport:** O número da porta do nó a ser descarregada.

**modo de transferência de fragmento \_ \_ :** (opcional) especifique o método de replicação, se deseja usar a replicação lógica PostgreSQL ou um comando de cópia entre operadores. Os valores possíveis são:

> -   `auto`: Exigir identidade de réplica se a replicação lógica for possível; caso contrário, use o comportamento herdado (por exemplo, para reparo de fragmentos, PostgreSQL 9,6). Esse é o valor padrão.
> -   `force_logical`: Use a replicação lógica, mesmo se a tabela não tiver uma identidade de réplica. Quaisquer instruções de atualização/exclusão simultâneas à tabela falharão durante a replicação.
> -   `block_writes`: Use cópia (bloqueio de gravações) para tabelas que não têm a chave primária ou a identidade da réplica.

**estratégia de rebalanceamento \_ :** (opcional) o nome de uma estratégia em [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Se esse argumento for omitido, a função escolherá a estratégia padrão, conforme indicado na tabela.

#### <a name="return-value"></a>Valor Retornado

N/D

#### <a name="example"></a>Exemplo

Aqui estão as etapas típicas para remover um único nó (por exemplo, ' 10.0.0.1 ' em uma porta PostgreSQL padrão):

1.  Dissipe o nó.

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  Aguarde até que o comando seja concluído

3.  Remover o nó

Ao descarregar vários nós, é recomendável usar [rebalance_table_shards](#rebalance_table_shards) em vez disso. Isso permite que o Citus (hiperescala) Planeje antecipadamente e mova fragmentos o número mínimo de vezes.

1.  Execute para cada nó que você deseja remover:

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  Esvaziar todos de uma vez com [rebalance_table_shards](#rebalance_table_shards)

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  Aguarde até que o rebalanceamento de descarga seja concluído

4.  Remover os nós

### <a name="replicate_table_shards"></a>replicar \_ fragmentos de tabela \_

A \_ função replicar \_ fragmentos de tabela () replica os fragmentos em replicação da tabela especificada. A função calcula primeiro a lista de fragmentos e locais replicados nos quais eles podem ser buscados para replicação. Em seguida, a função copia esses fragmentos e atualiza os metadados de fragmento correspondentes para refletir a cópia.

#### <a name="arguments"></a>Argumentos

**nome da tabela \_ :** o nome da tabela cujos fragmentos precisam ser replicados.

**fator de replicação de fragmento \_ \_ :** (opcional) o fator de replicação desejado para alcançar cada fragmento.

**máximo \_ de \_ cópias de fragmentos:** (opcional) número máximo de fragmentos a serem copiados para alcançar o fator de replicação desejado.

**\_ lista de fragmentos excluídos \_ :** (opcional) identificadores de fragmentos que não devem ser copiados durante a operação de replicação.

#### <a name="return-value"></a>Valor Retornado

N/D

#### <a name="examples"></a>Exemplos

O exemplo a seguir tentará replicar os fragmentos da tabela de eventos do GitHub \_ para o \_ fator de replicação de fragmento \_ .

```postgresql
SELECT replicate_table_shards('github_events');
```

Este exemplo tentará trazer os fragmentos da tabela de eventos do GitHub \_ para o fator de replicação desejado com um máximo de 10 cópias de fragmento. O rebalanceador copiará, no máximo, 10 fragmentos em sua tentativa de alcançar o fator de replicação desejado.

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>isolar \_ locatário \_ para \_ novo \_ fragmento

Essa função cria um novo fragmento para armazenar linhas com um único valor específico na coluna de distribuição. Ele é especialmente útil para o caso de uso de hiperescala (Citus) de vários locatários, em que um locatário grande pode ser colocado sozinho em seu próprio fragmento e, por fim, em seu próprio nó físico.

#### <a name="arguments"></a>Argumentos

**nome da tabela \_ :** o nome da tabela para obter um novo fragmento.

**ID do locatário \_ :** o valor da coluna de distribuição que será atribuída ao novo fragmento.

**opção CASCADE \_ :** (opcional) quando definida como \" Cascade, \" também isola um fragmento de todas as tabelas no [grupo de colocalização](concepts-hyperscale-colocation.md)da tabela atual.

#### <a name="return-value"></a>Valor Retornado

**ID do fragmento \_ :** a função retorna a ID exclusiva atribuída ao fragmento recém-criado.

#### <a name="examples"></a>Exemplos

Crie um novo fragmento para manter o LineItems para o locatário 135:

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>Próximas etapas

* Muitas das funções neste artigo modificam as [tabelas de metadados](reference-hyperscale-metadata.md) do sistema
* [Parâmetros de servidor](reference-hyperscale-parameters.md) personalizam o comportamento de algumas funções
