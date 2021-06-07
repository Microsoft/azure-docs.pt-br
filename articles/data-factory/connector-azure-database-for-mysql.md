---
title: Copiar e transformar dados no banco de dado do Azure para MySQL
description: Ganhe como copiar e transformar dados no banco de dado do Azure para MySQL usando Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 4d13f6f435a21b467cae1b8e14211a001792787f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012594"
---
# <a name="copy-and-transform-data-in-azure-database-for-mysql-by-using-azure-data-factory"></a>Copiar e transformar dados no banco de dado do Azure para MySQL usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para o banco de dados do Azure para MySQL e usar o fluxo de dados para transformar dados no banco de dado do Azure para MySQL. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

Esse conector é especializado para o [serviço de banco de dados do Azure para MySQL](../mysql/overview.md). Para copiar dados do banco de dado do MySQL genérico localizado localmente ou na nuvem, use o [conector do MySQL](connector-mysql.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Esse conector do banco de dados do Azure para MySQL tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor compatível](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Banco de Dados do Azure para MySQL.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Banco de Dados do Azure para MySQL:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A tipo da propriedade deve ser definida como: **AzureMySql** | Sim |
| connectionString | Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados do Azure para MySQL. <br/> Você também pode colocar uma senha no Azure Key Vault e extrair a configuração `password` da cadeia de conexão. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

Uma cadeia de conexão válida é `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Mais propriedades que podem ser definidas para seu caso:

| Propriedade | Descrição | Opções | Obrigatório |
|:--- |:--- |:--- |:--- |
| SSLMode | Esta opção especifica se o driver usa criptografia e verificação TLS ao se conectar ao MySQL. Por ex.: `SSLMode=<0/1/2/3/4>`| DESATIVADO (0) / PREFERENCIAL (1) **(padrão)** / NECESSÁRIO (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Não |
| UseSystemTrustStore | Esta opção especifica se deve usar um certificado de autoridade de certificação do repositório de confiança de sistema ou de um arquivo PEM especificado. Por ex.: `UseSystemTrustStore=<0/1>;`| Ativado (1) / Desativado (0) **(Padrão)** | Não |

**Exemplo:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
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
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Banco de Dados do Azure para MySQL.

Para copiar dados de/para o Banco de Dados do Azure para MySQL, defina o tipo da propriedade do conjunto de dados como **AzureMySqlTable**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | O tipo da propriedade do conjunto de dados deve ser definida como: **AzureMySqlTable** | Sim |
| tableName | Nome da tabela no banco de dados MySQL. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do banco de dados do Azure para MySQL.

### <a name="azure-database-for-mysql-as-source"></a>Banco de Dados do Azure para MySQL como fonte

Para copiar dados do Azure Database para MySQL, há suporte para as seguintes propriedades na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **AzureMySqlSource** | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "tableName" no conjunto de dados for especificado) |
| queryCommandTimeout | O tempo de espera antes que a solicitação de consulta expire. O padrão é 120 minutos (02:00:00) | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Banco de dados do Azure para MySQL como coletor

Para copiar dados para o banco de dado do Azure para MySQL, há suporte para as seguintes propriedades na seção **coletor** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do coletor da atividade de cópia deve ser definida como: **AzureMySqlSink** | Sim |
| preCopyScript | Especifique uma consulta SQL para que a atividade de cópia seja executada antes de gravar dados no banco de dado do Azure para MySQL em cada execução. Você pode usar essa propriedade para limpar os dados previamente carregados. | Não |
| writeBatchSize | Insere os dados na tabela do Azure Database para MySQL quando o tamanho do buffer atinge writeBatchSize.<br>O valor permitido é um inteiro que representa o número de linhas. | Não (o padrão é 10.000) |
| writeBatchTimeout | Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite.<br>Os valores permitidos são período. Um exemplo é 00:30:00 (30 minutos). | Não (o padrão é 00:00:30) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

Ao transformar dados no fluxo de dados de mapeamento, você pode ler e gravar em tabelas do banco de dados do Azure para MySQL. Para obter mais informações, confira [transformação de origem](data-flow-source.md) e [transformação do coletor](data-flow-sink.md) nos fluxos de dados de mapeamento. Você pode optar por usar um conjunto de dados do Azure para MySQL ou um conjunto de dados [embutido](data-flow-source.md#inline-datasets) como fonte e tipo de coletor.

### <a name="source-transformation"></a>Transformação de origem

A tabela abaixo lista as propriedades com suporte pela origem do banco de dados do Azure para MySQL. Você pode editar essas propriedades na guia **Opções de origem** .

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabela | Se você selecionar tabela como entrada, o fluxo de dados buscará todos os dados da tabela especificada no conjunto. | Não | - |*(somente para conjunto de linhas embutido)*<br>tableName |
| Consulta | Se você selecionar consulta como entrada, especifique uma consulta SQL para buscar dados da origem, o que substitui qualquer tabela que você especificar no DataSet. O uso de consultas é uma ótima maneira de reduzir linhas para teste ou pesquisas.<br><br>Não há suporte para a cláusula **order by** , mas você pode definir uma instrução SELECT FROM completa. Também pode usar funções de tabela definidas pelo usuário. **Select * de udfGetData ()** é um UDF no SQL que retorna uma tabela que você pode usar no fluxo de dados.<br>Exemplo de consulta: `select * from mytable where customerId > 1000 and customerId < 2000` ou `select * from "MyTable"` .| Não | String | Consulta |
| Tamanho do lote | Especifique um tamanho de lote para dividir dados grandes em lotes. | Não | Inteiro | batchSize |
| Nível de Isolamento | Escolha um dos seguintes níveis de isolamento:<br>-Leitura confirmada<br>-Leitura não confirmada (padrão)<br>-Leitura repetida<br>-Serializável<br>-Nenhum (ignorar nível de isolamento) | Não | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NONE</small> |isolationLevel |

#### <a name="azure-database-for-mysql-source-script-example"></a>Exemplo de script de origem do banco de dados do Azure para MySQL

Quando você usa o banco de dados do Azure para MySQL como o tipo de origem, o script de fluxo associado é:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzureMySQLSource
```

### <a name="sink-transformation"></a>Transformação de coletor

A tabela abaixo lista as propriedades com suporte pelo coletor de banco de dados do Azure para MySQL. Você pode editar essas propriedades na guia **Opções do coletor** .

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Método Update | Especifique quais operações são permitidas no destino do banco de dados. O padrão é permitir apenas inserções.<br>Para atualizar, upsertr ou excluir linhas, uma [transformação ALTER Row](data-flow-alter-row.md) é necessária para marcar linhas para essas ações. | Sim | `true` ou `false` | pode ser excluído <br/>Insertable <br/>atualizável <br/>upsertable |
| Colunas de chaves | Para atualizações, upserts e exclusões, coluna (s) de chave devem ser definidas para determinar qual linha alterar.<br>O nome da coluna que você escolhe como a chave será usado como parte da atualização subsequente, Upsert, Delete. Portanto, você deve escolher uma coluna que exista no mapeamento do coletor. | Não | Array | chaves |
| Ignorar colunas de chave de gravação | Se você não quiser gravar o valor na coluna de chave, selecione "ignorar a gravação de colunas de chave". | Não | `true` ou `false` | skipKeyWrites |
| Ação tabela |Determina se deve-se recriar ou remover todas as linhas da tabela de destino antes da gravação.<br>- **Nenhum**: nenhuma ação será feita para a tabela.<br>- **Recriar**: a tabela será descartada e recriada. Necessário ao criar uma tabela dinamicamente.<br>- **Truncar**: todas as linhas da tabela de destino serão removidas. | Não | `true` ou `false` | recriar<br/>truncate |
| Tamanho do lote | Especifique quantas linhas estão sendo gravadas em cada lote. Tamanhos de lote maiores aprimoram a compactação e a otimização de memória, mas geram risco de exceções de memória insuficiente ao armazenar dados em cache. | Não | Inteiro | batchSize |
| Scripts SQL anteriores e posteriores | Especifique scripts SQL de várias linhas que serão executados antes (pré-processamento) e após (pós-processamento) os dados são gravados no banco de dado do coletor. | Não | String | preSQLs<br>postSQLs |

#### <a name="azure-database-for-mysql-sink-script-example"></a>Exemplo de script de coletor do banco de dados do Azure para MySQL

Quando você usa o banco de dados do Azure para MySQL como tipo de coletor, o script de fluxo associado é:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureMySQLSink
```

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Mapeamento do tipo de dados do Banco de Dados do Azure para MySQL

Ao copiar dados do Banco de Dados do Azure para MySQL, os seguintes mapeamentos são usados de tipos de dados do MySQL para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipos de dados de Banco de Dados do Azure para MySQL | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
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
| `year` |`Int32` |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
