---
title: Copiar dados para ou da API do Azure Cosmos DB para MongoDB usando o Data Factory | Microsoft Docs
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte de ou para a API do Azure Cosmos DB para MongoDB para armazenamentos de coletor com suporte usando o Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 82418c03039219adedf45828d769d278a14499ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61259698"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Copiar dados de e para o Azure Cosmos DB (API do MongoDB) usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para a API do Azure Cosmos DB para MongoDB. O artigo baseia-se em [Atividade de Cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da Atividade de Cópia.

>[!NOTE]
>Esse conector somente dá suporte à cópia de dados de e para a API do Azure Cosmos DB para MongoDB. Para a API do SQL, consulte [conector de API do SQL do Cosmos DB](connector-azure-cosmos-db.md). Não há suporte para outros tipos de API.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados da API do Azure Cosmos DB para MongoDB para qualquer armazenamento de dados de coletor com suporte ou copiar dados de qualquer armazenamento de dados de origem compatível para a API do Azure Cosmos DB para MongoDB. Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores, consulte [Armazenamentos de dados e formatos compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

Você pode usar o conector da API do Azure Cosmos DB para MongoDB para:

- Copiar dados para e da [API do Azure Cosmos DB para MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Gravar no Azure Cosmos DB como **insert** ou **upsert**.
- Importar e exportar documentos JSON no estado em que se encontram, ou copiar dados de ou para um conjunto de dados tabular. Exemplos incluem um Banco de Dados SQL e um arquivo CSV. Para copiar documentos no estado em que se encontram para ou de arquivos JSON de outra coleção do Azure Cosmos DB, confira Importar ou exportar documentos JSON.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que podem ser usadas para definir entidades do Data Factory específicas para o API do Azure Cosmos DB para MongoDB.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado da API do Azure Cosmos DB para MongoDB:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | O **tipo** de propriedade deve ser definido como **CosmosDbMongoDbApi**. | Sim |
| connectionString |Especifique a cadeia de conexão da API do Azure Cosmos DB para MongoDB. Encontre-a no portal do Azure -> sua folha Cosmos DB -> cadeia de caracteres de conexão primária ou secundária, com o padrão de `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Marque esse campo como um tipo **SecureString** para armazená-lo com segurança no Data Factory. Você também pode [referenciar um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). |Sim |
| Banco de Dados | O nome do banco de dados que você deseja criar. | Sim |
| connectVia | O [Tempo de Integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você poderá usar o Azure Integration Runtime ou um tempo de execução da integração auto-hospedada (se o armazenamento de dados estiver localizado em uma rede privada). Se essa propriedade não for especificada, o Azure Integration Runtime padrão será usado. |Não  |

**Exemplo**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e propriedades disponíveis para definição de conjuntos de dados, consulte [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md). As propriedades a seguir são compatíveis com o conjunto de dados da API do Azure Cosmos DB para MongoDB:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | O **tipo** de propriedade do conjunto de dados deve ser definido como **CosmosDbMongoDbApiCollection**. |Sim |
| collectionName |O nome da coleção do Azure Cosmos DB. |Sim |

**Exemplo**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Esta seção fornece uma lista das propriedades compatíveis com fonte e coletor da API do Azure Cosmos DB para MongoDB.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>API do Azure Cosmos DB para MongoDB como fonte

As seguintes propriedades são suportadas na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | O **tipo** de propriedade da fonte da atividade de cópia deve ser definido como **CosmosDbMongoDbApiSource**. |Sim |
| filtro | Especifica o filtro de seleção usando operadores de consulta. Para retornar todos os documentos em uma coleção, omita esse parâmetro ou passe um documento vazio ({}). | Não  |
| cursorMethods.project | Especifica os campos a serem retornados nos documentos para projeção. Para retornar todos os campos nos documentos correspondentes, omita este parâmetro. | Não  |
| cursorMethods.sort | Especifica a ordem na qual a consulta retorna documentos correspondentes. Consulte [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Não  |
| cursorMethods.limit | Especifica o número máximo de documentos que o servidor retorna. Consulte [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Não  | 
| cursorMethods.skip | Especifica o número de documentos a serem ignorados e de onde o MongoDB começa a retornar resultados. Consulte [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Não  |
| batchSize | Especifica o número de documentos a serem retornados em cada lote da resposta da instância do MongoDB. Na maioria dos casos, modificar o tamanho do lote não afetará o usuário ou o aplicativo. O Cosmos DB limita cada lote para que ele não exceda 40 MB, que é a soma do tamanho do número de documentos do batchSize, portanto, diminua esse valor se o tamanho do documento for grande. | Não <br/>(o padrão é **100**) |

>[!TIP]
>Suporte do ADF consumindo o documento BSON em **Modo estrito**. Verifique se sua consulta de filtro está em Modo estrito em vez do modo Shell. Veja mais descrições no [manual do MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>API do Azure Cosmos DB para MongoDB como coletor

As seguintes propriedades são suportadas na seção Copy Activity **sink**:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | O **tipo** de propriedade do coletor de atividade de cópia deve ser definido como **CosmosDbMongoDbApiSink**. |Sim |
| writeBehavior |Descreve como gravar dados no Azure Cosmos DB. Valores permitidos são **insert** e **upsert**.<br/><br/>O comportamento de **upsert** é substituir o documento se um documento com a mesma ID já existir; caso contrário, inserir o documento.<br /><br />**Observação**: O Data Factory gerará automaticamente uma ID para um documento se uma ID não for especificada no documento original ou pelo mapeamento de coluna. Isso significa que, para **upsert** funcionar conforme esperado, o documento deve ter uma ID. |Não <br />(o padrão é **insert**) |
| writeBatchSize | A propriedade **writeBatchSize** controla o tamanho dos documentos que escrevemos em cada lote. Você pode tentar aumentar o valor de **writeBatchSize** para melhorar o desempenho e diminuir o valor se o tamanho do documento for grande. |Não <br />(o padrão é **10.000**) |
| writeBatchTimeout | O tempo de espera para o lote inserir operação seja concluída antes de expirar. O valor permitido é timespan. | Não <br/>(o padrão é **30:00:00** - 30 minutos) |

**Exemplo**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
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
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

>[!TIP]
>Para importar documentos JSON como eles estão, consulte a seção [importar ou exportar documentos JSON](#import-or-export-json-documents); para copiar de dados em formato tabular, consulte [mapeamento de esquema](#schema-mapping).

## <a name="import-or-export-json-documents"></a>Importar ou exportar documentos JSON

Você pode usar esse conector do Azure Cosmos DB para facilmente:

* Importar documentos JSON de várias fontes para o Azure Cosmos DB, incluindo o armazenamento de BLOBs do Azure, Azure Data Lake Storage e outros repositórios baseados em arquivo compatíveis com o Azure Data Factory.
* Exportar documentos JSON de uma coleção do Azure Cosmos DB para vários repositórios baseados em arquivo.
* Copiar documentos entre duas coleções do Azure Cosmos DB no estado em que se encontram.

Para efetuar essa cópia independente de esquema, ignore a seção da "estrutura" (também chamada de *esquema*) no conjunto de dados e mapeamento de esquema na atividade de cópia.

## <a name="schema-mapping"></a>Mapeamento de esquema

Para copiar dados da API do Azure Cosmos DB para MongoDB para o coletor tabular ou revertido, veja o [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping).

Especificamente para gravação no Cosmos DB, para certificar-se de preencher o Cosmos DB com a ID de objeto à direita da sua fonte de dados, por exemplo, há uma coluna de "id" na tabela de banco de dados SQL e esse valor é usado como a ID do documento no MongoDB a ser inserida. É necessário definir o mapeamento de esquema adequado de acordo com a definição do modo estrito MongoDB (`_id.$oid`) como o seguinte:

![ID do mapa em um coletor do MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Após a cópia de execução da atividade, o ObjectId BSON abaixo é gerado no coletor:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados compatíveis com a Atividade de Cópia como fontes e coletores no Azure Data Factory, confira [armazenamentos de dados compatíveis](copy-activity-overview.md##supported-data-stores-and-formats).
