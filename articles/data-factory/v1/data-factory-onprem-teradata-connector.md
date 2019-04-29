---
title: Mover dados do Teradata usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre o conector do Teradata para o serviço do Data Factory que permite mover dados do banco de dados Teradata
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 98eb76d8-5f3d-4667-b76e-e59ed3eea3ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d22318f4d9e233a57d521fe36f0827b9fc3af3e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610734"
---
# <a name="move-data-from-teradata-using-azure-data-factory"></a>Mover dados do Teradata usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-onprem-teradata-connector.md)
> * [Versão 2 (versão atual)](../connector-teradata.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do Teradata na V2](../connector-teradata.md).

Esse artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados de um banco de dados Teradata local. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um armazenamento de dados local do Teradata para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de repositórios de dados com suporte como coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Atualmente, o data factory dá suporte apenas à movimentação de dados de um armazenamento de dados Teradata para outros armazenamentos de dados, mas não à movimentação de dados de outros armazenamentos de dados para um armazenamento de dados Teradata.

## <a name="prerequisites"></a>Pré-requisitos
O data factory dá suporte à conexão a fontes Teradata locais por meio do Gateway de Gerenciamento de Dados. Consulte o artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e obter instruções passo a passo de como configurar o gateway.

O gateway é requerido mesmo que o banco de dados Teradata esteja hospedado em uma VM IaaS do Azure. Você pode instalar o gateway na mesma VM IaaS do armazenamento de dados ou em uma VM diferente, desde que o gateway possa conectar o banco de dados.

> [!NOTE]
> Consulte [Solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para ver dicas sobre como solucionar os problemas relacionados à conexão/gateway.

## <a name="supported-versions-and-installation"></a>Instalação e versões com suporte
Para o Gateway de Gerenciamento de Dados se conectar ao banco de dados Teradata, você precisa instalar o [Provedor de dados .NET para Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) versão 14 ou mais recente no mesmo sistema que o Gateway de Gerenciamento de Dados. Há suporte para o Teradata versão 12 e mais recente.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia que mova dados de um armazenamento de dados local Cassandra usando diferentes ferramentas/APIs.

- A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.
- Você também pode usar as ferramentas abaixo para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **Modelo do Azure Resource Manager**, **API .NET** e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são usadas para copiar dados do armazenamento de dados Teradata local, confira a seção [Exemplo de JSON: Copiar dados do Teradata para o Blob do Azure](#json-example-copy-data-from-teradata-to-azure-blob) neste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas para um armazenamento de dados Teradata:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado Teradata.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **OnPremisesTeradata** |Sim |
| Servidor |Nome do servidor Teradata. |Sim |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados Teradata. Os valores possíveis são: Anônima, Básica e Windows. |Sim |
| Nome de Usuário |Especifique o nome de usuário se você estiver usando a autenticação Basic ou Windows. |Não  |
| Senha |Especifique a senha da conta de usuário que você especificou para o nome de usuário. |Não  |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados Teradata local. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. Atualmente, não há nenhuma propriedade do tipo com suporte para o conjunto de dados Teradata.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

Por outro lado, as propriedades disponíveis na seção typeProperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Quando a fonte é do tipo **RelationalSource** (que inclui o Teradata), as seguintes propriedades estão disponíveis na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable. |Sim |

### <a name="json-example-copy-data-from-teradata-to-azure-blob"></a>Exemplo JSON: Copiar dados do Teradata para Blob do Azure
O exemplo a seguir fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados do Teradata para o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [OnPremisesTeradata](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um resultado de consulta no banco de dados Teradata para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções estão no artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço Teradata vinculado:**

```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Serviço vinculado do armazenamento de Blob do Azure:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Conjunto de dados de entrada do Teradata:**

O exemplo supõe que você criou uma tabela "MyTable" no Teradata e que ela contém uma coluna chamada "timestamp" para dados de série temporal.

A configuração "external": "true" informa ao serviço Data Factory que a tabela é externa ao data factory e não é produzida por uma atividade no data factory.

```json
{
    "name": "TeradataDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

```json
{
    "name": "AzureBlobTeradataDataSet",
    "properties": {
        "published": false,
        "location": {
            "type": "AzureBlobLocation",
            "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "linkedServiceName": "AzureStorageLinkedService"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
**Pipeline com Atividade de cópia:**

O pipeline contém uma Atividade de Cópia configurada para usar os conjuntos de dados de entrada e saída, e está agendada para ser executada por hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados na última hora para copiar.

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "TeradataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobTeradataDataSet"
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
                "name": "TeradataToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z",
        "isPaused": false
    }
}
```
## <a name="type-mapping-for-teradata"></a>Mapeamento de tipo para Teradata
Conforme mencionado no artigo sobre [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a Atividade de Cópia executa conversões automáticas de tipos de fonte em tipos de coletor com a abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover os dados para o Teradata, os seguintes mapeamentos são usados do tipo do Teradata para o tipo do .NET.

| Tipo de banco de dados Teradata | Tipo .NET Framework |
| --- | --- |
| Char |Cadeia de caracteres |
| Clob |Cadeia de caracteres |
| Graphic |Cadeia de caracteres |
| VarChar |Cadeia de caracteres |
| VarGraphic |Cadeia de caracteres |
| Blob |Byte[] |
| Byte |Byte[] |
| VarByte |Byte[] |
| BigInt |Int64 |
| ByteInt |Int16 |
| Decimal |Decimal |
| Double |Double |
| Número inteiro |Int32 |
| Número |Double |
| SmallInt |Int16 |
| Data |DateTime |
| Hora |TimeSpan |
| Hora com fuso horário |Cadeia de caracteres |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTimeOffset |
| Intervalo - dia |TimeSpan |
| Intervalo - dia para hora |TimeSpan |
| Intervalo - dia para minuto |TimeSpan |
| Interval Day To Second |TimeSpan |
| Intervalo - hora |TimeSpan |
| Intervalo - hora para minuto |TimeSpan |
| Interval Hour To Second |TimeSpan |
| Interval Minute |TimeSpan |
| Interval Minute To Second |TimeSpan |
| Interval Second |TimeSpan |
| Interval Year |Cadeia de caracteres |
| Interval Year To Month |Cadeia de caracteres |
| Interval Month |Cadeia de caracteres |
| Period(Date) |Cadeia de caracteres |
| Period(Time) |Cadeia de caracteres |
| Period(Time With Time Zone) |Cadeia de caracteres |
| Period(Timestamp) |Cadeia de caracteres |
| Period(Timestamp With Time Zone) |Cadeia de caracteres |
| Xml |Cadeia de caracteres |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas de coletor
Para saber mais sobre mapeamento de colunas no conjunto de dados de origem para colunas no conjunto de dados de coletor, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapeamento de colunas de conjunto de dados no Azure Data Factory).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de dados de modo que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, seja de que maneira for, você precisa garantir que os mesmos dados sejam lidos não importa quantas vezes uma fatia seja executada. Confira [Leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
