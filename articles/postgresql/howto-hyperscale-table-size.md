---
title: Determinar o tamanho da tabela-Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Como encontrar o tamanho real de tabelas distribuídas em um grupo de servidores de hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937546"
---
# <a name="determine-table-and-relation-size"></a>Determinar o tamanho da tabela e da relação

A maneira usual de localizar tamanhos de tabela no PostgreSQL, `pg_total_relation_size` , drasticamente, relata o tamanho das tabelas distribuídas em Citus (hiperescala).
Toda essa função faz em um grupo de servidores Citus (hiperescala) para revelar o tamanho das tabelas no nó de coordenador.  Na realidade, os dados em tabelas distribuídas residem nos nós de trabalho (em fragmentos), não no coordenador. Uma medida verdadeira de tamanho de tabela distribuída é obtida como uma soma de tamanhos de fragmentos. O Citus (subscale) fornece funções auxiliares para consultar essas informações.

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>Função</th>
<th>Retornos</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size (relation_name)</td>
<td><ul>
<li>Tamanho dos dados reais na tabela (a "<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">bifurcação principal</a>").</li>
<li>Uma relação pode ser o nome de uma tabela ou um índice.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size (relation_name)</td>
<td><ul>
<li><p>citus_relation_size mais:</p>
<blockquote>
<ul>
<li>tamanho do <a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">mapa de espaço livre</a></li>
<li>tamanho do <a href="https://www.postgresql.org/docs/current/static/storage-vm.html">mapa de visibilidade</a></li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size (relation_name)</td>
<td><ul>
<li><p>citus_table_size mais:</p>
<blockquote>
<ul>
<li>tamanho dos índices</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

Essas funções são análogas a três das funções de [tamanho de objeto](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)PostgreSQL padrão, exceto se não conseguirem se conectar a um nó, eles retornarão erros.

## <a name="example"></a>Exemplo

Veja como listar os tamanhos de todas as tabelas distribuídas:

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

Saída:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>Próximas etapas

* Saiba como [dimensionar um grupo de servidores](howto-hyperscale-scale-grow.md) para conter mais dados.
* Distinguir [tipos de tabela](concepts-hyperscale-nodes.md) em um grupo de servidores de hiperescala (Citus).
