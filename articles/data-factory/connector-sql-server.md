---
title: Copiar dados para e do SQL Server usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados para e do banco de dados do SQL Server que é local ou em uma VM do Azure usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: a6767c7c8931898c44fd748dbe4299b8ed23eb9c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443283"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Copiar dados para e do SQL Server usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-sqlserver-connector.md)
> * [Versão atual](connector-sql-server.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para um banco de dados do SQL Server. Ele amplia o [visão geral da atividade de cópia](copy-activity-overview.md) artigo que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de e para um banco de dados do SQL Server para qualquer armazenamento de dados de coletor com suporte. Ou, você pode copiar dados de qualquer armazenamento de dados de origem com suporte para um banco de dados do SQL Server. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SQL Server dá suporte a:

- Versões do SQL Server 2016, 2014, 2012, 2008 R2, 2008 e 2005.
- Copiar dados usando a autenticação do SQL ou do Windows.
- Como uma fonte, recuperando dados usando uma consulta SQL ou um procedimento armazenado.
- Como o coletor, acrescentar dados a uma tabela de destino ou à invocação de um procedimento armazenado com lógica personalizada durante a cópia.

>[!NOTE]
>SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) não é suportado por este conector agora. Solução alternativa, você pode usar um [conector ODBC genérico](connector-odbc.md) e um driver ODBC do SQL Server. Siga [neste guia](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) com configurações de cadeia de caracteres ODBC driver download e a conexão.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a cópia de dados de um banco de dados do SQL Server que não esteja acessível publicamente, você precisa configurar um integration runtime auto-hospedado. Para obter mais informações, confira [Tempo de execução da integração auto-hospedada](create-self-hosted-integration-runtime.md). O integration runtime fornece um driver de banco de dados interno do SQL Server. Você não precisa instalar manualmente nenhum driver ao copiar dados de ou para o banco de dados do SQL Server.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do banco de dados do SQL Server.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do SQL Server:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **SqlServer**. | Sim |
| connectionString |Especificar **connectionString** informações que é necessário para se conectar ao banco de dados do SQL Server usando a autenticação do SQL ou a autenticação do Windows. Consulte os exemplos a seguir.<br/>Marcar este campo como **SecureString** para armazená-la com segurança no Azure Data Factory. Você também pode colocar uma senha no Azure Key Vault. Se for autenticação do SQL, extrair o `password` configuração fora de cadeia de caracteres de conexão. Para obter mais informações, consulte o exemplo JSON, a tabela a seguir e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| userName |Se você usar a autenticação do Windows, especifique um nome de usuário. Um exemplo é **nome_do_domínio\\nome_de_usuário**. |Não |
| password |Especifique uma senha para a conta de usuário que você especificou para o nome de usuário. Marcar este campo como **SecureString** para armazená-la com segurança no Azure Data Factory. Ou, você pode [fazer referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Não |
| connectVia | Esse [Integration Runtime](concepts-integration-runtime.md) é usado para se conectar ao armazenamento de dados. Se o armazenamento de dados está acessível publicamente, você pode usar um tempo de execução de integração auto-hospedado ou o tempo de execução de integração do Azure. Se não especificado, o tempo de execução de integração do Azure padrão será usado. |Não |

>[!TIP]
>Se você atingir um erro com o código de erro "UserErrorFailedToConnectToSqlServer" e uma mensagem como "o limite de sessão para o banco de dados é XXX e foi atingido", adicionar `Pooling=false` à sua cadeia de caracteres de conexão e tente novamente.

**Exemplo 1: Usar autenticação do SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: Usar autenticação do SQL com uma senha no Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 3: Usar autenticação do Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do SQL Server.

Para copiar dados de e para um banco de dados do SQL Server, há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como **SqlServerTable**. | Sim |
| tableName |Essa propriedade é o nome da tabela ou exibição em que o serviço vinculado refere-se a instância de banco de dados do SQL Server. | Não para fonte, Sim para o coletor |

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para uso para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do SQL Server e do coletor.

### <a name="sql-server-as-a-source"></a>SQL Server como uma fonte

Para copiar dados do SQL Server, defina o tipo de origem na atividade de cópia como **SqlSource**. As propriedades a seguir têm suporte na seção de origem da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da origem da atividade de cópia deve ser definida como **SqlSource**. | Sim |
| sqlReaderQuery |Utiliza a consulta SQL personalizada para ler os dados. Um exemplo é `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Essa propriedade é o nome do procedimento armazenado que lê dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Esses parâmetros são para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e o uso de maiusculas e minúsculas dos parâmetros devem corresponder a nomes e o uso de maiusculas e minúsculas dos parâmetros de procedimento armazenado. |Não |

**Pontos a serem observados:**

- Se **sqlReaderQuery** é especificado para **SqlSource**, a atividade de cópia executará essa consulta na fonte do SQL Server para obter os dados. Você também pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** se o procedimento armazenado usa parâmetros.
- Se você não especificar **sqlReaderQuery** ou **sqlReaderStoredProcedureName**, as colunas definidas na seção "structure" do conjunto de dados JSON são usadas para construir uma consulta. A consulta `select column1, column2 from mytable` executa o SQL Server. Se a definição de conjunto de dados não tiver "structure", todas as colunas serão selecionadas da tabela.

**Exemplo: Use a consulta SQL**

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

**Exemplo: Usar um procedimento armazenado**

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
> Saiba mais sobre os comportamentos de gravação com suporte, configurações e as práticas recomendadas da [práticas recomendadas para carregar dados no SQL Server](#best-practice-for-loading-data-into-sql-server).

Para copiar dados para o SQL Server, defina o tipo de coletor na atividade de cópia como **SqlSink**. Há suporte para as seguintes propriedades na seção do coletor da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do coletor de atividade de cópia deve ser definida como **SqlSink**. | Sim |
| writeBatchSize |Número de linhas a serem inseridas na tabela SQL *por lote*.<br/>Os valores permitidos são inteiros para o número de linhas. Por padrão, o Azure Data Factory determina dinamicamente o tamanho de lote apropriado com base no tamanho da linha. |Não |
| writeBatchTimeout |Essa propriedade especifica o tempo de espera para a operação de inserção em lotes a ser concluída antes de atingir o tempo limite.<br/>Valores permitidos são para o período de tempo. Um exemplo é "00: 30:00" por 30 minutos. |Não |
| preCopyScript |Esta propriedade especifica uma consulta SQL para a atividade de cópia executar antes de gravar dados no SQL Server. É chamado apenas uma vez por execução de cópia. Você pode usar essa propriedade para limpar os dados previamente carregados. |Não |
| sqlWriterStoredProcedureName |Esse nome é para o procedimento armazenado que define como aplicar dados de origem na tabela de destino.<br/>Este procedimento armazenado é *chamado por lote*. Para fazer uma operação executada apenas uma vez e que não tenha relação alguma com os dados de origem, por exemplo, excluir ou truncar, use a propriedade `preCopyScript`. |Não |
| storedProcedureParameters |Esses parâmetros são usados para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e o uso de maiúsculas e minúsculas dos parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |
| sqlWriterTableType |Essa propriedade especifica um nome de tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados que estão sendo copiados com os dados existentes. |Não |

**Exemplo 1: Acrescentar dados**

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
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exemplo 2: Invocar um procedimento armazenado durante a cópia**

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
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>Prática recomendada para carregar dados no SQL Server

Quando você copia dados para o SQL Server, você pode exigir comportamento diferente de gravação:

- [Acrescentar](#append-data): Minha fonte de dados tem somente novos registros.
- [Upsert](#upsert-data): Minha fonte de dados tem inserções e atualizações.
- [Substituir](#overwrite-the-entire-table): Eu quero recarregue a tabela de dimensões todo cada vez.
- [Escrever com lógica personalizada](#write-data-with-custom-logic): Preciso de processamento extra antes da inserção final na tabela de destino.

Consulte as respectivas seções para saber como configurar no Azure Data Factory e as práticas recomendadas.

### <a name="append-data"></a>Acrescentar dados

Acrescentando dados é o comportamento padrão desse conector do coletor do SQL Server. O Azure Data Factory faz uma inserção em massa para gravar em sua tabela com eficiência. Você pode configurar a fonte e coletor adequadamente na atividade de cópia.

### <a name="upsert-data"></a>Upsert data

**Opção 1:** Quando você tem uma grande quantidade de dados para copiar, usar a abordagem a seguir para fazer um upsert: 

- Primeiro, use uma [tabela temporária](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) para carregamento em massa todos os registros usando a atividade de cópia. Porque as operações em relação a tabelas temporárias não estiverem conectadas, você pode carregar milhões de registros em segundos.
- Executar uma atividade de procedimento armazenado no Azure Data Factory para aplicar uma [mesclar](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou instrução de inserção/atualização. Use a tabela temporária como uma fonte para executar todas as atualiza ou insere como uma única transação. Dessa forma, o número de viagens de ida e volta e operações de log é reduzido. No final da atividade de procedimento armazenado, a tabela temporária pode ser truncada para estar pronto para o próximo ciclo de upsert.

Por exemplo, no Azure Data Factory, você pode criar um pipeline com uma **atividade de cópia** encadeado com um **atividade de procedimento armazenado**. O primeiro copia dados de seu armazenamento de origem em uma tabela temporária do banco de dados, por exemplo, **##UpsertTempTable**, como o nome da tabela no conjunto de dados. Em seguida, o último invoca um procedimento armazenado para mesclar a fonte de dados da tabela temp na tabela de destino e limpar a tabela temporária.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

No banco de dados, defina um procedimento armazenado com lógica de mesclagem, como o exemplo a seguir, que é apontado da atividade de procedimento armazenado anterior. Suponha que o destino é o **Marketing** tabela com três colunas: **ProfileID**, **State** e **Category**. Fazer o upsert com base nas **ProfileID** coluna.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Opção 2:** Você também pode optar por [invocar um procedimento armazenado dentro da atividade de cópia](#invoke-a-stored-procedure-from-a-sql-sink). Essa abordagem é executado a cada linha na tabela de origem em vez de usar inserção em massa como a abordagem padrão na atividade de cópia, não é adequada para upsert em larga escala.

### <a name="overwrite-the-entire-table"></a>Substituir a tabela inteira

Você pode configurar o **preCopyScript** propriedade em um coletor de atividade de cópia. Nesse caso, para cada atividade de cópia que é executado, Azure Data Factory executa o script pela primeira vez. Em seguida, ele executa a cópia para inserir os dados. Por exemplo, para substituir a tabela inteira com os dados mais recentes, especifica um script para primeiro excluir todos os registros antes de você em massa carregar os novos dados da origem.

### <a name="write-data-with-custom-logic"></a>Gravar dados com lógica personalizada

As etapas para gravar dados com lógica personalizada são semelhantes aos descritos na [Upsert dados](#upsert-data) seção. Quando você precisar aplicar extra de processamento antes da inserção final dos dados de origem na tabela de destino, de grande escala, você pode fazer uma das duas coisas: 

- Carregue em uma tabela temporária e, em seguida, invocar um procedimento armazenado. 
- Invocar um procedimento armazenado durante a cópia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocar um procedimento armazenado de um coletor SQL

Quando você copia dados em um banco de dados do SQL Server, você também pode configurar e invocar um procedimento armazenado especificado pelo usuário com parâmetros adicionais.

> [!TIP]
> Invocar um procedimento armazenado processa os dados linha por linha, em vez de por meio de uma operação em massa, que não é recomendável para cópia em larga escala. Saiba mais sobre [práticas recomendadas para carregar dados no SQL Server](#best-practice-for-loading-data-into-sql-server).

Você pode usar um procedimento armazenado quando os mecanismos internos de cópia não atendem à finalidade. Um exemplo é quando você deseja aplicar o processamento extra antes da inserção final dos dados de origem na tabela de destino. Alguns exemplos de processamento extra são quando você deseja mesclar colunas, pesquisar valores adicionais e inserir dados em mais de uma tabela.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert em uma tabela no banco de dados do SQL Server. Suponha que os dados de entrada e o coletor **Marketing** cada tabela tem três colunas: **ProfileID**, **State** e **Category**. Faça o upsert com base na coluna **ProfileID** e aplique-o apenas a uma categoria específica.

**Conjunto de dados de saída:** O "tableName" é o mesmo nome de parâmetro de tipo de tabela em seu procedimento armazenado, conforme mostrado no script de procedimento armazenado a seguir:

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
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Definir as **coletor SQL** seção na atividade de cópia da seguinte maneira:

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

No banco de dados, defina o procedimento armazenado com o mesmo nome que **SqlWriterStoredProcedureName**. Ele manipula os dados de entrada da sua origem especificada e mescla na tabela de saída. O nome do parâmetro do tipo de tabela no procedimento armazenado é o mesmo que **tableName** definidos no conjunto de dados.

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

No banco de dados, defina o tipo de tabela com o mesmo nome que **sqlWriterTableType**. O esquema do tipo de tabela é o mesmo que o esquema retornado pelos dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

O recurso de procedimento armazenado se beneficia de [parâmetros com valores de tabela](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Mapeamento de tipo de dados para o SQL Server

Quando você copia dados de e para o SQL Server, os seguintes mapeamentos são usados de tipos de dados do SQL Server para tipos de dados provisórios do Azure Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do SQL Server | Tipo de dados provisório do Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Para tipos de dados que mapeiam para o tipo provisório Decimal, no momento, o Azure Data Factory dá suporte à precisão de até 28. Se você tiver dados que exijam precisão maior que 28, considere converter para uma cadeia de caracteres em uma consulta SQL.

## <a name="troubleshoot-connection-issues"></a>Solucionar problemas de conexão

1. Configure sua instância do SQL Server para aceitar conexões remotas. Inicie **SQL Server Management Studio**, clique com botão direito **server**e selecione **propriedades**. Selecione **conexões** na lista e selecione o **permitir conexões remotas com este servidor** caixa de seleção.

    ![Habilitar conexões remotas](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Para obter etapas detalhadas, consulte [configurar a opção de configuração do servidor de acesso remoto](https://msdn.microsoft.com/library/ms191464.aspx).

2. Inicie **SQL Server Configuration Manager**. Expanda **Configuração de Rede do SQL Server** para a instância desejada e selecione **Protocolos para MSSQLSERVER**. Protocolos serão exibidos no painel direito. Habilitar TCP/IP clicando **TCP/IP** e selecionando **habilitar**.

    ![Habilitar TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Para obter mais informações e formas alternativas de habilitar o protocolo TCP/IP, consulte [habilitar ou desabilitar um protocolo de rede de servidor](https://msdn.microsoft.com/library/ms191294.aspx).

3. Na mesma janela, clique duas vezes **TCP/IP** para iniciar o **propriedades de TCP/IP** janela.
4. Alterne para a guia **Endereços IP** . Role para baixo para ver a **IPAll** seção. Anote o **a porta TCP**. O padrão é **1433**.
5. Crie uma **regra para o Firewall do Windows** no computador para permitir a entrada de tráfego por essa porta. 
6. **Verificar a conexão**: Para se conectar ao SQL Server usando um nome totalmente qualificado, use o SQL Server Management Studio de um computador diferente. Um exemplo é `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, confira [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
