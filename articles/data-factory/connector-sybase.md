---
title: Copiar dados do Sybase usando o Azure Data Factory
description: Saiba como copiar dados do Sybase para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: jingwang
ms.openlocfilehash: 2ef63eded5403c1cf5faddec71ed3503c3ae2138
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384802"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Copiar dados do Sybase usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-sybase-connector.md)
> * [Versão atual](connector-sybase.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados Sybase. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do Sybase tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dados Sybase para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Sybase dá suporte a:

- SAP Sybase SQL Anywhere (ASA) **versão 16 e superior**.
- Cópia de dados usando a autenticação **Básica** ou do **Windows**.

Não há suporte para Sybase IQ e ASE. Em vez disso, você pode usar o conector ODBC genérico com o driver Sybase.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector do Sybase, você precisa:

- Configurar um Integration Runtime auto-hospedado. Confira o artigo de [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.
- Instalar o [provedor de dados do Sybase iAnywhere.Data.SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 ou superior no computador do Integration Runtime.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao Sybase.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Sybase:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Sybase** | Sim |
| Servidor | Nome do servidor do Sybase. |Sim |
| Banco de Dados | Nome do banco de dados do Sybase. |Sim |
| authenticationType | Tipo de autenticação usado para se conectar ao banco de dados Sybase.<br/>Os valores permitidos são: **Básico** e **Windows**. |Sim |
| Nome de Usuário | Especifique o nome de usuário para se conectar ao banco de dados Sybase. |Sim |
| password | Especifique a senha da conta de usuário que você especificou para o nome de usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Sybase.

Para copiar dados do Sybase, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **sybasetable** | Sim |
| tableName | Nome da tabela no banco de dados do Sybase. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "SybaseTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se você estava usando o conjunto de dados com tipos `RelationalTable`, ele ainda tem suporte como está, mas é recomendável usar o novo de agora em diante.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem do Sybase.

### <a name="sybase-as-source"></a>Sybase como origem

Para copiar dados do Sybase, há suporte para as seguintes propriedades na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **Sybase** | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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
                "type": "SybaseSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se você estava usando a fonte com tipos `RelationalSource`, ela ainda tem suporte como está, mas é recomendável usar a nova no futuro.

## <a name="data-type-mapping-for-sybase"></a>Mapeamento de tipo de dados para Sybase

Ao copiar dados do Sybase, os seguintes mapeamentos são usados de tipos de dados do Sybase para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

O Sybase dá suporte a tipos T-SQL. Para uma tabela de mapeamento de tipos SQL para tipos de dados provisórios do Azure Data Factory, consulte a seção [Conector do banco de dados SQL do Azure – mapeamento de tipo de dados](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database).

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).



## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
