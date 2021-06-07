---
title: Copiar dados do MySQL usando o Azure Data Factory
description: Saiba mais sobre o conector do MySQL no Azure Data Factory que permite que você copie dados de um banco de dados MySQL para um armazenamento de dados que tem suporte como um coletor.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: jingwang
ms.openlocfilehash: b6a2253a4a124fe5e3725863c799f91714e66cab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375248"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Copiar dados do MySQL usando o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-mysql-connector.md)
> * [Versão atual](connector-mysql.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados MySQL. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!NOTE]
>Para copiar dados de ou para o serviço de [Banco de Dados do Azure para MySQL](../mysql/overview.md), use o [conector especializado do Banco de Dados do Azure para MySQL](connector-azure-database-for-mysql.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector MySQL tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor suportada](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dados MySQL para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector MySQL dá suporte ao MySQL **versão 5.6, 5.7 e 8.0**.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Integration Runtime fornece um driver MySQL interno a partir da versão 3.7, portanto, não é necessário instalar nenhum driver manualmente.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do MySQL.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do MySQL:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **MySql** | Sim |
| connectionString | Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados do Azure para MySQL.<br/> Você também pode colocar uma senha no Azure Key Vault e extrair a configuração `password` da cadeia de conexão. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos](#prerequisites). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

Uma cadeia de conexão válida é `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Mais propriedades que podem ser definidas para seu caso:

| Propriedade | Descrição | Opções | Obrigatório |
|:--- |:--- |:--- |:--- |
| SSLMode | Esta opção especifica se o driver usa criptografia e verificação TLS ao se conectar ao MySQL. Por exemplo,  `SSLMode=<0/1/2/3/4>` .| DESATIVADO (0) / PREFERENCIAL (1) **(padrão)** / NECESSÁRIO (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Não |
| SSLCert | O caminho completo e o nome de um arquivo. pem que contém o certificado SSL usado para provar a identidade do cliente. <br/> Para especificar uma chave privada para criptografar esse certificado antes de enviá-lo para o servidor, use a `SSLKey` propriedade.| | Sim, se estiver usando a verificação de SSL bidirecional. |
| SSLKey | O caminho completo e o nome de um arquivo que contém a chave privada usada para criptografar o certificado do lado do cliente durante a verificação de SSL bidirecional.|  | Sim, se estiver usando a verificação de SSL bidirecional. |
| UseSystemTrustStore | Esta opção especifica se deve usar um certificado de autoridade de certificação do repositório de confiança de sistema ou de um arquivo PEM especificado. Por ex.: `UseSystemTrustStore=<0/1>;`| Ativado (1) / Desativado (0) **(Padrão)** | Não |

**Exemplo:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
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
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Se estava usando o serviço vinculado do MySQL com a seguinte carga útil, ele ainda terá suporte como está, porém, é recomendável usar um novo de agora em diante.

**Carga anterior:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do MySQL.

Para copiar dados do MySQL, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **MySqlTable** | Sim |
| tableName | Nome da tabela no banco de dados MySQL. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "MySqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se você estava usando o conjunto de dados com tipos `RelationalTable`, ele ainda tem suporte como está, mas é recomendável usar o novo de agora em diante.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do MySQL.

### <a name="mysql-as-source"></a>MySQL como fonte

Para a cópia de dados do MySQL, as propriedades a seguir têm suporte na seção de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **MySqlSource** | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "MySqlSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se você estava usando a fonte com tipos `RelationalSource`, ela ainda tem suporte como se encontra, mas é recomendável empregar a nova de agora em diante.

## <a name="data-type-mapping-for-mysql"></a>Mapeamento de tipo de dados para o MySQL

Ao copiar dados do MySQL, os seguintes mapeamentos são usados de tipos de dados do MySQL para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados do MySQL | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int` |


## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividades

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
