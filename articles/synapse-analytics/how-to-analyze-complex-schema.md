---
title: Analisar o esquema com matrizes e estruturas aninhadas
description: Como analisar matrizes e estruturas aninhadas com Apache Spark e SQL
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: spark
ms.date: 06/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: d4004887a4806a5b8e50429031fb204f4eec5000
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627958"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Analisar tipos de dados complexos no Azure Synapse Analytics

Este artigo é relevante para arquivos e contêineres parquet no [link Synapse do Azure para Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). Você pode usar o Spark ou o SQL para ler ou transformar dados com esquemas complexos, como matrizes ou estruturas aninhadas. O exemplo a seguir é concluído com um único documento, mas pode ser facilmente dimensionado para bilhões de documentos com Spark ou SQL. O código incluído neste artigo usa PySpark (Python).

## <a name="use-case"></a>Caso de uso

Os tipos de dados complexos são cada vez mais comuns e representam um desafio para engenheiros de dados. Analisar o esquema aninhado e as matrizes pode envolver consultas SQL demoradas e complexas. Além disso, pode ser difícil renomear ou converter o tipo de dados de colunas aninhadas. Além disso, quando você estiver trabalhando com objetos profundamente aninhados, poderá encontrar problemas de desempenho.

Os engenheiros de dados precisam entender como processar com eficiência tipos de dados complexos e torná-los facilmente acessíveis a todos. No exemplo a seguir, você usa o Spark no Azure Synapse Analytics para ler e transformar objetos em uma estrutura plana por meio de quadros de dados. Você usa o modelo sem servidor do SQL no Azure Synapse Analytics para consultar esses objetos diretamente e retornar esses resultados como uma tabela normal.

## <a name="what-are-arrays-and-nested-structures"></a>O que são matrizes e estruturas aninhadas?

O objeto a seguir vem de [Application insights](../azure-monitor/app/app-insights-overview.md). Nesse objeto, há estruturas aninhadas e matrizes que contêm estruturas aninhadas.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Exemplo de esquema de matrizes e estruturas aninhadas
Quando você estiver imprimindo o esquema do quadro de dados do objeto (chamado de **DF**) com o comando `df.printschema` , verá a seguinte representação:

* Amarelo representa estruturas aninhadas.
* Green representa uma matriz com dois elementos.

[![Código com realce amarelo e verde, mostrando a origem do esquema](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

`_rid`, `_ts` e `_etag` foram adicionados ao sistema, pois o documento foi ingerido no armazenamento transacional Azure Cosmos DB.

O quadro de dados anterior conta apenas para 5 colunas e 1 linha. Após a transformação, o quadro de dados organizado terá 13 colunas e 2 linhas, em um formato tabular.

## <a name="flatten-nested-structures-and-explode-arrays"></a>Mesclar estruturas aninhadas e detalhar matrizes

Com o Spark no Azure Synapse Analytics, é fácil transformar estruturas aninhadas em colunas e elementos de matriz em várias linhas. Use as etapas a seguir para implementação.

[![Fluxograma mostrando etapas para transformações do Spark](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>Definir uma função para mesclar o esquema aninhado

Você pode usar essa função sem alteração. Crie uma célula em um [bloco de anotações PySpark](quickstart-apache-spark-notebook.md) com a seguinte função:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Usar a função para mesclar o esquema aninhado

Nesta etapa, você mescla o esquema aninhado do data frame (**DF**) em um novo quadro de dados ( `df_flat` ):

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

A função de exibição deve retornar 10 colunas e 1 linha. A matriz e seus elementos aninhados ainda estão lá.

### <a name="transform-the-array"></a>Transformar a matriz

Aqui, você transforma a matriz, `context_custom_dimensions` , no quadro de dados `df_flat` , em um novo quadro de dados `df_flat_explode` . No código a seguir, você também define qual coluna selecionar:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

A função de exibição deve retornar 10 colunas e 2 linhas. A próxima etapa é mesclar esquemas aninhados com a função definida na etapa 1.

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Usar a função para mesclar o esquema aninhado

Por fim, você usa a função para mesclar o esquema aninhado do quadro de dados `df_flat_explode` em um novo quadro de dados, `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

A função de exibição deve mostrar 13 colunas e 2 linhas.

A função `printSchema` do quadro de dados `df_flat_explode_flat` retorna o seguinte resultado:

[![Código mostrando o esquema final](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>Ler matrizes e estruturas aninhadas diretamente

Com o modelo sem servidor do SQL, você pode consultar e criar exibições e tabelas em tais objetos.

Primeiro, dependendo de como os dados foram armazenados, os usuários devem usar a taxonomia a seguir. Tudo o que é mostrado em letras maiúsculas é específico ao seu caso de uso:

| Em massa | Formatar |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; Account = ACCOUNTNAME; banco de dados = DatabaseName; coleção = CollectionName; região = REGIONTOQUERY ', segredo = ' YOURSECRET ' |' CosmosDB ' (link Synapse do Azure)|


Substitua cada campo da seguinte maneira:
* ' Sua massa acima ' é a cadeia de conexão da fonte de dados à qual você se conecta.
* ' Seu tipo acima ' é o formato que você usa para se conectar à origem.

```sql
select *
FROM
openrowset(
    BULK 'YOUR BULK ABOVE',
    FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Há dois tipos diferentes de operações:

- O primeiro tipo de operação é indicado na linha de código a seguir, que define a coluna chamada `contextdataeventTime` que se refere ao elemento aninhado, `Context.Data.eventTime` . 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  Essa linha define a coluna chamada `contextdataeventTime` que se refere ao elemento aninhado, `Context>Data>eventTime` .

- O segundo tipo de operação usa `cross apply` para criar novas linhas para cada elemento na matriz. Em seguida, ele define cada objeto aninhado. 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  Se a matriz tivesse cinco elementos com 4 estruturas aninhadas, o modelo sem servidor do SQL retornará 5 linhas e 4 colunas. O modelo sem servidor do SQL pode ser consultado no local, mapear a matriz em 2 linhas e exibir todas as estruturas aninhadas em colunas.

## <a name="next-steps"></a>Próximas etapas

* [Saiba como consultar o link do Synapse para Azure Cosmos DB com o Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Consultar tipos aninhados Parquet](./sql/query-parquet-nested-types.md)