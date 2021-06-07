---
title: Escalar horizontalmente o banco de dados do Azure para PostgreSQL grupo de servidores de hiperescala
description: Escalar horizontalmente o banco de dados do Azure para PostgreSQL grupo de servidores de hiperescala
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 17bdae658c7095c44a7ae9f30fd85a6c45bf1546
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96779967"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>Escalar horizontalmente seu grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc adicionando mais nós de trabalho
Este documento explica como escalar horizontalmente um grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc. Ele faz isso fazendo um cenário. **Se você não quiser executar o cenário e quiser apenas ler sobre como escalar horizontalmente, vá para a [escala](#scale-out)horizontal do parágrafo**.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>Introdução
Se você já estiver familiarizado com o modelo de dimensionamento da hiperescala do PostgreSQL habilitada para o Azure ARC ou o Citus (banco de dados do Azure para PostgreSQL), você pode ignorar este parágrafo. Se você não estiver, é recomendável começar lendo sobre este modelo de dimensionamento na página de documentação do banco de dados do Azure para PostgreSQL Citus (hiperescala). O banco de dados do Azure para PostgreSQL Citus (hiperescala) é a mesma tecnologia hospedada como um serviço no Azure (plataforma como um serviço também conhecida como PAAS) em vez de ser oferecida como parte dos serviços de dados habilitados para Arc do Azure:
- [Nós e tabelas](../../postgresql/concepts-hyperscale-nodes.md)
- [Determinar o tipo de aplicativo](../../postgresql/concepts-hyperscale-app-type.md)
- [Escolher uma coluna de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [Colocação de tabela](../../postgresql/concepts-hyperscale-colocation.md)
- [Distribuir e modificar tabelas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [Criar um banco de dados de vários locatários](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [Criar um painel de análise em tempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* Nos documentos acima, ignore as seções **entrar no portal do Azure**, & **criar um banco de dados do Azure para PostgreSQL-Citus (hiperescala)**. Implemente as etapas restantes em sua implantação do Azure Arc. Essas seções são específicas para o banco de dados do Azure para PostgreSQL Citus (hiperescala) oferecida como um serviço de PaaS na nuvem do Azure, mas as outras partes dos documentos são diretamente aplicáveis à sua hiperescala PostgreSQL habilitada para o Arc do Azure.

## <a name="scenario"></a>Cenário
Esse cenário refere-se ao grupo de servidores de hiperescala do PostgreSQL criado como um exemplo na documentação [criar um grupo de servidores de hiperescala do PostgreSQL habilitado para o Azure Arc](create-postgresql-hyperscale-server-group.md) .

### <a name="load-test-data"></a>Carregar dados de teste
O cenário usa uma amostra de dados do GitHub publicamente disponíveis, disponível no [site de dados do Citus](https://www.citusdata.com/) (os dados do Citus fazem parte da Microsoft).

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Conectar ao grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc

##### <a name="list-the-connection-information"></a>Listar as informações de conexão
Conecte-se ao grupo de servidores de hiperescala PostgreSQL do Azure com o Arc habilitado primeiro obtendo as informações de conexão: o formato geral deste comando é
```console
azdata arc postgres endpoint list -n <server name>
```
Por exemplo:
```console
azdata arc postgres endpoint list -n postgres01
```

Saída de exemplo:

```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>Conecte-se com a ferramenta de cliente de sua escolha.

Execute a consulta a seguir para verificar se você tem dois (ou mais nós de trabalho de hiperescala), cada um correspondente a um pod kubernetes:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>Criar um esquema de exemplo
Crie duas tabelas executando a seguinte consulta:

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

JSONB é o tipo de dados JSON em formato binário no PostgreSQL. Ele armazena um esquema flexível em uma única coluna e com PostgreSQL. O esquema terá um índice Iniciar para indexar cada chave e valor dentro dele. Com um índice Iniciar, torna-se rápido e fácil consultar várias condições diretamente nessa carga. Vamos continuar e criar alguns índices antes de carregarmos nossos dados:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Para fragmentar tabelas padrão, execute uma consulta para cada tabela. Especifique a tabela que desejamos fragmentar e a chave para a qual desejamos fragmentá-la. Nós iremos fragmentar a tabela de eventos e de usuários em user_id:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>Carregar dados de exemplo
Carregar os dados com cópia... DO PROGRAMA:

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>Consultar os dados
E agora, meça por quanto tempo uma consulta simples leva com dois nós:

```sql
SELECT COUNT(*) FROM github_events;
```
Anote o tempo de execução da consulta.


## <a name="scale-out"></a>Escalar horizontalmente
O formato geral do comando de expansão é:
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

> [!CAUTION]
> A versão prévia não é compatível com o dimensionamento reverso. Por exemplo, ainda não é possível reduzir o número de nós de trabalho. Se você precisar fazer isso, será necessário extrair/fazer backup dos dados, descartar o grupo de servidores, criar um novo grupo de servidores com menos nós de trabalho e, em seguida, importar os dados.

Neste exemplo, aumentamos o número de nós de trabalho de 2 para 4, executando o seguinte comando:

```console
azdata arc postgres server edit -n postgres01 -w 4
```

Após a adição de nós, você verá um estado pendente para o grupo de servidores. Por exemplo:
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

Depois que os nós estiverem disponíveis, o rebalanceador de fragmentos de hiperescala será executado automaticamente e redistribuirá os dados para os novos nós. A operação de expansão é uma operação online. Embora os nós sejam adicionados e os dados sejam redistribuídos entre os nós, os dados permanecem disponíveis para consultas.

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>Verificar a nova forma do grupo de servidores (opcional)
Use qualquer um dos métodos abaixo para verificar se o grupo de servidores agora está usando os nós de trabalho adicionais que você adicionou.

#### <a name="with-azdata"></a>Com azdata:
Execute o comando:
```console
azdata arc postgres server list
```

Ele retorna a lista de grupos de servidores criados em seu namespace e indica o número de nós de trabalho. Por exemplo:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>Com kubectl:
Execute o comando:
```console
kubectl get postgresql-12
```

Ele retorna a lista de grupos de servidores criados em seu namespace e indica o número de nós de trabalho. Por exemplo:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> [!NOTE]
> Se você criou um grupo de servidores da versão 11 PostgreSQL em vez de 12, execute o seguinte comando em vez disso: _kubectl Get PostgreSQL-11_

#### <a name="with-a-sql-query"></a>Com uma consulta SQL:
Conecte-se ao seu grupo de servidores com a ferramenta cliente de sua escolha e execute a seguinte consulta:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>Retornar ao cenário

Se você quiser comparar o tempo de execução da consulta de contagem de seleção com o conjunto de dados de exemplo, use a mesma consulta de contagem. Ele pode ser usado em todos os quatro nós de trabalho, sem nenhuma alteração na instrução SQL.

```sql
SELECT COUNT(*) FROM github_events;
```
Observe o tempo de execução.


> [!NOTE]
> Dependendo do seu ambiente-por exemplo, se você tiver implantado seu grupo de servidores de teste com `kubeadm` em uma VM de nó único, poderá ver um aperfeiçoamento modesto no tempo de execução. Para ter uma ideia melhor do tipo de melhoria de desempenho que você poderia alcançar com o Azure PostgreSQL habilitado para o Arc, Assista aos seguintes vídeos curtos:
>* [HTAP de alto desempenho com Citus (hiperescala do PostgreSQL do Azure)](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [Criando aplicativos HTAP com Python & hiperescala do Azure PostgreSQL (Citus)](https://www.youtube.com/watch?v=YDT8_riLLs0)


## <a name="next-steps"></a>Próximas etapas

- Leia sobre como escalar verticalmente [(memória, vCores) seu grupo de servidores de hiperescala do PostgreSQL habilitado para o Azure Arc](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- Leia sobre como definir parâmetros de servidor em seu grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc
- Leia os conceitos e os guias de instruções do banco de dados do Azure para PostgreSQL de hiperescala para distribuir seus dados em vários nós de hiperescala PostgreSQL e para se beneficiar de toda a potência do banco de postgres do Azure para o hiperescala. :
    * [Nós e tabelas](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinar o tipo de aplicativo](../../postgresql/concepts-hyperscale-app-type.md)
    * [Escolher uma coluna de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocação de tabela](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuir e modificar tabelas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Criar um banco de dados de vários locatários](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Criar um painel de análise em tempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* Nos documentos acima, ignore as seções **entrar no portal do Azure**, & **criar um banco de dados do Azure para PostgreSQL-Citus (hiperescala)**. Implemente as etapas restantes em sua implantação do Azure Arc. Essas seções são específicas para o banco de dados do Azure para PostgreSQL Citus (hiperescala) oferecida como um serviço de PaaS na nuvem do Azure, mas as outras partes dos documentos são diretamente aplicáveis à sua hiperescala PostgreSQL habilitada para o Arc do Azure.

- [Configuração de armazenamento e conceitos de armazenamento kubernetes](storage-configuration.md)
- [Modelo de recurso kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
