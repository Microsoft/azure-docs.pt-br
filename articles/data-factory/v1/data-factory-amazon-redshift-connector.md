---
title: Mover dados do Amazon Redshift usando o Azure Data Factory | Microsoft Docs
description: Saiba como mover dados do Amazon Redshift usando a atividade de cópia do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3a1497211cc42c702537cbbdfea32ff71a400c7c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836677"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Mover dados do Amazon Redshift usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-amazon-redshift-connector.md)
> * [Versão 2 (versão atual)](../connector-amazon-redshift.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do Amazon Redshift na V2](../connector-amazon-redshift.md).

Este artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados do Amazon Redshift. O artigo se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Atualmente, o Data Factory dá suporte apenas à movimentação de dados do Amazon Redshift para um [armazenamento de dados do coletor com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Não há suporte para a movimentação de dados de outros armazenamentos de dados para o Amazon Redshift.

> [!TIP]
> Para obter o melhor desempenho ao copiar grandes quantidades de dados do Amazon Redshift, considere o uso de comando **UNLOAD** do Redshift interno por meio do Amazon S3 (Serviço de Armazenamento Simples). Para obter detalhes, consulte [Usar UNLOAD para copiar dados do Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Pré-requisitos
* Se estiver movendo dados para um repositório de dados local, instale o [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) em um computador local. Conceder acesso a um gateway para o cluster do Amazon Redshift usando o endereço IP do computador local. Para obter instruções, consulte [Authorize access to the cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) (Autorizar acesso ao cluster).
* Para mover dados para um armazenamento de dados do Azure, consulte o [Endereço IP de computação e intervalos do SQL que são usados pelos datacenters do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia para mover dados de uma origem do Amazon Redshift usando diferentes ferramentas e APIs.

A maneira mais fácil de criar um pipeline é usando o Assistente de Cópia do Azure Data Factory. Para obter um rápido passo a passo sobre como criar um pipeline usando o Assistente de Cópia, confira o [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md).

Você também pode criar um pipeline usando o Visual Studio, o Azure PowerShell ou outras ferramentas. Modelos do Azure Resource Manager, a API do .NET ou a API REST também pode ser usada para criar o pipeline. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar serviços vinculados para vincular armazenamentos de dados de entrada e saída ao seu data factory.
2. Criar conjuntos de dados para representar dados de entrada e saída para a operação de cópia.
3. Criar um pipeline com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída.

Ao usar o Assistente de Cópia, as definições de JSON para essas entidades do Data Factory são criadas automaticamente. Ao usar ferramentas ou APIs (exceto a API .NET), você define as entidades do Data Factory usando o formato JSON. Exemplo JSON: Copiar dados do Amazon Redshift para o Armazenamento de Blobs do Azure mostra as definições de JSON para as entidades do Data Factory que são usadas para copiar dados de um armazenamento de dados do Amazon Redshift.

As seções que se seguem descrevem as propriedades de JSON que são usadas para definir as entidades do Data Factory para o Amazon Redshift.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

A tabela a seguir fornece descrições dos elementos JSON específicos para o serviço vinculado Amazon Redshift.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| **type** |Essa propriedade deve ser definida como: **AmazonRedshift**. |Sim |
| **server** |O endereço IP ou nome do host do servidor Amazon Redshift. |Sim |
| **port** |O número da porta TCP usada pelo servidor Amazon Redshift para ouvir conexões de cliente. |Não (o padrão é 5439) |
| **database** |O nome do banco de dados do Amazon Redshift. |Sim |
| **username** |O nome de usuário que tem acesso ao banco de dados. |Sim |
| **password** |A senha para a conta de usuário. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista das seções e propriedades disponíveis para definir os conjuntos de dados, veja o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções **structure**, **availability** e **policy** são similares para todos os tipos de conjunto de dados. Os exemplos de tipos de conjuntos de dados incluem o Azure SQL, o Armazenamento de Blobs do Azure e o Armazenamento de Tabela do Azure.

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório. **A seção typeProperties** de um conjunto de dados do tipo **RelationalTable**, que inclui o conjunto de dados do Amazon Redshift, tem as propriedades a seguir:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| **tableName** |O nome da tabela no banco de dados do Amazon Redshift ao qual o serviço vinculado se refere. |Não (se a propriedade **query** de uma atividade de cópia do tipo **RelationalSource** for especificada) |

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Para obter uma lista das seções e propriedades disponíveis para definir as atividades, veja o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades **name**, **description**, tabela **inputs**, tabela **outputs** e **policy** estão disponíveis para todos os tipos de atividade. As propriedades que estão disponíveis na seção **typeProperties** variam conforme cada tipo de atividade. Para a Atividade de Cópia, as propriedades variam conforme os tipos de fonte de dados e coletor.

Para a atividade de cópia, quando a origem é do tipo **AmazonRedshiftSource**, as seguintes propriedades estão disponíveis na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| **query** | Use a consulta personalizada para ler os dados. |Não (se a propriedade **tableName** de um conjunto de dados for especificada) |
| **redshiftUnloadSettings** | Contém o grupo de propriedades ao usar o comando **UNLOAD** do Redshift. | Não |
| **s3LinkedServiceName** | O Amazon S3 para usar como um repositório provisório. O serviço vinculado é especificado usando um nome do Azure Data Factory do tipo **AwsAccessKey**. | Necessário ao usar a propriedade **redshiftUnloadSettings** |
| **bucketName** | Indica o bucket do Amazon S3 a ser usado para armazenar os dados provisórios. Se essa propriedade não for fornecida, a atividade de cópia gerará um bucket automaticamente. | Necessário ao usar a propriedade **redshiftUnloadSettings** |

Como alternativa, você pode usar o tipo **RelationalSource**, que inclui o Amazon Redshift, com a propriedade a seguir na seção **typeProperties**. Observe que esse tipo de origem não dá suporte para o comando **UNLOAD** do Redshift.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| **query** |Use a consulta personalizada para ler os dados. | Não (se a propriedade **tableName** de um conjunto de dados for especificada) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Usar UNLOAD para copiar dados do Amazon Redshift

O comando [**UNLOAD**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) do Amazon Redshift descarrega os resultados de uma consulta para um ou mais arquivos no Amazon S3. Esse comando é recomendado pela Amazon para copiar conjuntos de dados grandes do Redshift.

**Exemplo: Copiar dados do Amazon Redshift para o SQL Data Warehouse do Azure**

Esse exemplo copia dados do Amazon Redshift para o SQL Data Warehouse do Azure. O exemplo usa o comando **UNLOAD** do Redshift, dados de cópia em etapas e o Microsoft PolyBase.

Para esse caso de uso de exemplo, a atividade de cópia primeiro descarrega os dados do Amazon Redshift para o Amazon S3 conforme configurado na opção **redshiftUnloadSettings**. Em seguida, os dados são copiados do Amazon S3 para o Armazenamento de Blobs do Azure conforme especificado na opção **stagingSettings**. Por fim, o PolyBase carrega os dados no SQL Data Warehouse do Azure. Todos os formatos provisórios são tratados pela atividade de cópia.

![Copiar o fluxo de trabalho do Amazon Redshift para o SQL Data Warehouse](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Exemplo JSON: Copiar dados do Amazon Redshift para o Armazenamento de Blobs do Azure
Este exemplo mostra como copiar dados de um banco de dados do Amazon Redshift para um Armazenamento de Blobs do Azure. Os dados podem ser copiados diretamente para qualquer [coletor com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia.

O exemplo tem as seguintes entidades de data factory:

* Um serviço vinculado do tipo [AmazonRedshift](#linked-service-properties)
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](#dataset-properties)
* Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa as propriedades [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties)

O exemplo copia dados de um resultado de consulta no Amazon Redshift para um blob do Azure a cada hora. As propriedades JSON que são usadas no exemplo são descritas nas seções que seguem as definições de entidade.

**Serviço vinculado do Amazon Redshift**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Serviço vinculado do armazenamento de Blob do Azure**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada do Amazon Redshift**

A propriedade **external** é definida como “true” para informar ao serviço do Data Factory que o conjunto de dados é externo ao data factory. Essa configuração de propriedade indica que o conjunto de dados não é produzido por uma atividade no data factory. Defina a propriedade como true em um conjunto de dados de entrada que não é produzido por uma atividade no pipeline.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob de hora em hora pela definição da propriedade **frequency** como "Hour" e a propriedade **interval** como 1. A propriedade **folderPath** para o blob é avaliada dinamicamente. O valor da propriedade se baseia na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e hora da hora de início.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Atividade de cópia em um pipeline com origem Azure Redshift (do tipo RelationalSource) e coletor Blob do Azure**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída. O pipeline está agendado para ser executado a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados a serem copiados da última hora.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Mapeamento de tipo para o Amazon Redshift
Como mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md), a atividade de cópia executa conversões automáticas do tipo de origem em tipo de coletor. Os tipos são convertidos usando uma abordagem em duas etapas:

1. Converter de tipo de origem nativo em um tipo .NET
2. Converter de um tipo .NET em um tipo de coletor nativo

Os seguintes mapeamentos são usados quando a Atividade de Cópia converte os dados de um tipo Amazon Redshift em um tipo .NET:

| Tipo Amazon Redshift | Tipo .NET |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimal |
| REAL |Single |
| DOUBLE PRECISION |Duplo |
| Boolean |String |
| CHAR |string |
| VARCHAR |string |
| DATE |DateTime |
| TIMESTAMP |DateTime |
| TEXT |string |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas de coletor
Para saber mais sobre como mapear colunas no conjunto de dados de origem para colunas no conjunto de dados de coletor, confira [Mapeando colunas de conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Leituras repetidas de fontes relacionais
Ao copiar dados de um armazenamento de dados relacional, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a **policy** de repetição para um conjunto de dados, a fim de executar novamente uma fatia quando ocorrer uma falha. Verifique se os mesmos dados são lidos, não importa quantas vezes a fatia é executada novamente. Além disso, verifique se os mesmos dados são lidos, independentemente de como você executa novamente a fatia. Para saber mais, confira [Leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Saiba mais sobre os principais fatores que afetam o desempenho da Atividade de Cópia e maneiras de otimizar o desempenho no [Guia de Desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Próximas etapas
Para obter instruções passo a passo para criar um pipeline com a atividade de cópia, consulte o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
