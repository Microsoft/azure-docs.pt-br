---
title: Mover dados do SAP Business Warehouse usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados do SAP Business Warehouse usando o Azure Data Factory.
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
ms.openlocfilehash: c928ad1fc9a8d6206c1b7e47591b17b6ae05ee4b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839876"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Mover dados do SAP Business Warehouse usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-sap-business-warehouse-connector.md)
> * [Versão 2 (versão atual)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Este artigo se aplica à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do SAP Business Warehouse na V2 ](../connector-sap-business-warehouse.md).


Este artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados de um SAP BW (Business Warehouse) local. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um repositório de dados local do SAP Business Warehouse para qualquer repositório de dados de coletor com suporte. Para obter uma lista de repositórios de dados com suporte como coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Atualmente, a data factory dá suporte apenas à movimentação de dados de um SAP Business Warehouse para outros repositórios de dados, mas não para a movimentação de dados de outros repositórios de dados para o SAP Business Warehouse. 

## <a name="supported-versions-and-installation"></a>Instalação e versões com suporte
Este conector dá suporte ao SAP Business Warehouse versão 7.x. Ele oferece suporte à cópia de dados do InfoCubes e QueryCubes (incluindo consultas BEx) usando consultas MDX.

Para habilitar a conectividade com a instância do SAP BW, instale os seguintes componentes:
- **Gateway de Gerenciamento de Dados**: O serviço Data Factory oferece suporte à conexão com armazenamentos de dados locais (incluindo SAP Business Warehouse) usando um componente denominado Gateway de Gerenciamento de Dados. Para saber mais sobre o Gateway de Gerenciamento de Dados e obter instruções passo a passo de como configurar o gateway, veja o artigo [Mover dados entre armazenamentos de dados locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md). O gateway é exigido mesmo que o SAP Business Warehouse esteja hospedado em uma VM (máquina virtual) IaaS do Azure. Você pode instalar o gateway na mesma VM do armazenamento de dados ou em uma VM diferente, desde que o gateway possa conectar o banco de dados.
- **Biblioteca do SAP NetWeaver** no computador do gateway. Você pode obter a biblioteca do SAP Netweaver com seu administrador do SAP, ou diretamente do [Centro de Download de Software SAP](https://support.sap.com/swdc). Pesquise pela **Nota SAP Nº 1025361** para obter o local de download da versão mais recente. Certifique-se de que a arquitetura para a biblioteca do SAP NetWeaver (32 bits ou 64 bits) corresponda à sua instalação do gateway. Em seguida, instale todos os arquivos incluídos no SDK RFC do SAP NetWeaver, de acordo com a Nota SAP. A biblioteca do SAP NetWeaver também está incluída na instalação das Ferramentas de Cliente SAP.

> [!TIP]
> Coloque as dlls extraídas do SDK do RFC do NetWeaver na pasta system32.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia que mova dados de um armazenamento de dados local Cassandra usando diferentes ferramentas/APIs. 

- A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados. 
- Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia. 

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. 

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são usadas para copiar dados de um SAP Business Warehouse local, consulte a seção [Exemplo de JSON: Copiar dados do SAP Business Warehouse para o Blob do Azure](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) deste artigo. 

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas para um repositório de dados do SAP BW:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço vinculado do SAP Business Warehouse (BW).

Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
server | Nome do servidor no qual reside a instância do SAP BW. | cadeia de caracteres | Sim
systemNumber | Número de sistema do sistema SAP BW. | Número decimal de dois dígitos representado como uma cadeia de caracteres. | Sim
clientId | ID de Cliente do cliente no sistema SAP W. | Número decimal de três dígitos representado como uma cadeia de caracteres. | Sim
Nome de Usuário | Nome do usuário que tem acesso ao servidor SAP | cadeia de caracteres | Sim
password | Senha do usuário. | cadeia de caracteres | Sim
gatewayName | O nome do gateway que o serviço Data Factory deve usar para se conectar à instância local do SAP BW. | cadeia de caracteres | Sim
encryptedCredential | A cadeia de caracteres de credencial criptografada. | cadeia de caracteres | Não

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. Não há propriedades específicas ao tipo com suporte para o conjunto de dados do SAP BW do tipo **RelationalTable**. 


## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

Por outro lado, as propriedades disponíveis na seção **typeProperties** da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Quando a fonte na atividade de cópia for do tipo **RelationalSource** (que inclui o SAP BW), as seguintes propriedades estão disponíveis na seção typeProperties:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query | Especifica a consulta MDX para ler dados da instância do SAP BW. | Consulta MDX. | Sim |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Exemplo JSON: Copiar dados do SAP Business Warehouse para o Blob do Azure
O exemplo a seguir fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Este exemplo mostra como copiar dados de um SAP Business Warehouse local para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.  

> [!IMPORTANT]
> Este exemplo fornece snippets de JSON. Ele não inclui instruções passo a passo para criar o data factory. Confira o artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo.

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [SapBw](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Atividade de Cópia que usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de uma instância do SAP Business Warehouse para um Blob do Azure por hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções estão no artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-business-warehouse-linked-service"></a>Serviço vinculado do SAP Business Warehouse
Esse serviço vinculado vincula sua instância do SAP BW à data factory. A propriedade type é definida como **SapBw**. A seção typeProperties fornece informações de conexão para a instância do SAP BW. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

### <a name="sap-bw-input-dataset"></a>Conjunto de dados de entrada do SAP BW
Esse conjunto de dados define o conjunto de dados do SAP Business Warehouse. Defina o tipo do conjunto de dados do Data Factory como **RelationalTable**. No momento, você não especifica quaisquer propriedades específicas ao tipo para um conjunto de dados do SAP BW. A consulta na definição da Atividade de Cópia especifica quais dados serão lidos da instância do SAP BW. 

Configurar a propriedade external como true informa ao serviço Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

As propriedades de frequência e intervalo definem a agenda. Nesse caso, os dados são lidos da instância do SAP BW por hora. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
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
Esse conjunto de dados define o conjunto de dados de saída do Blob do Azure. A propriedade type é definida como AzureBlob. A seção typeProperties fornece o local onde os dados copiados da instância do SAP BW serão armazenados. Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** (para a fonte do SAP BW) e o tipo **sink** está definido como **BlobSink**. A consulta especificada para a propriedade **query** seleciona os dados na última hora para copiar.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```



### <a name="type-mapping-for-sap-bw"></a>Mapeamento de tipo para SAP BW
Conforme mencionado no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) , a Atividade de cópia executa conversões automáticas de tipo de fonte para tipos de coletor, com a abordagem em duas etapas descritas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados do SAP BW, os seguintes mapeamentos serão usados dos tipos do SAP BW para os tipos do .NET.

Tipo de dados no Dicionário ABAP | Tipo de dados do .NET
-------------------------------- | --------------
ACCP |  Int
CHAR | String
CLNT | String
CURR | Decimal
CUKY | String
DEC | Decimal
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | Int
LANG | String
LCHR | String
LRAW | Byte[]
PREC | Int16
QUAN | Decimal
RAW | Byte[]
RAWSTRING | Byte[]
STRING | String
UNIT | String
DATS | String
NUMC | String
TIMS | string

> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas do conjunto de dados de coletor, confira [Mapeando colunas de conjunto de dados no Azure Data Factory](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas de coletor
Para saber mais sobre mapeamento de colunas no conjunto de dados de origem para colunas no conjunto de dados de coletor, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapeamento de colunas de conjunto de dados no Azure Data Factory).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de dados de modo que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, seja de que maneira for, você precisa garantir que os mesmos dados sejam lidos não importa quantas vezes uma fatia seja executada. Confira [Leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
