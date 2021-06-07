---
title: Copiar dados do DB2 com o Azure Data Factory
description: Saiba como copiar dados do DB2 para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: jingwang
ms.openlocfilehash: 642f12386a7695e026eb0c30016acf6f53fc9e95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381113"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Copiar dados do DB2 usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-db2-connector.md)
> * [Versão atual](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados DB2. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector de banco de dados DB2 tem suporte nas seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dados DB2 para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, esse conector de DB2 dá suporte às seguintes plataformas e versões do IBM DB2 com DRDA (Distributed Relational Database Architecture) SQLAM (SQL Access Manager) versão 9, 10 e 11.  Ele utiliza o protocolo DDM/DRDA.

* IBM DB2 para z/OS 12.1
* IBM DB2 para z/OS 11.1
* IBM DB2 para z/OS 10.1
* IBM DB2 para i 7.3
* IBM DB2 para i 7.2
* IBM DB2 para i 7.1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10.5
* IBM DB2 para LUW 10.1

>[!TIP]
>O conector de DB2 é criado com base no Provedor Microsoft OLE DB para DB2. Para solucionar problemas de erros do conector de DB2, confira [Códigos de erro do Provedor de Dados](/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Integration Runtime fornece um driver do DB2 interno, portanto, não será necessário instalar manualmente nenhum driver ao copiar dados do DB2.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do DB2.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do DB2:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Db2** | Sim |
| connectionString | Especifique as informações necessárias para se conectar à instância do DB2.<br/> Você também pode colocar uma senha no Azure Key Vault e extrair a configuração `password` da cadeia de conexão. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos](#prerequisites). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

Propriedades típicas dentro da cadeia de conexão:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| Servidor |Nome do servidor DB2. Você pode especificar o número da porta após o nome do servidor delimitado por dois pontos, por exemplo, `server:port`.<br>O conector de DB2 utiliza o protocolo DDM/DRDA e, por padrão, usa a porta 50000 caso não seja especificada. A porta que seu banco de dados DB2 específico usa pode ser diferente com base na versão e nas configurações, por exemplo, para DB2 LUW, a porta padrão é 50000, para AS400, a porta padrão é 446 ou 448 quando o TLS está habilitado. Confira os seguintes documentos do DB2 sobre como a porta é configurada normalmente: [DB2 z/OS](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.qb.dbconn.doc/doc/t0008229.html), [DB2 iSeries](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/ddp/rbal1ports.htm) e [DB2 LUW](https://www.ibm.com/support/knowledgecenter/en/SSEKCU_1.1.3.0/com.ibm.psc.doc/install/psc_t_install_typical_db2_port.html). |Sim |
| Banco de Dados |Nome do banco de dados DB2. |Sim |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados DB2.<br/>O valor permitido é: **Básica**. |Sim |
| Nome de Usuário |Especifica o nome de usuário para se conectar ao banco de dados DB2. |Sim |
| password |Especifique a senha da conta de usuário que você especificou para o nome de usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| packageCollection    | Especifique onde os pacotes necessários são criados automaticamente pelo ADF ao consultar o banco de dados. Se isso não estiver definido, o Data Factory usará o {nome de usuário} como valor padrão. | Não |
| certificateCommonName | Ao usar a criptografia do Protocolo SSL ou do Protocolo TLS, insira um valor para o nome comum do Certificado. | Não |

> [!TIP]
> Caso você receba uma mensagem de erro que declara `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`, o motivo é que um pacote necessário não foi criado para o usuário. Por padrão, o ADF tentará criar um pacote na coleção que tem o nome de usuário que você usou para se conectar ao DB2. Especifique a propriedade de coleção de pacote para indicar onde você deseja que o ADF crie os pacotes necessários ao consultar o banco de dados.

**Exemplo:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;password=<password>;packageCollection=<packagecollection>;certificateCommonName=<certname>;"
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
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;packageCollection=<packagecollection>;certificateCommonName=<certname>;",
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

Se você estava usando o serviço vinculado do DB2 com a carga a seguir, ainda terá suporte como está, mas é recomendável usar o novo de agora em diante.

**Carga anterior:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do DB2.

Para copiar dados do DB2, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **Db2Table** | Sim |
| esquema | Nome do esquema. |Não (se "query" na fonte da atividade for especificada)  |
| tabela | Nome da tabela. |Não (se "query" na fonte da atividade for especificada)  |
| tableName | Nome da tabela com esquema. Essa propriedade é compatível com versões anteriores. Use `schema` e `table` para uma nova carga de trabalho. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se você estava usando o conjunto de dados com tipos `RelationalTable`, ele ainda tem suporte como está, mas é recomendável usar o novo de agora em diante.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte DB2.

### <a name="db2-as-source"></a>DB2 como fonte

Para copiar dados do DB2, as seguintes propriedades têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **Db2Source** | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se você estava usando a fonte com tipos `RelationalSource`, ela ainda tem suporte como está, mas é recomendável usar a nova no futuro.

## <a name="data-type-mapping-for-db2"></a>Mapeamento de tipo de dados para o DB2

Ao copiar dados do DB2, os seguintes mapeamentos são usados de tipos de dados do DB2 para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo do Banco de Dados DB2 | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Binário |Byte[] |
| Blob |Byte[] |
| Char |String |
| Clob |String |
| Data |Datetime |
| DB2DynArray |String |
| DbClob |String |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |String |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |String |
| LongVarGraphic |String |
| Numérico |Decimal |
| Real |Single |
| SmallInt |Int16 |
| Hora |TimeSpan |
| Timestamp |Datetime |
| VarBinary |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).