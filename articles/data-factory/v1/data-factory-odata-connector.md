---
title: Mover dados de fontes OData | Microsoft Docs
description: Saiba mais sobre como mover dados de fontes OData usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b2c665de94750c4c6f41bda47960fdb9ba17e819
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824024"
---
# <a name="move-data-from-an-odata-source-using-azure-data-factory"></a>Mover dados de uma origem de OData usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-odata-connector.md)
> * [Versão 2 (versão atual)](../connector-odata.md)

> [!NOTE]
> Este artigo se aplica à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do OData na V2 ](../connector-odata.md).


Este artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados de uma origem OData. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de uma origem OData para qualquer repositório de dados de coletor com suporte. Para obter uma lista de repositórios de dados com suporte como coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Atualmente, o data factory dá suporte apenas para a movimentação de dados de uma origem OData para outros repositórios de dados, mas não para a movimentação de dados de outros repositórios de dados para uma origem OData.

## <a name="supported-versions-and-authentication-types"></a>Versões com suporte e tipos de autenticação
Esse conector OData dá suporte a OData versões 3.0 e 4.0 e você pode copiar dados de OData da nuvem e de fontes OData locais. Para o último, você precisa instalar o Gateway de Gerenciamento de Dados. Consulte o artigo [Mover dados entre local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre o Gateway de Gerenciamento de Dados.

Os tipos de autenticação abaixo têm suporte:

* Para acessar o feed OData da **nuvem**, você poderá usar autenticação anônima, básica (nome de usuário e senha) ou a OAtuth baseada no Azure Active Directory.
* Para acessar o feed OData **local**, você poderá usar autenticação anônima, básica (nome de usuário e senha) ou a do Windows.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de uma origem OData usando ferramentas/APIs diferentes.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as ferramentas abaixo para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **Modelo do Azure Resource Manager**, **API .NET** e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter um exemplo com as definições de JSON para entidades do Data Factory que são usadas para copiar dados de uma fonte OData, confira a seção [Exemplo de JSON: copiar dados da fonte OData para o Azure Blob](#json-example-copy-data-from-odata-source-to-azure-blob) neste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas a uma origem OData:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicos do serviço vinculado do OData.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **OData** |Sim |
| url |URL do serviço OData. |Sim |
| authenticationType |Tipo de autenticação usada para se conectar ao armazenamento de dados OData. <br/><br/> Para o OData de nuvem, os valores possíveis são Anônimo, Básico e OAuth (observe que o Azure Data Factory dá suporte no momento apenas a OAuth baseado no Azure Active Directory). <br/><br/> Para OData local, os valores possíveis são Anonymous, Basic e Windows. |Sim |
| Nome de Usuário |Especifique o nome de usuário se você estiver usando a autenticação Básica. |Sim (apenas se você estiver usando a autenticação Básica) |
| Senha |Especifique a senha da conta de usuário que você especificou para o nome de usuário. |Sim (apenas se você estiver usando a autenticação Básica) |
| authorizedCredential |Se você estiver usando OAuth, clique no botão **Autorizar** no Editor ou Assistente de Cópia do Data Factory e digite suas credenciais. O valor dessa propriedade será gerado automaticamente. |Sim (apenas se você estiver usando a autenticação OAuth) |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao serviço OData local. Especifique apenas se você estiver copiando dados de origem de OData local. |Não  |

### <a name="using-basic-authentication"></a>Usando a autenticação Básica
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Usando a autenticação anônima
```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Usando a autenticação do Windows para acessar uma fonte OData local
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Usando autenticação OAuth para acessar a fonte OData da nuvem
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source e.g. https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties do conjunto de dados do tipo **ODataResource** (que inclui o conjunto de dados do OData) tem as propriedades a seguir

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| caminho |Caminho para o recurso OData |Não  |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e política, estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Quando a fonte é do tipo **RelationalSource** (que inclui o OData), as seguintes propriedades estão disponíveis na seção typeProperties:

| Propriedade | DESCRIÇÃO | Exemplo | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |"?$select=Name, Description&$top=5" |Não  |

## <a name="type-mapping-for-odata"></a>Mapeamento de tipos para OData
Como mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de Cópia executa conversões automáticas dos tipos de fonte nos tipos de coletor com a seguinte abordagem de duas etapas.

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados do OData, os seguintes mapeamentos serão usados dos tipos do OData para os tipos do .NET.

| Tipo de dados OData | Tipo .NET |
| --- | --- |
| Edm.Binary |Byte[] |
| Edm.Boolean |Bool |
| Edm.Byte |Byte[] |
| Edm.DateTime |DateTime |
| Edm.Decimal |Decimal |
| Edm.Double |Double |
| Edm.Single |Single |
| Edm.Guid |Guid |
| Edm.Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| Edm.SByte |Int16 |
| Edm.String |String |
| Edm.Time |TimeSpan |
| Edm.DateTimeOffset |DateTimeOffset |

> [!Note]
> Tipos de dados complexos do OData, por exemplo, o objeto não tem suporte.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>Exemplo JSON: copiar dados da fonte OData para o Azure Blob
Este exemplo fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de uma fonte OData para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory. O exemplo tem as seguintes entidades do Data Factory:

1. Um serviço vinculado do tipo [OData](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [ODataResource](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Atividade de Cópia que usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de consulta em relação a uma fonte OData para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do OData:** este exemplo usa a autenticação anônima. Consulte a seção [Serviço vinculado a OData](#linked-service-properties) para ver os diferentes tipos de autenticação que você pode usar.

```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

**Serviço vinculado de armazenamento do Azure:**

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

**Conjunto de dados de entrada do OData:**

Configurar “external”: “true” informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "typeProperties":
        {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

A especificação do **caminho** na definição do conjunto de dados é opcional.

**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
    "name": "AzureBlobODataDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Atividade de cópia em um pipeline com origem OData e coletor Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **consulta** seleciona os dados mais recentes (mais novos) da fonte de OData.

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "?$select=Name, Description&$top=5",
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "ODataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobODataDataSet"
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
                "name": "ODataToBlob"
            }
        ],
        "start": "2017-02-01T18:00:00Z",
        "end": "2017-02-03T19:00:00Z"
    }
}
```

A especificação da **consulta** na definição do pipeline é opcional. A **URL** usada pelo serviço Data Factory para recuperar dados é a URL especificada no serviço vinculado (obrigatória) + caminho especificado no conjunto de dados (opcional) + consulta no pipeline (opcional).

### <a name="type-mapping-for-odata"></a>Mapeamento de tipos para OData
Como mencionado no artigo sobre as [Atividades de Movimentação de Dados](data-factory-data-movement-activities.md) , a atividade de Cópia executa conversões automáticas dos tipos de fonte nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados de repositórios de dados OData, os tipos de dados OData são mapeados para tipos .NET.

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas de coletor
Para saber mais sobre mapeamento de colunas no conjunto de dados de origem para colunas no conjunto de dados de coletor, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapeamento de colunas de conjunto de dados no Azure Data Factory).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de dados de modo que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, seja de que maneira for, você precisa garantir que os mesmos dados sejam lidos não importa quantas vezes uma fatia seja executada. Confira [Leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
