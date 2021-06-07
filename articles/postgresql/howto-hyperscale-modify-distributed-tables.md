---
title: Modificar tabelas distribuídas-Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Comandos SQL para criar e modificar tabelas distribuídas – Citus (hiperescala) usando o portal do Azure
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: cf9f9ca5b8690a38c6e5aa6f519378c0a2e3a4f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026428"
---
# <a name="distribute-and-modify-tables"></a>Distribuir e modificar tabelas

## <a name="distributing-tables"></a>Distribuindo tabelas

Para criar uma tabela distribuída, primeiro você precisa definir o esquema da tabela. Para fazer isso, você pode definir uma tabela usando a instrução [CREATE TABLE](http://www.postgresql.org/docs/current/static/sql-createtable.html) da mesma maneira que faria com uma tabela do PostgreSQL comum.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

Em seguida, você pode usar a \_ função criar tabela distribuída \_ () para especificar a coluna de distribuição de tabela e criar os fragmentos de trabalho.

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

A chamada de função informa hiperscale (Citus) de que a tabela de eventos do GitHub \_ deve ser distribuída na \_ coluna ID do repositório (por hash do valor da coluna). A função também cria fragmentos nos nós de trabalho usando os valores de configuração de fator de replicação citus. Fragment \_ e citus. Fragment. \_ \_

Ele cria um total de citus. \_ contagem de fragmentos de fragmentos, em que cada fragmento possui uma parte de um espaço de hash e é replicado com base no valor de configuração de fator de replicação padrão citus. Fragment \_ \_ . As réplicas de fragmento criadas no trabalho têm as mesmas definições de esquema de tabela, índice e restrição que a tabela no coordenador. Depois que as réplicas são criadas, a função salva todos os metadados distribuídos no coordenador.

Cada fragmento criado recebe uma ID de fragmento exclusiva e todas as suas réplicas têm a mesma ID de fragmento. Os fragmentos são representados no nó de trabalho como tabelas do PostgreSQL regulares nomeadas como TableName fragmentid \' \_ \' , em que TableName é o nome da tabela distribuída, e a ID de fragmento é a ID exclusiva atribuída. Você pode se conectar às instâncias de postgres de trabalho para exibir ou executar comandos em fragmentos individuais.

Agora você está pronto para inserir dados na tabela distribuída e executar consultas nela. Você também pode saber mais sobre o UDF usado nesta seção na referência de [DDL de tabela e fragmento](reference-hyperscale-functions.md#table-and-shard-ddl) .

### <a name="reference-tables"></a>Tabela de referência

O método acima distribui tabelas em vários fragmentos horizontais.  Outra possibilidade é distribuir tabelas em um único fragmento e replicar o fragmento para cada nó de trabalho. As tabelas distribuídas dessa maneira são chamadas de *tabelas de referência.* Eles são usados para armazenar dados que precisam ser acessados frequentemente por vários nós em um cluster.

Os candidatos comuns para tabelas de referência incluem:

-   Tabelas menores que precisam ingressar com tabelas distribuídas maiores.
-   Tabelas em aplicativos multilocatário que não têm uma coluna de ID de locatário ou que não estão \' associadas a um locatário. (Ou, durante a migração, até mesmo para algumas tabelas associadas a um locatário.)
-   Tabelas que precisam de restrições exclusivas em várias colunas e são pequenas o suficiente.

Por exemplo, suponha que um site de comércio eletrônico multilocatário precisa calcular o imposto sobre vendas de transações em qualquer uma de suas lojas. As informações fiscais não são \' específicas para nenhum locatário. Faz sentido colocá-lo em uma tabela compartilhada. Uma tabela de referência centrada nos EUA pode ser parecida com esta:

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

Agora, consultas como um imposto de cálculo para um carrinho de compras podem ingressar na `states` tabela sem sobrecarga de rede e adicionar uma chave estrangeira ao código de estado para uma melhor validação.

Além de distribuir uma tabela como um fragmento replicado único, o `create_reference_table` UDF marca-a como uma tabela de referência nas tabelas de metadados de hiperescala (Citus). O Citus (hiperscale) executa automaticamente confirmações de duas fases ([2pc](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)) para modificações em tabelas marcadas dessa forma, o que fornece garantias de consistência fortes.

Se você tiver uma tabela distribuída com uma contagem de fragmentos de uma, poderá atualizá-la para ser uma tabela de referência reconhecida como esta:

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

Para obter outro exemplo de como usar tabelas de referência, consulte o [tutorial de banco de dados de vários locatários](tutorial-design-database-hyperscale-multi-tenant.md).

### <a name="distributing-coordinator-data"></a>Distribuindo dados do coordenador

Se um banco de dados PostgreSQL existente for convertido no nó de coordenador para um cluster de hiperescala (Citus), os dados em suas tabelas poderão ser distribuídos com eficiência e com interrupção mínima para um aplicativo.

A `create_distributed_table` função descrita anteriormente funciona em tabelas vazias e não vazias e, para o último, ela distribui automaticamente linhas de tabela em todo o cluster. Você saberá se ele copia dados pela presença da mensagem, \" Observe: copiando dados da tabela local \. . \" Por exemplo:

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

As gravações na tabela são bloqueadas enquanto os dados são migrados e as gravações pendentes são tratadas como consultas distribuídas quando a função é confirmada. (Se a função falhar, as consultas se tornarão locais novamente.) As leituras podem continuar normalmente e se tornarão consultas distribuídas quando a função for confirmada.

Ao distribuir as tabelas A e B, em que um tem uma chave estrangeira para B, distribua primeiro a tabela de destino da chave B. Fazer isso na ordem errada causará um erro:

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

Se não for possível distribuir na ordem correta, descarte as chaves estrangeiras, distribua as tabelas e recrie as chaves estrangeiras.

Ao migrar dados de um banco de dado externo, como de uma nuvem do Amazon RDS para o Citus (subscale), primeiro crie as tabelas distribuídas de hiperescala (Citus) via `create_distributed_table` e, em seguida, copie os dados para a tabela.
A cópia em tabelas distribuídas evita a execução de espaço no nó de coordenador.

## <a name="colocating-tables"></a>Colocando tabelas

A colocação significa manter informações relacionadas nos mesmos computadores. Ele permite consultas eficientes, ao mesmo tempo em que aproveita a escalabilidade horizontal para todo o conjunto de todos. Para obter mais informações, consulte colocação em [colocalização](concepts-hyperscale-colocation.md).

As tabelas são colocalizadas em grupos. Para controlar manualmente a atribuição de grupo de colocalização de uma tabela, use o `colocate_with` parâmetro opcional de `create_distributed_table` . Se você não se \' preocupa com uma \' colocalização de tabela, omita esse parâmetro. Ele usa como padrão o valor `'default'` , que agrupa a tabela com qualquer outra tabela de colocalização padrão com o mesmo tipo de coluna de distribuição, contagem de fragmentos e fator de replicação. Se você quiser interromper ou atualizar essa colocação implícita, poderá usar `update_distributed_table_colocation()` .

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

Quando uma nova tabela não estiver relacionada a outras em seu grupo de colocalidades explícitas, especifique `colocated_with => 'none'` .

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

Dividir tabelas não relacionadas em seus próprios grupos de colocalidades melhorará o desempenho de [rebalanceamento de fragmentos](howto-hyperscale-scale-rebalance.md) , pois os fragmentos no mesmo grupo precisam ser movidos juntos.

Quando as tabelas estão de fato relacionadas (por exemplo, quando elas serão Unidas), pode fazer sentido para localizá-las explicitamente. Os ganhos da colocação apropriada são mais importantes do que qualquer sobrecarga de rebalanceamento.

Para colocar explicitamente várias tabelas, distribua uma e, em seguida, coloque as outras em seu grupo de colocalização. Por exemplo:

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

As informações sobre grupos de colocalização são armazenadas na tabela [pg_dist_colocation](reference-hyperscale-metadata.md#colocation-group-table) , enquanto [pg_dist_partition](reference-hyperscale-metadata.md#partition-table) revela quais tabelas são atribuídas a quais grupos.

## <a name="dropping-tables"></a>Descartando tabelas

Você pode usar o comando de soltar tabela padrão PostgreSQL para remover suas tabelas distribuídas. Assim como acontece com tabelas regulares, a tabela DROP remove todos os índices, regras, gatilhos e restrições existentes para a tabela de destino. Além disso, ele também descarta os fragmentos nos nós de trabalho e limpa seus metadados.

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>Modificando tabelas

O Citus (hiperescala) propaga automaticamente muitos tipos de instruções DDL.
A modificação de uma tabela distribuída no nó de coordenador também atualizará os fragmentos nos trabalhadores. Outras instruções DDL exigem a propagação manual e algumas outras são proibidas, como as que modificariam uma coluna de distribuição.
A tentativa de executar o DDL que não é elegível para a propagação automática gerará um erro e deixará as tabelas no nó do coordenador inalteradas.

Aqui está uma referência das categorias de instruções DDL que se propagam.
A propagação automática pode ser habilitada ou desabilitada com um [parâmetro de configuração](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean)

### <a name="addingmodifying-columns"></a>Adicionando/modificando colunas

O Citus (subscale) propaga a maioria dos comandos [ALTER TABLE](https://www.postgresql.org/docs/current/static/ddl-alter.html) automaticamente. Adicionar colunas ou alterar seus valores padrão funciona como faria em um banco de dados PostgreSQL de máquina única:

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

Alterações significativas em uma coluna existente, como renomear ou alterar seu tipo de dados, também são bem. No entanto, o tipo de dados da [coluna de distribuição](concepts-hyperscale-nodes.md#distribution-column) não pode ser alterado.
Esta coluna determina como os dados de tabela são distribuídos por meio do cluster de hiperescala (Citus) e modificar seu tipo de dados exigiria mover os dados.

A tentativa de fazer isso causa um erro:

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>Adicionando/removendo restrições

Usar o Citus (hiperescala) permite que você continue a desfrutar da segurança de um banco de dados relacional, incluindo restrições de banco de dados (consulte os [documentos](https://www.postgresql.org/docs/current/static/ddl-constraints.html)PostgreSQL).
Devido à natureza dos sistemas distribuídos, o Citus (subscale) não fará restrições de exclusividade de referência cruzada ou integridade referencial entre nós de trabalho.

Para configurar uma chave estrangeira entre tabelas distribuídas colocalizadas, inclua sempre a coluna de distribuição na chave. A inclusão da coluna de distribuição pode envolver a criação da chave.

As chaves estrangeiras podem ser criadas nessas situações:

-   entre duas tabelas locais (não distribuídas),
-   entre duas tabelas de referência,
-   entre duas tabelas distribuídas [colocalizadas](concepts-hyperscale-colocation.md) quando a chave inclui a coluna de distribuição ou
-   como uma tabela distribuída referenciando uma [tabela de referência](concepts-hyperscale-nodes.md#type-2-reference-tables)

Não há suporte para chaves estrangeiras de tabelas de referência para tabelas distribuídas.

> [!NOTE]
>
> As chaves primárias e as restrições de exclusividade devem incluir a coluna de distribuição. Adicioná-los a uma coluna de não distribuição gerará um erro

Este exemplo mostra como criar chaves primárias e estrangeiras em tabelas distribuídas:

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

Da mesma forma, inclua a coluna de distribuição em restrições de exclusividade:

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

Restrições não nulas podem ser aplicadas a qualquer coluna (distribuição ou não) porque não exigem pesquisas entre os trabalhadores.

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>Usando restrições não VÁLIDAs

Em algumas situações, pode ser útil impor restrições para novas linhas, ao mesmo tempo que permite que linhas não conformadas existentes permaneçam inalteradas. O Citus (hiperescala) dá suporte a esse recurso para restrições de verificação e chaves estrangeiras, usando o PostgreSQL \' s, \" não uma \" designação de restrição válida.

Por exemplo, considere um aplicativo que armazena perfis de usuário em uma [tabela de referência](concepts-hyperscale-nodes.md#type-2-reference-tables).

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

No decorrer do tempo, imagine que alguns não-endereços cheguem à tabela.

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

Gostaríamos de validar os endereços, mas o PostgreSQL normalmente não nos permite adicionar uma restrição de verificação que falha para as linhas existentes. No entanto *, ele permite que uma* restrição marcada como inválida:

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

Novas linhas agora estão protegidas.

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

Mais tarde, fora do horário de pico, um administrador de banco de dados pode tentar corrigir as linhas inválidas e revalidar a restrição.

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

A documentação do PostgreSQL tem mais informações sobre a restrição não válida e VALIDATE na seção [ALTER TABLE](https://www.postgresql.org/docs/current/sql-altertable.html) .

### <a name="addingremoving-indices"></a>Adicionando/removendo índices

O Citus (hiperescala) dá suporte à adição e remoção de [índices](https://www.postgresql.org/docs/current/static/sql-createindex.html):

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

A adição de um índice usa um bloqueio de gravação, que pode ser indesejável em um \" sistema de registro de vários locatários. \" Para minimizar o tempo de inatividade do aplicativo, crie o índice [simultaneamente](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY) . Esse método requer mais trabalho total do que uma compilação de índice padrão e leva mais tempo para ser concluído. No entanto, como permite que as operações normais continuem enquanto o índice é criado, esse método é útil para adicionar novos índices em um ambiente de produção.

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```
