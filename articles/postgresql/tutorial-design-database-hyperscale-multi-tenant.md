---
title: 'Tutorial: Criar um banco de dados multilocatário – Hiperescala (Citus) – Banco de Dados do Azure para PostgreSQL'
description: Este tutorial mostra como ativar um aplicativo multilocatário escalonável com Hiperescala (Citus) do Banco de Dados do Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: d8dee086f55c5596af50245b5271d98536eb7e57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026192"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Tutorial: criar um banco de dados multilocatário usando o Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus)

Neste tutorial, você usa o Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus) para saber como:

> [!div class="checklist"]
> * Criar um grupo de servidores Hyperscale (Citus)
> * Usar o utilitário psql para criar um esquema
> * Fragmentar tabelas entre nós
> * Ingerir dados de exemplo
> * Consultar dados do locatário
> * Consultar dados entre locatários
> * Personalizar o esquema por locatário

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Usar o utilitário psql para criar um esquema

Uma vez conectado ao Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus) usando o psql, é possível realizar algumas tarefas básicas. Este tutorial orienta você durante a criação de um aplicativo Web que permite aos anunciantes acompanharem suas campanhas.

Várias empresas podem usar o aplicativo, então, vamos criar uma tabela para armazenar as empresas e outra para as campanhas delas. No console do psql, execute estes comandos:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Cada campanha pagará para executar anúncios. Também adicione uma tabela para anúncios, executando o seguinte código no psql após o código acima:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Por fim, vamos rastrear as estatísticas sobre cliques e impressões para cada anúncio:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

Agora é possível ver as tabelas recém-criadas na lista de tabelas com este comando do psql:

```postgres
\dt
```

Aplicativos multilocatários podem impor exclusividade apenas por locatário, por isso, todas as chaves primárias e estrangeiras incluem a ID da empresa.

## <a name="shard-tables-across-nodes"></a>Fragmentar tabelas entre nós

Uma implantação em hiperescala armazena as linhas da tabela em diferentes nós com base no valor de uma coluna designada pelo usuário. Essa "coluna de distribuição" marca qual locatário possui quais linhas.

Vamos definir a coluna de distribuição como empresa\_id, o identificador do locatário. No psql, execute estas funções:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="ingest-sample-data"></a>Ingerir dados de exemplo

Agora, fora do psql, na linha de comando normal, baixe conjuntos de dados de exemplo:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Novamente dentro do psql, carregue em massa os dados. Certifique-se de executar o psql no mesmo diretório em que você baixou os arquivos de dados.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Esses dados agora serão espalhados entre nós de trabalho.

## <a name="query-tenant-data"></a>Consultar dados do locatário

Quando o aplicativo solicita dados para um locatário único, o banco de dados pode executar a consulta em um nó de trabalho único. Consultas de locatário único filtram por uma ID de locatário único. Por exemplo, a consulta a seguir filtra `company_id = 5` para anúncios e impressões. Tente executá-la no psql para ver os resultados.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Consultar dados entre locatários

Até agora todas as tabelas foram distribuídas por `company_id`, mas alguns dados não "pertencem" naturalmente a qualquer locatário em particular e podem ser compartilhados. Por exemplo, todas as empresas na plataforma de anúncios de exemplo talvez queiram obter informações geográficas do respectivo público-alvo com base em endereços IP.

Crie uma tabela para armazenar informações geográficas compartilhadas. Executar os seguintes comandos na psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Em seguida, faça com `geo_ips` uma "tabela de referência" para armazenar uma cópia da tabela em cada nó de trabalho.

```sql
SELECT create_reference_table('geo_ips');
```

Carregue-a com dados de exemplo. Lembre-se de executar esse comando em psql de dentro do diretório em que o conjunto de dados foi baixado.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Unir a tabela de cliques com geo\_ips é eficiente em todos os nós.
Aqui está uma junção para encontrar as localizações de todos aqueles que clicaram no anúncio
290. Tente executar a consulta no psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Personalizar o esquema por locatário

Cada locatário pode precisar armazenar informações especiais não necessárias para outras pessoas. No entanto, todos os locatários compartilham uma infraestrutura comum com um esquema de banco de dados idêntico. Para onde os dados extras podem ir?

Um truque é usar um tipo de coluna aberta como JSONB do PostgreSQL.  Nosso esquema tem um campo JSONB `clicks` chamado `user_data`.
Uma empresa (digamos empresa cinco), pode usar a coluna para rastrear se o usuário está em um dispositivo móvel.

Aqui está uma consulta para descobrir quem clica mais: visitantes móveis ou tradicionais.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Podemos otimizar essa consulta para uma única empresa criando um [índice parcial](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

De modo mais geral, é possível criar [índices GIN](https://www.postgresql.org/docs/current/static/gin-intro.html) em cada chave e valor dentro da coluna.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você criou recursos do Azure em um grupo de servidores. Caso esses recursos não sejam mais necessários no futuro, exclua o grupo de servidores. Pressione o botão *Excluir* na página *Visão geral* do grupo de servidores. Quando solicitado em uma página pop-up, confirme o nome do grupo de servidores e clique no botão *Excluir* final.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a provisionar um grupo de servidores Hyperscale (Citus). Você conectou ele com o psql, criou um esquema e distribuiu dados. Você aprendeu a consultar dados de dentro e entre locatários, além de personalizar o esquema por locatário.

- Saiba mais sobre os [tipos de nó](./concepts-hyperscale-nodes.md) do grupo de servidores
- Determinar o melhor [tamanho inicial](howto-hyperscale-scale-initial.md) para seu grupo de servidores
