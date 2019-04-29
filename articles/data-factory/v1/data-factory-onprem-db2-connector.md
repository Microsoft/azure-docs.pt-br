---
title: Mover dados do DB2 usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados de um banco de dados DB2 local usando a Atividade de Cópia do Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 72c88ef10bf1df217ec6e24ac744d0b30386b4a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824007"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Mover dados do DB2 usando a Atividade de Cópia do Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-onprem-db2-connector.md)
> * [Versão 2 (versão atual)](../connector-db2.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do DB2 na V2 ](../connector-db2.md).


Este artigo descreve como você pode usar a Atividade de Cópia no Azure Data Factory para copiar dados de um banco de dados DB2 local para um armazenamento de dados. Você pode copiar dados em qualquer armazenamento listado como um coletor suportado no artigo [Atividades de movimentação de dados no Data Factory](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Este tópico foi criado com base no artigo do Data Factory, que apresenta uma visão geral da movimentação de dados usando a Atividade de Cópia e lista as combinações de armazenamento de dados suportadas. 

Atualmente, o Data Factory dá suporte apenas à movimentação de dados de um banco de dados DB2 para um [armazenamento de dados do coletor suportado](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Não há suporte para a movimentação de dados de outros armazenamentos de dados para um banco de dados DB2.

## <a name="prerequisites"></a>Pré-requisitos
O Data Factory dá suporte à conexão com um banco de dados DB2 local usando o [gateway de gerenciamento de dados](data-factory-data-management-gateway.md). Para obter instruções passo a passo de como configurar o pipeline de dados de gateway para mover dados, confira o artigo [Mover dados de fontes locais para a nuvem](data-factory-move-data-between-onprem-and-cloud.md).

O gateway é necessário, mesmo se o DB2 estiver hospedado na VM da Iaas do Azure. Você pode instalar o gateway na mesma VM da IaaS que o armazenamento de dados. Se o gateway puder se conectar com o banco de dados, você poderá instalar o gateway em uma VM diferente.

O gateway de gerenciamento de dados fornece um driver DB2 interno, de modo que não é necessário instalar manualmente um driver para copiar dados do DB2.

> [!NOTE]
> Para obter dicas sobre como solucionar problemas de conexão e gateway, confira o artigo [Solução de problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).


## <a name="supported-versions"></a>Versões com suporte
O conector DB2 do Data Factory dá suporte às seguintes plataformas e versões do IBM DB2 com as versões 9, 10 e 11 do SQL Access Manager da DRDA (Distributed Relational Database Architecture):

* IBM DB2 para z/OS versão 11.1
* IBM DB2 para z/OS versão 10.1
* IBM DB2 para i (AS400) versão 7.2
* IBM DB2 para i (AS400) versão 7.1
* IBM DB2 para LUW (Linux, UNIX e Windows) versão 11
* IBM DB2 para LUW versão 10.5
* IBM DB2 para LUW versão 10.1

> [!TIP]
> Se você receber a mensagem de erro "O pacote correspondente a uma solicitação de execução da instrução SQL não foi encontrado. SQLSTATE=51002 SQLCODE=-805", é porque um pacote necessário não foi criado para o usuário normal no SO. Para resolver esse problema, siga estas instruções para seu tipo de servidor DB2:
> - DB2 para i (AS400): Permita que o usuário avançado crie a coleção para o usuário normal antes de executar a Atividade de Cópia. Para criar a coleção, use o comando: `create collection <username>`
> - DB2 para z/OS ou LUW: Use uma conta com privilégios elevados – um usuário avançado ou administrador com autoridades de pacote e permissões BIND, BINDADD, GRANT EXECUTE TO PUBLIC – para executar a cópia de uma vez. O pacote necessário é criado automaticamente durante a cópia. Posteriormente, você poderá voltar para o usuário normal para as execuções de cópia subsequentes.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia para mover dados de um armazenamento de dados DB2 local usando diferentes ferramentas e APIs: 

- A maneira mais fácil de criar um pipeline é usando o Assistente de Cópia do Azure Data Factory. Para obter um rápido passo a passo sobre como criar um pipeline usando o Assistente de Cópia, confira o [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md). 
- Você também pode usar ferramentas para criar um pipeline, incluindo o Portal do Azure, o Visual Studio, o Azure PowerShell, um modelo do Azure Resource Manager, a API .NET e a API REST. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia. 

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar serviços vinculados para vincular armazenamentos de dados de entrada e saída ao seu data factory.
2. Criar conjuntos de dados para representar dados de entrada e saída para a operação de cópia. 
3. Criar um pipeline com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. 

Ao usar o Assistente de Cópia, as definições de JSON para os serviços vinculados, conjuntos de dados e entidades de pipeline do Data Factory são criadas automaticamente para você. Ao usar ferramentas ou APIs (exceto a API .NET), você define as entidades do Data Factory usando o formato JSON. Exemplo JSON: Copiar dados do DB2 para o armazenamento de Blobs do Azure mostra as definições de JSON para as entidades do Data Factory que são usadas para copiar dados de um armazenamento de dados DB2 local.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas a um armazenamento de dados DB2.

## <a name="db2-linked-service-properties"></a>Propriedades do serviço vinculado do DB2
A tabela a seguir lista as propriedades JSON que são específicas a um serviço vinculado do DB2.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| **tipo** |Essa propriedade deve ser definida como **OnPremisesDb2**. |Sim |
| **server** |O nome do servidor DB2. |Sim |
| **database** |O nome do banco de dados DB2. |Sim |
| **schema** |O nome do esquema no banco de dados DB2. Essa propriedade diferencia maiúsculas de minúsculas. |Não  |
| **authenticationType** |O tipo de autenticação que é usado para se conectar ao banco de dados DB2. Os valores possíveis são: Anônimo, Básico e Windows. |Sim |
| **username** |O nome da conta de usuário, se você usar a autenticação Básica ou do Windows. |Não  |
| **password** |A senha para a conta de usuário. |Não  |
| **gatewayName** |O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados DB2 local. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista das seções e propriedades disponíveis para definir os conjuntos de dados, veja o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como **structure**, **availability** e **policy** de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, armazenamento de Blobs do Azure, armazenamento de Tabelas do Azure, entre outros).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** de um conjunto de dados do tipo **RelationalTable**, que inclui o conjunto de dados do DB2, tem a seguinte propriedade:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| **tableName** |O nome da tabela na instância do banco de dados DB2 à qual o serviço vinculado se refere. Essa propriedade diferencia maiúsculas de minúsculas. |Não (se a propriedade **query** de uma atividade de cópia do tipo **RelationalSource** for especificada) |

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia
Para obter uma lista das seções e propriedades disponíveis para definir as atividades de cópia, veja o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades da Atividade de Cópia, como **name**, **description**, tabela **inputs**, tabela **outputs** e **policy** estão disponíveis para todos os tipos de atividade. As propriedades que estão disponíveis na seção **typeProperties** da atividade variam conforme cada tipo de atividade. Para a Atividade de Cópia, as propriedades variam conforme os tipos de fonte de dados e coletor.

Para a Atividade de Cópia, quando a fonte for do tipo **RelationalSource** (que inclui o DB2), as seguintes propriedades estarão disponíveis na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| **query** |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""` |Não (se a propriedade **tableName** de um conjunto de dados for especificada) |

> [!NOTE]
> Os nomes de esquema e tabela diferenciam maiúsculas de minúsculas. Na instrução de consulta, coloque os nomes de propriedade usando "" (aspas duplas).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Exemplo JSON: Copiar dados do DB2 para o Blob do Azure
Esse exemplo fornece amostra de definições de JSON que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). O exemplo mostra como copiar dados de um banco de dados do DB2 no armazenamento de Blobs. No entanto, os dados podem ser copiados em [qualquer tipo de coletor de armazenamento de dados suportado](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia do Azure Data Factory.

O exemplo tem as seguintes entidades do Data Factory:

- Um serviço vinculado do DB2 do tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- Um serviço vinculado de armazenamento de Blobs do Azure do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa as propriedades [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

O exemplo copia dados de um resultado de consulta no banco de dados DB2 para um blob do Azure a cada hora. As propriedades JSON que são usadas no exemplo são descritas nas seções que seguem as definições de entidade.

Primeiramente, instale e configure um gateway de dados. As instruções estão no artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md).

**Serviço vinculado do DB2**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Serviço vinculado do armazenamento de Blob do Azure**

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

**Conjunto de dados de entrada do DB2**

O exemplo supõe que você tenha criado uma tabela no DB2 chamada "MyTable" que tenha uma coluna rotulada "timestamp" para os dados seriais de tempo.

A propriedade **external** está definida como "true." Essa configuração informa o serviço Data Factory que o conjunto de dados é externo ao data factory e não é produzido por uma atividade no data factory. Observe que a propriedade **type** é definida como **RelationalTable**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
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

**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob de hora em hora pela definição da propriedade **frequency** como "Hour" e a propriedade **interval** como 1. A propriedade **folderPath** do blob é avaliada dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e hora da hora de início.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline para a atividade de cópia**

O pipeline contém uma atividade de cópia que está configurada para usar os conjuntos de dados especificados de entrada e saída e que está agendada para ser executada de hora em hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados da tabela "Orders".

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Mapeamento de tipo para DB2
Como mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md), a Atividade de Cópia executa conversões automáticas do tipo de fonte em tipo de coletor usando a seguinte abordagem de duas etapas:

1. Converter de tipo de origem nativo em um tipo .NET
2. Converter de um tipo .NET em um tipo de coletor nativo

Os seguintes mapeamentos são usados quando a Atividade de Cópia converte os dados de um tipo DB2 em um tipo .NET:

| Tipo do banco de dados DB2 | Tipo .NET Framework |
| --- | --- |
| SmallInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Data |DateTime |
| Hora |TimeSpan |
| Timestamp |Datetime |
| Xml |Byte[] |
| Char |Cadeia de caracteres |
| VarChar |Cadeia de caracteres |
| LongVarChar |Cadeia de caracteres |
| DB2DynArray |Cadeia de caracteres |
| Binário |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |Cadeia de caracteres |
| VarGraphic |Cadeia de caracteres |
| LongVarGraphic |Cadeia de caracteres |
| Clob |Cadeia de caracteres |
| Blob |Byte[] |
| DbClob |Cadeia de caracteres |
| SmallInt |Int16 |
| Número inteiro |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Data |DateTime |
| Hora |TimeSpan |
| Timestamp |Datetime |
| Xml |Byte[] |
| Char |Cadeia de caracteres |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas de coletor
Para saber mais sobre como mapear colunas no conjunto de dados de origem para colunas no conjunto de dados de coletor, confira [Mapeando colunas de conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Leituras repetidas de fontes relacionais
Ao copiar dados de um armazenamento de dados relacional, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a propriedade **policy** de repetição para um conjunto de dados, a fim de executar novamente uma fatia quando ocorrer uma falha. Certifique-se de que os mesmos dados sejam lidos, não importa quantas vezes a fatia seja executada e independentemente de como você executa novamente a fatia. Para saber mais, confira [Leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Saiba mais sobre os principais fatores que afetam o desempenho da Atividade de Cópia e maneiras de otimizar o desempenho no [Guia de Desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md).
