---
title: Copiar dados de e para SQL Server
description: Saiba mais sobre como mover dados de e para o banco de SQL Server no local ou em uma VM do Azure usando Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 8187a71e89c364ef5a52d7ad4615ed03e6b24a4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592041"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Copiar dados de e para SQL Server usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-sqlserver-connector.md)
> * [Versão atual](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para um banco de SQL Server. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector de SQL Server tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Você pode copiar dados de um SQL Server banco de dados para qualquer armazenamento de dado de coletor com suporte. Ou então, você pode copiar dados de qualquer armazenamento de dados de origem com suporte para um SQL Server. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SQL Server dá suporte a:

- SQL Server versão 2005 e superior.
- Copiar dados usando a autenticação do SQL ou do Windows.
- Como fonte, recuperar dados usando uma consulta SQL ou um procedimento armazenado. Você também pode optar por copiar paralelamente da origem SQL Server, consulte a seção [cópia paralela do banco de dados SQL](#parallel-copy-from-sql-database) para obter detalhes.
- Como um coletor, criar automaticamente a tabela de destino se não existir com base no esquema de origem; acrescentar dados a uma tabela ou invocar um procedimento armazenado com lógica personalizada durante a cópia. 

Não há suporte para [SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb) .

>[!NOTE]
>O SQL Server [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) não tem suporte neste conector agora. Para solucionar o trabalho, você pode usar um [conector ODBC genérico](connector-odbc.md) e um SQL Server driver ODBC. Siga [este guia](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver) com as configurações de cadeia de conexão e download do driver ODBC.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas para o conector de banco de dados SQL Server.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado SQL Server:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como **SqlServer**. | Sim |
| connectionString |Especifique as informações de **ConnectionString** necessárias para se conectar ao banco de dados do SQL Server usando a autenticação do SQL ou a autenticação do Windows. Consulte os exemplos a seguir.<br/>Você também pode colocar uma senha em Azure Key Vault. Se for a autenticação do SQL, extraia a `password` configuração da cadeia de conexão. Para obter mais informações, consulte o exemplo de JSON após a tabela e [armazenar as credenciais em Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| userName |Especifique um nome de usuário se você usar a autenticação do Windows. Um exemplo é **nome_do_domínio\\nome_de_usuário**. |Não |
| password |Especifique uma senha para a conta de usuário que você especificou para o nome de usuário. Marque este campo como **SecureString** para armazená-lo com segurança em Azure data Factory. Você também pode [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Não |
| connectVia | Esse [Integration Runtime](concepts-integration-runtime.md) é usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos](#prerequisites). Se não especificado, o Azure Integration Runtime padrão será usado. |Não |

>[!TIP]
>Se você encontrar um erro com o código de erro "UserErrorFailedToConnectToSqlServer" e uma mensagem como "o limite de sessão para o banco de dados é XXX e foi atingido", adicione `Pooling=false` à sua cadeia de conexão e tente novamente.

**Exemplo 1: usar a autenticação do SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: usar a autenticação do SQL com uma senha no Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

**Exemplo 3: usar a autenticação do Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de SQL Server DataSet.

Para copiar dados de e para um SQL Server, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como **Sqlservertable**. | Sim |
| esquema | Nome do esquema. |Não para fonte, Sim para o coletor  |
| tabela | Nome da tabela/exibição. |Não para fonte, Sim para o coletor  |
| tableName | Nome da tabela/exibição com esquema. Essa propriedade é compatível com versões anteriores. Para uma nova carga de trabalho, use `schema` e `table`. | Não para fonte, Sim para o coletor |

**Exemplo**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para uso para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte de SQL Server e pelo coletor.

### <a name="sql-server-as-a-source"></a>SQL Server como uma fonte

>[!TIP]
>Para carregar dados de SQL Server com eficiência usando o particionamento de dados, saiba mais em [cópia paralela do banco de dados SQL](#parallel-copy-from-sql-database).

Para copiar dados do SQL Server, defina o tipo de origem na atividade de cópia como **SqlSource**. As propriedades a seguir têm suporte na seção de origem da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como **sqlsource**. | Sim |
| sqlReaderQuery |Utiliza a consulta SQL personalizada para ler os dados. Um exemplo é `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Essa propriedade é o nome do procedimento armazenado que lê dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Esses parâmetros são para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e maiúsculas e minúsculas dos parâmetros devem corresponder aos nomes e maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |
| isolationLevel | Especifica o comportamento de bloqueio de transação para a origem do SQL. Os valores permitidos são: **ReadCommitted**, **READUNCOMMITTED**, **REPEATABLEREAD**, **Serializable**, **snapshot**. Se não for especificado, o nível de isolamento padrão do banco de dados será usado. Veja [este documento](/dotnet/api/system.data.isolationlevel) para obter mais detalhes. | Não |
| partitionOptions | Especifica as opções de particionamento de dados usadas para carregar dados de SQL Server. <br>Os valores permitidos são: **None** (padrão), **PhysicalPartitionsOfTable** e **DynamicRange**.<br>Quando uma opção de partição é habilitada (ou seja, não `None` ), o grau de paralelismo para carregar dados simultaneamente de SQL Server é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) configuração na atividade de cópia. | Não |
| partitionSettings | Especifique o grupo de configurações para o particionamento de dados. <br>Aplicar quando a opção de partição não estiver `None` . | Não |
| ***Em `partitionSettings` :*** | | |
| partitionColumnName | Especifique o nome da coluna de origem **no tipo inteiro ou de data/hora** ( `int` , `smallint` , `bigint` ,,,, `date` `smalldatetime` `datetime` `datetime2` ou `datetimeoffset` ) que será usado pelo particionamento de intervalo para cópia paralela. Se não for especificado, o índice ou a chave primária da tabela será detectado automaticamente e usado como a coluna de partição.<br>Aplicar quando a opção de partição for `DynamicRange`. Se você usar uma consulta para recuperar os dados de origem, conecte-se  `?AdfDynamicRangePartitionCondition ` à cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela do banco de dados SQL](#parallel-copy-from-sql-database) . | Não |
| partitionUpperBound | O valor máximo da coluna de partição para divisão de intervalo de partição. Esse valor é usado para decidir o stride da partição, não para filtrar as linhas na tabela. Todas as linhas da tabela ou do resultado da consulta serão particionadas e copiadas. Se não for especificado, a atividade de cópia detectará o valor automaticamente.  <br>Aplicar quando a opção de partição for `DynamicRange`. Para obter um exemplo, consulte a seção [cópia paralela do banco de dados SQL](#parallel-copy-from-sql-database) . | Não |
| partitionLowerBound | O valor mínimo da coluna de partição para divisão de intervalo de partição. Esse valor é usado para decidir o stride da partição, não para filtrar as linhas na tabela. Todas as linhas da tabela ou do resultado da consulta serão particionadas e copiadas. Se não for especificado, a atividade de cópia detectará o valor automaticamente.<br>Aplicar quando a opção de partição for `DynamicRange`. Para obter um exemplo, consulte a seção [cópia paralela do banco de dados SQL](#parallel-copy-from-sql-database) . | Não |

**Observe os seguintes pontos:**

- Se **sqlReaderQuery** for especificado para **sqlsource**, a atividade de cópia executará essa consulta em relação à fonte de SQL Server para obter os dados. Você também pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** se o procedimento armazenado usa parâmetros.
- Ao usar o procedimento armazenado na origem para recuperar dados, observe que, se o procedimento armazenado for projetado como retornando um esquema diferente quando um valor de parâmetro diferente for passado, você poderá encontrar uma falha ou conferir um resultado inesperado ao importar o esquema da interface do usuário ou ao copiar dados para o SQL Database com a criação automática de tabela.

**Exemplo: usar consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemplo: usar um procedimento armazenado**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**A definição do procedimento armazenado**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL Server como um coletor

> [!TIP]
> Saiba mais sobre os comportamentos de gravação com suporte, as configurações e as práticas recomendadas da [prática recomendada para carregar dados em SQL Server](#best-practice-for-loading-data-into-sql-server).

Para copiar dados para o SQL Server, defina o tipo de coletor na atividade de cópia como **SqlSink**. Há suporte para as seguintes propriedades na seção do coletor da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do coletor da atividade de cópia deve ser definida como **sqlsink**. | Sim |
| preCopyScript |Esta propriedade especifica uma consulta SQL para que a atividade de cópia seja executada antes de gravar dados em SQL Server. É chamado apenas uma vez por execução de cópia. Você pode usar essa propriedade para limpar os dados previamente carregados. |Não |
| tableOption | Especifica se a [tabela do coletor deve ser criada automaticamente](copy-activity-overview.md#auto-create-sink-tables) se não existir com base no esquema de origem. Não há suporte para a criação de tabela automática quando o coletor especifica o procedimento armazenado. Os valores permitidos são `none` (padrão) e `autoCreate`. |Não |
| sqlWriterStoredProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem em uma tabela de destino. <br/>Este procedimento armazenado é *chamado por lote*. Para operações que são executadas apenas uma vez e não têm nada a ver com os dados de origem, por exemplo, excluir ou truncar, use a `preCopyScript` propriedade.<br>Consulte o exemplo de [invocar um procedimento armazenado de um coletor SQL](#invoke-a-stored-procedure-from-a-sql-sink). | Não |
| storedProcedureTableTypeParameterName |O nome do parâmetro do tipo de tabela especificado no procedimento armazenado.  |Não |
| sqlWriterTableType |O nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados que estão sendo copiados com os dados existentes. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Valores permitidos são pares de nome e valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. | Não |
| writeBatchSize |Número de linhas a serem inseridas na tabela SQL *por lote*.<br/>Os valores permitidos são inteiros para o número de linhas. Por padrão, Azure Data Factory determina dinamicamente o tamanho do lote apropriado com base no tamanho da linha. |Não |
| writeBatchTimeout |Essa propriedade especifica o tempo de espera para a operação de inserção em lotes a ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são para o TimeSpan. Um exemplo é "00:30:00" por 30 minutos. Se nenhum valor for especificado, o tempo limite padrão será "02:00:00". |Não |
| maxConcurrentConnections |O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não |

**Exemplo 1: acrescentar dados**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exemplo 2: invocar um procedimento armazenado durante a cópia**

Saiba mais detalhes em [Invocar um procedimento armazenado de um coletor SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>Cópia paralela do banco de dados SQL

O conector de SQL Server na atividade de cópia fornece particionamento de dados interno para copiar dados em paralelo. Você pode encontrar opções de particionamento de dados na guia **origem** da atividade de cópia.

![Captura de tela das opções de partição](./media/connector-sql-server/connector-sql-partition-options.png)

Quando você habilita a cópia particionada, a atividade de cópia executa consultas paralelas em sua fonte de SQL Server para carregar dados por partições. O grau paralelo é controlado pela configuração do [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) na atividade de cópia. Por exemplo, se você definir `parallelCopies` como quatro, data Factory gerar e executar quatro consultas simultaneamente com base na opção de partição especificada e nas configurações, e cada consulta recuperará uma parte dos dados do seu SQL Server.

É recomendável habilitar a cópia paralela com o particionamento de dados, especialmente quando você carrega grandes quantidades de dados de seu SQL Server. Veja a seguir as configurações sugeridas para cenários diferentes. Ao copiar dados para o armazenamento de dados baseado em arquivo, é recomendável gravar em uma pasta como vários arquivos (apenas especifique o nome da pasta). nesse caso, o desempenho é melhor do que gravar em um único arquivo.

| Cenário                                                     | Configurações sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga total de uma tabela grande, com partições físicas.        | **Opção de partição**: partições físicas da tabela. <br><br/>Durante a execução, Data Factory detecta automaticamente as partições físicas e copia os dados por partições. <br><br/>Para verificar se a tabela tem uma partição física ou não, você pode fazer referência a [essa consulta](#sample-query-to-check-physical-partition). |
| Carga total de uma tabela grande, sem partições físicas, enquanto com uma coluna de número inteiro ou de data e hora para o particionamento de dados. | **Opções de partição**: partição de intervalo dinâmico.<br>**Coluna de partição** (opcional): Especifique a coluna usada para particionar dados. Se não for especificado, a coluna índice ou chave primária será usada.<br/>**Limite superior** da partição e **limite inferior da partição** (opcional): especifique se você deseja determinar o stride da partição. Isso não é para filtrar as linhas na tabela, todas as linhas da tabela serão particionadas e copiadas. Se não for especificado, a atividade de cópia detectará automaticamente os valores.<br><br>Por exemplo, se a coluna de partição "ID" tiver valores varia de 1 a 100, e você definir o limite inferior como 20 e o limite superior como 80, com cópia paralela como 4, Data Factory recuperará dados por 4 partições-IDs no intervalo <= 20, [21, 50], [51, 80] e >= 81, respectivamente. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, sem partições físicas, enquanto com uma coluna de número inteiro ou data/hora para o particionamento de dados. | **Opções de partição**: partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Coluna de partição**: especifique a coluna usada para particionar dados.<br>**Limite superior** da partição e **limite inferior da partição** (opcional): especifique se você deseja determinar o stride da partição. Isso não é para filtrar as linhas na tabela, todas as linhas no resultado da consulta serão particionadas e copiadas. Se não for especificado, a atividade de cópia detectará o valor automaticamente.<br><br>Durante a execução, Data Factory substitui `?AdfRangePartitionColumnName` pelo nome real da coluna e os intervalos de valores de cada partição e envia para SQL Server. <br>Por exemplo, se a coluna de partição "ID" tiver valores varia de 1 a 100, e você definir o limite inferior como 20 e o limite superior como 80, com cópia paralela como 4, Data Factory recuperará dados por 4 partições-IDs no intervalo <= 20, [21, 50], [51, 80] e >= 81, respectivamente. <br><br>Aqui estão mais exemplos de consultas para cenários diferentes:<br> 1. consulte a tabela inteira: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. consulta de uma tabela com seleção de coluna e filtros de cláusula WHERE adicionais: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. consultar com subconsultas: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. consulta com partição na subconsulta: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Práticas recomendadas para carregar dados com a opção de partição:

1. Escolha coluna distinta como coluna de partição (como chave primária ou chave exclusiva) para evitar a distorção de dados. 
2. Se a tabela tiver uma partição interna, use a opção de partição "partições físicas da tabela" para obter um melhor desempenho.    
3. Se você usar Azure Integration Runtime para copiar dados, poderá definir "[unidades de integração de dados (DIU)](copy-activity-performance-features.md#data-integration-units)" maiores (>4) para utilizar mais recursos de computação. Verifique os cenários aplicáveis ali.
4. O "[grau de paralelismo de cópia](copy-activity-performance-features.md#parallel-copy)" controla os números de partição, definindo esse número muito grande em algum momento que afeta o desempenho, recomenda definir esse número como (DIU ou número de nós de ir de hospedagem interna) * (2 a 4).

**Exemplo: carregamento completo de uma tabela grande com partições físicas**

```json
"source": {
    "type": "SqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exemplo: consulta com a partição do intervalo dinâmico**

```json
"source": {
    "type": "SqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Exemplo de consulta para verificar a partição física

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Se a tabela tiver uma partição física, você verá "HasPartition" como "Sim", como a seguir.

![Resultado da consulta SQL](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-sql-server"></a>Prática recomendada para carregar dados em SQL Server

Ao copiar dados para o SQL Server, você pode exigir um comportamento de gravação diferente:

- [Append](#append-data): meus dados de origem têm apenas registros novos.
- [Upsert](#upsert-data): meus dados de origem têm inserções e atualizações.
- [Substituir](#overwrite-the-entire-table): Eu quero recarregar toda a tabela de dimensões a cada vez.
- [Gravar com lógica personalizada](#write-data-with-custom-logic): preciso de processamento extra antes da inserção final na tabela de destino.

Consulte as respectivas seções sobre como configurar o em Azure Data Factory e as práticas recomendadas.

### <a name="append-data"></a>Acrescentar dados

Acrescentar dados é o comportamento padrão desse conector do coletor de SQL Server. Azure Data Factory faz uma inserção em massa para gravar em sua tabela com eficiência. Você pode configurar a origem e o coletor de acordo com a atividade de cópia.

### <a name="upsert-data"></a>Upsert data

**Opção 1:** Quando você tem uma grande quantidade de dados a serem copiados, você pode carregar em massa todos os registros em uma tabela de preparo usando a atividade de cópia e, em seguida, executar uma atividade de procedimento armazenado para aplicar uma instrução [Merge](/sql/t-sql/statements/merge-transact-sql) ou Insert/Update em uma única imagem. 

Atualmente, a atividade de cópia não dá suporte nativo ao carregamento de dados em uma tabela temporária de banco de dado. Há uma maneira avançada de configurá-lo com uma combinação de várias atividades, consulte [otimizar cenários de Upsert em massa do banco de dados SQL](https://github.com/scoriani/azuresqlbulkupsert). Abaixo mostra um exemplo de como usar uma tabela permanente como preparo.

Por exemplo, em Azure Data Factory, você pode criar um pipeline com uma **atividade de cópia** encadeada com uma **atividade de procedimento armazenado**. O primeiro copia os dados do seu repositório de origem para uma SQL Server tabela de preparo, por exemplo, **UpsertStagingTable**, como o nome da tabela no conjunto de dados. Em seguida, o último invoca um procedimento armazenado para mesclar dados de origem da tabela de preparo na tabela de destino e limpar a tabela de preparo.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

No banco de dados, defina um procedimento armazenado com lógica de MESCLAgem, como o exemplo a seguir, que é apontado da atividade de procedimento armazenado anterior. Suponha que o destino seja a tabela de **marketing** com três colunas: **ProfileId**, **estado** e **categoria**. Faça o Upsert com base na coluna **ProfileId** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**Opção 2:** Você pode optar por [invocar um procedimento armazenado na atividade de cópia](#invoke-a-stored-procedure-from-a-sql-sink). Essa abordagem executa cada lote (conforme governado pela `writeBatchSize` Propriedade) na tabela de origem em vez de usar BULK INSERT como a abordagem padrão na atividade de cópia.

### <a name="overwrite-the-entire-table"></a>Substituir a tabela inteira

Você pode configurar a propriedade **preCopyScript** em um coletor de atividade de cópia. Nesse caso, para cada atividade de cópia executada, Azure Data Factory executa o script primeiro. Em seguida, ele executa a cópia para inserir os dados. Por exemplo, para substituir a tabela inteira com os dados mais recentes, especifique um script para primeiro excluir todos os registros antes de carregar em massa os novos dados da origem.

### <a name="write-data-with-custom-logic"></a>Gravar dados com lógica personalizada

As etapas para gravar dados com lógica personalizada são semelhantes às descritas na seção de [dados Upsert](#upsert-data) . Quando você precisa aplicar processamento extra antes da inserção final dos dados de origem na tabela de destino, você pode carregar em uma tabela de preparo e, em seguida, invocar a atividade de procedimento armazenado, ou invocar um procedimento armazenado no coletor de atividade de cópia para aplicar dados.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocar um procedimento armazenado de um coletor SQL

Ao copiar dados para o banco de SQL Server, você também pode configurar e invocar um procedimento armazenado especificado pelo usuário com parâmetros adicionais em cada lote da tabela de origem. O recurso de procedimento armazenado aproveita os [parâmetros com valor de tabela](/dotnet/framework/data/adonet/sql/table-valued-parameters).

Você pode usar um procedimento armazenado quando os mecanismos internos de cópia não atendem à finalidade. Um exemplo é quando você deseja aplicar processamento extra antes da inserção final dos dados de origem na tabela de destino. Alguns exemplos de processamento extra são quando você deseja mesclar colunas, Pesquisar valores adicionais e inserir em mais de uma tabela.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert em uma tabela no banco de dados do SQL Server. Suponha que os dados de entrada e a tabela de **marketing** do coletor tenham três colunas: **ProfileId**, **estado** e **categoria**. Faça o Upsert com base na coluna **ProfileId** e aplique-o somente para uma categoria específica chamada "produtoA".

1. No banco de dados, defina o tipo de tabela com o mesmo nome que **sqlWriterTableType**. O esquema do tipo de tabela é o mesmo que o esquema retornado pelos dados de entrada.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. No banco de dados, defina o procedimento armazenado com o mesmo nome que **sqlWriterStoredProcedureName**. Ele manipula os dados de entrada da sua origem especificada e mescla na tabela de saída. O nome do parâmetro do tipo de tabela no procedimento armazenado é o mesmo que **TableName** definido no DataSet.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. Em Azure Data Factory, defina a seção **SQL Sink** na atividade de cópia da seguinte maneira:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Mapeamento de tipo de dados para SQL Server

Quando você copia dados de e para SQL Server, os seguintes mapeamentos são usados de tipos de dados SQL Server para Azure Data Factory tipos de dados provisórios. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do SQL Server | Tipo de dados provisório do Azure Data Factory |
|:--- |:--- |
| BIGINT |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Objeto |
| texto |String, Char[] |
| time |TimeSpan |
|  timestamp |Byte[] |
| TINYINT |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |String |

>[!NOTE]
> Para tipos de dados que são mapeados para o tipo provisório decimal, a atividade de cópia atualmente dá suporte à precisão de até 28. Se você tiver dados que exijam precisão maior que 28, considere converter para uma cadeia de caracteres em uma consulta SQL.

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique [Atividade GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="using-always-encrypted"></a>Como usar o Always Encrypted

Ao copiar dados de/para SQL Server com [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), use o [conector odbc genérico](connector-odbc.md) e SQL Server driver odbc por meio de Integration Runtime hospedados internamente. Este conector de SQL Server não oferece suporte a Always Encrypted agora. 

Mais especificamente:

1. Configure um Integration Runtime auto-hospedado se você não tiver um. Confira o artigo de [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.

2. Baixe o driver ODBC de 64 bits para SQL Server [aqui](/sql/connect/odbc/download-odbc-driver-for-sql-server)e instale na máquina Integration Runtime. Saiba mais sobre como esse driver funciona [usando Always Encrypted com o driver ODBC para SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Crie um serviço vinculado com o tipo ODBC para se conectar ao banco de dados SQL. Para usar a autenticação do SQL, especifique a cadeia de conexão ODBC como abaixo e selecione Autenticação **básica** para definir o nome de usuário e a senha.

    ```
    Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
    ```

4. Crie DataSet e copie a atividade com o tipo ODBC adequadamente. Saiba mais no artigo [conector ODBC](connector-odbc.md) .

## <a name="troubleshoot-connection-issues"></a>Solucionar problemas de conexão

1. Configure sua instância de SQL Server para aceitar conexões remotas. Inicie o **SQL Server Management Studio**, clique com o botão direito do mouse em **servidor** e selecione **Propriedades**. Selecione **conexões** na lista e marque a caixa de seleção **permitir conexões remotas com este servidor** .

    ![Habilitar conexões remotas](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Para obter etapas detalhadas, consulte [Configurar a opção de configuração de servidor de acesso remoto](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option).

2. Iniciar **SQL Server Configuration Manager**. Expanda **Configuração de Rede do SQL Server** para a instância desejada e selecione **Protocolos para MSSQLSERVER**. Os protocolos aparecem no painel direito. Habilite o TCP/IP clicando com o botão direito do mouse em **TCP/IP** e selecionando **habilitar**.

    ![Habilitar TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Para obter mais informações e maneiras alternativas de habilitar o protocolo TCP/IP, consulte [habilitar ou desabilitar um protocolo de rede do servidor](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol).

3. Na mesma janela, clique duas vezes em **TCP/IP** para iniciar a janela **Propriedades de TCP/IP** .
4. Alterne para a guia **endereços IP** . Role para baixo para ver a seção **IPAll** . Anote a **porta TCP**. O padrão é **1433**.
5. Crie uma **regra para o Firewall do Windows** no computador para permitir a entrada de tráfego por essa porta. 
6. **Verificar conexão**: para se conectar ao SQL Server usando um nome totalmente qualificado, use SQL Server Management Studio de um computador diferente. Um exemplo é `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).