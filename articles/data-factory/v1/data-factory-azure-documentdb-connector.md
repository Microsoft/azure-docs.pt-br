---
title: Mover dados de/para o Azure Cosmos DB | Microsoft Docs
description: Saiba como mover dados de/para a coleção Azure Cosmos DB usando o Azure Data Factory
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1016a7800ddcfd4066ec3f6d6dce00d01ad83471
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839497"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Mover dados de e para o Azure Cosmos DB usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-documentdb-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do Azure Cosmos DB na V2 ](../connector-azure-cosmos-db.md).

Este artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados de/para o Azure Cosmos DB (API do SQL). Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Cosmos DB ou do Azure Cosmos DB para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de repositórios de dados com suporte como fontes ou coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats).

> [!IMPORTANT]
> O conector do Azure Cosmos DB dá suporte apenas à API do SQL.

Para copiar os dados como estão de/para arquivos JSON ou outra coleção do Cosmos DB, confira [Importação/exportação de documentos JSON](#importexport-json-documents).

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de/para o Azure Cosmos DB usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON. Para obter exemplos com definições de JSON das entidades do Data Factory usadas para copiar dados de/para o Cosmos DB, consulte a seção [Exemplos de JSON](#json-examples) desse artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas para o Cosmos DB:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do Azure Cosmos DB.

| **Property** | **Descrição** | **Obrigatório** |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **DocumentDb** |Sim |
| connectionString |Especifique as informações necessárias para se conectar ao banco de dados do Azure Cosmos DB. |Sim |

Exemplo:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **DocumentDbCollection** tem as propriedades a seguir.

| **Propriedade** | **Descrição** | **Necessária** |
| --- | --- | --- |
| collectionName |Nome da coleção de documentos do Cosmos DB. |Sim |

Exemplo:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Esquema do Data Factory
Para armazenamentos de dados sem esquema, como o Azure Cosmos DB, o serviço do Data Factory infere o esquema em uma das seguintes maneiras:

1. Se você especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o serviço do Data Factory respeitará essa estrutura do esquema. Nesse caso, se uma linha não contiver um valor de uma coluna, um valor nulo será fornecido para ele.
2. Se você não especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o serviço do Data Factory inferirá o esquema usando a primeira linha dos dados. Nesse caso, se a primeira linha não contiver o esquema completo, algumas colunas estarão ausentes no resultado da operação de cópia.

Portanto, para fontes de dados sem esquema, a prática recomendada é especificar a estrutura de dados usando a propriedade **structure** .

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, veja o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e política, estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Atividade de cópia usa apenas uma entrada e produz apenas uma saída.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

No caso da Atividade de cópia, quando a fonte é do tipo **DocumentDbCollectionSource**, as seguintes propriedades estão disponíveis na seção **typeProperties**:

| **Property** | **Descrição** | **Valores permitidos** | **Obrigatório** |
| --- | --- | --- | --- |
| query |Especifique a consulta para ler dados. |Cadeia de consulta com suporte no Azure Cosmos DB. <br/><br/>Exemplo: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificada, a instrução SQL executada será: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Caractere especial para indicar que o documento está aninhado |Qualquer caractere. <br/><br/>O Azure Cosmos DB é um repositório NoSQL para documentos JSON, em que estruturas aninhadas são permitidas. O Azure Data Factory permite que o usuário indique a hierarquia via nestingSeparator, que é "." nos exemplos acima. Com o separador, a atividade de cópia vai gerar o objeto "Name" com três elementos filhos First, Middle e Last, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela. |Não |

**DocumentDbCollectionSink** dá suporte às seguintes propriedades:

| **Property** | **Descrição** | **Valores permitidos** | **Necessária** |
| --- | --- | --- | --- |
| nestingSeparator |Um caractere especial no nome da coluna de fonte para indicar que esse documento aninhado é necessário. <br/><br/>No exemplo acima: `Name.First` na tabela de saída produz a seguinte estrutura JSON no documento do Cosmos DB:<br/><br/>"Name": {<br/>    "First": "John"<br/>}, |Caractere que é usado para separar os níveis de aninhamento.<br/><br/>O valor padrão é `.` (ponto). |Caractere que é usado para separar os níveis de aninhamento. <br/><br/>O valor padrão é `.` (ponto). |
| writeBatchSize |Número de solicitações paralelas ao serviço Azure Cosmos DB para criar documentos.<br/><br/>Você pode ajustar o desempenho ao copiar dados de/para o Cosmos DB usando essa propriedade. Você pode esperar um melhor desempenho ao aumentar writeBatchSize, pois mais solicitações paralelas para o Cosmos DB são enviadas. No entanto, é necessário evitar a limitação que pode gerar a mensagem de erro: "A taxa de solicitação é alta".<br/><br/>A limitação é decida por uma série de fatores, incluindo o tamanho dos documentos, o número de termos incluídos, a política de indexação da coleção de destino, etc. Para operações de cópia, você pode usar uma coleção melhor (por exemplo, S3) para ter mais taxa de transferência disponível (solicitação de 2.500 unidades/segundo). |Inteiro |Não (padrão: 5) |
| writeBatchTimeout |Tempo de espera para a operação ser concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: “00:30:00” (30 minutos). |Não |

## <a name="importexport-json-documents"></a>Importar/Exportar documentos JSON
Usando esse conector do Cosmos DB, você pode facilmente

* Importar documentos JSON de várias fontes para o Cosmos DB, incluindo Blobs do Azure, Azure Data Lake, Sistema de Arquivos local ou outros repositórios com base em arquivos com suporte pelo Azure Data Factory.
* Exporte documentos JSON de coleção do Cosmos DB para vários repositórios baseados em arquivo.
* Migrar dados entre duas coleções do Cosmos DB no estado em que se encontram.

Para atingir essa cópia independente de esquema,
* Ao usar o assistente de cópia, marque a opção **"Exportar como estão para arquivos JSON ou uma coleção Cosmos DB"** .
* Ao usar a edição de JSON, não especifique a seção "structure" nos conjuntos de dados do Cosmos DB nem a propriedade "nestingSeparator" na fonte/coletor do Cosmos DB na atividade de cópia. Para importar/exportar para arquivos JSON, no conjunto de dados do armazenamento de arquivo especifique o tipo de formato, como "JsonFormat," configure "filePattern" e ignore as configurações de formato restantes, consulte a seção [Formato JSON](data-factory-supported-file-and-compression-formats.md#json-format) para obter detalhes.

## <a name="json-examples"></a>Exemplos de JSON
Os exemplos a seguir fornecem as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para o Azure Cosmos DB e Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Exemplo: Copiar dados do Azure Cosmos DB para o Blob do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [DocumentDB](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [DocumentDbCollection](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Atividade de cópia que usa [DocumentDbCollectionSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados no Azure Cosmos DB para o Blob do Azure. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do Azure Cosmos DB:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
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
**Conjunto de dados de entrada do Azure DocumentDB:**

O exemplo pressupõe que você tem uma coleção chamada **Person** em um banco de dados do Azure Cosmos DB.

Definir “external”: ”true” e especificar a política externalData informa o serviço Azure Data Factory que essa é uma tabela externa à fábrica de dados e não é produzida por uma atividade nessa fábrica de dados.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Conjunto de dados de saída de Blob do Azure:**

Dados são copiados para um novo blob a cada hora com o caminho para o blob que reflete a data e hora específicas com granularidade de hora.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Documento JSON de exemplo na coleção Person em um banco de dados do Cosmos DB:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
O Cosmos DB dá suporte a consultas de documentos usando um SQL como sintaxe nos documentos JSON hierárquicos.

Exemplo:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

O pipeline a seguir copia dados da coleção Person no banco de dados no Azure Cosmos DB para um Blob do Azure. Como parte da atividade de cópia, os conjuntos de dados de entrada e saída foram especificados.

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Exemplo: Copiar dados do Blob do Azure para o Azure Cosmos DB
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo DocumentDB.
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo DocumentDbCollection.
5. Um [pipeline](data-factory-create-pipelines.md) com a Atividade de cópia que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e DocumentDbCollectionSink.

O exemplo copia dados do Blob do Azure para o Azure Cosmos DB. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

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
**Serviço vinculado do Azure Cosmos DB:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Conjunto de dados de entrada de Blob do Azure:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Conjunto de dados de saída do Azure Cosmos DB:**

O exemplo copia dados para uma coleção chamada "Person".

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
O pipeline a seguir copia dados do Blob do Azure para a coleção Person no Cosmos DB. Como parte da atividade de cópia, os conjuntos de dados de entrada e saída foram especificados.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          },
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Se a entrada de blob de exemplo for

```
1,John,,Doe
```
Então, a saída JSON no Cosmos DB será:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
O Azure Cosmos DB é um repositório NoSQL para documentos JSON, em que estruturas aninhadas são permitidas. O Azure Data Factory permite que o usuário indique a hierarquia via **nestingSeparator**, que é "." neste exemplo. Com o separador, a atividade de cópia vai gerar o objeto "Name" com três elementos filhos First, Middle e Last, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela.

## <a name="appendix"></a>Anexo
1. **Pergunta:** A Atividade de Cópia dá suporte à atualização de registros existentes?

    **Resposta:** Nº
2. **Pergunta:** Como uma nova tentativa de cópia para o Azure Cosmos DB lida com os registros já copiados?

    **Resposta:** Se os registros tiverem um campo "ID" e a operação de cópia tentar inserir um registro com a mesma ID, a operação de cópia vai gerar um erro.
3. **Pergunta:** O Data Factory dá suporte ao [intervalo ou particionamento de dados com base em hash](../../cosmos-db/sql-api-partition-data.md)?

    **Resposta:** Nº
4. **Pergunta:** Posso especificar mais de uma coleção do Azure Cosmos DB para uma tabela?

    **Resposta:** Nº Somente uma coleção pode ser especificada no momento.

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
