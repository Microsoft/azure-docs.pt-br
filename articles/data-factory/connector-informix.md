---
title: Copiar dados do e para o IBM Informix usando o Azure Data Factory
description: Saiba como copiar dados do e para o IBM Informix usando uma atividade de cópia em um pipeline de Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/20/2021
ms.author: jingwang
ms.openlocfilehash: 23fc5cabb947f579177a26afc6baec873f2df154
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727882"
---
# <a name="copy-data-from-and-to-ibm-informix-using-azure-data-factory"></a>Copiar dados do e para o IBM Informix usando o Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um armazenamento de dados do IBM Informix. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do Informix tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados da origem da Informix para qualquer armazenamento de dados de coletor com suporte ou copiar de qualquer armazenamento de dados de origem com suporte para o coletor Informix. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).


## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector Informix, você precisa:

- Configurar um Integration Runtime auto-hospedado. Confira o artigo de [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.
- Instale o driver ODBC do Informix para o armazenamento de dados no computador Integration Runtime. Para instalação e instalação de driver, consulte o artigo [Guia do driver ODBC do Informix](https://www.ibm.com/support/knowledgecenter/SSGU8G_11.70.0/com.ibm.odbc.doc/odbc.htm) no IBM Knowledge Center para obter detalhes ou entre em contato com a equipe de suporte da IBM para obter diretrizes de instalação de driver.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector Informix.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Informix:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **Informix** | Sim |
| connectionString | A cadeia de conexão ODBC excluindo a parte da credencial. Você pode especificar a cadeia de conexão ou usar o DSN do sistema (nome da fonte de dados) que você configurou no computador Integration Runtime (você ainda precisa especificar a parte da credencial no serviço vinculado adequadamente). <br> Você também pode colocar uma senha em Azure Key Vault e extrair a `password` configuração da cadeia de conexão. Consulte [armazenar credenciais em Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes.| Sim |
| authenticationType | Tipo de autenticação usado para se conectar ao armazenamento de dados Informix.<br/>Os valores permitidos são: **Básica** e **Anônima**. | Sim |
| userName | Especifique o nome de usuário se você estiver usando a autenticação Básica. | Não |
| password | Especifique a senha da conta de usuário que você especificou para userName. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| credencial | A parte da credencial de acesso da cadeia de conexão especificada no formato propriedade-valor específico do driver. Marque esse campo como uma SecureString. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de acordos do Informix.

Para copiar dados do Informix, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **informixtable** | Sim |
| tableName | Nome da tabela no Informix. | Não para fonte (se "query" na fonte da atividade for especificada);<br/>Sim para coletor |

**Exemplo**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem da Informix.

### <a name="informix-as-source"></a>Informix como fonte

Para copiar dados do Informix, há suporte para as seguintes propriedades na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **Informix** | Sim |
| Consulta | Utiliza a consulta personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="informix-as-sink"></a>Informix como coletor

Para copiar dados para a Informix, há suporte para as seguintes propriedades na seção **coletor** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do coletor da atividade de cópia deve ser definida como: **InformixSink** | Sim |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são: período. Exemplo: "00:30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize.<br/>Os valores permitidos são: inteiro (número de linhas). |Não (o padrão é 0 – detectado automaticamente) |
| preCopyScript |Especifica uma consulta SQL para a atividade de cópia, a ser executada antes de gravar dados no armazenamento de dados em cada execução. Você pode usar essa propriedade para limpar os dados previamente carregados. |Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Informix output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "InformixSink"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
