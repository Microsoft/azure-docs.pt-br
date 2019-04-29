---
title: Copiar dados de/para SAP Cloud for Customer usando Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do SAP Cloud for Customer para armazenamentos de dados de coletor compatíveis (ou) de armazenamentos de dados de origem compatíveis para o SAP Cloud for Customer usando o Data Factory.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 04/17/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: e4625b934f9e1cf98254f3dee59f9c26e8e16fb5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578701"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Copiar dados do SAP Cloud for Customer (C4C) usando o Azure Data Factory

Este artigo descreve como usar Copy Activity no Azure Data Factory para copiar dados de/para SAP Cloud for Customer (C4C). Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do SAP Cloud for Customer para qualquer armazenamento de dados de coletor compatível ou dados de qualquer armazenamento de dados de origem compatível para o SAP Cloud for Customer. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector permite que o Azure Data Factory copie dados de/para SAP Cloud for Customer, inclusive as soluções SAP Cloud for Sales, SAP Cloud for Service e SAP Cloud for Social Engagement.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades usadas para definir entidades de Data Factory específicas do conector do SAP Cloud for Customer.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir são compatíveis com o serviço vinculado SAP Cloud for Customer:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **SapCloudForCustomer**. | Sim |
| url | A URL do serviço SAP C4C OData. | Sim |
| Nome de Usuário | Especifique o nome de usuário para se conectar ao SAP C4C. | Sim |
| Senha | Especifique a senha da conta de usuário que você especificou para o nome de usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não para fonte, Sim para o coletor |

>[!IMPORTANT]
>Para copiar dados para o SAP Cloud for Customer, explicitamente [crie um IR Azure](create-azure-integration-runtime.md#create-azure-ir) com um local próximo do SAP Cloud for Customer e associe no serviço vinculado como o seguinte exemplo:

**Exemplo:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.cn/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista de propriedades compatíveis com o conjunto de dados do SAP Cloud for Customer.

Para copiar dados do SAP Cloud for Customer, defina a propriedade type do conjunto de dados como **SapCloudForCustomerResource**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **SapCloudForCustomerResource** |Sim |
| caminho | Especifique o caminho da entidade SAP C4C OData. |Sim |

**Exemplo:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades compatíveis com a fonte do SAP Cloud for Customer.

### <a name="sap-c4c-as-source"></a>SAP C4C como fonte

Para copiar dados do SAP Cloud for Customer, defina o tipo de origem na atividade de cópia como **SapCloudForCustomerSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **SapCloudForCustomerSource**  | Sim |
| query | Especifique a consulta OData personalizada para ler dados. | Não  |

Consulta de exemplo para obter dados de um dia específico:`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C como coletor

Para copiar dados do SAP Cloud for Customer, defina o tipo de coletor na atividade de cópia como **SapCloudForCustomerSource**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **SapCloudForCustomerSink**  | Sim |
| writeBehavior | O comportamento da operação de gravação. Pode ser “Inserir”, “Atualizar”. | Não. Padrão “Inserir”. |
| writeBatchSize | O tamanho do lote da operação de gravação. O tamanho do lote para obter o melhor desempenho pode ser diferente da tabela ou do servidor diferente. | Não. Padrão 10. |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapeamento do tipo de dados para o SAP Cloud for Customer

Durante a cópia de dados do SAP Cloud for Customer, os mapeamentos a seguir são usados do tipos de dados do SAP Cloud for Customer para os tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados SAP C4C OData | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
