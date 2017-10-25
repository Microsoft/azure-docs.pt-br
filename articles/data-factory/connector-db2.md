---
title: Copiar dados do DB2 usando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados do DB2 para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: aef796aa4f2604e6cd06452f3d3c6f648850aa38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Copiar dados do DB2 usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-onprem-db2-connector.md)
> * [Versão 2 – Versão prévia](connector-db2.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados DB2. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está em GA (disponibilidade geral), consulte [Conector do DB2 na V1](v1/data-factory-onprem-db2-connector.md).

## <a name="supported-scenarios"></a>Cenários com suporte

Você pode copiar dados de um banco de dados DB2 para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector DB2 dá suporte às plataformas e versões do IBM DB2 a seguir com as versões 9, 10 e 11 do SQL Access Manager (SQLAM) da Distributed Relational Database Architecture (DRDA):

* IBM DB2 para z/OS 11.1
* IBM DB2 para z/OS 10.1
* IBM DB2 para i 7.2
* IBM DB2 para i 7.1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10.5
* IBM DB2 para LUW 10.1

> [!TIP]
> Se você receber uma mensagem de erro informando “O pacote correspondente a uma solicitação de execução da instrução SQL não foi encontrado. SQLSTATE=51002 SQLCODE=-805”, o motivo disso é que um pacote necessário não foi criado para o usuário normal nesse sistema operacional. Siga essas instruções, de acordo com o tipo do seu servidor DB2:
> - DB2 para i (AS400): permita que o usuário avançado crie uma coleção para o usuário de logon antes de usar a atividade de cópia. Comando: `create collection <username>`
> - DB2 para z/OS ou LUW: use uma conta com privilégios elevados – usuário avançado ou administrador com autoridades de pacote e permissões BIND, BINDADD, GRANT EXECUTE TO PUBLIC – para executar a atividade de cópia de uma vez. Em seguida, o pacote necessário será criado automaticamente durante a cópia. Posteriormente, você poderá voltar para o usuário normal para a execução de cópias subsequentes.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a cópia de dados de um banco de dados DB2 que não esteja acessível publicamente, você precisará configurar um Integration Runtime auto-hospedado. Para saber mais sobre Tempos de execução de integração auto-hospedados, consulte o artigo [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md). O Integration Runtime fornece um driver do DB2 interno, portanto, não será necessário instalar manualmente nenhum driver ao copiar dados do DB2.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia usando o SDK do .NET, o SDK do Python, o Azure PowerShell, a API REST ou o modelo do Azure Resource Manager. Confira o [Tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do DB2.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do DB2:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Db2** | Sim |
| server |Nome do servidor DB2. |Sim |
| database |Nome do banco de dados DB2. |Sim |
| schema |Nome do esquema no banco de dados. O nome do esquema diferencia maiúsculas de minúsculas. |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados DB2.<br/>O valor permitido é: **Básica**. |Sim |
| Nome de Usuário |Especifica o nome de usuário para se conectar ao banco de dados DB2. |Sim |
| Senha |Especifique a senha da conta de usuário que você especificou para o nome de usuário. Marque esse campo como SecureString. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime auto-hospedado ou o Integration Runtime do Azure (se seu armazenamento de dados estiver publicamente acessível). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

**Exemplo:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do DB2.

Para copiar dados do DB2, defina a propriedade type do conjunto de dados como **RelationalTable**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **RelationalTable** | Sim |
| tableName | Nome da tabela no banco de dados DB2. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte DB2.

### <a name="db2-as-source"></a>DB2 como fonte

Para copiar dados do DB2, defina o tipo de origem na atividade de cópia como **RelationalSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **RelationalSource** | Sim |
| query | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Não (se "tableName" no conjunto de dados for especificado) |

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
                "type": "RelationalSource",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-db2"></a>Mapeamento de tipo de dados para o DB2

Ao copiar dados do DB2, os seguintes mapeamentos são usados de tipos de dados do DB2 para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo do Banco de Dados DB2 | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Binário |Byte[] |
| Blob |Byte[] |
| Char |Cadeia de caracteres |
| Clob |Cadeia de caracteres |
| Data |Datetime |
| DB2DynArray |Cadeia de caracteres |
| DbClob |Cadeia de caracteres |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Duplo |Duplo |
| Float |Duplo |
| Graphic |Cadeia de caracteres |
| Número inteiro |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Cadeia de caracteres |
| LongVarGraphic |Cadeia de caracteres |
| Numérico |Decimal |
| Real |Single |
| SmallInt |Int16 |
| Hora |TimeSpan |
| Timestamp |Datetime |
| VarBinary |Byte[] |
| VarChar |Cadeia de caracteres |
| VarGraphic |Cadeia de caracteres |
| xml |Byte[] |


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).