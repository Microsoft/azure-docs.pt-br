---
title: Enviar dados por push para o índice de pesquisa usando Data Factory
description: Saiba mais sobre como enviar dados por push ao índice de Pesquisa Cognitiva do Azure usando Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0226ab75d53733b94a9ae5734b42b7340998759c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379260"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Enviar dados por push para um índice de Pesquisa Cognitiva do Azure usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-search-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-search.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector de pesquisa cognitiva do Azure na v2](../connector-azure-search.md).

Este artigo descreve como usar a atividade de cópia para enviar dados por push de um armazenamento de dados de origem com suporte para um índice de Pesquisa Cognitiva do Azure. Armazenamentos de dados de origem com suporte listados na coluna de origem a [fontes e coletores com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

## <a name="enabling-connectivity"></a>Habilitando a conectividade
Para permitir que o serviço se conecte a um armazenamento de dados local do Data Factory, instale o Gateway de Gerenciamento de Dados em seu ambiente local. Você pode instalar o gateway na mesma máquina que hospeda o banco de dados ou em uma máquina separada para evitar a concorrência por recursos com o armazenamento de dados.

O Gateway de Gerenciamento de Dados conecta fontes de dados locais a serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [Mover dados entre local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre o Gateway de Gerenciamento de Dados.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que envia dados de um armazenamento de dados de origem para um índice de pesquisa usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net** e **API REST**. Confira o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções detalhadas para criar um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados para o índice de pesquisa, confira a seção [exemplo de JSON: copiar dados do SQL Server para um índice de pesquisa cognitiva do Azure](#json-example-copy-data-from-sql-server-to-azure-cognitive-search-index) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas a um índice de pesquisa:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

A tabela a seguir fornece descrições para elementos JSON que são específicos para o serviço vinculado do Azure Pesquisa Cognitiva.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| type | A propriedade Type deve ser definida como: **AzureSearch**. | Sim |
| url | URL para o serviço de pesquisa. | Sim |
| chave | Chave de administração para o serviço de pesquisa. | Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, veja o artigo [Criando conjuntos de dados](data-factory-create-datasets.md) . As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados. A seção **typeproperties** é diferente para cada tipo de conjunto de texto. A seção typeProperties para um conjunto de dados do tipo **AzureSearchIndex** tem as propriedades a seguir:

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| type | A propriedade type deve ser definida como: **AzureSearchIndex**.| Sim |
| indexName | Nome do índice de pesquisa. O Data Factory não cria o índice. O índice deve existir no Pesquisa Cognitiva do Azure. | Sim |


## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja o artigo [Criando pipelines](data-factory-create-pipelines.md) . As propriedades, como nome, descrição, tabelas de entrada e saída, e diversas políticas, estão disponíveis para todos os tipos de atividade. Por outro lado, as propriedades disponíveis na seção typeProperties variam de acordo com cada tipo de atividade. Para a Atividade de Cópia, elas variam de acordo com os tipos de fonte e coletor.

Na Atividade de Cópia, quando o coletor é do tipo **AzureSearchIndexSink**, as seguintes propriedades estão disponíveis na seção typeProperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Especifica se deve mesclar ou substituir quando já existe um documento no índice. Veja a [propriedade WriteBehavior](#writebehavior-property).| Merge (padrão)<br/>Carregar| Não |
| WriteBatchSize | Carrega dados no índice de pesquisa quando o tamanho do buffer atinge writeBatchSize. Veja a [propriedade WriteBatchSize](#writebatchsize-property) para obter detalhes. | 1 a 1.000. O valor padrão é 1000. | Não |

### <a name="writebehavior-property"></a>Propriedade WriteBehavior
Upsert do AzureSearchSink ao gravar dados. Em outras palavras, ao gravar um documento, se a chave do documento já existir no índice de pesquisa, o Azure Pesquisa Cognitiva atualizará o documento existente em vez de lançar uma exceção de conflito.

O AzureSearchSink fornece estes dois comportamentos de upsert (usando o SDK da AzureSearch):

- **Mesclar**: combine todas as colunas no novo documento com a existente. Para colunas com valor nulo no novo documento, o valor existente é preservado.
- **Carregar**: o novo documento substituirá o existente. Para colunas não especificadas no novo documento, o valor será definido como nulo se houver um valor não nulo no documento existente ou não.

O comportamento padrão é **Mesclar**.

### <a name="writebatchsize-property"></a>Propriedade WriteBatchSize
O serviço de Pesquisa Cognitiva do Azure dá suporte à gravação de documentos como um lote. Um lote pode conter de 1 a 1.000 ações. Uma ação manipula um documento para executar a operação de carregamento/mesclagem.

### <a name="data-type-support"></a>Suporte do tipo de dados
A tabela a seguir especifica se um tipo de dados do Azure Pesquisa Cognitiva tem suporte ou não.

| Tipo de dados Pesquisa Cognitiva do Azure | Com suporte no coletor de Pesquisa Cognitiva do Azure |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| Boolean | Y |
| DataTimeOffset | Y |
| Matriz de cadeia de caracteres | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-sql-server-to-azure-cognitive-search-index"></a>Exemplo de JSON: copiar dados de SQL Server para o índice de Pesquisa Cognitiva do Azure

O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureSearch](#linked-service-properties).
2. Um serviço vinculado do tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureSearchIndex](#dataset-properties).
4. Um [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) e [AzureSearchIndexSink](#copy-activity-properties).

O exemplo copia os dados de série temporal de um banco de dado SQL Server para pesquisar índice por hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados em seu computador local. As instruções estão no artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço vinculado do Azure Pesquisa Cognitiva:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**Serviço vinculado do SQL Server**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**Conjunto de dados de entrada do SQL Server**

O exemplo supõe que você criou uma tabela "MyTable" em SQL Server e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal. Você pode consultar várias tabelas no mesmo banco de dados usando um único conjunto de dados, mas uma única tabela deve ser usada para a typeProperty de tableName do conjunto de dados.

Configurar "external": "true" informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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

**Conjunto de Pesquisa Cognitiva de saída do Azure:**

O exemplo copia dados para um índice de Pesquisa Cognitiva do Azure chamado **produtos**. O Data Factory não cria o índice. Para testar o exemplo, crie um índice com esse nome. Crie o índice de pesquisa com o mesmo número de colunas do conjunto de dados de entrada. Novas entradas são adicionadas ao índice de pesquisa a cada hora.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**Atividade de cópia em um pipeline com fonte SQL e coletor de índice de Pesquisa Cognitiva do Azure:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **fonte** está definido como **SqlSource** e o tipo de **coletor** está definido como **AzureSearchIndexSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
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

Se você estiver copiando dados de um armazenamento de dados de nuvem para o Azure Pesquisa Cognitiva, a `executionLocation` propriedade será necessária. O snippet JSON a seguir mostra a alteração necessária na Atividade de Cópia `typeProperties` como um exemplo. Verifique a seção [Copiar dados entre armazenamentos de dados de nuvem](data-factory-data-movement-activities.md#global) para obter mais detalhes e valores com suporte.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Copiar de uma origem de nuvem
Se você estiver copiando dados de um armazenamento de dados de nuvem para o Azure Pesquisa Cognitiva, a `executionLocation` propriedade será necessária. O snippet JSON a seguir mostra a alteração necessária na Atividade de Cópia `typeProperties` como um exemplo. Verifique a seção [Copiar dados entre armazenamentos de dados de nuvem](data-factory-data-movement-activities.md#global) para obter mais detalhes e valores com suporte.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

Você também pode mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de coletor na definição da atividade de cópia. Para obter detalhes, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapear colunas de conjunto de dados no Azure Data Factory).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho e a movimentação dos dados (Atividade de Cópia), além de várias maneiras de otimizar.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

* [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criação de um pipeline com uma Atividade de cópia.
