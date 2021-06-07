---
title: 'Tutorial: Criar um painel em tempo real – Hiperescala (Citus) – Banco de Dados do Azure para PostgreSQL'
description: Este tutorial mostra como paralelizar consultas de painel em tempo real com Hiperescala (Citus) do Banco de Dados do Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 480af87519f8e11c14f009058b0f518bdfc34a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026260"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Tutorial: Criar um painel de análise em tempo real usando o Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus)

Neste tutorial, você usa o Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus) para saber como:

> [!div class="checklist"]
> * Criar um grupo de servidores Hyperscale (Citus)
> * Usar o utilitário psql para criar um esquema
> * Fragmentar tabelas entre nós
> * Gerar dados de exemplo
> * Executar rollups
> * Consultar dados brutos e agregados
> * Expirar dados

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Usar o utilitário psql para criar um esquema

Uma vez conectado ao Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus) usando o psql, é possível realizar algumas tarefas básicas. Este tutorial orienta você a ingerir dados de tráfego do Web Analytics e, em seguida, acumular os dados para fornecer painéis em tempo real com base nesses dados.

Vamos criar uma tabela que consumirá todos os nossos dados brutos de tráfego da Web. Execute os seguintes comandos no terminal do psql:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Também vamos criar uma tabela que conterá nossas agregações por minuto e uma tabela que mantém a posição do nosso último rollup. Execute os seguintes comandos em psql também:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Agora é possível ver as tabelas recém-criadas na lista de tabelas com este comando do psql:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Fragmentar tabelas entre nós

Uma implantação em hiperescala armazena as linhas da tabela em diferentes nós com base no valor de uma coluna designada pelo usuário. Essa "coluna de distribuição" marca como os dados são fragmentados entre nós.

Vamos definir a coluna de distribuição como site\_id, a chave de fragmentação. No psql, execute estas funções:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="generate-sample-data"></a>Gerar dados de exemplo

Agora, nosso grupo de servidores deve estar pronto para ingerir alguns dados. Podemos executar o seguinte localmente da nossa conexão `psql` para inserir dados continuamente.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

A consulta insere aproximadamente oito linhas por segundo. As linhas são armazenadas em diferentes nós de trabalho, conforme direcionado pela coluna de distribuição, `site_id`.

   > [!NOTE]
   > Deixe a consulta de geração de dados em execução e abra uma segunda conexão do psql para os comandos restantes neste tutorial.
   >

## <a name="query"></a>Consulta

A opção de hospedagem em hiperescala permite que vários nós processem as consultas em paralelo para aumentar a velocidade. Por exemplo, o banco de dados calcula agregações como SUM e COUNT em nós de trabalho e combina os resultados em uma resposta final.

Aqui está uma consulta para contagem de solicitações da Web por minuto, juntamente com algumas estatísticas.
Tente executá-la no psql e observe os resultados.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>Acumulando dados

A consulta anterior funciona bem nos estágios iniciais, mas o desempenho diminuirá conforme os dados são escalados. Até mesmo com o processamento distribuído, é mais rápido pré-computar os dados do que recalculá-los repetidamente.

Podemos garantir que nosso painel permaneça rápido, acumulando regularmente os dados brutos em uma tabela de agregação. Você pode experimentar com a duração de agregação. Usamos uma tabela de agregação por minuto, mas você poderia dividir dados em 5, 15 ou 60 minutos em vez disso.

Para executar esse pacote cumulativo mais facilmente, vamos colocá-lo em uma função plpgsql. Execute estes comandos no psql para criar a função `rollup_http_request`.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Com nossa função no local, execute-a para acumular os dados:

```sql
SELECT rollup_http_request();
```

E com nossos dados em um formulário pré-agregado, é possível consultar a tabela de rollup para obter o mesmo relatório anterior. Execute a seguinte consulta:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Expirar dados antigos

Os rollups realizam consultas mais rapidamente, porém ainda é necessário expirar os dados antigos para evitar custos de armazenamento não associados. Decida por quanto tempo você quer manter os dados para cada granularidade e usar consultas padrão para excluir dados expirados. No exemplo a seguir, decidimos manter os dados brutos de um dia e agregações por minuto de um mês:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

Em produção, é possível encapsular essas consultas em uma função e chamá-la a cada minuto em um trabalho de Cron.

## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você criou recursos do Azure em um grupo de servidores. Caso esses recursos não sejam mais necessários no futuro, exclua o grupo de servidores. Pressione o botão *Excluir* na página *Visão geral* do grupo de servidores. Quando solicitado em uma página pop-up, confirme o nome do grupo de servidores e clique no botão *Excluir* final.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a provisionar um grupo de servidores Hyperscale (Citus). Você conectou ele com o psql, criou um esquema e distribuiu dados. Você aprendeu a consultar os dados na forma bruta, agregar regularmente esses dados, consultar as tabelas agregadas e expirar os dados antigos.

- Saiba mais sobre os [tipos de nó](./concepts-hyperscale-nodes.md) do grupo de servidores
- Determinar o melhor [tamanho inicial](howto-hyperscale-scale-initial.md) para seu grupo de servidores
