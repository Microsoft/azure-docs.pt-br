---
title: Colocação de tabela-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Como armazenar informações relacionadas juntas para consultas mais rápidas
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 842563319e09a001fd6e85403d8aee6fb14690ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90884429"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Colocação de tabela no banco de dados do Azure para PostgreSQL – Citus (hiperescala)

A colocalização significa armazenar informações relacionadas nos mesmos nós. As consultas podem ficar rápidas quando todos os dados necessários estiverem disponíveis sem qualquer tráfego de rede. A colocação de dados relacionados em nós diferentes permite que as consultas sejam executadas com eficiência em paralelo em cada nó.

## <a name="data-colocation-for-hash-distributed-tables"></a>Colocação de dados para tabelas distribuídas por hash

No banco de dados do Azure para PostgreSQL – Citus (hiperescala), uma linha é armazenada em um fragmento se o hash do valor na coluna de distribuição estiver dentro do intervalo de hash do fragmento. Os fragmentos com o mesmo intervalo de hash sempre são colocados no mesmo nó. Linhas com valores de coluna de distribuição iguais sempre estão no mesmo nó entre tabelas.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-shards.png" alt-text="Fragmentos":::

## <a name="a-practical-example-of-colocation"></a>Um exemplo prático de colocação

Considere as tabelas a seguir que podem ser parte de um SaaS Web Analytics multilocatário:

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

Agora queremos responder a consultas que podem ser emitidas por um painel voltado para o cliente. Um exemplo de consulta é "retornar o número de visitas na última semana para todas as páginas que começam com"/blog "no locatário seis."

Se nossos dados estavam na opção de implantação Single-Server, poderíamos expressar facilmente nossa consulta usando o conjunto avançado de operações relacionais oferecidas pelo SQL:

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Desde que o [conjunto de trabalho](https://en.wikipedia.org/wiki/Working_set) para essa consulta caiba na memória, uma tabela de servidor único é uma solução apropriada. Vamos considerar as oportunidades de dimensionar o modelo de dados com a opção de implantação de hiperescala (Citus).

### <a name="distribute-tables-by-id"></a>Distribuir tabelas por ID

As consultas de servidor único começam a diminuir a velocidade conforme o número de locatários e os dados armazenados para cada locatário crescem. O conjunto de trabalho para de se ajustar na memória e a CPU se torna um afunilamento.

Nesse caso, podemos fragmentar os dados em vários nós usando o Citus (hiperescala). A primeira e mais importante opção que precisamos fazer quando decidimos fragmentar é a coluna de distribuição. Vamos começar com uma opção simples de usar `event_id` para a tabela de eventos e `page_id` para a `page` tabela:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Quando os dados são dispersos em diferentes trabalhadores, não podemos realizar uma junção como faria em um único nó PostgreSQL. Em vez disso, precisamos emitir duas consultas:

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

Posteriormente, os resultados das duas etapas precisam ser combinados pelo aplicativo.

Executar as consultas deve consultar dados em fragmentos espalhados entre nós.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-inefficient-queries.png" alt-text="Fragmentos":::

Nesse caso, a distribuição de dados cria desvantagens substanciais:

-   Sobrecarga da consulta de cada fragmento e execução de várias consultas.
-   Sobrecarga de Q1 retornando muitas linhas para o cliente.
-   Q2 torna-se grande.
-   A necessidade de escrever consultas em várias etapas requer alterações no aplicativo.

Os dados são dispersos, portanto, as consultas podem ser paralelizadas. Só é benéfico se a quantidade de trabalho que a consulta faz é consideravelmente maior do que a sobrecarga de consultar muitos fragmentos.

### <a name="distribute-tables-by-tenant"></a>Distribuir tabelas por locatário

Em hiperescala (Citus), as linhas com o mesmo valor de coluna de distribuição têm a garantia de estar no mesmo nó. A partir de então, podemos criar nossas tabelas com `tenant_id` como a coluna de distribuição.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Agora o Citus (subscale) pode responder à consulta de servidor único original sem modificação (Q1):

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Devido ao filtro e à junção em tenant_id, o Citus (hiperescala) sabe que toda a consulta pode ser respondida usando o conjunto de fragmentos colocalizados que contêm os dados para esse locatário específico. Um único nó PostgreSQL pode responder à consulta em uma única etapa.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-better-query.png" alt-text="Fragmentos":::

Em alguns casos, as consultas e os esquemas de tabela devem ser alterados para incluir a ID do locatário em restrições exclusivas e condições de junção. Essa alteração é normalmente simples.

## <a name="next-steps"></a>Próximas etapas

- Veja como os dados de locatário estão colocalizados no [tutorial de vários locatários](tutorial-design-database-hyperscale-multi-tenant.md).
