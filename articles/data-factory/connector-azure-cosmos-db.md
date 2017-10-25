---
title: Copiar dados para/do Azure Cosmos DB usando o Data Factory.| Microsoft Docs
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte para o Azure Cosmos DB ou do Cosmos DB para armazenamentos de coletor com suporte, usando o Data Factory.
services: data-factory, cosmosdb
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 7d914684a0ee5598cee7972b78c3ec6296184466
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Copiar dados de ou para o Azure Cosmos DB usando o Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-azure-documentdb-connector.md)
> * [Versão 2 – Versão prévia](connector-azure-cosmos-db.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do e para o Azure Cosmos DB (API DocumentDB). Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está em GA (disponibilidade geral), consulte [Conector do Azure Cosmos DB do Azure na V1](v1/data-factory-azure-documentdb-connector.md).

## <a name="supported-scenarios"></a>Cenários com suporte

Você pode copiar dados do Azure Cosmos DB para qualquer armazenamento de dados de coletor com suporte ou copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Cosmos DB. Para obter uma lista de armazenamentos de dados com suporte, que funcionam como fontes/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Azure Cosmos DB dá suporte a:

- [API DocumentDB](https://docs.microsoft.com/en-us/azure/cosmos-db/documentdb-introduction) do Cosmos DB.
- Importação/exportação de documentos JSON no estado em que se encontram ou a cópia de dados de/para um conjunto de dados tabular, por exemplo, banco de dados SQL, arquivos CSV, etc.

Para copiar documentos no estado em que se encontram de/para arquivos JSON ou outra coleção do Cosmos DB, confira [Importação/Exportação de documentos JSON](#importexport-json-documents).

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia usando o SDK do .NET, o SDK do Python, o Azure PowerShell, a API REST ou o modelo do Azure Resource Manager. Confira o [Tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas para o Azure Cosmos DB.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Azure Cosmos DB:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **CosmosDb**. | Sim |
| connectionString |Especifique as informações necessárias para se conectar ao banco de dados do Azure Cosmos DB. Observe que você precisa especificar as informações de banco de dados na cadeia de conexão como no exemplo abaixo. Marque esse campo como uma SecureString. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

**Exemplo:**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Azure Cosmos DB.

Para copiar dados de/para o Azure Cosmos DB, defina a propriedade type do conjunto de dados como **DocumentDbCollection**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **DocumentDbCollection** |Sim |
| collectionName |Nome da coleção de documentos do Cosmos DB. |Sim |

**Exemplo:**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Esquema do Data Factory

Para armazenamentos de dados sem esquema, como o Azure Cosmos DB, a atividade de cópia infere o esquema em uma das seguintes maneiras. Portanto, a menos que você queira [importar/exportar documentos JSON no estado em que se encontram](#importexport-json-documents), a melhor prática é especificar a estrutura dos dados na seção **estrutura**.

1. Se você especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o serviço do Data Factory respeitará essa estrutura do esquema. Nesse caso, se uma linha não contiver um valor de uma coluna, um valor nulo será fornecido para ele.
2. Se você não especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o serviço do Data Factory inferirá o esquema usando a primeira linha dos dados. Nesse caso, se a primeira linha não contiver o esquema completo, algumas colunas estarão ausentes no resultado da operação de cópia.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor do Azure Cosmos DB.

### <a name="azure-cosmos-db-as-source"></a>Azure Cosmos DB como fonte

Para copiar dados do Azure Cosmos DB, defina o tipo de fonte na atividade de cópia como **DocumentDbCollectionSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **DocumentDbCollectionSource** |Sim |
| query |Especifique a consulta do Cosmos DB para ler dados.<br/><br/>Exemplo: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificada, a instrução SQL executada será: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Caractere especial para indicar que o documento está aninhado e como nivelar o conjunto de resultados.<br/><br/>Por exemplo, se uma consulta do Cosmos DB retornar um resultado aninhado `"Name": {"First": "John"}`, a atividade de cópia identificará o nome da coluna como "Name.First" com o valor "John" quando o nestedSeparator for um ponto. |Não (o padrão é o ponto `.`) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>Azure Cosmos DB como coletor

Para copiar dados do Azure Cosmos DB, defina o tipo de coletor na atividade de cópia como **DocumentDbCollectionSink**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **DocumentDbCollectionSink** |Sim |
| nestingSeparator |Um caractere especial no nome da coluna de fonte para indicar que esse documento aninhado é necessário. <br/><br/>Por exemplo, `Name.First` na estrutura do conjunto de dados de saída gera a seguinte estrutura JSON no documento do Cosmos DB:`"Name": {"First": "[value maps to this column from source]"}` quando o nestedSeparator é um ponto. |Não (o padrão é o ponto `.`) |
| writeBatchTimeout |Tempo de espera para a operação ser concluída antes de atingir o tempo limite.<br/><br/>Os valores permitidos são: período. Exemplo: "00:30:00" (30 minutos). |Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>Importar/Exportar documentos JSON

Usando esse conector do Cosmos DB, você pode facilmente

* Importar documentos JSON de várias fontes para o Cosmos DB, incluindo Blob do Azure, Azure Data Lake Store e outros repositórios com base em arquivos com suporte pelo Azure Data Factory.
* Exportar documentos JSON da coleção do Cosmos DB para vários repositórios com base em arquivo.
* Copiar documentos entre duas coleções do Cosmos DB no estado em que se encontram.

Para conseguir realizar essa cópia independente de esquema:

- Em conjunto de dados do Cosmos DB, não especifique a seção "estrutura"; e em fonte/coletor de atividade de cópia do Cosmos DB, não especifique a propriedade "nestingSeparator".
- Ao importar de/exportar para arquivos JSON, no conjunto de dados correspondente do repositório de arquivos, especifique o tipo de formato como "JsonFormat" e a configuração "filePattern" corretamente (consulte a seção [Formato JSON](supported-file-formats-and-compression-codecs.md#json-format) para obter detalhes) e não especifique a seção "estrutura" e ignore o restante das configurações de formato.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).