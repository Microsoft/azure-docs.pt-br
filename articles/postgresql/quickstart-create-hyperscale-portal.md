---
title: Guia de Início Rápido – Criar um grupo de servidores – Hiperescala (Citus) – Banco de Dados do Azure para PostgreSQL
description: Guia de início rápido para criar e consultar tabelas distribuídas em Hiperescala (Citus) do Banco de Dados do Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/17/2020
ms.openlocfilehash: 03a6e927a074067e85f1a3adca38cae386d1af38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026209"
---
# <a name="quickstart-create-a-hyperscale-citus-server-group-in-the-azure-portal"></a>Guia de Início Rápido – Criar um grupo de servidores Hiperescala (Citus) no portal do Azure

O Banco de Dados do Azure para PostgreSQL é um serviço gerenciado usado para executar, gerenciar e dimensionar bancos de dados altamente disponíveis do PostgreSQL na nuvem. Este Guia de Início Rápido mostra como criar um Banco de Dados do Azure para PostgreSQL – grupo de servidores de Hiperescala (Citus) usando o portal do Azure. Você explorará os dados distribuídos: fragmentar tabelas entre os nós, ingerir dados de amostra e efetuar consultas que são executadas em vários nós.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Criar e distribuir tabelas

Uma vez conectado ao nó coordenador em hiperescala usando o psql, você pode concluir algumas tarefas básicas.

Dentro dos servidores da Hiperescala (Citus), há três tipos de tabelas:

- Tabelas fragmentadas ou distribuídas (espalhadas para auxiliar na escala de desempenho e paralelização)
- Tabelas de referência (várias cópias mantidas)
- Tabelas locais (geralmente usadas para tabelas de administrador interno)

Neste guia de início rápido, nos concentraremos principalmente em tabelas distribuídas e nos familiarizaremos com elas.

O modelo de dados que vamos trabalhar é simples: dados de evento e do usuário do GitHub. Eventos incluem a criação de fork, confirmações do git relacionadas a uma organização e muito mais.

Depois de se conectar via psql, vamos criar nossas tabelas. No console do psql, execute:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

O `payload` campo de `github_events` tem um tipo de dados JSONB. O JSONB é o tipo de dados JSON em formato binário no Postgres. O tipo de dados torna mais fácil armazenar um esquema flexível em uma única coluna.

O Postgres pode criar um índice `GIN` neste tipo que indexará cada chave e valor dentro dele. Com um índice, se tornará rápido e fácil consultar o conteúdo com várias condições. Vamos em frente para criar alguns índices antes de podermos carregar nossos dados. No psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Em seguida, vamos pegar as tabelas Postgres no nó coordenador e instruir a Hiperescala (Citus) a fragmentá-las entre os trabalhadores. Para fazer isso, executaremos uma consulta para cada tabela especificando a chave para fragmentá-la. No exemplo atual, fragmentaremos a tabela de eventos e de usuários em `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

Estamos prontos para carregar dados. Ainda no psql, saia da shell para baixar os arquivos:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Em seguida, carregue os dados dos arquivos nas tabelas distribuídas:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Executar consultas

Agora é a hora da diversão: realmente efetuar algumas consultas. Vamos começar com um simples `count (*)` para ver a quantidade de dados carregada:

```sql
SELECT count(*) from github_events;
```

Funcionou muito bem. Voltaremos para esse tipo de agregação em breve, mas agora vamos examinar algumas outras consultas. Na coluna `payload` JSONB há um bom volume de dados, mas varia com base no tipo de evento. Os eventos `PushEvent` contêm um tamanho que inclui o número de confirmações distintos para o envio por push. É possível usar isso para localizar o número total de confirmações por hora:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Até agora, as consultas envolveram os eventos github\_de modo exclusivo, mas podemos combinar essas informações com os usuários do github\_. Uma vez que criamos usuários fragmentados e eventos no mesmo identificador (`user_id`), as linhas de ambas as tabelas com IDs de usuário correspondentes serão [colocadas](concepts-hyperscale-colocation.md) nos mesmos nós de banco de dados e podem ser unidas facilmente.

Se ingressarmos em `user_id`, a Hiperescala (Citus) poderá efetuar push da execução da junção para os fragmentos para execução em paralelo nos nós de trabalho. Por exemplo, vamos encontrar os usuários que criaram o maior número de repositórios:

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você criou recursos do Azure em um grupo de servidores. Caso esses recursos não sejam mais necessários no futuro, exclua o grupo de servidores. Pressione o botão **Excluir** na página **Visão geral** do grupo de servidores. Quando solicitado em uma página pop-up, confirme o nome do grupo de servidores e clique no botão **Excluir** final.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu como provisionar um grupo de servidores Hyperscale (Citus). Você conectou ele com o psql, criou um esquema e distribuiu dados.

- Siga um tutorial para [compilar aplicativos escalonáveis de multilocatário](./tutorial-design-database-hyperscale-multi-tenant.md)
- Determinar o melhor [tamanho inicial](howto-hyperscale-scale-initial.md) para seu grupo de servidores
