---
title: Copiar dados do Netezza usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do Netezza para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 02/01/2019
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: 9bf90c9d3ce593ba5bf6339cd9cec31bb49f14f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399920"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Copiar dados do Netezza usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do Netezza. O artigo baseia-se em [Atividade de Cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do Netezza para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores, consulte [Armazenamentos de dados e formatos compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

O Azure Data Factory fornece um driver interno para habilitar a conectividade. Você não precisa instalar manualmente nenhum driver para usar este conector.

## <a name="get-started"></a>Introdução

Você pode criar um pipeline que usa uma atividade de cópia usando o SDK do .NET, o SDK do Python, o Azure PowerShell, a API REST ou um modelo do Azure Resource Manager. Consulte o [tutorial Atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre como criar um pipeline que tenha uma atividade de cópia.

As seções a seguir fornecem detalhes sobre propriedades que você pode usar para definir entidades do Data Factory que são específicas para o conector Netezza.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para o serviço vinculado do Netezza:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade deve ser definida como **Netezza**. | Sim |
| connectionString | Uma cadeia de conexão ODBC para conectar-se ao Netezza. <br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. Você também pode colocar uma senha no Azure Key Vault e extrair a configuração `pwd` da cadeia de conexão. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) que oferece mais detalhes. | Sim |
| connectVia | O [Tempo de Integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode escolher um Runtime de Integração auto-hospedado ou o Tempo de Execução de Integração do Azure (se o seu armazenamento de dados estiver acessível publicamente). Se não especificado, o Tempo de Execução de Integração do Azure padrão será usado. |Não  |

Uma cadeia de conexão válida é `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. A tabela a seguir descreve as propriedades mais que você pode definir:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| SecurityLevel | O nível de segurança SSL/TLS () que usa o driver para a conexão ao repositório de dados. Exemplo: `SecurityLevel=preferredSecured`. Os valores para os quais há suporte são:<br/>- **Apenas não seguro** (**onlyUnSecured**): O driver não usa SSL.<br/>- **Preferencial não seguro (preferredUnSecured) (padrão)**: Se o servidor fornece uma opção, o driver não usa SSL. <br/>- **Preferencial seguro (preferredUnSecured) (padrão)**: Se o servidor fornece uma opção, o driver não usa SSL. <br/>- **Somente seguro (onlySecured)**: O driver não se conecta a menos que uma conexão SSL esteja disponível. | Não  |
| CaCertFile | O caminho completo para o certificado SSL que é usado pelo servidor. Exemplo: `CaCertFile=<cert path>;`| Sim, se o SSL estiver habilitado |

**Exemplo**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a senha no Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
            },
            "pwd": { 
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

Esta seção fornece uma lista de propriedades que o conjunto de dados do Netezza suporta.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir conjuntos de dados, consulte [Conjuntos de Dados](concepts-datasets-linked-services.md).

Para copiar dados do Netezza, defina as **tipo** propriedade do conjunto de dados como **NetezzaTable**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **NetezzaTable** | Sim |
| tableName | Nome da tabela. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Esta seção fornece uma lista de propriedades que a fonte do Netezza suporta.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza como fonte

Para copiar dados do Netezza, defina as **fonte** tipo de atividade de cópia para **NetezzaSource**. As seguintes propriedades são suportadas na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade **tipo** da origem da Atividade de Cópia deve ser configurada para **NetezzaSource**. | Sim |
| query | Utiliza a consulta SQL personalizada para ler os dados. Exemplo: `"SELECT * FROM MyTable"` | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores no Azure Data Factory, consulte [Armazenamentos e formatos de dados compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).
