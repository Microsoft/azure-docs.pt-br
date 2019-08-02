---
title: Mover dados do Amazon Simple Storage Service usando o Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados do Amazon S3 (Simple Storage Service) usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0e2468fdd44374343894416c8e39c263cecaa7d5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839557"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Mover dados do Amazon Simple Storage Service usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Versão 2 (versão atual)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [ o conector do Amazon S3 na V2 ](../connector-amazon-simple-storage-service.md).

Este artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados do Amazon S3 (Simple Storage Service). Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados do Amazon S3 para qualquer repositório de dados de coletor com suporte. Para obter uma lista de repositórios de dados com suporte como coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Atualmente, a data factory dá suporte apenas para a movimentação de dados do Amazon S3 para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para o Amazon S3.

## <a name="required-permissions"></a>Permissões necessárias
Para copiar dados do Amazon S3, verifique se você recebeu as permissões a seguir:

* `s3:GetObject` e `s3:GetObjectVersion` para operações de objeto do Amazon S3
* `s3:ListBucket` para operações de bucket do Amazon S3. Se você estiver usando o assistente de cópia Data Factory, `s3:ListAllMyBuckets` também será exigido.

Parra detalhes sobre a lista completa e detalhada das permissões da Amazon S3,consulte [Especificar permissões em uma política](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de uma origem do Amazon S3 usando diferentes ferramentas ou APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Para uma rápida explicação passo a passo, consulte [Tutorial: Criar um pipeline usando Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md).

Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas ou APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON. Para obter um exemplo com definições de JSON para entidades do Data Factory que são usadas para copiar dados do armazenamento de dados do Amazon S3, confira a seção [Exemplo de JSON: Copiar dados do Amazon S3 para o Blob do Azure](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) neste artigo.

> [!NOTE]
> Para obter detalhes sobre os formatos de arquivo e de compactação com suporte para uma atividade de cópia, consulte [Formatos de arquivo e de compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

As seções seguintes fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas ao Amazon S3.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
Um serviço vinculado vincula um armazenamento de dados a um data factory. Crie um serviço vinculado do tipo **AwsAccessKey** para vincular o armazenamento de dados do Amazon S3 ao data factory. A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço vinculado do Amazon S3 (AwsAccessKey).

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| accessKeyID |ID da chave de acesso secreta. |cadeia de caracteres |Sim |
| secretAccessKey |A chave de acesso do secreta em si. |Cadeia de caracteres secreta criptografada |Sim |

>[!NOTE]
>Este conector requer chaves de acesso da conta IAM para copiar dados do Amazon S3. [Não há suporte para a credencial de segurança temporária](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html).
>

Veja um exemplo:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um conjunto de dados de modo a representar dados de entrada em um Armazenamento de Blobs do Azure, defina a propriedade de tipo do conjunto de dados para: **AmazonS3**. Defina a propriedade **linkedServiceName** do conjunto de dados para o nome do serviço vinculado do Amazon S3. Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira [Criando conjuntos de dados](data-factory-create-datasets.md). 

As seções como structure, availability e policy são similares para todos os tipos de conjunto de dados (como banco de dados SQL Azure, Blob do Azure, Tabela do Azure etc.). A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** para o conjunto de dados do tipo **AmazonS3** (que inclui o conjunto de dados do Amazon S3) tem as propriedades a seguir:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| bucketName |O nome do bucket S3. |string |Sim |
| key |A chave do objeto S3. |string |Não |
| prefix |Prefixo da chave do objeto S3. Objetos cujas chaves começam com esse prefixo serão selecionados. Aplica-se apenas quando a chave está vazia. |string |Não |
| version |A versão do objeto S3 se o controle de versão do S3 está habilitado. |string |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format) e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser copiar arquivos no estado em que se encontram entre repositórios com base em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não | |
| compression | Especifique o tipo e o nível de compactação para os dados. Os tipos que recebem suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Os níveis compatíveis são: **Ideal** e **Mais Rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não | |


> [!NOTE]
> **bucketName + chave** especifica a localização do objeto S3 em que bucket é o contêiner raiz para objetos S3 e a chave é o caminho completo para o objeto S3.

### <a name="sample-dataset-with-prefix"></a>Conjunto de dados de exemplo com o prefixo

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>Conjunto de dados de exemplo (com a versão)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Caminhos dinâmicos para S3
No exemplo anterior, usamos valores fixos para as propriedades de **chave** e **bucketName** no conjunto de dados do Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Você pode fazer com que o Data Factory calcule essas propriedades dinamicamente em tempo de execução usando variáveis de sistema como SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Você pode fazer o mesmo para a propriedade de **prefixo**de um conjunto de dados do Amazon S3. Veja [Funções e variáveis do sistema do Data Factory](data-factory-functions-variables.md) para obter uma lista das funções e variáveis com suporte.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, consulte [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade. As propriedades disponíveis na seção **typeProperties** da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, as propriedades variam conforme os tipos de fonte e coletor. Quando a origem da atividade de cópia é do tipo **FileSystemSource** (que inclui o Amazon S3), a seguinte propriedade está disponível na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Especifica se devemos listar recursivamente objetos S3 no diretório. |true/false |Não |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Exemplo JSON: Copiar dados do Amazon S3 para o Armazenamento de Blobs do Azure
Este exemplo mostra como copiar dados de um Amazon S3 para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados [diretamente](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para qualquer uma das fontes que são suportadas usando a atividade de cópia no Data Factory.

O exemplo fornece definições de JSON para as entidades de Data Factory a seguir. Você pode usar essas definições para criar um pipeline para copiar dados do Amazon S3 para o armazenamento de BLOBs usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Um serviço vinculado do tipo [AwsAccessKey](#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AmazonS3](#dataset-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um Amazon S3 para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

### <a name="amazon-s3-linked-service"></a>Serviço vinculado do Amazon S3

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure

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

### <a name="amazon-s3-input-dataset"></a>Conjunto de dados de entrada do Amazon S3

Configurar **"external": true** informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory. Defina essa propriedade como true em um conjunto de dados de entrada que não é produzido por uma atividade no pipeline.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída de Blob do Azure

Dados são gravados em um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e hora da hora de início.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Atividade de cópia em um pipeline com a origem do Amazon S3 e o coletor de blob

O pipeline contém uma atividade de cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo de **fonte** está definido como **FileSystemSource** e o tipo de **coletor** está definido como **BlobSink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
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
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Para mapear colunas de um conjunto de dados de origem para colunas de um conjunto de dados do coletor, consulte [mapeando colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

* Para saber mais sobre os principais fatores que afetam o desempenho e a movimentação dos dados (atividade de cópia) no Azure Data Factory, além de várias maneiras de otimizá-la, consulte o [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md).

* Para obter instruções passo a passo para criar um pipeline com uma atividade de cópia, consulte o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
