---
title: Copiar dados para ou do Banco de Dados SQL do Azure usando o Data Factory | Microsoft Docs
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte para o Banco de Dados SQL do Azure ou do Banco de Dados SQL do Azure para armazenamentos de dados de coletor com suporte, usando o Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: jingwang
ms.openlocfilehash: 5dbd739070b1f66fe5ff04f319a3818269d0bdaa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449601"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copiar dados de ou para o Banco de Dados SQL do Azure usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-sql-connector.md)
> * [Versão atual](connector-azure-sql-database.md)

Este artigo descreve como copiar dados para e do banco de dados SQL. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do banco de dados SQL tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [suporte para a matriz de origem/coletor](copy-activity-overview.md) tabela
- [Mapeamento de fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Especificamente, este conector de banco de dados SQL do Azure dá suporte a estas funções:

- Copiar dados usando a autenticação do SQL e autenticação de token de aplicativo do Azure Active Directory (Azure AD) com um identidades de serviço gerenciado ou entidade de segurança para recursos do Azure.
- Como uma fonte, recuperando dados usando uma consulta SQL ou um procedimento armazenado.
- Como um coletor, acrescentar dados a uma tabela de destino ou invocar um procedimento armazenado com lógica personalizada durante a cópia.

>[!NOTE]
>Banco de dados SQL do Azure [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) não é suportado por este conector agora. Solução alternativa, você pode usar um [conector ODBC genérico](connector-odbc.md) e um driver ODBC do SQL Server por meio de um tempo de execução de integração auto-hospedado. Siga [neste guia](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) com configurações de cadeia de caracteres ODBC driver download e a conexão.

> [!IMPORTANT]
> Se você copiar dados usando o tempo de execução de integração do Azure Data Factory, configure uma [firewall do SQL Server do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para que os serviços do Azure possam acessar o servidor.
> Se você copiar dados usando um tempo de execução de integração auto-hospedado, configure o firewall do SQL Server do Azure para permitir que o intervalo de IP apropriado. Este intervalo inclui o IP do computador que é usado para se conectar ao banco de dados SQL.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir entidades do Azure Data Factory específicas ao conector de um banco de dados SQL.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

Essas propriedades são suportadas por um serviço vinculado ao Banco de Dados SQL do Azure:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade  **tipo** deve ser definida como **AzureSqlDatabase**. | Sim |
| connectionString | Especifique as informações necessárias para se conectar à instância do Banco de Dados SQL do Azure para a propriedade **connectionString**. <br/>Marcar este campo como **SecureString** para armazená-la com segurança no Azure Data Factory. Você também pode colocar uma chave da entidade de serviço ou a senha no Azure Key Vault. Se for autenticação do SQL, extrair o `password` configuração fora de cadeia de caracteres de conexão. Para obter mais informações, consulte o exemplo JSON, a tabela a seguir e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço |
| servicePrincipalKey | Especifique a chave do aplicativo. Marcar este campo como **SecureString** armazená-lo com segurança no Azure Data Factory ou [fazem referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço |
| tenant | Especifique as informações de locatário, como o nome de domínio ou ID, em que reside o aplicativo de locatário. Recuperá-lo passando o mouse no canto superior direito do portal do Azure. | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço |
| connectVia | Esse [Integration Runtime](concepts-integration-runtime.md) é usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o armazenamento de dados está localizado em uma rede privada. Se não especificado, o tempo de execução de integração do Azure padrão será usado. | Não |

Para diferentes tipos de autenticação, consulte as seções a seguir sobre pré-requisitos e amostras JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- [Autenticação de token do aplicativo Azure AD: Entidade de serviço](#service-principal-authentication)
- [Autenticação de token do aplicativo Azure AD: identidades gerenciadas para recursos do Azure](#managed-identity)

>[!TIP]
>Se você atingir um erro com o código de erro "UserErrorFailedToConnectToSqlServer" e uma mensagem como "o limite de sessão para o banco de dados é XXX e foi atingido", adicionar `Pooling=false` à sua cadeia de caracteres de conexão e tente novamente.

### <a name="sql-authentication"></a>Autenticação do SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exemplo de serviço vinculado que usa autenticação SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Senha no cofre de chaves do Azure** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

### <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

Para usar uma autenticação de token de aplicativo do Azure AD baseada no serviço principal, siga estas etapas:

1. [Criar um aplicativo do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) do portal do Azure. Anote o nome do aplicativo e os seguintes valores que definem o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. [Provisionar um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) para seu Azure SQL Server no portal do Azure se você ainda não fez isso. O administrador do Azure AD deve ser um usuário do Azure AD ou um grupo do Azure AD, mas não pode ser um diretor de serviços. Essa etapa é feita para que, na próxima etapa, você possa usar uma identidade do Azure AD para criar um usuário de banco de dados contido para a entidade de serviço.

3. [Criar usuários de banco de dados independente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para a entidade de serviço. Conectar-se ao banco de dados de ou para o qual você deseja copiar dados usando ferramentas como o SQL Server Management Studio, com uma identidade do Azure AD que tenha pelo menos permissão ALTER ANY USER. Execute o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Conceda que a entidade de serviço as permissões necessárias como faria normalmente para usuários do SQL ou outras pessoas. Execute o seguinte código. Para obter mais opções, consulte [este documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Configure um serviço de banco de dados do SQL Azure vinculado no Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemplo de serviço vinculado que usa autenticação principal de serviço

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar essa identidade gerenciada para a autenticação de banco de dados SQL. A fábrica designada pode acessar e copiar dados de ou para seu banco de dados usando essa identidade.

Para usar a autenticação de identidade gerenciada, siga estas etapas.

1. [Provisionar um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) para seu Azure SQL Server no portal do Azure se você ainda não fez isso. O administrador do AD do Azure pode ser um usuário do AD do Azure ou um grupo do AD do Azure. Se você conceder ao grupo com uma função de administrador de identidade gerenciada, ignore as etapas 3 e 4. O administrador tem acesso completo ao banco de dados.

2. [Criar usuários de banco de dados independente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para o Azure Data Factory identidade gerenciada. Conectar-se ao banco de dados de ou para o qual você deseja copiar dados usando ferramentas como o SQL Server Management Studio, com uma identidade do Azure AD que tenha pelo menos permissão ALTER ANY USER. Execute o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Conceda que a fábrica de dados gerenciados identidade necessárias permissões que você normalmente faz para os usuários do SQL e outros. Execute o seguinte código. Para obter mais opções, consulte [este documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Configure um serviço de banco de dados do SQL Azure vinculado no Azure Data Factory.

**Exemplo**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte [conjuntos de dados](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). Esta seção fornece uma lista de propriedades com suporte pelo conjunto de dados do banco de dados SQL do Azure.

Para copiar dados de ou para o banco de dados SQL, há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **tipo** do conjunto de dados deve ser definida como **AzureSqlTable**. | Sim |
| tableName | O nome da tabela ou exibição na instância do Banco de Dados SQL do Azure à qual o serviço vinculado se refere. | Não para fonte, Sim para o coletor |

#### <a name="dataset-properties-example"></a>Exemplo de propriedades do conjunto de dados

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela origem e pelo coletor do Banco de Dados SQL do Azure.

### <a name="azure-sql-database-as-the-source"></a>Banco de Dados SQL do Azure como a origem

Para copiar dados do banco de dados SQL, defina as **tipo** propriedade na fonte da atividade de cópia para **SqlSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | O **tipo** propriedade da fonte da atividade de cópia deve ser definida como **SqlSource**. | Sim |
| sqlReaderQuery | Essa propriedade usa a consulta SQL personalizada para ler dados. Um exemplo é `select * from MyTable`. | Não |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. | Não |
| storedProcedureParameters | Parâmetros para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e o uso de maiusculas e minúsculas dos parâmetros devem corresponder a nomes e o uso de maiusculas e minúsculas dos parâmetros de procedimento armazenado. | Não |

**Pontos a serem observados:**

- Se **sqlReaderQuery** é especificado para **SqlSource**, a atividade de cópia executará essa consulta na fonte do banco de dados SQL para obter os dados. Você também pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** se o procedimento armazenado usa parâmetros.
- Se você não especificar **sqlReaderQuery** ou **sqlReaderStoredProcedureName**, as colunas definidas na seção "structure" do conjunto de dados JSON são usadas para construir uma consulta. A consulta `select column1, column2 from mytable` é executado no banco de dados do Azure SQL. Se a definição de conjunto de dados não tiver "structure", todas as colunas serão selecionadas da tabela.

#### <a name="sql-query-example"></a>Exemplo de consulta SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

#### <a name="stored-procedure-example"></a>Exemplo de procedimento armazenado

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

### <a name="stored-procedure-definition"></a>Definição do procedimento armazenado

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

### <a name="azure-sql-database-as-the-sink"></a>Banco de Dados SQL do Azure como o coletor

> [!TIP]
> Saiba mais sobre os comportamentos de gravação com suporte, configurações e as práticas recomendadas da [práticas recomendadas para carregar dados no banco de dados SQL](#best-practice-for-loading-data-into-azure-sql-database).

Para copiar dados para o banco de dados SQL, defina as **tipo** coletor de propriedade na atividade de cópia para **SqlSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | O **tipo** propriedade do coletor de atividade de cópia deve ser definida como **SqlSink**. | Sim |
| writeBatchSize | Número de linhas a serem inseridas na tabela SQL *por lote*.<br/> O valor permitido é **inteiro** (número de linhas). Por padrão, o Azure Data Factory determina dinamicamente o tamanho de lote apropriado com base no tamanho da linha. | Não |
| writeBatchTimeout | O tempo de espera para o lote inserir operação seja concluída antes de expirar.<br/> O valor permitido é **timespan**. Um exemplo é "00: 30:00" (30 minutos). | Não |
| preCopyScript | Especifique uma consulta SQL para a atividade de cópia executar antes de gravar dados no banco de dados SQL. É chamado apenas uma vez por execução de cópia. Use essa propriedade para limpar os dados pré-carregados. | Não |
| sqlWriterStoredProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem em uma tabela de destino. <br/>Este procedimento armazenado é *chamado por lote*. Para operações que são executados apenas uma vez e não têm nada a fonte de dados, por exemplo, excluir ou truncar, use o `preCopyScript` propriedade. | Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Valores permitidos são pares de nome e valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. | Não |
| sqlWriterTableType | Especifique um nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza os dados que estão sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados que estão sendo copiados com os dados existentes. | Não |

**Exemplo 1: Acrescentar dados**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Prática recomendada para carregar dados no banco de dados SQL

Quando você copia dados para o banco de dados SQL, você pode exigir comportamento diferente de gravação:

- [Acrescentar](#append-data): Minha fonte de dados tem somente novos registros.
- [Upsert](#upsert-data): Minha fonte de dados tem inserções e atualizações.
- [Substituir](#overwrite-the-entire-table): Eu quero recarregar uma tabela de dimensões todo cada vez.
- [Escrever com lógica personalizada](#write-data-with-custom-logic): Preciso de processamento extra antes da inserção final na tabela de destino.

Consulte as respectivas seções sobre como configurar no Azure Data Factory e as práticas recomendadas.

### <a name="append-data"></a>Acrescentar dados

Acrescentando dados é o comportamento padrão desse conector de coletor do banco de dados SQL. O Azure Data Factory faz uma inserção em massa para gravar em sua tabela com eficiência. Você pode configurar a fonte e coletor adequadamente na atividade de cópia.

### <a name="upsert-data"></a>Upsert data

**Opção 1:** Quando você tem uma grande quantidade de dados para copiar, usar a abordagem a seguir para fazer um upsert: 

- Primeiro, use uma [tabela temporária no escopo do banco de dados](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) para carregamento em massa todos os registros usando a atividade de cópia. Porque as operações em relação a tabelas temporárias do escopo do banco de dados não estiverem conectadas, você pode carregar milhões de registros em segundos.
- Executar uma atividade de procedimento armazenado no Azure Data Factory para aplicar uma [mesclar](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou instrução de inserção/atualização. Use a tabela temporária como a origem para executar todas as atualiza ou insere como uma única transação. Dessa forma, o número de viagens de ida e volta e operações de log é reduzido. No final da atividade de procedimento armazenado, a tabela temporária pode ser truncada para estar pronto para o próximo ciclo de upsert.

Por exemplo, no Azure Data Factory, você pode criar um pipeline com uma **atividade de cópia** encadeado com um **atividade de procedimento armazenado**. O primeiro copia dados de seu armazenamento de origem em uma tabela temporária do banco de dados SQL, por exemplo, **##UpsertTempTable**, como o nome da tabela no conjunto de dados. Em seguida, o último invoca um procedimento armazenado para mesclar a fonte de dados da tabela temp na tabela de destino e limpar a tabela temporária.

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

Você pode configurar o **preCopyScript** propriedade no coletor de atividade de cópia. Nesse caso, para cada atividade de cópia que é executado, Azure Data Factory executa o script pela primeira vez. Em seguida, ele executa a cópia para inserir os dados. Por exemplo, para substituir a tabela inteira com os dados mais recentes, especifica um script para primeiro excluir todos os registros antes de você em massa carregar os novos dados da origem.

### <a name="write-data-with-custom-logic"></a>Gravar dados com lógica personalizada

As etapas para gravar dados com lógica personalizada são semelhantes aos descritos na [Upsert dados](#upsert-data) seção. Quando você precisar aplicar extra de processamento antes da inserção final dos dados de origem na tabela de destino, de grande escala, você pode fazer uma das duas coisas:

- Carregue em uma tabela temporária com escopo de banco de dados e, em seguida, invocar um procedimento armazenado. 
- Invocar um procedimento armazenado durante a cópia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocar um procedimento armazenado de um coletor SQL

Quando você copia dados para o banco de dados SQL, você também pode configurar e invocar um procedimento armazenado especificado pelo usuário com parâmetros adicionais.

> [!TIP]
> Invocar um procedimento armazenado processa os dados linha por linha, em vez de por meio de uma operação em massa, que não é recomendável para cópia em larga escala. Saiba mais sobre [práticas recomendadas para carregar dados no banco de dados SQL](#best-practice-for-loading-data-into-azure-sql-database).

Você pode usar um procedimento armazenado quando os mecanismos internos de cópia não atendem à finalidade. Um exemplo é quando você deseja aplicar o processamento extra antes da inserção final dos dados de origem na tabela de destino. Alguns exemplos de processamento extra são quando você deseja mesclar colunas, pesquisar valores adicionais e inserir em mais de uma tabela.

A amostra a seguir mostra como usar um procedimento armazenado para fazer um upsert em uma tabela no Banco de Dados SQL do Azure. Suponha que os dados de entrada e o coletor **Marketing** cada tabela tem três colunas: **ProfileID**, **State** e **Category**. Faça o upsert com base na coluna **ProfileID** e aplique-o apenas a uma categoria específica.

**Conjunto de dados de saída:** O "tableName" é o mesmo nome de parâmetro de tipo de tabela em seu procedimento armazenado, conforme mostrado no script de procedimento armazenado a seguir:

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

O recurso de procedimento armazenado se beneficia de [parâmetros com valores de tabela](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Obtenha detalhes em [transformação de origem](data-flow-source.md) e [transformação do coletor](data-flow-sink.md) no mapeamento de fluxo de dados.

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapeamento do tipo de dados do Banco de Dados SQL do Azure

Quando dados são copiados de ou para o banco de dados SQL, os seguintes mapeamentos são usados de tipos de dados do banco de dados SQL para tipos de dados provisórios do Azure Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do Banco de Dados SQL do Azure | Tipo de dados provisório do Azure Data Factory |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Para tipos de dados que mapeiam para o tipo provisório Decimal, no momento, o Azure Data Factory dá suporte à precisão de até 28. Se você tiver dados com precisão maior que 28, considere a conversão em uma cadeia de caracteres na consulta SQL.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [formatos e armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
