---
title: Copiar dados do Teradata usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre o conector do Teradata do serviço do Data Factory que permite que você copie dados do banco de dados do Teradata para armazenamentos de dados com suporte pelo Data Factory como coletores.
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
ms.openlocfilehash: e9fd818990c8a985a77c2e7eeea19bf63c440e4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347642"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Copiar dados do Teradata usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-onprem-teradata-connector.md)
> * [Versão atual](connector-teradata.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados Teradata. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de um banco de dados Teradata para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Teradata dá suporte a:

- Teradata **versão 12 e superior**.
- Cópia de dados usando a autenticação **Básica** ou do **Windows**.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector do Teradata, você precisa:

- Configurar um Integration Runtime auto-hospedado. Consulte o artigo [Self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Integration Runtime auto-hospedado) para obter detalhes.
- Instalar o [Provedor de dados .NET para Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) versão 14 ou superior no computador do Integration Runtime.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao Teradata.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Teradata:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Teradata** | Sim |
| Servidor | Nome do servidor Teradata. | Sim |
| authenticationType | Tipo de autenticação usado para se conectar ao banco de dados Teradata.<br/>Valores permitidos são: **Básico** e **Windows**. | Sim |
| Nome de Usuário | Especifique o nome de usuário para se conectar ao banco de dados Teradata. | Sim |
| Senha | Especifique a senha da conta de usuário que você especificou para o nome de usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "Basic",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Teradata.

Para copiar dados do Teradata, defina a propriedade type do conjunto de dados como **RelationalTable**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **RelationalTable** | Sim |
| tableName | Nome da tabela no banco de dados Teradata. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem do Teradata.

### <a name="teradata-as-source"></a>Teradata como origem

Para copiar dados do Teradata, defina o tipo de origem na atividade de cópia como **RelationalSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **RelationalSource** | Sim |
| query | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-teradata"></a>Mapeamento de tipo de dados para Teradata

Ao copiar dados do Teradata, os seguintes mapeamentos são usados de tipos de dados do Teradata para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados do Teradata | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |Cadeia de caracteres |
| Clob |Cadeia de caracteres |
| Data |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Cadeia de caracteres |
| Número inteiro |Int32 |
| Intervalo - dia |TimeSpan |
| Intervalo - dia para hora |TimeSpan |
| Intervalo - dia para minuto |TimeSpan |
| Interval Day To Second |TimeSpan |
| Intervalo - hora |TimeSpan |
| Intervalo - hora para minuto |TimeSpan |
| Interval Hour To Second |TimeSpan |
| Interval Minute |TimeSpan |
| Interval Minute To Second |TimeSpan |
| Interval Month |Cadeia de caracteres |
| Interval Second |TimeSpan |
| Interval Year |Cadeia de caracteres |
| Interval Year To Month |Cadeia de caracteres |
| Número |Double |
| Period(Date) |Cadeia de caracteres |
| Period(Time) |Cadeia de caracteres |
| Period(Time With Time Zone) |Cadeia de caracteres |
| Period(Timestamp) |Cadeia de caracteres |
| Period(Timestamp With Time Zone) |Cadeia de caracteres |
| SmallInt |Int16 |
| Hora |TimeSpan |
| Hora com fuso horário |Cadeia de caracteres |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTimeOffset |
| VarByte |Byte[] |
| VarChar |Cadeia de caracteres |
| VarGraphic |Cadeia de caracteres |
| Xml |Cadeia de caracteres |


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
