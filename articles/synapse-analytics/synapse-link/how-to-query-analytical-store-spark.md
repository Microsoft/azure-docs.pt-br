---
title: Fazer a interação com o Azure Cosmos DB usando o Apache Spark no Link do Azure Synapse
description: Como interagir com o Azure Cosmos DB usando o Apache Spark no Link do Azure Synapse
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 4a8367ea41ea96d8a412af965346684737d190fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627567"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-in-azure-synapse-link"></a>Fazer a interação com o Azure Cosmos DB usando o Apache Spark no Link do Azure Synapse

Neste artigo, você aprenderá a interagir com o Azure Cosmos DB usando o Synapse Apache Spark. Com seu suporte completo para Scala, Python, SparkSQL e C#, o Synapse Apache Spark é fundamental para análise, engenharia de dados, ciência de dados e cenários de exploração de dados no [Link do Azure Synapse para Azure Cosmos DB](../../cosmos-db/synapse-link.md).

Os seguintes recursos têm suporte ao interagir com o Azure Cosmos DB:
* O Synapse Apache Spark permite analisar dados em seus contêineres do Azure Cosmos DB que são habilitados com o Link do Azure Synapse quase em tempo real sem afetar o desempenho das cargas de trabalho transacionais. As duas seguintes opções estão disponíveis para consultar o [armazenamento de análise](../../cosmos-db/analytical-store-introduction.md) do Azure Cosmos DB do Spark:
    + Carregar para o DataFrame do Spark
    + Criar tabela do Spark
* O Synapse Apache Spark também permite ingerir dados no Azure Cosmos DB. É importante observar que os dados sempre são incluídos em contêineres do Azure Cosmos DB por meio do armazenamento transacional. Quando o Link do Synapse está habilitado, todas as novas inserções, atualizações e exclusões são sincronizadas automaticamente com o armazenamento de análise.
* O Synapse Apache Spark também dá suporte ao streaming estruturado do Spark com Azure Cosmos DB como uma fonte, bem como um coletor. 

As seções a seguir orientam você pela sintaxe das funcionalidades acima. Os gestos no workspace do Azure Synapse Analytics são projetados para proporcionar uma experiência fácil de usar para começar. Os gestos ficam visíveis quando você clica com o botão direito do mouse em um contêiner do Azure Cosmos DB na guia **Dados** do workspace do Azure Synapse. Com gestos, você pode gerar código rapidamente e ajustá-lo às suas necessidades. Os gestos também são perfeitos para descobrir dados com um único clique.

