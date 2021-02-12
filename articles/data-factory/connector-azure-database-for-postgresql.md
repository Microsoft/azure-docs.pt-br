---
title: Copiar e transformar dados no banco de dado do Azure para PostgreSQL
description: Saiba como copiar e transformar dados no banco de dados do Azure para PostgreSQL usando o Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/01/2021
ms.openlocfilehash: 8b1177278583bdb46f17119eb59235e70c58e806
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223078"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-by-using-azure-data-factory"></a>Copiar e transformar dados no banco de dado do Azure para PostgreSQL usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para o banco de dados do Azure para PostgreSQL e usar o fluxo de dados para transformar dados no banco de dado do Azure para PostgreSQL. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

Esse conector é especializado para o [serviço banco de dados do Azure para PostgreSQL](../postgresql/overview.md). Para copiar dados de um banco de dado PostgreSQL genérico localizado localmente ou na nuvem, use o [conector PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Esse conector do banco de dados do Azure para PostgreSQL tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com uma [fonte/matriz de coletor com suporte](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir oferecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas para o conector do banco de dados do Azure para PostgreSQL.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do banco de dados do Azure para PostgreSQL:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **AzurePostgreSql**. | Sim |
| connectionString | Uma cadeia de conexão ODBC para se conectar ao Banco de Dados do Azure para PostgreSQL.<br/>Você também pode colocar uma senha em Azure Key Vault e extrair a `password` configuração da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais em Azure Key Vault](store-credentials-in-key-vault.md) para obter mais detalhes. | Sim |
| connectVia | Essa propriedade representa o [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

Uma cadeia de conexão válida é `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Aqui estão mais propriedades que podem ser definidas de acordo com o seu caso:

| Propriedade | Descrição | Opções | Obrigatório |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| O método que o driver usa para criptografar dados enviados entre o driver e o servidor de banco de dados. Por exemplo, `EncryptionMethod=<0/1/6>;`| 0 (Sem criptografia) **(Padrão)** / 1 (SSL) / 6 (RequestSSL) | Não |
| ValidateServerCertificate (VSC) | Determina se o driver valida o certificado enviado pelo servidor de banco de dados quando a criptografia SSL está habilitada (método de criptografia = 1). Por exemplo, `ValidateServerCertificate=<0/1>;`| 0 (Desabilitado) **(Padrão)** / 1 (Habilitado) | Não |

**Exemplo**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Exemplo**:

**_Armazenar a senha no Azure Key Vault_* _

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte [DataSets in Azure data Factory](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades às quais o banco de dados do Azure para PostgreSQL dá suporte em DataSets.

Para copiar dados do banco de dados do Azure para PostgreSQL, defina a propriedade Type do DataSet como _ * AzurePostgreSqlTable * *. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como **AzurePostgreSqlTable** | Sim |
| tableName | Nome da tabela | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte [pipelines e atividades no Azure data Factory](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte por uma fonte do banco de dados do Azure para PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>O Banco de Dados do Azure para PostgreSQL como fonte

Para copiar dados do Banco de Dados do Azure para PostgreSQL, defina o tipo de fonte na atividade de cópia como **AzurePostgreSqlSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como **AzurePostgreSqlSource** | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `SELECT * FROM mytable` ou `SELECT * FROM "MyTable"`. Observação no PostgreSQL, o nome da entidade é tratado como não diferencia maiúsculas de minúsculas, se não estiver entre aspas. | Não (se a Propriedade TableName no DataSet for especificada) |

**Exemplo**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Banco de dados do Azure para PostgreSQL como coletor

Para copiar dados para o banco de dado do Azure para PostgreSQL, há suporte para as seguintes propriedades na seção **coletor** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do coletor da atividade de cópia deve ser definida como **AzurePostgreSQLSink**. | Sim |
| preCopyScript | Especifique uma consulta SQL para que a atividade de cópia seja executada antes de gravar os dados no Azure Database para PostgreSQL em cada execução. Você pode usar essa propriedade para limpar os dados previamente carregados. | Não |
| writeMethod | O método usado para gravar dados no banco de dado do Azure para PostgreSQL.<br>Os valores permitidos são: **CopyCommand** (visualização, que é mais alto desempenho), **BulkInsert** (padrão). | Não |
| writeBatchSize | O número de linhas carregadas no banco de dados do Azure para PostgreSQL por lote.<br>O valor permitido é um inteiro que representa o número de linhas. | Não (o padrão é 1 milhão) |
| writeBatchTimeout | Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite.<br>Os valores permitidos são cadeias de caracteres TimeSpan. Um exemplo é 00:30:00 (30 minutos). | Não (o padrão é 00:30:00) |

**Exemplo**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

Ao transformar dados no fluxo de dados de mapeamento, você pode ler e gravar em tabelas do banco de dados do Azure para PostgreSQL. Para obter mais informações, confira [transformação de origem](data-flow-source.md) e [transformação do coletor](data-flow-sink.md) nos fluxos de dados de mapeamento. Você pode optar por usar um banco de dados do Azure para PostgreSQL, ou um [DataSet embutido](data-flow-source.md#inline-datasets) como fonte e tipo de coletor.

### <a name="source-transformation"></a>Transformação de origem

A tabela abaixo lista as propriedades com suporte pela origem do banco de dados do Azure para PostgreSQL. Você pode editar essas propriedades na guia **Opções de origem** .

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabela | Se você selecionar tabela como entrada, o fluxo de dados buscará todos os dados da tabela especificada no conjunto. | Não | - |*(somente para conjunto de linhas embutido)*<br>tableName |
| Consulta | Se você selecionar consulta como entrada, especifique uma consulta SQL para buscar dados da origem, o que substitui qualquer tabela que você especificar no DataSet. O uso de consultas é uma ótima maneira de reduzir linhas para teste ou pesquisas.<br><br>Não há suporte para a cláusula **order by** , mas você pode definir uma instrução SELECT FROM completa. Também pode usar funções de tabela definidas pelo usuário. **Select * de udfGetData ()** é um UDF no SQL que retorna uma tabela que você pode usar no fluxo de dados.<br>Exemplo de consulta: `select * from mytable where customerId > 1000 and customerId < 2000` ou `select * from "MyTable"` . Observação no PostgreSQL, o nome da entidade é tratado como não diferencia maiúsculas de minúsculas, se não estiver entre aspas.| Não | String | Consulta |
| Tamanho do lote | Especifique um tamanho de lote para dividir dados grandes em lotes. | Não | Integer | batchSize |
| Nível de Isolamento | Escolha um dos seguintes níveis de isolamento:<br>-Leitura confirmada<br>-Leitura não confirmada (padrão)<br>-Leitura repetida<br>-Serializável<br>-Nenhum (ignorar nível de isolamento) | Não | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NONE</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Exemplo de script de origem do banco de dados do Azure para PostgreSQL

Quando você usa o banco de dados do Azure para PostgreSQL como tipo de origem, o script de fluxo do data associado é:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>Transformação de coletor

A tabela abaixo lista as propriedades com suporte no coletor do banco de dados do Azure para PostgreSQL. Você pode editar essas propriedades na guia **Opções do coletor** .

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Método Update | Especifique quais operações são permitidas no destino do banco de dados. O padrão é permitir apenas inserções.<br>Para atualizar, upsertr ou excluir linhas, uma [transformação ALTER Row](data-flow-alter-row.md) é necessária para marcar linhas para essas ações. | Sim | `true` ou `false` | pode ser excluído <br/>Insertable <br/>atualizável <br/>upsertable |
| Colunas de chaves | Para atualizações, upserts e exclusões, coluna (s) de chave devem ser definidas para determinar qual linha alterar.<br>O nome da coluna que você escolhe como a chave será usado como parte da atualização subsequente, Upsert, Delete. Portanto, você deve escolher uma coluna que exista no mapeamento do coletor. | Não | Array | chaves |
| Ignorar colunas de chave de gravação | Se você não quiser gravar o valor na coluna de chave, selecione "ignorar a gravação de colunas de chave". | Não | `true` ou `false` | skipKeyWrites |
| Ação tabela |Determina se deve-se recriar ou remover todas as linhas da tabela de destino antes da gravação.<br>- **Nenhum**: nenhuma ação será feita para a tabela.<br>- **Recriar**: a tabela será descartada e recriada. Necessário ao criar uma tabela dinamicamente.<br>- **Truncar**: todas as linhas da tabela de destino serão removidas. | Não | `true` ou `false` | recriar<br/>truncate |
| Tamanho do lote | Especifique quantas linhas estão sendo gravadas em cada lote. Tamanhos de lote maiores aprimoram a compactação e a otimização de memória, mas geram risco de exceções de memória insuficiente ao armazenar dados em cache. | Não | Integer | batchSize |
| Scripts SQL anteriores e posteriores | Especifique scripts SQL de várias linhas que serão executados antes (pré-processamento) e após (pós-processamento) os dados são gravados no banco de dado do coletor. | Não | String | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Exemplo de script de coletor do banco de dados do Azure para PostgreSQL

Quando você usa o banco de dados do Azure para PostgreSQL como tipo de coletor, o script de fluxo associado é:

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
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para obter mais informações sobre as propriedades, consulte [atividade de pesquisa em Azure data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
