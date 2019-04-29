---
title: Enviar dados ao índice do Search usando o Data Factory | Microsoft Docs
description: Saiba mais sobre como enviar dados por push ao Índice do Azure Search usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7ad328eec7e16b5368b78a0dfccbf5c09adb5c13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567223"
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Enviar dados para um índice do Azure Search usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-azure-search-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-search.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do Azure Search na V2 ](../connector-azure-search.md).

Este artigo descreve como usar a Atividade de Cópia para enviar por push dados de um repositório de dados de origem com suporte para o índice do Azure Search. Armazenamentos de dados de origem com suporte listados na coluna de origem a [fontes e coletores com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Este artigo se baseia no artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com a Atividade de Cópia e combinações de armazenamentos de dados com suporte.

## <a name="enabling-connectivity"></a>Habilitando a conectividade
Para permitir que o serviço se conecte a um armazenamento de dados local do Data Factory, instale o Gateway de Gerenciamento de Dados em seu ambiente local. Você pode instalar o gateway na mesma máquina que hospeda o banco de dados ou em uma máquina separada para evitar a concorrência por recursos com o armazenamento de dados.

O Gateway de Gerenciamento de Dados conecta fontes de dados locais a serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [Mover dados entre local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre o Gateway de Gerenciamento de Dados.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que envie dados por push de um repositório de dados de origem para o índice do Azure Search usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as ferramentas abaixo para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **Modelo do Azure Resource Manager**, **API .NET** e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Confira como obter um exemplo com as definições de JSON para entidades do Data Factory que são usadas para copiar dados para o índice Azure Search na seção [Exemplo de JSON: copiar dados do SQL Server local para o índice do Azure Search](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) deste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas ao índice do Azure Search:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

A tabela a seguir fornece descrições dos elementos JSON específicos para o serviço vinculado Azure Search.

| Propriedade | DESCRIÇÃO | Obrigatório |
| -------- | ----------- | -------- |
| type | A propriedade type deve ser definida como: **AzureSearch**. | Sim |
| url | URL para o serviço Azure Search. | Sim |
| key | Chave de administração para o serviço Azure Search. | Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, veja o artigo [Criando conjuntos de dados](data-factory-create-datasets.md) . As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados. A seção **typeProperties** é diferente para cada tipo de conjunto de dados. A seção typeProperties para um conjunto de dados do tipo **AzureSearchIndex** tem as propriedades a seguir:

| Propriedade | DESCRIÇÃO | Obrigatório |
| -------- | ----------- | -------- |
| type | A propriedade type deve ser definida como: **AzureSearchIndex**.| Sim |
| indexName | Nome do índice do Azure Search. O Data Factory não cria o índice. O índice deve existir no Azure Search. | Sim |


## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja o artigo [Criando pipelines](data-factory-create-pipelines.md) . As propriedades, como nome, descrição, tabelas de entrada e saída, e diversas políticas, estão disponíveis para todos os tipos de atividade. Por outro lado, as propriedades disponíveis na seção typeProperties variam de acordo com cada tipo de atividade. Para a Atividade de Cópia, elas variam de acordo com os tipos de fonte e coletor.

Na Atividade de Cópia, quando o coletor é do tipo **AzureSearchIndexSink**, as seguintes propriedades estão disponíveis na seção typeProperties:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Especifica se deve mesclar ou substituir quando já existe um documento no índice. Veja a [propriedade WriteBehavior](#writebehavior-property).| Merge (padrão)<br/>Carregar| Não  |
| WriteBatchSize | Carrega dados para o índice do Azure Search quando o tamanho do buffer atinge writeBatchSize. Veja a [propriedade WriteBatchSize](#writebatchsize-property) para obter detalhes. | 1 a 1.000. O valor padrão é 1000. | Não  |

### <a name="writebehavior-property"></a>Propriedade WriteBehavior
Upsert do AzureSearchSink ao gravar dados. Em outras palavras, ao escrever um documento, se a chave do documento já existe no índice do Azure Search, o Azure Search atualiza o documento existente, em vez de lançar uma exceção de conflito.

O AzureSearchSink fornece estes dois comportamentos de upsert (usando o SDK da AzureSearch):

- **Mesclar**: combine todas as colunas no novo documento com a existente. Para colunas com valor nulo no novo documento, o valor existente é preservado.
- **Carregar**: O novo documento substituirá o existente. Para colunas não especificadas no novo documento, o valor será definido como nulo se houver um valor não nulo no documento existente ou não.

O comportamento padrão é **Mesclar**.

### <a name="writebatchsize-property"></a>Propriedade WriteBatchSize
O serviço de Azure Search oferece suporte a escrever documentos como um lote. Um lote pode conter de 1 a 1.000 ações. Uma ação manipula um documento para executar a operação de carregamento/mesclagem.

### <a name="data-type-support"></a>Suporte ao tipo de dados
A tabela a seguir especifica se um tipo de dados do Azure Search tem suporte ou não.

| Tipo de dados do Azure Search | Suporte no coletor do Azure Search |
| ---------------------- | ------------------------------ |
| String | S |
| Int32 | S |
| Int64 | S |
| Double | S |
| Boolean | S |
| DataTimeOffset | S |
| String Array | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Exemplo JSON: copiar dados do SQL Server local para o índice do Azure Search

O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureSearch](#linked-service-properties).
2. Um serviço vinculado do tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureSearchIndex](#dataset-properties).
4. Um [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) e [AzureSearchIndexSink](#copy-activity-properties).

O exemplo copia dados de série temporal de um banco de dados do SQL Server local para um índice do Azure Search por hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados em seu computador local. As instruções estão no artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço vinculado ao Azure Search:**

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

O exemplo supõe que você criou uma tabela "MyTable" no SQL Server e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal. Você pode consultar várias tabelas no mesmo banco de dados usando um único conjunto de dados, mas uma única tabela deve ser usada para a typeProperty de tableName do conjunto de dados.

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

**Conjunto de dados de saída do Azure Search:**

O exemplo copia dados para um índice do Azure Search chamado **produtos**. O Data Factory não cria o índice. Para testar o exemplo, crie um índice com esse nome. Crie o índice do Azure Search com o mesmo número de colunas do que o conjunto de dados de entrada. Novas entradas são adicionadas ao índice do Azure Search a cada hora.

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

**Atividade de cópia em um pipeline com origem SQL e coletor de Índice do Azure Search:**

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

Se você estiver copiando dados de um armazenamento de dados de nuvem para o Azure Search, a propriedade `executionLocation` será necessária. O snippet JSON a seguir mostra a alteração necessária na Atividade de Cópia `typeProperties` como um exemplo. Verifique a seção [Copiar dados entre armazenamentos de dados de nuvem](data-factory-data-movement-activities.md#global) para obter mais detalhes e valores com suporte.

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
Se você estiver copiando dados de um armazenamento de dados de nuvem para o Azure Search, a propriedade `executionLocation` será necessária. O snippet JSON a seguir mostra a alteração necessária na Atividade de Cópia `typeProperties` como um exemplo. Verifique a seção [Copiar dados entre armazenamentos de dados de nuvem](data-factory-data-movement-activities.md#global) para obter mais detalhes e valores com suporte.

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
Confira os seguintes artigos:

* [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criação de um pipeline com uma Atividade de cópia.
