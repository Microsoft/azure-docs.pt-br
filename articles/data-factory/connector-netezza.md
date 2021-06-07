---
title: Copiar dados do Netezza usando o Azure Data Factory
description: Saiba como copiar dados do Netezza para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: jingwang
ms.openlocfilehash: 4b12c1f24f389634004de3d487a693bc588a7241
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374347"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Copiar dados do Netezza usando o Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do Netezza. O artigo baseia-se em [Atividade de Cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da Atividade de Cópia.

>[!TIP]
>Para o cenário de migração de dados do Netezza para o Azure, saiba mais em [Usar o Azure Data Factory para migrar dados do servidor do Netezza local para o Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do Netezza tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor compatível](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)


Você pode copiar dados do Netezza para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores, consulte [Armazenamentos de dados e formatos compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

O conector do Netezza dá suporte à cópia paralela da origem. Confira a seção [Cópia paralela do Netezza](#parallel-copy-from-netezza) para obter detalhes.

O Azure Data Factory fornece um driver interno para habilitar a conectividade. Você não precisa instalar manualmente nenhum driver para usar este conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

Você pode criar um pipeline que usa uma atividade de cópia usando o SDK do .NET, o SDK do Python, o Azure PowerShell, a API REST ou um modelo do Azure Resource Manager. Consulte o [tutorial Atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre como criar um pipeline que tenha uma atividade de cópia.

As seções a seguir fornecem detalhes sobre propriedades que você pode usar para definir entidades do Data Factory que são específicas para o conector Netezza.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para o serviço vinculado do Netezza:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | O **tipo** propriedade deve ser definida como **Netezza**. | Sim |
| connectionString | Uma cadeia de conexão ODBC para conectar-se ao Netezza. <br/>Você também pode colocar uma senha no Azure Key Vault e extrair a configuração `pwd` da cadeia de conexão. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos](#prerequisites). Se não especificado, o Azure Integration Runtime padrão será usado. |Não |

Uma cadeia de conexão válida é `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. A tabela a seguir descreve as propriedades mais que você pode definir:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| SecurityLevel | O nível de segurança que o driver usa para a conexão com o armazenamento de dados. O driver dá suporte para conexões SSL com autenticação unidirecional usando SSL versão 3. <br>Exemplo: `SecurityLevel=preferredSecured`. Os valores com suporte são:<br/>- **Apenas inseguro** (**onlyUnSecured**): O driver não usa SSL.<br/>- **Preferencialmente não segura (preferredUnSecured) (padrão)**: se o servidor fornece uma opção, o driver não usa SSL. <br/>- **Seguro preferencial (preferredSecured)**: se o servidor fornecer uma opção, o driver usará SSL. <br/>- **Apenas protegido (onlySecured)**: O driver não se conecta a menos que uma conexão SSL esteja disponível. | Não |
| CaCertFile | O caminho completo para o certificado SSL que é usado pelo servidor. Exemplo: `CaCertFile=<cert path>;`| Sim, se o SSL estiver habilitado |

**Exemplo**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Esta seção fornece uma lista de propriedades que o conjunto de dados do Netezza suporta.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir conjuntos de dados, consulte [Conjuntos de Dados](concepts-datasets-linked-services.md).

Para copiar dados do Netezza, defina as **tipo** propriedade do conjunto de dados como **NetezzaTable**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **NetezzaTable** | Sim |
| esquema | Nome do esquema. |Não (se "query" na fonte da atividade for especificada)  |
| tabela | Nome da tabela. |Não (se "query" na fonte da atividade for especificada)  |
| tableName | Nome da tabela com esquema. Essa propriedade é compatível com versões anteriores. Use `schema` e `table` para uma nova carga de trabalho. | Não (se "query" na fonte da atividade for especificada) |

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

>[!TIP]
>Para carregar dados do Netezza com eficiência usando o particionamento de dados, saiba mais na seção [Cópia paralela do Netezza](#parallel-copy-from-netezza).

Para copiar dados do Netezza, defina as **fonte** tipo de atividade de cópia para **NetezzaSource**. As seguintes propriedades são suportadas na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **tipo** da origem da Atividade de Cópia deve ser configurada para **NetezzaSource**. | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Exemplo: `"SELECT * FROM MyTable"` | Não (se "tableName" no conjunto de dados for especificado) |
| partitionOptions | Especifica as opções de particionamento de dados usadas para carregar dados do Netezza. <br>Os valores permitidos são: **None** (padrão), **DataSlice** e **DynamicRange**.<br>Quando uma opção de partição é habilitada (ou seja, não `None`), o grau de paralelismo para carregar dados simultaneamente de um banco de dados do Netezza é controlado pela configuração [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) na atividade de cópia. | Não |
| partitionSettings | Especifique o grupo de configurações para o particionamento de dados. <br>Aplicar quando a opção de partição não for `None`. | Não |
| partitionColumnName | Especifique o nome da coluna de origem **no tipo de inteiro** que será usado pelo particionamento de intervalo para cópia paralela. Se não especificado, a chave primária da tabela será detectada automaticamente e usada como a coluna de partição. <br>Aplicar quando a opção de partição for `DynamicRange`. Se você usar uma consulta para recuperar os dados de origem, conecte `?AdfRangePartitionColumnName` na cláusula WHERE. Confira o exemplo na seção [Cópia paralela do Netezza](#parallel-copy-from-netezza). | Não |
| partitionUpperBound | O valor máximo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange`. Se você usar uma consulta para recuperar os dados de origem, conecte `?AdfRangePartitionUpbound` na cláusula WHERE. Para obter um exemplo, confira a seção [Cópia paralela do Netezza](#parallel-copy-from-netezza). | Não |
| partitionLowerBound | O valor mínimo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange`. Se você usar uma consulta para recuperar os dados de origem, conecte `?AdfRangePartitionLowbound` na cláusula WHERE. Para obter um exemplo, confira a seção [Cópia paralela do Netezza](#parallel-copy-from-netezza). | Não |

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

## <a name="parallel-copy-from-netezza"></a>Cópia paralela do Netezza

O conector do Netezza Data Factory fornece particionamento de dados interno para copiar dados do Netezza em paralelo. Você pode encontrar opções de particionamento de dados na tabela **Origem** da atividade de cópia.

![Captura de tela das opções de partição](./media/connector-netezza/connector-netezza-partition-options.png)

Quando você habilita a cópia particionada, o Data Factory executa consultas paralelas com relação à sua origem Netezza para carregar dados por partições. O grau paralelo é controlado pela configuração do [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) na atividade de cópia. Por exemplo, ao definir `parallelCopies` como quatro, o Data Factory gera e executa simultaneamente quatro consultas com base na opção de partição especificada e nas configurações e cada consulta recupera uma parte dos dados do banco de dados Netezza.

É recomendável habilitar a cópia paralela com o particionamento de dados, especialmente quando você carrega grandes quantidades de dados do banco de dados Netezza. Veja a seguir as configurações sugeridas para cenários diferentes. Ao copiar dados para o armazenamento de dados baseado em arquivo, recomendamos gravá-los em uma pasta como vários arquivos (apenas especifique o nome da pasta) para obter um desempenho melhor do que gravar em um arquivo.

| Cenário                                                     | Configurações sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa de uma tabela grande.                                   | **Opção de partição**: fatia de dados. <br><br/>Durante a execução, o Data Factory particiona automaticamente os dados com base nas [fatias de dados internas do Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html) e copiam os dados por partições. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada.                 | **Opção de partição**: fatia de dados.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Durante a execução, o Data Factory substitui `?AdfPartitionCount` (com número de cópia paralela definido na atividade de cópia) e `?AdfDataSliceCondition` pela lógica de partição da fatia de dados e envia para o Netezza. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, tendo uma coluna de inteiros com valor distribuído uniformemente para o particionamento de intervalo. | **Opções de partição**: partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna de partição**: especifique a coluna usada para particionar dados. Você pode particionar em relação à coluna com tipo de dados Integer.<br>**Limite superior da partição** e **Limite inferior da partição**: especifique se deseja filtrar a coluna de partição para recuperar dados somente entre os intervalos inferior e superior.<br><br>Durante a execução, o Data Factory substitui `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound` e `?AdfRangePartitionLowbound` pelo nome real da coluna e os intervalos de valores para cada partição e envia para o Netezza. <br>Por exemplo, se a sua coluna de partição "ID" estiver definida com o limite inferior como 1 e o limite superior como 80, com a cópia paralela definida como 4, o Data Factory recuperará dados por 4 partições. Suas IDs estão entre [1, 20], [21, 40], [41, 60] e [61, 80], respectivamente. |

**Exemplo: consulta com a partição da fatia de dados**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Exemplo: consulta com a partição do intervalo dinâmico**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividades

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores no Azure Data Factory, consulte [Armazenamentos e formatos de dados compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).
