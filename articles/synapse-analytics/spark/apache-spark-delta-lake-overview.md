---
title: Visão geral de como usar o Linux Foundation Delta Lake no Apache Spark para o Azure Synapse Analytics
description: Saiba como usar o Delta Lake no Apache Spark para o Azure Synapse Analytics, para criar e usar tabelas com propriedades ACID.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 1e0dfd597e7f445eeba6cef332d8ea12b27dc3a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676308"
---
# <a name="linux-foundation-delta-lake-overview"></a>Visão geral do Linux Foundation Delta Lake

Este artigo foi adaptado para mais clareza com base no equivalente original publicado [aqui](https://docs.delta.io/latest/quick-start.html). Ele ajudará você a explorar rapidamente os principais recursos do [Delta Lake](https://delta.io). O artigo fornece snippets de código que mostram como fazer leituras e gravações em tabelas do Delta Lake com base em consultas interativas, em lote e de streaming. Os snippets de código também estão disponíveis em um conjunto de notebooks [PySpark aqui](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb), [Scala aqui](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb) e [C# aqui](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb)

Veja o que abordaremos:

* Criar uma tabela
* Ler dados
* Atualizar os dados da tabela
* Substituir os dados da tabela
* Atualização condicional sem substituição
* Ler versões mais antigas dos dados usando a viagem no tempo
* Gravar um fluxo de dados em uma tabela
* Ler um fluxo de alterações de uma tabela
* Suporte a SQL

## <a name="configuration"></a>Configuração

Lembre-se de modificar o trecho abaixo conforme apropriado para o seu ambiente.

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

Resulta em:

'/delta/delta-table-335323'

## <a name="create-a-table"></a>Criar uma tabela

Para criar uma tabela do Delta Lake, grave um DataFrame fora do DataFrame no formato delta. Você pode alterar o formato de Parquet, CSV, JSON etc. para delta.

O código a seguir mostra como criar uma tabela do Delta Lake usando o esquema inferido do DataFrame.

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

Resulta em:

| ID|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>Ler dados

Leia os dados da tabela do Delta Lake especificando o caminho para os arquivos e o formato delta.

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

Resulta em:

| ID|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

A ordem dos resultados é diferente da mostrada acima, pois não havia nenhuma ordem explicitamente especificada antes da geração dos resultados.

## <a name="update-table-data"></a>Atualizar os dados da tabela

O Delta Lake dá suporte a várias operações para modificar tabelas usando APIs padrão do DataFrame. Esse é um dos grandes aprimoramentos adicionados pelo formato delta. O exemplo a seguir executa um trabalho em lotes para substituir os dados da tabela.

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

Resulta em:

| ID|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

Aqui você pode ver que todos os cinco registros foram atualizados para conter novos valores.

## <a name="save-as-catalog-tables"></a>Salvar os dados como tabelas de catálogo

O Delta Lake pode fazer gravações em tabelas de catálogos gerenciadas ou externas.

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

Resulta em:

|Banco de Dados|         tableName|isTemporary|
|--------|------------------|-----------|
| default|externaldeltatable|      false|
| default| manageddeltatable|      false|

Com esse código, você criou uma tabela no catálogo de um dataframe existente, chamada de tabela gerenciada. Em seguida, você definiu uma nova tabela externa no catálogo que usa uma localização existente, chamada de tabela externa. Na saída, você pode ver que ambas as tabelas, independentemente de como foram criadas, estão listadas no catálogo.

Agora você pode examinar as propriedades estendidas das duas tabelas

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

Resulta em:

|col_name                    |data_type                                                                                                    |comentário|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|id                          |BIGINT                                                                                                       |null   |
|                            |                                                                                                             |       |
|Informações detalhadas da tabela  |                                                                                                             |       |
|Banco de dados                    |default                                                                                                      |       |
|Tabela                       |manageddeltatable                                                                                            |       |
|Proprietário                       |trusted-service-user                                                                                         |       |
|Hora de criação                |Sáb 25 de abr 00h35min34s UTC 2020                                                                                 |       |
|Último acesso                 |Qui 1 de jan 00h00min00s UTC 1970                                                                                 |       |
|Criado por                  |Spark 2.4.4.2.6.99.201-11401300                                                                              |       |
|Tipo                        |GERENCIADO                                                                                                      |       |
|Provedor                    |delta                                                                                                        |       |
|Propriedades da tabela            |[transient_lastDdlTime=1587774934]                                                                           |       |
|Estatísticas                  |2\.407 bytes                                                                                                   |       |
|Localização                    |abfss://data@<data lake>.dfs.core.windows.net/synapse/workspaces/<workspace name>/warehouse/manageddeltatable|       |
|Biblioteca Serde               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                                                           |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                                                             |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat                                                    |       |
|Propriedades de armazenamento          |[serialization.format=1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

Resulta em:

|col_name                    |data_type                                                             |comentário|
|----------------------------|----------------------------------------------------------------------|-------|
|id                          |BIGINT                                                                |null   |
|                            |                                                                      |       |
|Informações detalhadas da tabela  |                                                                      |       |
|Banco de dados                    |default                                                               |       |
|Tabela                       |externaldeltatable                                                    |       |
|Proprietário                       |trusted-service-user                                                  |       |
|Hora de criação                |Sáb 25 de abr 00h35min38s UTC 2020                                          |       |
|Último acesso                 |Qui 1 de jan 00h00min00s UTC 1970                                          |       |
|Criado por                  |Spark 2.4.4.2.6.99.201-11401300                                       |       |
|Tipo                        |EXTERNAL                                                              |       |
|Provedor                    |DELTA                                                                 |       |
|Propriedades da tabela            |[transient_lastDdlTime=1587774938]                                    |       |
|Localização                    |abfss://data@<data lake>.dfs.core.windows.net/delta/delta-table-587152|       |
|Biblioteca Serde               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                    |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                      |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat             |       |
|Propriedades de armazenamento          |[serialization.format=1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>Atualização condicional sem substituição

O Delta Lake fornece APIs programáticas para atualização condicional, exclusão e mesclagem (isso é normalmente chamado de upsert) de dados em tabelas.

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

Resulta em:

| ID|
|---|
|106|
|108|
|  5|
|  7|
|  9|

Aqui, você acabou de adicionar 100 a cada ID par.

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

Resulta em:

| ID|
|---|
|  5|
|  7|
|  9|

Observe que todas as linhas pares foram excluídas.

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

Resulta em:

| ID|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

Aqui você tem uma combinação dos dados existentes. Os dados existentes receberam o valor -1 no caminho do código update(WhenMatched). Os dados que foram criados na parte superior do snippet e foram adicionados por meio do caminho do código de inserção (WhenNotMatched) também foram adicionados.

### <a name="history"></a>Histórico

O Delta Lake tem a capacidade de permitir a análise do histórico de uma tabela. Ou seja, as alterações que foram feitas na tabela delta subjacente. A célula abaixo mostra como é simples inspecionar o histórico.

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

Resulta em:

|version|          timestamp|userId|userName|operation|                                                operationParameters| trabalho|notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|2020-04-25 00:36:27|  null|    null|    MESCLAR|                       [predicate -> (oldData.`ID` = newData.`ID`)]|null|    nulo|     null|          3|          null|        false|
|      3|2020-04-25 00:36:08|  null|    null|   Delete (excluir)|[predicate -> ["((`ID` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|null|    nulo|     null|          2|          null|        false|
|      2|2020-04-25 00:35:51|  null|    null|   UPDATE| [predicate -> ((ID#744L % cast(2 as bigint)) = cast(0 as bigint))]|null|    nulo|     nulo|          1|          null|        false|
|      1|2020-04-25 00:35:05|  null|    null|    WRITE|                             [mode -> Overwrite, partitionBy -> []]|null|    nulo|     null|          0|          nulo|        false|
|      0|2020-04-25 00:34:34|  null|    null|    WRITE|                         [mode -> ErrorIfExists, partitionBy -> []]|null|    nulo|     nulo|       nulo|          null|         true|

Aqui você pode ver todas as modificações feitas nos snippets de código acima.

## <a name="read-older-versions-of-data-using-time-travel"></a>Ler versões mais antigas dos dados usando a viagem no tempo

É possível consultar instantâneos anteriores da tabela do Delta Lake usando um recurso chamado viagem no tempo. Se você quiser acessar os dados substituídos, consulte um instantâneo da tabela antes da substituição do primeiro conjunto de dados usando a opção versionAsOf.

Depois de executar a célula abaixo, você verá o primeiro conjunto de dados anterior à substituição. A viagem no tempo é um recurso extremamente eficiente que aproveita o poder do log de transações do Delta Lake para acessar dados que não estão mais na tabela. A remoção da opção da versão 0 (ou a especificação da versão 1) permitirá que você veja os dados mais recentes novamente. Para obter mais informações, confira [Consultar um instantâneo mais antigo de uma tabela](https://docs.delta.io/latest/delta-batch.html#deltatimetravel).

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

Resulta em:

| ID|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

Aqui você pode ver que retornou à versão mais antiga dos dados.

## <a name="write-a-stream-of-data-to-a-table"></a>Gravar um fluxo de dados em uma tabela

Você também pode fazer gravações em uma tabela do Delta Lake usando o Streaming Estruturado do Spark. O log de transações do Delta Lake garante o processamento exatamente uma vez, mesmo quando há outros fluxos ou consultas em lote em execução simultaneamente na tabela. Por padrão, os fluxos são executados no modo de acréscimo, que adiciona novos registros à tabela.

Para obter mais informações sobre a integração do Delta Lake ao Streaming Estruturado, confira [Leituras e gravações em streaming de tabela](https://docs.delta.io/latest/delta-streaming.html).

Nas células abaixo, veja o que estamos fazendo:

* A célula 30 mostra os dados recém-acrescentados
* A célula 31 inspeciona o histórico
* A célula 32 interrompe o trabalho de streaming estruturado
* A célula 33 inspeciona o histórico. Você observará que os acréscimos foram interrompidos

Primeiro, você vai configurar um trabalho simples de streaming do Spark para gerar uma sequência e fazer a gravação do trabalho na tabela delta.

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>Ler um fluxo de alterações de uma tabela

Enquanto o fluxo está sendo gravado na tabela do Delta Lake, você também pode fazer uma leitura dessa tabela como uma fonte de streaming. Por exemplo, você pode iniciar outra consulta de streaming que imprime todas as alterações feitas na tabela do Delta Lake.

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

Resulta em:

| ID|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

Resulta em:

|version|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|ATUALIZAÇÃO DE STREAMING|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MESCLAR|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          Delete (excluir)|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       null|

Aqui você está removendo algumas das colunas menos interessantes para simplificar a experiência de exibição do histórico.

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

Resulta em:

|version|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|ATUALIZAÇÃO DE STREAMING|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MESCLAR|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          Delete (excluir)|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       null|

::: zone-end

## <a name="convert-parquet-to-delta"></a>Converter Parquet em Delta

Faça uma conversão in-loco do formato Parquet em Delta.

Aqui você testará se a tabela existente está ou não no formato delta.
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Resulta em:

Falso

Agora você vai converter os dados no formato delta e confirmar se isso funcionou.

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Resulta em:

Verdadeiro

## <a name="sql-support"></a>Suporte a SQL

O Delta dá suporte a comandos do utilitário de tabela por meio do SQL. Você pode usar o SQL para:

* Obter o histórico da DeltaTable
* Executar vácuo em uma DeltaTable
* Converter um arquivo Parquet em Delta

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

Resulta em:

|version|          timestamp|userId|userName|       operation| operationParameters| trabalho|notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|2020-04-25 00:37:09|  null|    null|ATUALIZAÇÃO DE STREAMING|[outputMode -> Ap...|null|    nulo|     null|          4|          null|         true|
|      4|2020-04-25 00:36:27|  null|    null|           MESCLAR|[predicate -> (ol...|null|    nulo|     null|          3|          null|        false|
|      3|2020-04-25 00:36:08|  null|    null|          Delete (excluir)|[predicate -> ["(...|null|    nulo|     null|          2|          null|        false|
|      2|2020-04-25 00:35:51|  null|    null|          UPDATE|[predicate -> ((i...|null|    nulo|     nulo|          1|          null|        false|
|      1|2020-04-25 00:35:05|  null|    null|           WRITE|[mode -> Overwrit...|null|    nulo|     null|          0|          nulo|        false|
|      0|2020-04-25 00:34:34|  null|    null|           WRITE|[mode -> ErrorIfE...|null|    nulo|     nulo|       nulo|          null|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

Resulta em:

|                caminho|
|--------------------|
|abfss://data@arca...|

Agora, você vai confirmar se uma tabela não é uma tabela no formato delta, convertê-la no formato delta usando o Spark SQL e confirmar se ela foi convertida corretamente.

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Resulta em:

Verdadeiro

Para obter a documentação completa, confira a [página de documentação do Delta Lake](https://docs.delta.io/latest/delta-intro.html)

Para obter mais informações, confira [Projeto do Delta Lake](https://github.com/delta-io/delta).

## <a name="next-steps"></a>Próximas etapas

* [Documentação do .NET para Apache Spark](/dotnet/spark)
* [Azure Synapse Analytics](../index.yml)