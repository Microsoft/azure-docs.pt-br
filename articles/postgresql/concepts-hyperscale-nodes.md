---
title: Nós – Citus (hiperescala) – banco de dados do Azure para PostgreSQL
description: Saiba mais sobre os tipos de nós e tabelas em um grupo de servidores no banco de dados do Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2019
ms.openlocfilehash: b3eda2c8de8319552f32938f20ff98af0e0a49fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91314819"
---
# <a name="nodes-and-tables-in-azure-database-for-postgresql--hyperscale-citus"></a>Nós e tabelas no banco de dados do Azure para PostgreSQL – Citus (hiperescala)

## <a name="nodes"></a>Nós

O tipo de Hospedagem de hiperescala (Citus) permite que os servidores do banco de dados do Azure para PostgreSQL (chamados de nós) se coordenem entre si em uma arquitetura "nada compartilhado". Os nós em um grupo de servidores contêm, coletivamente, mais dados e usam mais núcleos de CPU do que seria possível em um único servidor. A arquitetura também permite que o banco de dados seja dimensionado adicionando mais nós ao grupo de servidores.

### <a name="coordinator-and-workers"></a>Coordenador e trabalhadores

Cada grupo de servidores tem um nó de coordenador e vários trabalhadores. Os aplicativos enviam suas consultas para o nó de coordenador, que os retransmite para os trabalhadores relevantes e acumula seus resultados. Os aplicativos não podem se conectar diretamente aos trabalhadores.

O Citus (hiperescala) permite que o administrador de banco de dados *distribua* tabelas, armazenando linhas diferentes em nós de trabalho diferentes. As tabelas distribuídas são a chave para o desempenho de hiperescala (Citus). A incapacidade de distribuir tabelas as deixa totalmente no nó de coordenador e não pode tirar proveito do paralelismo entre máquinas.

Para cada consulta em tabelas distribuídas, o coordenador a roteia para um único nó de trabalho ou a paralelize em vários dependendo se os dados necessários residem em um único nó ou vários. O coordenador decide o que fazer por meio das tabelas de metadados de consultoria. Essas tabelas acompanham os nomes DNS e a integridade dos nós de trabalho e a distribuição de dados entre nós.

## <a name="table-types"></a>Tipos de tabela

Há três tipos de tabelas em um grupo de servidores de hiperescala (Citus), cada uma armazenada de forma diferente em nós e usada para finalidades diferentes.

### <a name="type-1-distributed-tables"></a>Tipo 1: tabelas distribuídas

O primeiro tipo e mais comum são tabelas distribuídas. Eles parecem ser tabelas normais para instruções SQL, mas são particionados horizontalmente entre nós de trabalho. Isso significa que as linhas da tabela são armazenadas em nós diferentes, em tabelas de fragmento chamadas de fragmentos.

O Citus (hiperscale) executa não apenas instruções SQL, mas DDL em um cluster.
Alterar o esquema de uma tabela distribuída em cascata para atualizar todos os fragmentos da tabela entre os trabalhadores.

#### <a name="distribution-column"></a>Coluna de distribuição

O Citus (hiperscale) usa a fragmentação de algoritmos para atribuir linhas a fragmentos. A atribuição é feita de forma determinista com base no valor de uma coluna de tabela chamada coluna de distribuição. O administrador de cluster deve designar esta coluna ao distribuir uma tabela.
Fazer a escolha certa é importante para desempenho e funcionalidade.

### <a name="type-2-reference-tables"></a>Tipo 2: tabelas de referência

Uma tabela de referência é um tipo de tabela distribuída cujo conteúdo inteiro está concentrado em um único fragmento. O fragmento é replicado em todos os trabalhadores. As consultas em qualquer trabalho podem acessar as informações de referência localmente, sem a sobrecarga de rede de solicitar linhas de outro nó. Tabelas de referência não têm nenhuma coluna de distribuição porque não há necessidade de distinguir fragmentos separados por linha.

As tabelas de referência são geralmente pequenas e são usadas para armazenar dados relevantes para consultas em execução em qualquer nó de trabalho. Um exemplo são valores enumerados como status da ordem ou categorias de produtos.

### <a name="type-3-local-tables"></a>Tipo 3: tabelas locais

Quando você usa o Citus (hiperescala), o nó de coordenador ao qual você se conecta é um banco de dados PostgreSQL normal. Você pode criar tabelas comuns no coordenador e optar por não fragmentá-las.

Um bom candidato para tabelas locais seria pequenas tabelas administrativas que não participam de consultas de junção. Um exemplo é uma tabela de usuários para entrada e autenticação do aplicativo.

## <a name="shards"></a>Fragmentos

A seção anterior descreveu como as tabelas distribuídas são armazenadas como fragmentos em nós de trabalho. Esta seção aborda mais detalhes técnicos.

A `pg_dist_shard` tabela de metadados no coordenador contém uma linha para cada fragmento de cada tabela distribuída no sistema. A linha corresponde a uma ID de fragmento com um intervalo de inteiros em um espaço de hash (shardminvalue, shardmaxvalue).

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Se o nó de coordenador quiser determinar qual fragmento contém uma linha `github_events` , ele faz hash do valor da coluna de distribuição na linha. Em seguida, o nó verifica qual \' intervalo de s de fragmentos contém o valor de hash. Os intervalos são definidos de forma que a imagem da função de hash seja sua União não-junção.

### <a name="shard-placements"></a>Posicionamentos de fragmentos

Suponha que o fragmento 102027 esteja associado à linha em questão. A linha é lida ou gravada em uma tabela chamada `github_events_102027` em um dos trabalhadores. Qual trabalhador? Isso é determinado inteiramente pelas tabelas de metadados. O mapeamento do fragmento para o Worker é conhecido como o posicionamento do fragmento.

O nó coordenador reescreve as consultas em fragmentos que se referem a tabelas específicas como `github_events_102027` e executa esses fragmentos nos trabalhadores apropriados. Veja um exemplo de uma consulta executada nos bastidores para localizar o nó que contém a ID de fragmento 102027.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>Próximas etapas

- [Determinar o tipo do aplicativo](concepts-hyperscale-app-type.md) para preparar a modelagem de dados
