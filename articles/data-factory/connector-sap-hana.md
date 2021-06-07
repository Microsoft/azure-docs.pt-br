---
title: Copiar dados de SAP HANA
description: Saiba como copiar dados do SAP HANA para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/22/2020
ms.openlocfilehash: ce3c1e22dd030c0730bf4d9859591c00860908a7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382269"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copiar dados do SAP HANA usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-sap-hana-connector.md)
> * [Versão atual](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados SAP HANA. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para aprender o suporte geral do ADF no cenário de integração de dados do SAP, consulte [integração de dados SAP usando Azure data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada sobre cada conector SAP, análise e diretrizes.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector de SAP HANA tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dados SAP HANA para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte, que funcionam como fontes/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SAP HANA dá suporte à:

- Cópia de dados de qualquer versão do banco de dados SAP HANA.
- Copiar dados de **modelos de informações do Hana** (como exibições analíticas e de cálculo) e **tabelas de linha/coluna**.
- Cópia de dados usando a autenticação **Básica** ou do **Windows**.
- Cópia paralela de uma fonte de SAP HANA. Consulte a seção [cópia paralela de SAP Hana](#parallel-copy-from-sap-hana) para obter detalhes.

> [!TIP]
> Para copiar dados **no** armazenamento de dados do SAP HANA, use o conector ODBC genérico. Consulte a seção [SAP Hana Sink](#sap-hana-sink) com detalhes. Observe que os serviços vinculados para o conector do SAP HANA e o conector ODBC são de tipos diferentes, portanto, não podem ser reutilizados.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector do SAP HANA, você precisa:

- Configurar um Integration Runtime auto-hospedado. Confira o artigo de [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.
- Instalar o driver ODBC do SAP HANA no computador do Integration Runtime. Baixe o driver ODBC do SAP HANA do [Centro de Download de Software SAP](https://support.sap.com/swdc). Pesquisa com a palavra-chave **CLIENTE SAP HANA para Windows**.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do SAP HANA.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do SAP HANA:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **SapHana** | Sim |
| connectionString | Especifique as informações necessárias para se conectar ao SAP HANA usando a **autenticação básica** ou a **autenticação do Windows**. Consulte os exemplos a seguir.<br>Na cadeia de conexão, servidor/porta é obrigatório (a porta padrão é 30015) e o nome de usuário e a senha são obrigatórios ao usar a autenticação básica. Para obter configurações avançadas adicionais, consulte [SAP Hana Propriedades de conexão ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Você também pode colocar a senha em Azure Key Vault e extrair a configuração de senha da cadeia de conexão. Consulte [armazenar credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| userName | Especifique o nome de usuário ao usar a autenticação do Windows. Exemplo: `user@domain.com` | Não |
| password | Especifique a senha para a conta de usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |Sim |

**Exemplo: usar autenticação básica**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: usar a autenticação do Windows**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
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

Se você estiver usando SAP HANA serviço vinculado com a seguinte carga, ele ainda terá suporte como está, enquanto você é sugerido para usar o novo no futuro.

**Exemplo:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do SAP HANA.

Para copiar dados de SAP HANA, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **SapHanaTable** | Sim |
| esquema | Nome do esquema no banco de dados de SAP HANA. | Não (se "query" na fonte da atividade for especificada) |
| tabela | Nome da tabela no banco de dados SAP HANA. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se você estava usando o conjunto de dados com tipos `RelationalTable`, ele ainda tem suporte como está, mas é recomendável usar o novo de agora em diante.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA como fonte

>[!TIP]
>Para ingerir dados de SAP HANA com eficiência usando o particionamento de dados, saiba mais na seção [cópia paralela do SAP Hana](#parallel-copy-from-sap-hana) .

Para copiar dados de SAP HANA, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **SapHanaSource** | Sim |
| Consulta | Especifica a consulta SQL para ler dados da instância do SAP HANA. | Sim |
| partitionOptions | Especifica as opções de particionamento de dados usadas para ingerir dados de SAP HANA. Saiba mais na seção  [cópia paralela de SAP Hana](#parallel-copy-from-sap-hana) .<br>Permitir valores são: **nenhum**   (padrão), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Saiba mais na seção  [cópia paralela de SAP Hana](#parallel-copy-from-sap-hana) . `PhysicalPartitionsOfTable` Só pode ser usado ao copiar dados de uma tabela, mas não de uma consulta. <br>Quando uma opção de partição é habilitada (ou seja, não `None` ), o grau de paralelismo para carregar dados simultaneamente de SAP Hana é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) configuração na atividade de cópia. | Falso |
| partitionSettings | Especifique o grupo de configurações para o particionamento de dados.<br>Aplicar quando a opção de partição for `SapHanaDynamicRange`. | Falso |
| partitionColumnName | Especifique o nome da coluna de origem que será usada pela partição para cópia paralela. Se não for especificado, o índice ou a chave primária da tabela será detectado automaticamente e usado como a coluna de partição.<br>Aplicar quando a opção de partição for  `SapHanaDynamicRange` . Se você usar uma consulta para recuperar os dados de origem, conecte-se à  `?AdfHanaDynamicRangePartitionCondition` cláusula WHERE. Consulte o exemplo em [cópia paralela da seção SAP Hana](#parallel-copy-from-sap-hana) . | Sim ao usar a `SapHanaDynamicRange` partição. |
| packetSize | Especifica o tamanho do pacote de rede (em kilobytes) para dividir os dados em vários blocos. Se você tiver uma grande quantidade de dados a serem copiados, aumentar o tamanho do pacote poderá aumentar a velocidade de leitura de SAP HANA na maioria dos casos. O teste de desempenho é recomendado ao ajustar o tamanho do pacote. | Não.<br>O valor padrão é 2048 (2MB). |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se você estivesse usando `RelationalSource` a fonte de cópia digitada, ainda há suporte para ela, embora seja recomendável usar a nova no futuro.

## <a name="parallel-copy-from-sap-hana"></a>Cópia paralela do SAP HANA

O conector de SAP HANA Data Factory fornece particionamento de dados interno para copiar dados de SAP HANA em paralelo. Você pode encontrar opções de particionamento de dados na tabela **Origem** da atividade de cópia.

![Captura de tela das opções de partição](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Quando você habilita a cópia particionada, o Data Factory executa consultas paralelas em sua fonte de SAP HANA para recuperar dados por partições. O grau paralelo é controlado pela configuração do [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) na atividade de cópia. Por exemplo, se você definir `parallelCopies` como quatro, data Factory gerar e executar quatro consultas simultaneamente com base na opção de partição especificada e nas configurações, e cada consulta recuperará uma parte dos dados do seu SAP Hana.

É recomendável habilitar a cópia paralela com o particionamento de dados especialmente quando você ingerir grandes quantidades de dados de seu SAP HANA. Veja a seguir as configurações sugeridas para cenários diferentes. Ao copiar dados para o armazenamento de dados baseado em arquivo, é recomendável gravar em uma pasta como vários arquivos (apenas especifique o nome da pasta). nesse caso, o desempenho é melhor do que gravar em um único arquivo.

| Cenário                                           | Configurações sugeridas                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Carga completa de uma tabela grande.                        | **Opção de partição**: partições físicas da tabela. <br><br/>Durante a execução, Data Factory detecta automaticamente o tipo de partição física da tabela de SAP HANA especificada e escolhe a estratégia de partição correspondente:<br>- **Particionamento de intervalo**: obter a coluna de partição e os intervalos de partição definidos para a tabela e, em seguida, copiar os dados por intervalo. <br>- **Particionamento de hash**: Use a chave de partição de hash como coluna de partição, depois particione e copie os dados com base em intervalos calculados do ADF. <br>- **Particionamento Round Robin** ou **nenhuma partição**: Use a chave primária como coluna de partição, depois particione e copie os dados com base em intervalos calculados do ADF. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada. | **Opção de partição**: partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna usada para aplicar a partição de intervalo dinâmico. <br><br>Durante a execução, Data Factory calcula primeiro os intervalos de valor da coluna de partição especificada, distribui uniformemente as linhas em um número de buckets de acordo com o número de valores de coluna de partição distintos e a configuração de cópia paralela do ADF, em seguida, substitui `?AdfHanaDynamicRangePartitionCondition` pela filtragem do intervalo de valores da coluna de partição para cada partição e envia ao SAP Hana.<br><br>Se você quiser usar várias colunas como coluna de partição, poderá concatenar os valores de cada coluna como uma coluna na consulta e especificá-la como coluna de partição no ADF, como `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition` . |

**Exemplo: consultar com partições físicas de uma tabela**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exemplo: consulta com a partição do intervalo dinâmico**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Mapeamento de tipo de dados para SAP HANA

Ao copiar dados do SAP HANA, os seguintes mapeamentos são usados de tipos de dados do SAP HANA para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados do SAP HANA | Tipo de dados provisório do Data Factory |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| bigint             | Int64                          |
| BINARY             | Byte[]                         |
| Bintext            | String                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | String                         |
| DATE               | DateTime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| real               | Único                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | String                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| Ponto de extremidade        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| timestamp          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="sap-hana-sink"></a>Coletor do SAP HANA

Atualmente, o conector de SAP HANA não tem suporte como coletor, enquanto você pode usar o conector ODBC genérico com SAP HANA driver para gravar dados em SAP HANA. 

Siga os [pré-requisitos](#prerequisites) para configurar o Integration Runtime auto-hospedado e instale o driver SAP Hana ODBC primeiro. Crie um serviço vinculado de ODBC para se conectar ao armazenamento de dados de SAP HANA, conforme mostrado no exemplo a seguir, em seguida, crie um conjunto e um coletor de atividade de cópia com o tipo ODBC adequadamente. Saiba mais no artigo [conector ODBC](connector-odbc.md) .

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
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

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
