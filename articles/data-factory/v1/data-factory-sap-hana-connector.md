---
title: Mover dados do SAP HANA usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados do SAP HANA usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 96d16552cfadca9b345d0f0cd0a344249897f571
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258429"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Mover dados do SAP HANA usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-sap-hana-connector.md)
> * [Versão 2 (versão atual)](../connector-sap-hana.md)

> [!NOTE]
> Este artigo se aplica à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do SAP HANA na V2](../connector-sap-business-warehouse.md).

Esse artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados de um SAP HANA local. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um armazenamento de dados local do SAP HANA para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de repositórios de dados com suporte como coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Atualmente, o Data Factory dá suporte apenas à movimentação de dados de um SAP HANA para outros armazenamentos de dados, mas não à movimentação de dados de outros armazenamentos de dados para um SAP HANA.

## <a name="supported-versions-and-installation"></a>Instalação e versões com suporte
Esse conector oferece suporte a qualquer versão do banco de dados SAP HANA. Ele oferece suporte à cópia de dados dos modelos de informações do HANA (como os modos de exibição de Análise e de Cálculo) e às tabelas Linha/Coluna usando consultas SQL.

Para habilitar a conectividade com a instância do SAP HANA, instale os seguintes componentes:
- **Gateway de Gerenciamento de Dados**: o serviço Data Factory oferece suporte à conexão com armazenamentos de dados locais (incluindo SAP HANA) usando um componente denominado Gateway de Gerenciamento de Dados. Para saber mais sobre o Gateway de Gerenciamento de Dados e obter instruções passo a passo de como configurar o gateway, veja o artigo [Mover dados entre armazenamentos de dados locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md). O gateway é requerido mesmo que o SAP HANA esteja hospedado em uma máquina virtual (VM) IaaS do Azure. Você pode instalar o gateway na mesma VM do armazenamento de dados ou em uma VM diferente, desde que o gateway possa conectar o banco de dados.
- **Driver ODBC do SAP HANA** no computador do gateway. Baixe o driver ODBC do SAP HANA do [Centro de Download de Software SAP](https://support.sap.com/swdc). Pesquisa com a palavra-chave **CLIENTE SAP HANA para Windows**. 

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia que mova dados de um armazenamento de dados local SAP HANA usando diferentes ferramentas/APIs. 

- A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados. 
- Você também pode usar as ferramentas abaixo para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **Modelo do Azure Resource Manager**, **API .NET** e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia. 

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. 

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são usadas para copiar dados do SAP HANA local, consulte a seção [Exemplo de JSON: copiar dados do SAP HANA para o Blob do Azure](#json-example-copy-data-from-sap-hana-to-azure-blob) neste artigo. 

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas para um repositório de dados do SAP HANA:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado SAP HANA.

Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
Servidor | Nome do servidor no qual reside a instância do SAP HANA. Se o servidor estiver usando uma porta personalizada, especifique `server:port`. | cadeia de caracteres | Sim
authenticationType | Tipo de autenticação. | cadeia de caracteres. "Básico" ou "Windows" | Sim 
Nome de Usuário | Nome do usuário que tem acesso ao servidor SAP | cadeia de caracteres | Sim
Senha | Senha do usuário. | cadeia de caracteres | Sim
gatewayName | O nome do gateway que o serviço Data Factory deve usar para se conectar à instância local do SAP HANA. | cadeia de caracteres | Sim
encryptedCredential | A cadeia de caracteres de credencial criptografada. | cadeia de caracteres | Não 

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. Não há propriedades específicas ao tipo com suporte para o conjunto de dados do SAP HANA do tipo **RelationalTable**. 


## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

Por outro lado, as propriedades disponíveis na seção **typeProperties** da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Quando a fonte na atividade de cópia for do tipo **RelationalSource** (que inclui o SAP HANA), as seguintes propriedades estão disponíveis na seção typeProperties:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query | Especifica a consulta SQL para ler dados da instância do SAP HANA. | Consulta SQL. | Sim |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>Exemplo JSON: copiar dados do SAP HANA para o Blob do Azure
O exemplo a seguir fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Este exemplo mostra como copiar dados de um SAP HANA local para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer um dos coletores listados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.  

> [!IMPORTANT]
> Este exemplo fornece snippets de JSON. Ele não inclui instruções passo a passo para criar o data factory. Confira o artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo.

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [SapHana](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Atividade de Cópia que usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de uma instância do SAP HANA para um Blob do Azure por hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções estão no artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-hana-linked-service"></a>Serviço vinculado SAP HANA
Esse serviço vinculado vincula sua instância do SAP HANA à data factory. A propriedade type é definida como **SapHana**. A seção typeProperties fornece informações de conexão para a instância do SAP HANA.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure
O serviço vinculado vincula sua conta de Armazenamento do Azure à data factory. A propriedade type é definida como **AzureStorage**. A seção typeProperties fornece informações de conexão para a conta do Armazenamento do Azure.

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

### <a name="sap-hana-input-dataset"></a>Conjunto de dados de entrada do SAP HANA

Esse conjunto de dados define o conjunto de dados do SAP HANA. Defina o tipo do conjunto de dados do Data Factory como **RelationalTable**. No momento, você não especifica quaisquer propriedades específicas ao tipo para um conjunto de dados do SAP HANA. A consulta na definição da Atividade de Cópia especifica quais dados serão lidos da instância do SAP HANA. 

Configurar a propriedade external como true informa ao serviço Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

As propriedades de frequência e intervalo definem a agenda. Nesse caso, os dados são lidos da instância do SAP HANA por hora. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída de Blob do Azure
Esse conjunto de dados define o conjunto de dados de saída do Blob do Azure. A propriedade type é definida como AzureBlob. A seção typeProperties fornece o local onde os dados copiados da instância do SAP HANA serão armazenados. Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Pipeline com Atividade de cópia

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** (para a fonte do SAP HANA) e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados na última hora para copiar.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>Mapeamento de tipo para SAP HANA
Conforme mencionado no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) , a Atividade de cópia executa conversões automáticas de tipo de fonte para tipos de coletor, com a abordagem em duas etapas descritas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados do SAP HANA, os seguintes mapeamentos serão usados dos tipos do SAP HANA para os tipos do .NET.

Tipo SAP HANA | Tipo baseado no .NET
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16
INT | Int32
BIGINT | Int64
REAL | Single
DOUBLE | Single
DECIMAL | Decimal
BOOLEAN | Byte
VARCHAR | Cadeia de caracteres
NVARCHAR | Cadeia de caracteres
CLOB | Byte[]
ALPHANUM | Cadeia de caracteres
BLOB | Byte[]
DATE | DateTime
TIME | TimeSpan
TIMESTAMP | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Limitações conhecidas
Há algumas limitações conhecidas ao copiar dados do SAP HANA:

- Cadeias de caracteres NVARCHAR são truncadas para o comprimento máximo de 4.000 caracteres Unicode
- Não há suporte para SMALLDECIMAL
- Não há suporte para VARBINARY
- As datas válidas são entre 30/12/1899 e 31/12/9999

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas de coletor
Para saber mais sobre mapeamento de colunas no conjunto de dados de origem para colunas no conjunto de dados de coletor, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapeamento de colunas de conjunto de dados no Azure Data Factory).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de dados de modo que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, seja de que maneira for, você precisa garantir que os mesmos dados sejam lidos não importa quantas vezes uma fatia seja executada. Confira [Leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
