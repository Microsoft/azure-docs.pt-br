---
title: Copiar dados para o índice de pesquisa
description: Saiba mais sobre como enviar por push ou copiar dados de um índice do Azure Search usando a atividade de cópia em um pipeline do Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 82f5d4f4c3f12b6e14e260fa004bb031247e1096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597497"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Copiar dados para um índice de Pesquisa Cognitiva do Azure usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-search-connector.md)
> * [Versão atual](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados para o índice de Pesquisa Cognitiva do Azure. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte no índice de pesquisa. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector de Pesquisa Cognitiva do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Azure Pesquisa Cognitiva:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AzureSearch** | Sim |
| url | URL para o serviço de pesquisa. | Sim |
| chave | Chave de administração para o serviço de pesquisa. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

> [!IMPORTANT]
> Ao copiar dados de um armazenamento de dados de nuvem no índice de pesquisa, no serviço vinculado do Azure Pesquisa Cognitiva, você precisará consultar um Azure Integration Runtime com uma região explícita no connactVia. Defina a região como aquela em que o serviço de pesquisa reside. Aprenda mais do [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de Pesquisa Cognitiva do Azure.

Para copiar dados para o Azure Pesquisa Cognitiva, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **AzureSearchIndex** | Sim |
| indexName | Nome do índice de pesquisa. O Data Factory não cria o índice. O índice deve existir no Pesquisa Cognitiva do Azure. | Sim |

**Exemplo:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte de Pesquisa Cognitiva do Azure.

### <a name="azure-cognitive-search-as-sink"></a>Pesquisa Cognitiva do Azure como coletor

Para copiar dados para o Azure Pesquisa Cognitiva, defina o tipo de fonte na atividade de cópia como **AzureSearchIndexSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **AzureSearchIndexSink** | Sim |
| writeBehavior | Especifica se deve mesclar ou substituir quando já existe um documento no índice. Veja a [propriedade WriteBehavior](#writebehavior-property).<br/><br/>Os valores permitidos são: **Merge** (padrão) e **Upload**. | Não |
| writeBatchSize | Carrega dados no índice de pesquisa quando o tamanho do buffer atinge writeBatchSize. Veja a [propriedade WriteBatchSize](#writebatchsize-property) para obter detalhes.<br/><br/>Os valores permitidos são: inteiros de 1 a 1.000; o valor padrão é 1.000. | Não |
| maxConcurrentConnections |O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não |

### <a name="writebehavior-property"></a>Propriedade WriteBehavior

Upsert do AzureSearchSink ao gravar dados. Em outras palavras, ao gravar um documento, se a chave do documento já existir no índice de pesquisa, o Azure Pesquisa Cognitiva atualizará o documento existente em vez de lançar uma exceção de conflito.

O AzureSearchSink fornece estes dois comportamentos de upsert (usando o SDK da AzureSearch):

- **Mesclar**: combine todas as colunas no novo documento com a existente. Para colunas com valor nulo no novo documento, o valor existente é preservado.
- **Carregar**: o novo documento substituirá o existente. Para colunas não especificadas no novo documento, o valor será definido como nulo se houver um valor não nulo no documento existente ou não.

O comportamento padrão é **Mesclar**.

### <a name="writebatchsize-property"></a>Propriedade WriteBatchSize

O serviço de Pesquisa Cognitiva do Azure dá suporte à gravação de documentos como um lote. Um lote pode conter de 1 a 1.000 ações. Uma ação manipula um documento para executar a operação de carregamento/mesclagem.

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
                "referenceName": "<Azure Cognitive Search output dataset name>",
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

## <a name="data-type-support"></a>Suporte do tipo de dados

A tabela a seguir especifica se um tipo de dados do Azure Pesquisa Cognitiva tem suporte ou não.

| Tipo de dados Pesquisa Cognitiva do Azure | Com suporte no coletor de Pesquisa Cognitiva do Azure |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| Boolean | Y |
| DataTimeOffset | Y |
| Matriz de cadeia de caracteres | N |
| GeographyPoint | N |

Atualmente, outros tipos de dados, por exemplo, complexType, não são suportados. Para obter uma lista completa de tipos de dados com suporte do Azure Pesquisa Cognitiva, consulte [tipos de dados com suporte (azure pesquisa cognitiva)](/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).