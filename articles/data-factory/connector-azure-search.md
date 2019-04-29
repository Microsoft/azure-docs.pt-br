---
title: Copiar dados para o índice do Search usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como enviar por push ou copiar dados de um índice do Azure Search usando a atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 1c8cbcd2e5f137b1e8381dcce164ae9a4b87e804
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60998465"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Copiar dados em um índice do Azure Search usando o Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-azure-search-connector.md)
> * [Versão atual](connector-azure-search.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados no índice do Azure Search. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o índice do Azure Search. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Azure Search.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Azure Search:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AzureSearch** | Sim |
| url | URL para o serviço Azure Search. | Sim |
| chave | Chave de administração para o serviço Azure Search. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

> [!IMPORTANT]
> Ao copiar dados de um armazenamento de dados de nuvem no índice de Azure Search, no Azure Search, serviço vinculado, você precisa para se referir a um tempo de execução de integração do Azure com a região explícita no connactVia. Defina a região como a que seu Azure Search reside. Aprenda mais do [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Exemplo:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Azure Search.

Para copiar dados no Azure Search, defina a propriedade type do conjunto de dados como **RelationalTable**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **AzureSearchIndex** | Sim |
| indexName | Nome do índice do Azure Search. O Data Factory não cria o índice. O índice deve existir no Azure Search. | Sim |

**Exemplo:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do Azure Search.

### <a name="azure-search-as-sink"></a>Azure Search como coletor

Para copiar no Azure Search, defina o tipo de fonte na atividade de cópia como **AzureSearchIndexSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **AzureSearchIndexSink** | Sim |
| writeBehavior | Especifica se deve mesclar ou substituir quando já existe um documento no índice. Veja a [propriedade WriteBehavior](#writebehavior-property).<br/><br/>Valores permitidos são: **Mesclar** (padrão) e **Carregar**. | Não  |
| writeBatchSize | Carrega dados para o índice do Azure Search quando o tamanho do buffer atinge writeBatchSize. Veja a [propriedade WriteBatchSize](#writebatchsize-property) para obter detalhes.<br/><br/>Os valores permitidos são: inteiros de 1 a 1.000; o valor padrão é 1.000. | Não  |

### <a name="writebehavior-property"></a>Propriedade WriteBehavior

Upsert do AzureSearchSink ao gravar dados. Em outras palavras, ao escrever um documento, se a chave do documento já existe no índice do Azure Search, o Azure Search atualiza o documento existente, em vez de lançar uma exceção de conflito.

O AzureSearchSink fornece estes dois comportamentos de upsert (usando o SDK da AzureSearch):

- **Mesclar**: combine todas as colunas no novo documento com a existente. Para colunas com valor nulo no novo documento, o valor existente é preservado.
- **Carregar**: O novo documento substituirá o existente. Para colunas não especificadas no novo documento, o valor será definido como nulo se houver um valor não nulo no documento existente ou não.

O comportamento padrão é **Mesclar**.

### <a name="writebatchsize-property"></a>Propriedade WriteBatchSize

O serviço de Azure Search oferece suporte a escrever documentos como um lote. Um lote pode conter de 1 a 1.000 ações. Uma ação manipula um documento para executar a operação de carregamento/mesclagem.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

### <a name="data-type-support"></a>Suporte ao tipo de dados

A tabela a seguir especifica se um tipo de dados do Azure Search tem suporte ou não.

| Tipo de dados do Azure Search | Suporte no coletor do Azure Search |
| ---------------------- | ------------------------------ |
| String | S |
| Int32 | S |
| Int64 | S |
| Double | S |
| Boolean | S |
| DataTimeOffset | S |
| String Array | N |
| GeographyPoint | N |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
