---
title: Tabelas de metadados compartilhados
description: O Azure Synapse Analytics fornece um modelo de metadados compartilhados em que a criação de uma tabela no Pool do Apache Spark sem servidor o tornará acessível por meio do pool de SQL sem servidor e do pool de SQL dedicado sem duplicação dos dados.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: a8080720480beaeb7bc8692f2dcddddad5da0e3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548454"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Tabelas de metadados compartilhados do Azure Synapse Analytics


O Azure Synapse Analytics permite que os diferentes mecanismos computacionais do workspace compartilhem bancos de dados e tabelas compatíveis com Parquet entre os pools do Apache Spark e o pool de SQL sem servidor.

Depois que um banco de dados for criado por um trabalho do Spark, você poderá criar tabelas nele com o Spark que usam Parquet como o formato de armazenamento. Os nomes das tabelas serão convertidos em letras minúsculas e precisarão ser consultados usando um nome com letras minúsculas. Essas tabelas ficarão imediatamente disponíveis para consulta por qualquer um dos pools do Spark do workspace do Azure Synapse. Elas também podem ser usadas em qualquer um dos trabalhos do Spark sujeitos a permissões.

As tabelas criadas, gerenciadas e externas do Spark também são disponibilizadas como tabelas externas com o mesmo nome no banco de dados sincronizado correspondente no pool de SQL sem servidor. [Como expor uma tabela do Spark no SQL](#expose-a-spark-table-in-sql) fornece mais detalhes sobre a sincronização da tabela.

Como as tabelas são sincronizadas com o pool de SQL sem servidor de maneira assíncrona, haverá um atraso até que elas sejam exibidas.

## <a name="manage-a-spark-created-table"></a>Gerenciar uma tabela criada pelo Spark

Use o Spark para gerenciar bancos de dados criados pelo Spark. Por exemplo, exclua-o por meio de um trabalho do Pool do Apache Spark sem servidor e crie tabelas nele por meio do Spark.

Se você criar objetos nesse banco de dados de um pool de SQL sem servidor ou tentar remover o banco de dados, a operação falhará. O banco de dados Spark original não pode ser alterado pelo pool de SQL sem servidor.

## <a name="expose-a-spark-table-in-sql"></a>Expor uma tabela do Spark no SQL

### <a name="shared-spark-tables"></a>Tabelas do Spark compartilhadas

O Spark fornece dois tipos de tabelas que o Azure Synapse expõe no SQL automaticamente:

- Tabelas gerenciadas

  O Spark fornece muitas opções de como armazenar dados em tabelas gerenciadas, como TEXT, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA e LIBSVM. Normalmente, esses arquivos são armazenados no diretório `warehouse`, no qual os dados da tabela gerenciada são armazenados.

- Tabelas externas

  O Spark também fornece maneiras de criar tabelas externas com base nos dados existentes, fornecendo a opção `LOCATION` ou usando o formato Hive. Essas tabelas externas podem ser criadas em uma variedade de formatos de dados, incluindo Parquet.

No momento, o Azure Synapse compartilha apenas tabelas externas e gerenciadas do Spark que armazenam os dados no formato Parquet com os mecanismos SQL. As tabelas com suporte de outros formatos não são sincronizadas automaticamente. Você poderá conseguir sincronizar essas tabelas explicitamente como uma tabela externa no próprio Banco de Dados SQL se o mecanismo SQL der suporte ao formato subjacente da tabela.

### <a name="share-spark-tables"></a>Compartilhar tabelas do Spark

As tabelas gerenciadas e externas do Spark compartilháveis que são expostas no mecanismo SQL como tabelas externas com as seguintes propriedades:

- A fonte de dados da tabela externa do SQL é a fonte de dados que representa a pasta de localização da tabela do Spark.
- O formato de arquivo da tabela externa do SQL é Parquet.
- A credencial de acesso da tabela externa do SQL é passagem.

Como todos os nomes de tabelas do Spark são nomes de tabelas SQL válidos e todos os nomes de coluna do Spark são nomes de coluna SQL válidos, os nomes de tabelas e colunas do Spark serão usados para a tabela SQL externa.

As tabelas do Spark fornecem tipos de dados diferentes dos mecanismos do Synapse SQL. A seguinte tabela mapeia os tipos de dados de tabelas do Spark para os tipos SQL:

| Tipo de dados do Spark | Tipo de dados SQL | Comentários |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | Com a ordenação `Latin1_General_100_BIN2_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Serializado em JSON com a ordenação `Latin1_General_100_BIN2_UTF8` |
| `map`       |    `varchar(max)`   | Serializado em JSON com a ordenação `Latin1_General_100_BIN2_UTF8` |
| `struct`    |    `varchar(max)`   | Serializado em JSON com a ordenação `Latin1_General_100_BIN2_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Modelo de segurança

Os bancos de dados e as tabelas do Spark, bem como as respectivas representações sincronizadas no mecanismo SQL, serão protegidos no nível de armazenamento subjacente. Como eles, atualmente, não têm permissões nos próprios objetos, os objetos podem ser vistos no Pesquisador de Objetos.

A entidade de segurança que cria uma tabela gerenciada é considerada o proprietário dessa tabela e tem todos os direitos na tabela, bem como nas pastas e nos arquivos subjacentes. Além disso, o proprietário do banco de dados se tornará automaticamente o coproprietário da tabela.

Se você criar uma tabela externa SQL ou do Spark com passagem de autenticação, os dados só serão protegidos nos níveis de pasta e de arquivo. Se alguém consultar esse tipo de tabela externa, a identidade de segurança do emissor da consulta será transmitida para o sistema de arquivos, que verificará se há direitos de acesso.

Para obter mais informações sobre como definir permissões nas pastas e nos arquivos, confira [Banco de dados compartilhado do Azure Synapse Analytics](database.md).

## <a name="examples"></a>Exemplos

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Criar uma tabela gerenciada com suporte do Parquet no Spark e consultá-la no pool de SQL sem servidor

Nesse cenário, você tem um banco de dados do Spark chamado `mytestdb`. Confira [Criar um banco de dados do Spark e se conectar a ele com o pool de SQL sem servidor](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool).

Crie uma tabela gerenciada do Spark com o SparkSQL executando o seguinte comando:

```sql
    CREATE TABLE mytestdb.myparquettable(id int, name string, birthdate date) USING Parquet
```

Esse comando cria a tabela `myparquettable` no banco de dados `mytestdb`. Os nomes das tabelas serão convertidos em letras minúsculas. Após um pequeno atraso, você poderá ver a tabela no pool de SQL sem servidor. Por exemplo, execute a instrução a seguir no pool de SQL sem servidor.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Verifique se `myparquettable` está incluída nos resultados.

>[!NOTE]
>Uma tabela que não esteja usando Parquet como o formato de armazenamento não será sincronizada.

Em seguida, insira alguns valores na tabela do Spark, por exemplo, com as seguintes instruções C# Spark em um notebook C#:

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myparquettable");
```

Agora você pode ler os dados no pool de SQL sem servidor da seguinte maneira:

```sql
SELECT * FROM mytestdb.dbo.myparquettable WHERE name = 'Alice';
```

Você deverá obter a seguinte linha como resultado:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Criar uma tabela externa com suporte do Parquet no Spark e consultá-la no pool de SQL sem servidor

Neste exemplo, crie uma tabela externa do Spark com base nos arquivos de dados do Parquet que foram criados no exemplo anterior para a tabela gerenciada.

Por exemplo, com o SparkSQL, execute:

```sql
CREATE TABLE mytestdb.myexternalparquettable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Substitua o espaço reservado `<fs>` pelo nome do sistema de arquivos que é o sistema de arquivos padrão do workspace e o espaço reservado `<synapse_ws>` pelo nome do workspace do Synapse que você está usando para executar este exemplo.

O exemplo anterior cria a tabela `myextneralparquettable` no banco de dados `mytestdb`. Após um pequeno atraso, você poderá ver a tabela no pool de SQL sem servidor. Por exemplo, execute a instrução a seguir no pool de SQL sem servidor.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Verifique se `myexternalparquettable` está incluída nos resultados.

Agora você pode ler os dados no pool de SQL sem servidor da seguinte maneira:

```sql
SELECT * FROM mytestdb.dbo.myexternalparquettable WHERE name = 'Alice';
```

Você deverá obter a seguinte linha como resultado:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre os metadados compartilhados do Azure Synapse Analytics](overview.md)
- [Saiba mais sobre os bancos de dados de metadados compartilhados do Azure Synapse Analytics](database.md)


