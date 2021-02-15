---
title: Copiar dados de/para o banco de dado SQL do Azure
description: Saiba como copiar dados para/do Banco de Dados SQL do Azure usando o Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 738b875a273faddd20a67be0f6feb90825f66c9f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370505"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Copiar dados de e para o Banco de Dados SQL do Azure usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-sql-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-sql-database.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do Banco de Dados SQL do Azure na V2](../connector-azure-sql-database.md).

Este artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados bidirecionalmente de e para o Banco de Dados SQL do Azure. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários com suporte
Você pode copiar dados **de um Banco de Dados SQL do Azure** para os seguintes armazenamentos de dados:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos armazenamentos de dados a seguir **para um Banco de Dados SQL do Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Tipos de autenticação com suporte
O conector do Banco de Dados SQL do Azure dá suporte à autenticação básica.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados bidirecionalmente de um Banco de Dados SQL do Azure usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net** e **API REST**. Confira o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções detalhadas para criar um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie um **Data Factory**. Um data factory pode conter um ou mais pipelines.
2. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory. Por exemplo, se você estiver copiando dados de um armazenamento de BLOBs do Azure para o banco de dados SQL do Azure, crie dois serviços vinculados para vincular sua conta de armazenamento do Azure e o banco de dados SQL do Azure ao seu data factory. Para propriedades do serviço vinculado que são específicas do Banco de Dados SQL do Azure, consulte a seção [propriedades do serviço vinculado](#linked-service-properties).
3. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado na última etapa, você cria um conjunto de dados para especificar o contêiner de blobs e a pasta que contém os dados de entrada. Além disso, você cria outro conjunto de dados para especificar a tabela SQL no banco de dado SQL do Azure que mantém a data copiada do armazenamento de BLOBs. Para propriedades do conjunto de dados que são específicas do Azure Data Lake Store, consulte a seção [propriedades do conjunto de dados](#dataset-properties).
4. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, você usa BlobSource como fonte e SqlSink como coletor para a atividade de cópia. De modo similar, se estiver copiando do Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure, você usará SqlSource e BlobSink na atividade de cópia. Para propriedades da atividade de cópia que são específicas do Banco de Dados SQL do Azure, consulte a seção [propriedades da atividade de cópia](#copy-activity-properties). Para obter detalhes sobre como usar um armazenamento de dados como uma origem ou um coletor, clique no link na seção anterior para o armazenamento de dados.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON. Para obter exemplos com definições de JSON das entidades do Data Factory usadas para copiar dados bidirecionalmente em um Banco de Dados SQL do Azure, confira a seção [Exemplos de JSON](#json-examples-for-copying-data-to-and-from-sql-database) neste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas para um Banco de Dados SQL do Azure:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
Um serviço vinculado do SQL do Azure vincula o banco de dados SQL do Azure ao seu data factory. A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço de vinculado de SQL Azure.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| type |A propriedade Type deve ser definida como: **AzureSqlDatabase** |Sim |
| connectionString |Especifique as informações necessárias para se conectar à instância do Banco de Dados SQL do Azure para a propriedade connectionString. Há suporte somente para autenticação básica. |Sim |

> [!IMPORTANT]
> Configure o [Firewall do Banco de Dados SQL do Azure](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) do servidor de banco de dados para [permitir que os Serviços do Azure acessem o servidor](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Além disso, se você estiver copiando os dados para o Banco de Dados SQL do Azure de fora do Azure, inclusive a partir das fontes de dados locais com o gateway do data factory, configure o devido intervalo de endereços IP do computador que está enviando os dados para o Banco de Dados SQL do Azure.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um DataSet para representar dados de entrada ou de saída no Azure SQL Database, você define a propriedade Type do conjunto para: **AzureSqlTable**. Defina a propriedade **linkedServiceName** do conjunto de dados para o nome do serviço vinculado do SQL do Azure.

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** do conjunto de dados do tipo **AzureSqlTable** tem as propriedades a seguir:

| Propriedade | Descrição | Necessária |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição na instância do Banco de Dados SQL do Azure à qual o serviço vinculado se refere. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e política, estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Atividade de cópia usa apenas uma entrada e produz apenas uma saída.

Por outro lado, as propriedades disponíveis na seção **typeProperties** da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Se você estiver movendo dados do banco de dados SQL do Azure, defina o tipo de fonte na atividade de cópia como **sqlsource**. Da mesma forma, se você estiver movendo os dados para o Azure SQL Database, defina o tipo de coletor na atividade de cópia como **sqlsink**. Esta seção fornece uma lista das propriedades com suporte de SqlSource e SqlSink.

### <a name="sqlsource"></a>SqlSource
Na atividade de cópia, quando a fonte é do tipo **SqlSource**, as seguintes propriedades estão disponíveis na seção **typeProperties**:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| sqlReaderQuery |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

Se **sqlReaderQuery** for especificado para SqlSource, a Atividade de Cópia executa essa consulta em relação à fonte do Banco de Dados SQL do Azure para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).

Se você não especificar sqlReaderQuery nem sqlReaderStoredProcedureName, as colunas definidas na seção da estrutura do JSON do conjunto de dados serão usadas para compilar uma consulta (`select column1, column2 from mytable`) para executar no Banco de Dados SQL do Azure. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.

> [!NOTE]
> Quando você usa **sqlReaderStoredProcedureName**, ainda é necessário especificar um valor para a propriedade **tableName** no JSON do conjunto de dados. Contudo, não há nenhuma validação executada nessa tabela.
>
>

### <a name="sqlsource-example"></a>Exemplo de SqlSource

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**A definição do procedimento armazenado:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>SqlSink
**SqlSink** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanupScript |Especifique uma consulta da Atividade de Cópia a executar para que os dados de uma fatia específica sejam removidos. Para saber mais, consulte [cópia repetida](#repeatable-copy). |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique um nome de coluna para a Atividade de Cópia a preencher com o identificador de fatias gerado automaticamente, que é usado para limpar os dados de uma fatia específica ao executar novamente. Para saber mais, consulte [cópia repetida](#repeatable-copy). |Nome de uma coluna com tipo de dados de binário (32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que define como aplicar os dados de origem à tabela de destino, por exemplo, para fazer upserts ou transformações usando sua própria lógica de negócios. <br/><br/>Observe que esse procedimento armazenado será **invocado por lote**. Se você deseja executar uma operação que é executada apenas uma vez e que não tem nenhuma relação com os dados de origem, por exemplo, excluir/truncar, use a propriedade `sqlWriterCleanupScript`. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |
| sqlWriterTableType |Especifique um nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados sendo copiados com dados existentes. |Um nome de tipo de tabela. |Não |

#### <a name="sqlsink-example"></a>Exemplo de SqlSink

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Exemplos JSON para cópia de dados do Banco de Dados SQL
Os exemplos a seguir fornecem exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados entre o Banco de Dados SQL do Azure e o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Exemplo: Copiar dados do Banco de Dados SQL do Azure para o Blob do Azure
O mesmo define as entidades do Data Factory a seguir:

1. Um serviço vinculado do tipo [AzureSqlDatabase](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AzureSqlTable](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [Blob do Azure](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com uma Atividade de Cópia que usa [SqlSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia os dados de série temporal (por hora, diariamente, etc.) de uma tabela no Azure SQL Database para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do banco de dados SQL do Azure:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Consulte a seção Serviço vinculado do SQL Azure para obter a lista de propriedades com o suporte deste serviço vinculado.

**Serviço vinculado do armazenamento de Blob do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Consulte o artigo [Blob do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter a lista de propriedades com o suporte deste serviço vinculado.


**Conjunto de dados de entrada do SQL Azure:**

O exemplo supõe que você criou uma tabela "MyTable" no Azure SQL e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

A configuração "external": "true" informa ao serviço de Azure Data Factory que o conjunto de os é externo ao data factory e não é produzido por uma atividade no data factory.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

Consulte a seção Propriedades do tipo de conjunto de dados do SQL Azure para obter a lista de propriedades com o suporte deste tipo de conjunto de dados.

**Conjunto de resultados de saída de blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Consulte a seção [Propriedades do tipo de conjunto de dados do Blog do Azure](data-factory-azure-blob-connector.md#dataset-properties) para obter a lista de propriedades com o suporte deste tipo de conjunto de dados.

**Uma atividade de cópia em um pipeline com origem SQL e coletor de Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **fonte** está definido como **SqlSource** e o tipo de **coletor** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Neste exemplo, **sqlReaderQuery** é especificada para SqlSource. A Atividade de Cópia executa essa consulta em relação à fonte do Banco de Dados SQL do Azure para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).

Se você não especificar sqlReaderQuery nem sqlReaderStoredProcedureName, as colunas definidas na seção da estrutura do JSON do conjunto de dados serão usadas para compilar uma consulta para executar no Banco de Dados SQL do Azure. Por exemplo: `select column1, column2 from mytable`. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.

Consulte a seção [Sql Source](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para obter a lista de propriedades com suporte em SqlSource e BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Exemplo: Copiar dados do Blob do Azure para o Banco de Dados SQL do Azure
O exemplo define as entidades do Data Factory a seguir:

1. Um serviço vinculado do tipo [AzureSqlDatabase](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureSqlTable](#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com a atividade de Cópia que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlSink](#copy-activity-properties).

O exemplo copia dados de série temporal (por hora, diariamente, etc.) do blob do Azure para uma tabela no banco de dados SQL do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL do Azure:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Consulte a seção Serviço vinculado do SQL Azure para obter a lista de propriedades com o suporte deste serviço vinculado.

**Serviço vinculado do armazenamento de Blob do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Consulte o artigo [Blob do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter a lista de propriedades com o suporte deste serviço vinculado.


**Conjunto de dados de entrada de blob do Azure:**

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte do ano, mês e dia da hora de início e o nome de arquivo usa a parte da hora de início. a configuração "external": "true" informa ao serviço de Data Factory que essa tabela é externa à data factory e não é produzida por uma atividade no data factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
Consulte a seção [Propriedades do tipo de conjunto de dados do Blog do Azure](data-factory-azure-blob-connector.md#dataset-properties) para obter a lista de propriedades com o suporte deste tipo de conjunto de dados.

**Conjunto de dados de saída do Banco de Dados SQL do Azure:**

O exemplo copia dados para uma tabela chamada "MyTable" no SQL do Azure. Crie a tabela no SQL Azure com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Consulte a seção Propriedades do tipo de conjunto de dados do SQL Azure para obter a lista de propriedades com o suporte deste tipo de conjunto de dados.

**Uma atividade de cópia em um pipeline com origem de Blob e coletor SQL:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **fonte** está definido como **BlobSource** e o tipo de **coletor** está definido como **SqlSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Consulte a seção [Sql Sink](#sqlsink) e [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) para obter a lista de propriedades com o suporte de SqlSink e de BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade no banco de dados de destino
Esta seção fornece um exemplo para copiar dados de uma tabela de origem sem uma coluna de identidade para uma tabela de destino com uma coluna de identidade.

**Tabela de origem:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Tabela de destino:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
Observe que a tabela de destino tem uma coluna de identidade.

**Definição de JSON do conjunto de dados de origem**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Definição de JSON do conjunto de dados de destino**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Observe que sua tabela de origem e de destino têm um esquema diferente (a de destino tem uma coluna adicional com identidade). Nesse cenário, você precisa especificar a propriedade **Structure** na definição do conjunto de dados de destino, que não inclui a coluna de identidade.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocar o procedimento armazenado do coletor SQL
Para obter um exemplo de como invocar um procedimento armazenado do coletor SQL em uma atividade de cópia de um pipeline, confira o artigo [Invoke stored procedure for SQL sink in copy activity](data-factory-invoke-stored-procedure-from-copy-activity.md) (Invocar procedimento armazenado para coletor SQL na atividade de cópia).

## <a name="type-mapping-for-azure-sql-database"></a>Mapeamento de tipo do Banco de Dados SQL do Azure
Conforme mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas dos tipos de origem nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados bidirecionalmente no Banco de Dados SQL do Azure, os mapeamentos a seguir são usados do tipo SQL para o tipo .NET, e vice-versa. O mapeamento é o mesmo que o mapeamento de tipo de dados do SQL Server para o ADO.NET.

| Tipo de mecanismo do Banco de Dados do SQL Server | Tipo de .NET Framework |
| --- | --- |
| BIGINT |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Object * |
| texto |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| TINYINT |Byte |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas de coletor
Para saber mais sobre mapeamento de colunas no conjunto de dados de origem para colunas no conjunto de dados de coletor, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapeamento de colunas de conjunto de dados no Azure Data Factory).

## <a name="repeatable-copy"></a>Cópia repetida
Ao copiar dados para o Banco de Dados do SQL Server, a atividade de cópia acrescenta dados à tabela de coletor por padrão. Para executar um UPSERT em vez disso, consulte o artigo [gravação repetida no sqlsink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) .

Ao copiar dados de armazenamentos de dados relacionais, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de dados de modo que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, seja de que maneira for, você precisa garantir que os mesmos dados sejam lidos não importa quantas vezes uma fatia seja executada. Consulte [leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.