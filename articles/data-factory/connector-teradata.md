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
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 63f28c8b6eaceed12e1f76e9c0c5984e3b63b500
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561419"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Copiar dados do Teradata usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
>
> * [Versão 1](v1/data-factory-onprem-teradata-connector.md)
> * [Versão atual](connector-teradata.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados Teradata. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de um banco de dados Teradata para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Teradata dá suporte a:

- Teradata **versão 14.10, 15.0, 15.10, 16.0, 16.10 e 16.20**.
- Cópia de dados usando a autenticação **Básica** ou do **Windows**.
- Cópia paralela de fonte Teradata. Ver [cópia do Teradata paralela](#parallel-copy-from-teradata) seção com detalhes.

> [!NOTE]
>
> O Azure Data Factory atualizado o conector do Teradata desde v3.18 Integration Runtime auto-hospedado, o que for capacitada por um driver ODBC interno e oferece opções flexíveis de conexão, bem como cópia paralela do out-of-box para melhorar o desempenho. Qualquer carga de trabalho existente usando o conector do Teradata anterior capacitados pelo provedor de dados .NET para Teradata ainda tem suporte como-está, enquanto são sugeridas para usar o novo no futuro. Observe que o novo caminho requer um conjunto diferente de origem de cópia/de conjunto de dados de serviço vinculado. Consulte a seção respectiva nos detalhes da configuração.

## <a name="prerequisites"></a>Pré-requisitos

Se seu Teradata não está acessível publicamente, você precisará configurar um Integration Runtime auto-hospedado. Para obter mais informações sobre o integration, runtime consulte [integration runtime auto-hospedado](create-self-hosted-integration-runtime.md). O Integration Runtime fornece um driver interno do Teradata a partir da versão 3.18, portanto você não precisa instalar manualmente nenhum driver. O driver requer "Visual C++ Redistributable 2012 atualização 4" no computador do IR auto-hospedado, baixar do [aqui](https://www.microsoft.com/en-sg/download/details.aspx?id=30679) se você ainda não tiver instalado.

Para obter a versão de IR auto-hospedado menor do que 3.18, você precisará instalar o [.NET Data Provider para Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) versão 14 ou superior no computador do Integration Runtime. 

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao Teradata.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Teradata:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Teradata** | Sim |
| connectionString | Especifica as informações necessárias para se conectar à instância do banco de dados Teradata. Consulte os exemplos a seguir.<br/>Você também pode colocar uma senha no Azure Key Vault e extrair a configuração `password` da cadeia de conexão. Consulte a [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. | Sim |
| username | Especifique o nome de usuário para se conectar ao banco de dados Teradata. Aplica-se ao usar a autenticação do Windows. | Não |
| password | Especifique a senha da conta de usuário que você especificou para o nome de usuário. Você também pode optar por [fazer referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). <br>Aplica-se ao usar a autenticação do Windows ou referenciando a senha no cofre de chaves para a autenticação básica. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |Sim |

**Exemplo: usando a autenticação básica**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: usando a autenticação do Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Se você estivesse usando Teradata serviço vinculado capacitado pelo provedor de dados .NET para Teradata com a seguinte carga, ele ainda tem suporte como-está, enquanto são sugeridas para usar o novo no futuro.

**Carga anterior:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
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

Para copiar dados do Teradata, há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **TeradataTable** | Sim |
| database | Nome do banco de dados Teradata. | Não (se "query" na fonte da atividade for especificada) |
| table | Nome da tabela no banco de dados Teradata. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

> [!NOTE]
>
> Se você estivesse usando o conjunto de dados de tipo "RelationalTable" como o seguinte, ainda é compatível como-está, enquanto são sugeridas para usar o novo no futuro.

**Carga anterior:**

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

> [!TIP]
>
> Saiba mais sobre [cópia do Teradata paralela](#parallel-copy-from-teradata) seção sobre como carregar dados do Teradata usando o particionamento de dados com eficiência.

Para copiar dados do Teradata, as propriedades a seguir têm suporte na atividade de cópia **origem** seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **TeradataSource** | Sim |
| query | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`.<br>Quando você habilita a carga particionada, você precisa associar parâmetros de partição interna correspondente em sua consulta. Veja exemplos na [cópia do Teradata paralela](#parallel-copy-from-teradata) seção. | Não (se a tabela no conjunto de dados for especificada) |
| partitionOptions | Especifica os opções usadas para carregar dados de Teradata de particionamento de dados. <br>Permitir que os valores são: **None** (padrão), **Hash** e **DynamicRange**.<br>Quando a opção de partição está habilitada (não ' None'), também configure **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** definir na atividade de cópia, por exemplo, como 4, que determina o grau de paralelo carreguem dados simultaneamente do Teradata banco de dados. | Não |
| partitionSettings | Especifique o grupo, as configurações de particionamento de dados. <br>Se aplicam quando a opção de partição não é `None`. | Não |
| partitionColumnName | Especifique o nome da coluna de origem **no tipo de inteiro** que será usado por meio do particionamento de intervalo para a cópia paralela. Se não for especificado, a chave primária da tabela será automaticamente detectado e usado como a coluna de partição. <br>Se aplicam quando a opção de partição está `Hash` ou `DynamicRange`. Se você usar a consulta para recuperar dados de origem, de gancho `?AdfHashPartitionCondition` ou `?AdfRangePartitionColumnName` na cláusula WHERE. Consulte o exemplo [cópia do Teradata paralela](#parallel-copy-from-teradata) seção. | Não |
| partitionUpperBound | Valor máximo da coluna de partição para copiar dados. <br>Se aplicam quando a opção de partição é `DynamicRange`. Se você usar a consulta para recuperar dados de origem, de gancho `?AdfRangePartitionUpbound` na cláusula WHERE. Consulte o exemplo [cópia do Teradata paralela](#parallel-copy-from-teradata) seção. | Não |
| PartitionLowerBound | Valor mínimo da coluna de partição para copiar dados. <br>Se aplicam quando a opção de partição é `DynamicRange`. Se você usar a consulta para recuperar dados de origem, de gancho `?AdfRangePartitionLowbound` na cláusula WHERE. Consulte o exemplo [cópia do Teradata paralela](#parallel-copy-from-teradata) seção. | Não |

> [!NOTE]
>
> Se você estivesse usando a origem de cópia do tipo "RelationalSource", ele ainda tem suporte como-está, mas não dá suporte a nova carga paralela internos do Teradata (opções de partição). São sugeridas para usar esse novo no futuro.

**Exemplo: copiar dados usando uma consulta básica sem partição**

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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Cópia paralela do Teradata

Conector do Teradata de fábrica de dados fornece para copiar dados do Teradata em paralelo com o excelente desempenho de particionamento de dados internos. Você pode encontrar as opções de particionamento de dados na atividade de cópia -> fonte Teradata:

![Opções de partição](./media/connector-teradata/connector-teradata-partition-options.png)

Quando você habilita a cópia particionada, o data factory executa consultas em paralelo em relação à sua fonte Teradata para carregar dados por partições. O grau de paralelo é configurado e controlado por meio **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** definindo na atividade de cópia. Por exemplo, se você definir `parallelCopies` como quatro, fábrica de dados simultaneamente gera e executa quatro consultas com base em sua opção de partição especificada e configurações, cada parte de recuperação de dados do banco de dados Teradata.

São sugeridas para habilitar cópia paralela com dados de particionamento, especialmente quando você carregar grandes quantidades de dados do banco de dados Teradata. A seguir estão as configurações sugeridas para cenários diferentes:

| Cenário                                                     | Configurações sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carregamento completo da tabela grande                                   | **Opção de partição**: Hash. <br><br/>Durante a execução, o data Factory automaticamente detecta coluna PK, aplica hash em relação a ela e copia os dados por partições. |
| Carregar grandes quantidades de dados usando a consulta personalizada                 | **Opção de partição**: Hash.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna usada para aplicar hash partição. Se não for especificado, o ADF detectará automaticamente a coluna PK da tabela especificada no conjunto de dados Teradata.<br><br>Durante a execução, a substituição de fábrica de dados `?AdfHashPartitionCondition` com a lógica de partição de hash e enviar para Teradata. |
| Carregar grandes quantidades de dados usando uma consulta personalizada, ter uma coluna de inteiros com valor uniformemente distribuído para o particionamento por intervalos | **Opções de partição**: Partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna usada para particionar dados. Você pode particionar em relação à coluna com tipo de dados inteiro.<br>**Limite superior de partição** e **limite inferior de partição**: Especifique se você deseja filtrar em relação à coluna de partição para recuperar apenas os dados entre o intervalo inferior e superior.<br><br>Durante a execução, data factory replace `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, e `?AdfRangePartitionLowbound` com o nome real da coluna e valor intervalos para cada partição e envie para Teradata. <br>Por exemplo, se sua coluna de partição "ID" definido com o limite inferior como 1 e o limite superior como 80, com o conjunto de cópia paralela como 4, o ADF recuperar dados por 4 partições com a ID entre [1,20], [21, 40], [41, 60] e [61, 80]. |

**Exemplo: consulta com a partição hash**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Exemplo: consulta com a partição de intervalo dinâmico**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
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
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Número inteiro |Int32 |
| Intervalo - dia |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Intervalo - dia para hora |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Intervalo - dia para minuto |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Interval Day To Second |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Intervalo - hora |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Intervalo - hora para minuto |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Interval Hour To Second |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Interval Minute |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Interval Minute To Second |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Interval Month |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Interval Second |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Interval Year |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Interval Year To Month |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Número |Double |
| Período (Date) |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Período) |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Período (com o fuso horário) |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Período (Timestamp) |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| Período (carimbo de hora com fuso horário) |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Hora com fuso horário |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |Cadeia de caracteres |
| VarGraphic |Sem suporte. Aplica a conversão explícita na consulta de origem. |
| xml |Sem suporte. Aplica a conversão explícita na consulta de origem. |


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
