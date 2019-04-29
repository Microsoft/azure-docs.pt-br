---
title: Copiar dados do MongoDB usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do MongoDB para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 12/20/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: ca6040bb74839f30a2f1b13297f6037f05240c67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400433"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Copiar dados do MongoDB usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados MongoDB. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!IMPORTANT]
>O ADF lança essa nova versão do conector do MongoDB que fornece suporte nativo aprimorado ao MongoDB. Se você estiver usando o conector MongoDB anterior em sua solução, que é tem suporte no estado em que se encontra para oferecer compatibilidade com versões anteriores, confira o artigo [Conector do MongoDB (herdado)](connector-mongodb-legacy.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de um banco de dados MongoDB para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do MongoDB dá suporte a **versões até 3.4**.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de um banco de dados do MongoDB que não esteja acessível publicamente, você precisará configurar um Integration Runtime auto-hospedado. Consulte o artigo [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md) para saber mais detalhes.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do MongoDB.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do MongoDB:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type |A propriedade type deve ser definida como: **MongoDbV2** |Sim |
| connectionString |Especifique a cadeia de conexão MongoDB, por exemplo, `mongodb://[username:password@]host[:port][/[database][?options]]`. Confira o [manual do MongoDB na cadeia de conexão](https://docs.mongodb.com/manual/reference/connection-string/) para obter mais detalhes. <br/><br />Marque esse campo como um tipo **SecureString** para armazená-lo com segurança no Data Factory. Você também pode [referenciar um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). |Sim |
| Banco de Dados | O nome do banco de dados que você deseja criar. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime auto-hospedado ou o Integration Runtime do Azure (se seu armazenamento de dados estiver publicamente acessível). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

**Exemplo:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://[username:password@]host[:port][/[database][?options]]"
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

Para obter uma lista completa de seções e propriedades disponíveis para definição de conjuntos de dados, consulte [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md). As propriedades a seguir têm suporte para o conjunto de dados do MongoDB:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **MongoDbV2Collection** | Sim |
| collectionName |Nome da coleção no banco de dados MongoDB. |Sim |

**Exemplo:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte MongoDB.

### <a name="mongodb-as-source"></a>MongoDB como fonte

As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **MongoDbV2Source** | Sim |
| filtro | Especifica o filtro de seleção usando operadores de consulta. Para retornar todos os documentos em uma coleção, omita esse parâmetro ou passe um documento vazio ({}). | Não  |
| cursorMethods.project | Especifica os campos a serem retornados nos documentos para projeção. Para retornar todos os campos nos documentos correspondentes, omita este parâmetro. | Não  |
| cursorMethods.sort | Especifica a ordem na qual a consulta retorna documentos correspondentes. Consulte [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Não  |
| cursorMethods.limit | Especifica o número máximo de documentos que o servidor retorna. Consulte [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Não  |
| cursorMethods.skip | Especifica o número de documentos a serem ignorados e de onde o MongoDB começa a retornar resultados. Consulte [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Não  |
| batchSize | Especifica o número de documentos a serem retornados em cada lote da resposta da instância do MongoDB. Na maioria dos casos, modificar o tamanho do lote não afetará o usuário ou o aplicativo. O Cosmos DB limita cada lote para que ele não exceda 40 MB, que é a soma do tamanho do número de documentos do batchSize, portanto, diminua esse valor se o tamanho do documento for grande. | Não <br/>(o padrão é **100**) |

>[!TIP]
>Suporte do ADF consumindo o documento BSON em **Modo estrito**. Verifique se sua consulta de filtro está em Modo estrito em vez do modo Shell. Veja mais descrições no [manual do MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
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

## <a name="export-json-documents-as-is"></a>Exportar documentos JSON no estado em que se encontra

Você pode usar esse conector do MongoDB para exportar documentos JSON no estado em que se encontra de uma coleção do MongoDB para vários armazenamentos baseados em arquivo ou para o Azure Cosmos DB. Para efetuar essa cópia independente de esquema, ignore a seção da "estrutura" (também chamada de *esquema*) no conjunto de dados e mapeamento de esquema na atividade de cópia.

## <a name="schema-mapping"></a>Mapeamento de esquema

Para copiar dados do MongoDB para o coletor de tabela, confira o [mapeamento do esquema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
