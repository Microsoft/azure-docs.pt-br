---
title: Mover dados de/para a Tabela do Azure | Microsoft Docs
description: Saiba como mover dados para/do Armazenamento de Tabela do Azure usando o Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0c4f961dda273c7f3885159818dabf228abced42
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839475"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Mover dados para e da Tabela do Azure | Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-table-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-table-storage.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do Armazenamento de Tabelas do Azure na V2](../connector-azure-table-storage.md).

Este artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados bidirecionalmente no Armazenamento de Tabelas do Azure. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia. 

Você pode copiar dados de qualquer repositório de dados de origem com suporte para o Armazenamento de Tabelas do Azure ou do Armazenamento de Tabelas do Azure para qualquer repositório de dados do coletor com suporte. Para obter uma lista de repositórios de dados com suporte como fontes ou coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados bidirecionalmente de um Armazenamento de Tabelas do Azure usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia. 

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor: 

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. 

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON. Para obter exemplos com definições de JSON das entidades do Data Factory usadas para copiar dados bidirecionalmente em um Armazenamento de Tabelas do Azure, confira a seção [Exemplos de JSON](#json-examples) neste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas ao Armazenamento de Tabelas do Azure: 

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
Existem dois tipos de serviço vinculado que você pode usar para vincular um armazenamento de blobs do Azure a um data factory do Azure. Elas são: o serviços vinculado **AzureStorage** e **AzureStorageSas**. O serviço vinculado do Armazenamento do Azure fornece o data factory com acesso global ao Armazenamento do Azure. Já o serviço vinculado SAS (Assinatura de Acesso Compartilhado) do Armazenamento do Azure fornece o data factory com acesso restrito/associado ao tempo ao Armazenamento do Azure. Não há outras diferenças entre esses dois serviços vinculados. Escolha o serviço vinculado que atenda às suas necessidades. As seções a seguir fornecem mais detalhes sobre esses dois serviços vinculados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** para o conjunto de dados do tipo **AzureTable** tem as propriedades a seguir.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância do Banco de Dados da Tabela do Azure à qual o serviço vinculado se refere. |Sim. Quando um nome de tabela é especificado sem uma azureTableSourceQuery, todos os registros da tabela são copiados para o destino. Se uma azureTableSourceQuery também for especificada, os registros da tabela que atende à consulta são copiados para o destino. |

### <a name="schema-by-data-factory"></a>Esquema do Data Factory
Para armazenamentos de dados sem esquema, como a Tabela do Azure, o serviço do Data Factory infere o esquema usando uma das seguintes maneiras:

1. Se você especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o serviço do Data Factory respeitará essa estrutura do esquema. Nesse caso, se uma linha não contiver um valor de uma coluna, um valor nulo será fornecido para ele.
2. Se você não especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o Data Factory inferirá o esquema usando a primeira linha dos dados. Nesse caso, se a primeira linha não contém o esquema completo, algumas colunas não estão presentes no resultado da operação de cópia.

Portanto, para fontes de dados sem esquema, a prática recomendada é especificar a estrutura de dados usando a propriedade **structure** .

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, conjuntos de dados de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

**AzureTableSource** dá suporte às seguintes propriedades na seção typeProperties:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| azureTableSourceQuery |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta de tabela do Azure. Veja exemplos na próxima seção. |Nº Quando um nome de tabela é especificado sem uma azureTableSourceQuery, todos os registros da tabela são copiados para o destino. Se uma azureTableSourceQuery também for especificada, os registros da tabela que atende à consulta são copiados para o destino. |
| azureTableSourceIgnoreTableNotFound |Indique se assimilar a exceção da tabela não existe. |TRUE<br/>FALSE |Não |

### <a name="azuretablesourcequery-examples"></a>Exemplos do azureTableSourceQuery
Se a coluna Tabela do Azure é do tipo cadeia de caracteres:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Se a coluna Tabela do Azure é do tipo datetime:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** dá suporte às seguintes propriedades na seção typeProperties:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Valor de chave de partição padrão que pode ser utilizado pelo coletor. |Um valor de cadeia de caracteres. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são usados como chaves de partição. Se não especificado, AzureTableDefaultPartitionKeyValue será utilizado como a chave da partição. |Um nome de coluna. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores são usados como chaves de linha. Se não especificado, um GUID é usado para cada linha. |Um nome de coluna. |Não |
| azureTableInsertType |O modo para inserir dados na tabela do Azure.<br/><br/>Essa propriedade controla se linhas existentes na tabela de saída com a partição correspondente e as chaves de linha terão seus valores substituídos ou mesclados. <br/><br/>Para saber mais sobre como essas configurações (mesclagem e substituição) funcionam, consulte os tópicos [Inserir ou Mesclar Entidade](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [Inserir ou Substituir Entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx). <br/><br> Essa configuração se aplica ao nível de linha e não ao nível de tabela e nenhuma das opções excluirá as linhas na tabela de saída que não existirem na entrada. |mesclar (padrão)<br/>substituir |Não |
| writeBatchSize |Insere dados na tabela do Azure quando o writeBatchSize ou writeBatchTimeout for atingido. |Inteiro (número de linhas) |Não (padrão: 10000) |
| writeBatchTimeout |Insere dados na tabela do Azure quando o writeBatchSize ou writeBatchTimeout for atingido |TimeSpan<br/><br/>Exemplo: "00:20:00" (20 minutos) |Não (padrão para 90 seg. de valor de tempo padrão de cliente de armazenamento) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Antes de poder usar a coluna de destino como o azureTablePartitionKeyName, será necessário mapear uma coluna de origem para uma coluna de destino usando a propriedade JSON do conversor.

No exemplo a seguir, a coluna de origem DivisionID é mapeada para a coluna de destino: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
DivisionID é especificada como a chave de partição.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>Exemplos de JSON
Os exemplos a seguir fornecem as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tais exemplos mostram como copiar dados de/para o Armazenamento de Tabela do Azure e o Banco de Dados de Blob do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores com suporte. Para obter mais informações, consulte a seção "Formatos e armazenamentos de dados com suporte" em [Mover dados usando a Atividade de Cópia](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Exemplo: copiar dados da Tabela do Azure para o Blob do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (usado para tabela e blob).
2. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureTable](#dataset-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. O [pipeline](data-factory-create-pipelines.md) com a Atividade de cópia que usa AzureTableSource e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados pertencentes à partição padrão em uma tabela do Azure para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado de armazenamento do Azure:**

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
O Azure Data Factory oferece suporte a dois tipos de serviços vinculados de Armazenamento do Azure: **AzureStorage** e **AzureStorageSas**. Para o primeiro, você especifica a cadeia de conexão que inclui a chave de conta, e para o mais recente, você especifica o URI de SAS (Assinatura de Acesso Compartilhado). Confira a seção [Serviços vinculados](#linked-service-properties) para obter detalhes.  

**Conjunto de dados de entrada da Tabela do Azure:**

O exemplo pressupõe que você tenha criado uma tabela "MyTable" na Tabela do Azure.

Configurar “external”: “true” informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Atividade de cópia em um pipeline com AzureTableSource e BlobSink:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **AzureTableSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada com a propriedade **AzureTableSourceQuery** seleciona os dados da partição padrão na última hora a serem copiados.

```JSON
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Exemplo: copiar dados do Blob do Azure para a Tabela do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (usado para tabela e blob)
2. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureTable](#dataset-properties).
4. O [pipeline](data-factory-create-pipelines.md) com a Atividade de cópia que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [AzureTableSink](#copy-activity-properties).

O exemplo copia os dados de série temporal de um blob do Azure para uma tabela do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço de armazenamento vinculado do Azure (para Tabela e Blob do Azure):**

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

O Azure Data Factory oferece suporte a dois tipos de serviços vinculados de Armazenamento do Azure: **AzureStorage** e **AzureStorageSas**. Para o primeiro, você especifica a cadeia de conexão que inclui a chave de conta, e para o mais recente, você especifica o URI de SAS (Assinatura de Acesso Compartilhado). Confira a seção [Serviços vinculados](#linked-service-properties) para obter detalhes.

**Conjunto de dados de entrada de Blob do Azure:**

Os dados são coletados de um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte do ano, mês e dia da hora de início e o nome de arquivo usa a parte da hora de início. A configuração "external": "true" informa o serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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

**Conjunto de dados de saída de Tabela do Azure:**

O exemplo copia dados para uma tabela chamada "MyTable" na Tabela do Azure. Crie uma tabela do Azure com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Atividade de cópia em um pipeline com BlobSource e AzureTableSink:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **BlobSource** e o tipo **sink** está definido como **AzureTableSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
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
## <a name="type-mapping-for-azure-table"></a>Mapeamento de tipo de Tabela do Azure
Como mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de Cópia executa conversões automáticas dos tipos de fonte nos tipos de coletor com a seguinte abordagem de duas etapas.

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Quando dados forem movidos para e da Tabela do Azure, os seguintes [mapeamentos definidos pelo serviço Tabela do Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) serão usados nos tipos OData da Tabela do Azure para o tipo .NET e vice-versa.

| Tipo de dados OData | Tipo .NET | Detalhes |
| --- | --- | --- |
| Edm.Binary |byte[] |Uma matriz de bytes de até 64 KB. |
| Edm.Boolean |bool |Um valor booliano. |
| Edm.DateTime |DateTime |Um valor de 64 bits expressado como Tempo Universal Coordenado (UTC). O intervalo de data e hora com suporte começa à 00:00 de 1º de janeiro de 1601 D.C. (C.E.), UTC. O intervalo termina em 31 de dezembro de 9999. |
| Edm.Double |double |Um valor de ponto flutuante de 64 bits. |
| Edm.Guid |Guid |Um identificador global exclusivo de 128 bits. |
| Edm.Int32 |Int32 |Um inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um inteiro de 64 bits. |
| Edm.String |String |Um valor codificado em UTF-16. Valores de cadeia de caracteres podem ter até 64 KB. |

### <a name="type-conversion-sample"></a>Exemplo de conversão de tipo
O exemplo a seguir é para copiar dados de um Blob do Azure para Tabela do Azure com conversões de tipo.

Suponha que o conjunto de dados de Blob está no formato CSV e contém três colunas. Uma delas é uma coluna de data e hora com um formato de data e hora personalizado usando nomes abreviados em francês para o dia da semana.

Defina o conjunto de dados de origem de Blob conforme demonstrado a seguir, juntamente com definições de tipo para as colunas.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
        "structure":
        [
            { "name": "userid", "type": "Int64"},
            { "name": "name", "type": "String"},
            { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
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
Dado o mapeamento de tipo OData da Tabela do Azure para o tipo .NET, você definiria a tabela na Tabela do Azure com o esquema a seguir.

**Esquema da tabela do Azure:**

| Nome da coluna | Tipo |
| --- | --- |
| userid |Edm.Int64 |
| name |Edm.String |
| lastlogindate |Edm.DateTime |

Em seguida, defina o conjunto de dados de Tabela do Azure conforme demonstrado a seguir. Você não precisa especificar a seção "estrutura" com as informações de tipo, pois o tipo de informação já está especificado no armazenamento de dados subjacente.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

Nesse caso, o Data Factory fará automaticamente as conversões de tipo, inclusive o campo Data e hora com o formato de data e hora personalizado usando a cultura "fr-fr" ao mover dados de Blob para a Tabela do Azure.

> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas do conjunto de dados de coletor, confira [Mapeando colunas de conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Consulte o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