> [!IMPORTANT]
> Você deve estar ciente sobre algumas restrições no esquema analítico que poderão gerar um comportamento inesperado em operações de carregamento de dados.
> Por exemplo, somente as primeiras 1.000 propriedades de esquema transacional estão disponíveis no esquema analítico, as propriedades com espaços não estão disponíveis etc. Caso esteja obtendo resultados inesperados, verifique as [restrições de esquema de armazenamento analítico](../../cosmos-db/analytical-store-introduction.md#schema-constraints) para obter mais detalhes.

## <a name="query-azure-cosmos-db-analytical-store"></a>Consultar o armazenamento de análise do Azure Cosmos DB

Antes de aprender sobre as duas opções possíveis para consultar o armazenamento de análise do Azure Cosmos DB, carregar para o Spark DataFrame e criar a tabela do Spark, vale a pena explorar as diferenças na experiência para que você possa escolher a opção adequada às suas necessidades.

A diferença na experiência é relativa a se as alterações de dados subjacentes no contêiner de Azure Cosmos DB devem ser refletidas automaticamente na análise realizada no Spark. Quando um DataFrame do Spark é registrado ou uma tabela do Spark é criada no armazenamento de análise de um contêiner, os metadados relativos ao instantâneo atual dos dados no armazenamento de análise são buscados para o Spark para uma aplicação eficiente da análise subsequente. É importante observar que, como o Spark segue uma política de avaliação lenta, a menos que uma ação seja invocada no DataFrame do Spark ou uma consulta do SparkSQL seja executada na tabela do Spark, os dados reais não serão buscados no armazenamento de análise do contêiner subjacente.

No caso de **carregamento para o DataFrame do Spark**, os metadados buscados são armazenados em cache durante o tempo de vida da sessão do Spark, portanto, as ações subsequentes invocadas no DataFrame são avaliadas no instantâneo do armazenamento de análise no momento da criação do DataFrame.

Por outro lado, no caso **criar uma tabela do Spark**, os metadados do estado do armazenamento de análise não são armazenados em cache no Spark e recarregados em cada execução de consulta do SparkSQL em relação à tabela do Spark.

Portanto, você pode escolher entre carregar para o DataFrame do Spark e criar uma tabela do Spark dependendo de se deseja que sua análise do Spark seja avaliada em um instantâneo fixo do armazenamento de análise ou no instantâneo mais recente do armazenamento de análise, respectivamente.

> [!NOTE]
> Para consultar a API do Azure Cosmos DB das contas do Mongo DB, saiba mais sobre a [representação de esquema de fidelidade completa](../../cosmos-db/analytical-store-introduction.md#analytical-schema) no armazenamento de análise e os nomes de propriedade estendida a serem usados.

### <a name="load-to-spark-dataframe"></a>Carregar para o DataFrame do Spark

Neste exemplo, você criará um DataFrame do Spark que aponta para o armazenamento de análise do Azure Cosmos DB. Em seguida, você pode executar uma análise adicional invocando as ações do Spark no DataFrame. Esta operação não afeta o repositório transacional.

A sintaxe no **Python** seria a seguinte:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

A sintaxe equivalente no **Scala** seria a seguinte:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>Criar tabela do Spark

Neste exemplo, você criará uma tabela do Spark que aponta o armazenamento de análise do Azure Cosmos DB. Em seguida, você pode executar uma análise adicional invocando consultas do SparkSQL na tabela. Essa operação não afeta o armazenamento transacional nem gera nenhuma movimentação de dados. Se você decidir excluir essa tabela do Spark, o contêiner do Azure Cosmos DB subjacente e o armazenamento de análise correspondente não serão afetados. 

Esse cenário é conveniente para reutilizar tabelas do Spark por meio de ferramentas de terceiros e fornecer acessibilidade aos dados subjacentes para o tempo de execução.

A sintaxe para criar uma tabela do Spark é a seguinte:
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> Se você tiver cenários em que o esquema do contêiner do Azure Cosmos DB subjacente mude ao longo do tempo e quiser que o esquema atualizado se reflita automaticamente nas consultas na tabela do Spark, poderá conseguir isso definindo a opção `spark.cosmos.autoSchemaMerge`  como `true` nas opções da tabela do Spark.


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>Gravar o DataFrame do Spark no contêiner do Azure Cosmos DB

Neste exemplo, você escreverá um DataFrame do Spark em um contêiner do Azure Cosmos DB. Esta operação afetará o desempenho de cargas de trabalho transacionais e consumirá as unidades de solicitação provisionadas no contêiner do Azure Cosmos DB ou no banco de dados compartilhado.

A sintaxe no **Python** seria a seguinte:
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

A sintaxe equivalente no **Scala** seria a seguinte:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Carregar DataFrame de streaming do contêiner
Nesse gesto, você usará o recurso de streaming do Spark para carregar dados de um contêiner para um dataframe. Os dados serão armazenados na conta principal do data lake (e do sistema de arquivos) que você conectou ao workspace. 
> [!NOTE]
> Se você estiver procurando fazer referência a bibliotecas externas no Synapse Apache Spark, saiba mais [aqui](#external-library-management). Por exemplo, se você pretende ingerir um DataFrame do Spark para um contêiner da API do Cosmos DB para o Mongo DB, pode aproveitar o conector do Mongo DB para Spark [aqui](https://docs.mongodb.com/spark-connector/master/).

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Carregar DataFrame de streaming do contêiner do Azure Cosmos DB
Neste exemplo, você usará o recurso de streaming estruturado do Spark para carregar dados de um contêiner do Azure Cosmos DB em um DataFrame de streaming do Spark usando a funcionalidade de feed de alterações no Azure Cosmos DB. Os dados do ponto de verificação usados pelo Spark serão armazenados na conta principal do data lake (e sistema de arquivos) que você conectou ao workspace.

Se a pasta */localReadCheckpointFolder* não for criada (no exemplo abaixo), ela será criada automaticamente. Esta operação afetará o desempenho de cargas de trabalho transacionais e consumirá as Unidades de Solicitação provisionadas no contêiner do Azure Cosmos DB ou no banco de dados compartilhado.

A sintaxe no **Python** seria a seguinte:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

A sintaxe equivalente no **Scala** seria a seguinte:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Gravar DataFrame de streaming para o contêiner do Azure Cosmos DB
Neste exemplo, você escreverá um DataFrame de streaming em um contêiner do Azure Cosmos DB. Esta operação afetará o desempenho de cargas de trabalho transacionais e consumirá as Unidades de Solicitação provisionadas no contêiner do Azure Cosmos DB ou no banco de dados compartilhado. Se a pasta */localWriteCheckpointFolder* não for criada (no exemplo abaixo), ela será criada automaticamente. 

A sintaxe no **Python** seria a seguinte:

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

# If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

A sintaxe equivalente no **Scala** seria a seguinte:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

// If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```

## <a name="external-library-management"></a>Gerenciamento de biblioteca externa

Neste exemplo, você aprenderá a fazer referência a bibliotecas externas de arquivos JAR ao usar notebooks do Spark em workspaces do Synapse Apache Spark. Você pode colocar os arquivos JAR em um contêiner na conta do data Lake principal que você conectou ao workspace e adicionar a seguinte instrução de `%configure` em seu notebook do Spark:

```cmd
%%configure -f
{
    "jars": [
        "abfss://<storage container name>@<data lake account name>.dfs.core.windows.net/<path to jar>"
    ]
}
```
Se você pretende enviar definições remotas de trabalho do Spark para um pool do Apache Spark sem servidor, pode aprender a fazer referência a bibliotecas externas seguindo este [tutorial](../spark/apache-spark-job-definitions.md).

## <a name="next-steps"></a>Próximas etapas

* [Exemplos para começar a usar o Link do Azure Synapse no GitHub](https://aka.ms/cosmosdb-synapselink-samples)
* [Saiba o que é compatível com o Link do Azure Synapse para Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Conectar-se ao Link do Azure Synapse para Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
