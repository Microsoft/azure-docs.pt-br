---
title: Copiar dados para ou do Azure Cosmos DB (API SQL) usando o Data Factory | Microsoft Docs
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte de ou para o Azure Cosmos DB (API do SQL) para armazenamentos de coletor com suporte usando o Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: eca5e4cc96996c35e7c2181746cdb3de2e5a602c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61259510"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Copiar dados de e para o Azure Cosmos DB (SQL API) usando o Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-azure-documentdb-connector.md)
> * [Versão atual](connector-azure-cosmos-db.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do e para o Azure Cosmos DB (API do SQL). O artigo baseia-se em [Atividade de Cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da Atividade de Cópia.

>[!NOTE]
>Esse suporte de conector somente suporta copiar dados para/da API SQL do Cosmos DB. Para a API do MongoDB, confira o [conector para API do Azure Cosmos DB para MongoDB](connector-azure-cosmos-db-mongodb-api.md). Não há suporte para outros tipos de API.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do Azure Cosmos DB (API do SQL) para qualquer armazenamento de dados de coletor com suporte ou copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Cosmos DB (API do SQL). Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores, consulte [Armazenamentos de dados e formatos compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

Você pode usar o conector do Azure Cosmos DB (API do SQL) para:

- Copiar dados para e da [API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) do Azure Cosmos DB.
- Gravar no Azure Cosmos DB como **insert** ou **upsert**.
- Importar e exportar documentos JSON no estado em que se encontram, ou copiar dados de ou para um conjunto de dados tabular. Exemplos incluem um Banco de Dados SQL e um arquivo CSV. Para copiar documentos no estado em que se encontram para ou de arquivos JSON de outra coleção do Azure Cosmos DB, confira Importar ou exportar documentos JSON.

O Data Factory integra-se com a [biblioteca de executor em massa do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para fornecer o melhor desempenho quando você escreve para o Azure Cosmos DB.

> [!TIP]
> O [vídeo de Migração de Dados](https://youtu.be/5-SRNiC_qOU) orienta você pelas etapas de copiar dados do Armazenamento de Blobs do Azure para o Azure Cosmos DB. O vídeo também descreve considerações de ajuste de desempenho para a ingestão de dados para o Azure Cosmos DB em geral.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que você pode usar para definir entidades do Data Factory específicas para o Azure Cosmos DB (API do SQL).

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Azure Cosmos DB (API do SQL):

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade **type** deve ser definida como **CosmosDb**. | Sim |
| connectionString |Especifique as informações necessárias para se conectar ao banco de dados do Azure Cosmos DB.<br />**Observação**: Você deve especificar informações de banco de dados na cadeia de conexão conforme mostrado nos exemplos a seguir. <br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. Você também pode colocar a chave de conta no Azure Key Vault e efetuar pull da configuração `accountKey` da cadeia de conexão. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| connectVia | O [Tempo de Integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você poderá usar o Azure Integration Runtime ou um tempo de execução da integração auto-hospedada (se o armazenamento de dados estiver localizado em uma rede privada). Se essa propriedade não for especificada, o Azure Integration Runtime padrão será usado. |Não  |

**Exemplo**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
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

**Exemplo: armazenar a chave da conta no Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Esta seção fornece uma lista das propriedades compatíveis com o conjunto de dados do Azure Cosmos DB (API do SQL). 

Para obter uma lista completa de seções e propriedades disponíveis para definição de conjuntos de dados, consulte [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md). 

Para copiar dados de ou para o Azure Cosmos DB (API do SQL), defina a propriedade **type** do conjunto de dados como **DocumentDbCollection**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade **type** do conjunto de dados deve ser definida como **DocumentDbCollection**. |Sim |
| collectionName |O nome da coleção de documentos do Azure Cosmos DB. |Sim |

**Exemplo**

```json
{
    "name": "CosmosDbSQLAPIDataset",
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

Para armazenamentos de dados sem esquema como o Azure Cosmos DB, a atividade de cópia infere o esquema de uma das maneiras descritas na lista a seguir. A menos que você queira [importar ou exportar documentos JSON no estado em que se encontram](#import-or-export-json-documents), a melhor prática é especificar a estrutura dos dados na seção **estrutura**.

* Se você especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o Data Factory respeitará essa estrutura do esquema. 

    Se uma linha não contiver um valor de uma coluna, um valor nulo será fornecido para ele.
* Se você não especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o serviço do Data Factory inferirá o esquema usando a primeira linha dos dados. 

    Se a primeira linha não contiver o esquema completo, algumas colunas estarão ausentes no resultado da operação de cópia.

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Esta seção fornece uma lista das propriedades compatíveis com fonte e coletor do Azure Cosmos DB (API do SQL).

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (API do SQL) como origem

Para copiar dados do Azure Cosmos DB (API do SQL), defina o tipo **source** na Atividade de Cópia como **DocumentDbCollectionSource**. 

As seguintes propriedades são suportadas na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade **type** da fonte da atividade de cópia deve ser definida como **DocumentDbCollectionSource**. |Sim |
| query |Especifique a consulta do Azure Cosmos DB para ler dados.<br/><br/>Exemplo:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não  <br/><br/>Se não for especificada, esta instrução SQL será executada: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Um caractere especial para indicar que o documento está aninhado e como nivelar o conjunto de resultados.<br/><br/>Por exemplo, se uma consulta do Azure Cosmos DB retornar o resultado aninhado `"Name": {"First": "John"}`, a Atividade de Cópia identificará o nome da coluna como `Name.First`, com o valor "John", quando o valor **nestedSeparator** for **.** (ponto). |Não <br />(o padrão é **.** (ponto)) |

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (API do SQL) como coletor

Para copiar dados para o Azure Cosmos DB (API do SQL), defina o tipo **sink** na Atividade de Cópia como **DocumentDbCollectionSink**. 

As seguintes propriedades são suportadas na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade **type** do coletor da atividade de cópia deve ser definida como **DocumentDbCollectionSink**. |Sim |
| writeBehavior |Descreve como gravar dados no Azure Cosmos DB. Valores permitidos são **insert** e **upsert**.<br/><br/>O comportamento de **upsert** é substituir o documento se um documento com a mesma ID já existir; caso contrário, inserir o documento.<br /><br />**Observação**: O Data Factory gerará automaticamente uma ID para um documento se uma ID não for especificada no documento original ou pelo mapeamento de coluna. Isso significa que, para **upsert** funcionar conforme esperado, o documento deve ter uma ID. |Não <br />(o padrão é **insert**) |
| writeBatchSize | O Data Factory usa a [biblioteca de executor em massa do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para gravar dados no Azure Cosmos DB. A propriedade **writeBatchSize** controla o tamanho dos documentos fornecidos pelo ADF à biblioteca. Você pode tentar aumentar o valor de **writeBatchSize** para melhorar o desempenho e diminuir o valor se o tamanho do documento for grande - veja as dicas abaixo. |Não <br />(o padrão é **10.000**) |
| nestingSeparator |Um caractere especial no nome da coluna **fonte** para indicar que esse documento aninhado é necessário. <br/><br/>Por exemplo, `Name.First` na estrutura do conjunto de dados de saída gera a seguinte estrutura JSON no documento do Azure Cosmos DB quando o **nestedSeparator** é **.** (ponto): `"Name": {"First": "[value maps to this column from source]"}`  |Não <br />(o padrão é **.** (ponto)) |

>[!TIP]
>O Cosmos DB limita o tamanho da solicitação única para 2MB. A fórmula é Tamanho da Solicitação = Tamanho Único do Documento * Tamanho do Lote de Gravação. Se você digitar o erro dizendo **"O tamanho da solicitação é muito grande"**, **reduza o `writeBatchSize`valor** na configuração do coletor de cópia.

**Exemplo**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
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
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-or-export-json-documents"></a>Importar ou exportar documentos JSON

Use esse conector do Azure Cosmos DB (API do SQL) para facilmente:

* Importar documentos JSON de várias fontes para o Azure Cosmos DB, incluindo o armazenamento de BLOBs do Azure, Azure Data Lake Storage e outros repositórios baseados em arquivo compatíveis com o Azure Data Factory.
* Exportar documentos JSON de uma coleção do Azure Cosmos DB para vários repositórios baseados em arquivo.
* Copiar documentos entre duas coleções do Azure Cosmos DB no estado em que se encontram.

Para obter uma cópia independente de esquema:

* Quando você usa a ferramenta Copiar Dados, selecione a opção **Exportar no estado em que se encontram para arquivos JSON ou uma coleção do Cosmos DB**.
* Quando você usa a criação de atividade, não especifique a seção **estrutura** (também chamada de *esquema*) no conjunto de dados do Azure Cosmos DB. Além disso, não especifique a propriedade **nestingSeparator** na fonte ou no coletor do Azure Cosmos DB na Atividade de Cópia. Ao importar ou exportar para arquivos JSON, no conjunto de dados de armazenamento de arquivo correspondente, especifique o tipo **format** como **JsonFormat** e configure **filePattern** como descrito na seção [formato JSON](supported-file-formats-and-compression-codecs.md#json-format). Em seguida, não especifique a seção **estrutura** e ignore o restante das configurações de formato.

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados compatíveis com a Atividade de Cópia como fontes e coletores no Azure Data Factory, confira [armazenamentos de dados compatíveis](copy-activity-overview.md##supported-data-stores-and-formats).
