---
title: Controlar o acesso da conta de armazenamento para o pool de SQL sem servidor
description: Descreve como o pool de SQL sem servidor acessa o Armazenamento do Azure e como você pode controlar o acesso ao armazenamento do pool de SQL sem servidor no Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 726395e9f004130699dab061cfa752a2e516c834
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552947"
---
# <a name="control-storage-account-access-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Controlar o acesso à conta de armazenamento para o pool de SQL sem servidor no Azure Synapse Analytics

Uma consulta do pool de SQL sem servidor lê arquivos diretamente no Armazenamento do Azure. As permissões para acessar os arquivos no armazenamento do Azure são controladas em dois níveis:
- **Nível de armazenamento** – o usuário deve ter permissão para acessar os arquivos de armazenamento subjacentes. O administrador de armazenamento deve permitir que a entidade de segurança do Azure AD leia/grave arquivos ou gere uma chave SAS que será usada para acessar o armazenamento.
- **Nível de serviço do SQL** – o usuário deve ter concedido uma permissão para ler dados usando a [tabela externa](develop-tables-external-tables.md) ou para executar a função `OPENROWSET`. Leia mais sobre [as permissões obrigatórias nesta seção](develop-storage-files-overview.md#permissions).

Este artigo descreve os tipos de credenciais que você pode usar e como a pesquisa de credenciais é aplicada para usuários do SQL e do Azure AD.

## <a name="supported-storage-authorization-types"></a>Tipos de autorização de armazenamento compatíveis

Um usuário que fez logon em um pool de SQL sem servidor precisará estar autorizado a acessar e consultar os arquivos no Armazenamento do Azure se os arquivos não estiverem disponíveis publicamente. Você pode usar três tipos de autorização para acessar um armazenamento não público: [Identidade do Usuário](?tabs=user-identity), [Assinatura de acesso compartilhado](?tabs=shared-access-signature) e [Identidade Gerenciada](?tabs=managed-identity).

> [!NOTE]
> **Passagem do Azure AD** é o comportamento padrão quando você cria um workspace.

### <a name="user-identity"></a>[Identidade do Usuário](#tab/user-identity)

A **Identidade do Usuário**, também conhecida como "passagem do Azure AD", é um tipo de autorização em que a identidade do usuário do Azure AD que fez logon no pool de SQL sem servidor é usada para autorizar o acesso a dados. Antes de acessar os dados, o administrador do Armazenamento do Azure deve conceder permissões ao usuário do Azure AD. Conforme indicado na tabela a seguir, não é compatível para o tipo de usuário do SQL.

> [!IMPORTANT]
> O token de autenticação do AAD pode ser armazenado em cache pelos aplicativos clientes. Por exemplo, o PowerBI armazena em cache o token do AAD e reutiliza o mesmo token por uma hora. As consultas de longa execução poderão falhar se o token expirar no meio da execução da consulta. Se você estiver enfrentando falhas de consulta causadas pela expiração do token de acesso do AAD no meio da consulta, considere a possibilidade de alternar para a assinatura de [Identidade Gerenciada](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types) ou de [Acesso Compartilhado](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#supported-storage-authorization-types).

Você precisa ter uma função de Proprietário/Colaborador/Leitor de dados do blob de armazenamento para usar sua identidade para acessar os dados. Como alternativa, você pode especificar regras de ACL refinadas para acessar arquivos e pastas. Mesmo se você for um Proprietário de uma Conta de Armazenamento, ainda precisará se adicionar a uma das funções de dados do blob de armazenamento.
Para saber mais sobre o controle de acesso no Azure Data Lake Storage Gen2, examine o artigo [Controle de acesso no Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md).


### <a name="shared-access-signature"></a>[Assinatura de acesso compartilhado](#tab/shared-access-signature)

A **SAS (Assinatura de Acesso Compartilhado)** fornece acesso delegado aos recursos de uma conta de armazenamento. Com a SAS, um cliente pode conceder acesso aos recursos em uma conta de armazenamento, sem compartilhar as chaves de conta. A SAS oferece controle granular sobre o tipo de acesso que você concede aos clientes que têm uma SAS, incluindo intervalo de validade, permissões concedidas, intervalo de endereços IP aceitáveis e o protocolo aceitável (HTTPS/HTTP).

Você pode obter um token SAS navegando até o **Portal do Azure -> Conta de Armazenamento -> Assinatura de Acesso Compartilhado -> Configurar permissões -> Gerar SAS e cadeia de conexão.**

> [!IMPORTANT]
> Quando um token SAS é gerado, ele inclui um ponto de interrogação ("?") no início do token. Para usar o token no pool de SQL sem servidor, você precisa remover o ponto de interrogação ("?") ao criar uma credencial. Por exemplo:
>
> Token SAS: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

Para permitir acesso usando um token SAS, você precisa criar uma credencial no escopo do banco de dados ou no escopo do servidor 


> [!IMPORTANT]
> Você não pode acessar contas de armazenamento privado com o token SAS. Considere a possibilidade de alternar para a autenticação por [Identidade Gerenciada](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types) ou via [passagem do Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) a fim de acessar armazenamentos protegidos.

### <a name="managed-identity"></a>[Identidade gerenciada](#tab/managed-identity)

A **Identidade Gerenciada** também é conhecida como MSI. É um recurso do Azure AD (Azure Active Directory) que fornece serviços do Azure para o pool de SQL sem servidor. Além disso, ele implanta uma identidade gerenciada automaticamente no Azure AD. Essa identidade pode ser usada para autorizar a solicitação de acesso a dados no Armazenamento do Azure.

Antes de acessar os dados, o administrador do Armazenamento do Azure deve conceder permissões para a Identidade Gerenciada a fim de acessar os dados. A concessão de permissões para a Identidade Gerenciada é feita da mesma forma que a concessão de permissão para qualquer outro usuário do Azure AD.

### <a name="anonymous-access"></a>[Acesso anônimo](#tab/public-access)

Você pode acessar os arquivos publicamente disponíveis colocados em contas do Armazenamento do Azure que [permitem acesso anônimo](../../storage/blobs/anonymous-read-access-configure.md).

---

### <a name="supported-authorization-types-for-databases-users"></a>Tipos de autorização compatíveis com usuários de bancos de dados

Na tabela abaixo, você pode encontrar os tipos de autorização disponíveis:

| Tipo de autorização                    | *Usuário do SQL*    | *Usuário do Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [Identidade do Usuário](?tabs=user-identity#supported-storage-authorization-types)       | Sem suporte | Com suporte      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Com suporte     | Com suporte      |
| [Identidade gerenciada](?tabs=managed-identity#supported-storage-authorization-types) | Sem suporte | Com suporte      |

### <a name="supported-storages-and-authorization-types"></a>Tipos de autorização e armazenamentos compatíveis

Você pode usar as seguintes combinações de autorização e tipos de Armazenamento do Azure:

| Tipo de autorização  | Armazenamento de Blobs   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)    | Com suporte\*      | Não compatível   | Com suporte\*     |
| [Identidade gerenciada](?tabs=managed-identity#supported-storage-authorization-types) | Com suporte      | Com suporte        | Suportado     |
| [Identidade do Usuário](?tabs=user-identity#supported-storage-authorization-types)    | Suportado\*      | Suportado\*        | Suportado\*     |

\* O token SAS e a Identidade do Azure AD podem ser usados para acessar um armazenamento que não está protegido pelo firewall.


### <a name="querying-firewall-protected-storage"></a>Como consultar o armazenamento protegido por firewall

Ao acessar o armazenamento protegido pelo firewall, é possível usar a **Identidade do Usuário** ou a **Identidade Gerenciada**.

> [!NOTE]
> O recurso de firewall no Armazenamento está em versão prévia pública e disponível em todas as regiões de nuvem pública. 

#### <a name="user-identity"></a>Identidade do Usuário

Para acessar o armazenamento protegido com o firewall por meio da identidade do usuário, você pode usar o módulo Az.Storage do PowerShell.
#### <a name="configuration-via-azure-portal"></a>Configuração por meio do portal do Azure

1. Pesquise pela sua conta de armazenamento no portal do Azure.
1. Acesse Rede na seção Configurações.
1. Na seção "Instâncias de recurso", adicione uma exceção para o workspace do Azure Synapse.
1. Selecione Microsoft.Synapse/workspaces como tipo de recurso.
1. Selecione o nome do seu workspace como o nome da instância.
1. Clique em Salvar.

#### <a name="configuration-via-powershell"></a>Configuração por meio do PowerShell

Siga estas etapas para configurar o firewall da conta de armazenamento e adicionar uma exceção para o workspace do Synapse.

1. Abra o PowerShell ou [instale o PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows)
2. Instale os módulos Az.Storage 3.4.0 e Az.Synapse 0.7.0: 
    ```powershell
    Install-Module -Name Az.Storage -RequiredVersion 3.4.0
    Install-Module -Name Az.Synapse -RequiredVersion 0.7.0
    ```
    > [!IMPORTANT]
    > Lembre-se de usar a **versão 3.4.0**. Você pode verificar sua versão do Az.Storage executando este comando:  
    > ```powershell 
    > Get-Module -ListAvailable -Name  Az.Storage | select Version
    > ```
    > 

3. Conecte-se ao seu locatário do Azure: 
    ```powershell
    Connect-AzAccount
    ```
4. Defina as variáveis no PowerShell: 
    - Nome do grupo de recursos: você pode encontrar a informação no portal do Azure, na visão geral da conta de armazenamento.
    - Nome da conta: nome da conta de armazenamento que é protegida por regras de firewall.
    - ID do locatário: você pode encontrá-la nas informações de locatário do Azure Active Directory no portal do Azure.
    - Nome do workspace: o nome do workspace do Azure Synapse.

    ```powershell
        $resourceGroupName = "<resource group name>"
        $accountName = "<storage account name>"
        $tenantId = "<tenant id>"
        $workspaceName = "<synapse workspace name>"
        
        $workspace = Get-AzSynapseWorkspace -Name $workspaceName
        $resourceId = $workspace.Id
        $index = $resourceId.IndexOf("/resourceGroups/", 0)
        # Replace G with g - /resourceGroups/ to /resourcegroups/
        $resourceId = $resourceId.Substring(0,$index) + "/resourcegroups/" + $resourceId.Substring($index + "/resourceGroups/".Length)
        $resourceId
    ```
    > [!IMPORTANT]
    > Verifique se a ID do recurso corresponde a esse modelo na impressão da variável resourceId.
    >
    > É importante escrever **resourcegroups** em letras minúsculas.
    > Exemplo de uma ID do recurso: 
    > ```
    > /subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Synapse/workspaces/{name-of-workspace}
    > ```
    > 
5. Adicione a regra de rede de armazenamento: 
    ```powershell
        Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId
    ```
6. Verifique se a regra foi aplicada em sua conta de armazenamento: 
    ```powershell
        $rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
        $rule.ResourceAccessRules | ForEach-Object { 
            if ($_.ResourceId -cmatch "\/subscriptions\/(\w\-*)+\/resourcegroups\/(.)+") { 
                Write-Host "Storage account network rule is successfully configured." -ForegroundColor Green
                $rule.ResourceAccessRules
            } else {
                Write-Host "Storage account network rule is not configured correctly. Remove this rule and follow the steps in detail." -ForegroundColor Red
                $rule.ResourceAccessRules
            }
        }
    ```

#### <a name="managed-identity"></a>Identidade Gerenciada
Você precisa [Permitir a configuração de serviços Microsoft confiáveis...](../../storage/common/storage-network-security.md#trusted-microsoft-services) e [atribuir uma função do Azure](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights) explicitamente à [identidade gerenciada atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/overview.md) para essa instância do recurso. Nesse caso, o escopo de acesso para a instância corresponde à função do Azure atribuída à identidade gerenciada.

## <a name="credentials"></a>Credenciais

Para consultar um arquivo localizado no Armazenamento do Azure, o seu ponto de extremidade do pool de SQL sem servidor precisa de uma credencial que contenha as informações de autenticação. São usados dois tipos de credenciais:
- O argumento CREDENTIAL no nível do servidor é usado para consultas ad hoc executadas usando a função `OPENROWSET`. O nome da credencial deve corresponder à URL de armazenamento.
- DATABASE SCOPED CREDENTIAL é usada para tabelas externas. A tabela externa faz referência a `DATA SOURCE` com a credencial que deve ser usada para acessar o armazenamento.

Para permitir que um usuário crie ou remova uma credencial, o administrador pode conceder a permissão GRANT/DENY ALTER ANY CREDENTIAL a um usuário:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Os usuários de banco de dados que acessam o armazenamento externo devem ter permissão para usar credenciais.

### <a name="grant-permissions-to-use-credential"></a>Conceder permissões para usar credencial

Para usar a credencial, um usuário deve ter a permissão `REFERENCES` em uma credencial específica. Para conceder uma permissão `REFERENCES` ON uma credencial_de_armazenamento para um usuário_específico, execute:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

## <a name="server-scoped-credential"></a>Credencial no escopo do servidor

As credenciais no escopo do servidor são usadas quando o logon do SQL chama a função `OPENROWSET` sem `DATA_SOURCE` para ler arquivos em alguma conta de armazenamento. O nome da credencial com escopo de servidor **precisa** corresponder à URL de base do armazenamento do Azure (opcionalmente seguido do nome do contêiner). Uma credencial é adicionada executando [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true). Você precisará fornecer um argumento CREDENTIAL NAME.

> [!NOTE]
> Não há suporte para o argumento `FOR CRYPTOGRAPHIC PROVIDER`.

O nome do argumento CREDENTIAL no nível do servidor deve corresponder ao caminho completo da conta de armazenamento (e, opcionalmente, do contêiner), no seguinte formato: `<prefix>://<storage_account_path>[/<container_name>]`. Os caminhos de contas de armazenamento são descritos na tabela a seguir:

| Fonte de dados externa       | Prefixo | Caminho da conta de armazenamento                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Armazenamento do Blobs do Azure         | HTTPS  | <conta_de_armazenamento>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | HTTPS  | <conta_de_armazenamento>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | HTTPS  | <conta_de_armazenamento>.dfs.core.windows.net              |

As credenciais no escopo do servidor permitem o acesso ao Armazenamento do Azure usando os seguintes tipos de autenticação:

### <a name="user-identity"></a>[Identidade do Usuário](#tab/user-identity)

Os usuários do Azure AD podem acessar qualquer arquivo no armazenamento do Azure quando eles têm a função `Storage Blob Data Owner`, `Storage Blob Data Contributor` ou `Storage Blob Data Reader`. Os usuários do Azure AD não precisam de credenciais para acessar o armazenamento. 

Os usuários do SQL não podem usar a autenticação do Azure AD para acessar o armazenamento.

### <a name="shared-access-signature"></a>[Assinatura de acesso compartilhado](#tab/shared-access-signature)

O script a seguir cria uma credencial no nível do servidor que pode ser usada pela função `OPENROWSET` para acessar qualquer arquivo no Armazenamento do Azure usando o token SAS. Crie essa credencial para habilitar a entidade de segurança SQL que executa a função `OPENROWSET` a fim de ler arquivos protegidos com a chave SAS no Armazenamento do Azure que corresponde à URL no nome da credencial.

Substitua <*mystorageaccountname*> pelo nome real da conta de armazenamento e <*mystorageaccountcontainername*> pelo nome real do contêiner:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

Opcionalmente, você pode usar somente a URL base da conta de armazenamento, sem o nome do contêiner.

### <a name="managed-identity"></a>[Identidade gerenciada](#tab/managed-identity)

O script a seguir cria uma credencial no nível do servidor que pode ser usada pela função `OPENROWSET` para acessar qualquer arquivo no armazenamento do Azure usando a identidade gerenciada pelo workspace.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

Opcionalmente, você pode usar somente a URL base da conta de armazenamento, sem o nome do contêiner.

### <a name="public-access"></a>[Acesso público](#tab/public-access)

A credencial no escopo do banco de dados não é necessária para permitir o acesso a arquivos disponíveis publicamente. Crie uma [fonte de dados sem credencial no escopo do banco de dados](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) para acessar arquivos publicamente disponíveis no Armazenamento do Azure.

---

## <a name="database-scoped-credential"></a>Credencial no escopo do banco de dados

As credenciais no escopo do banco de dados são usadas quando qualquer entidade de segurança chama a função `OPENROWSET` com `DATA_SOURCE` ou seleciona dados de [tabela externa](develop-tables-external-tables.md) que não acessam arquivos públicos. A credencial no escopo do banco de dados não precisa corresponder ao nome da conta de armazenamento. Ela será usada explicitamente no DATA SOURCE que define a localização do armazenamento.

As credenciais no escopo do banco de dados permitem o acesso ao Armazenamento do Azure usando os seguintes tipos de autenticação:

### <a name="azure-ad-identity"></a>[Identidade do Azure AD](#tab/user-identity)

Os usuários do Azure AD podem acessar qualquer arquivo no armazenamento do Azure quando eles têm, pelo menos, a função `Storage Blob Data Owner`, `Storage Blob Data Contributor` ou `Storage Blob Data Reader`. Os usuários do Azure AD não precisam de credenciais para acessar o armazenamento.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

Os usuários do SQL não podem usar a autenticação do Azure AD para acessar o armazenamento.

### <a name="shared-access-signature"></a>[Assinatura de acesso compartilhado](#tab/shared-access-signature)

O script a seguir cria uma credencial que é usada para acessar arquivos no armazenamento usando o token SAS especificado na credencial. O script criará uma fonte de dados externa de exemplo que usa esse token SAS para acessar o armazenamento.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[Identidade gerenciada](#tab/managed-identity)

O script a seguir cria uma credencial no escopo do banco de dados que pode ser usada para representar o usuário atual do Azure AD como identidade gerenciada do serviço. O script criará uma fonte de dados externa de exemplo que usa a identidade do workspace para acessar o armazenamento.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

A credencial no escopo do banco de dados não precisa corresponder ao nome da conta de armazenamento, pois ela será usada explicitamente na fonte de dados (argumento DATA SOURCE) que define o local do armazenamento.

### <a name="public-access"></a>[Acesso público](#tab/public-access)

A credencial no escopo do banco de dados não é necessária para permitir o acesso a arquivos disponíveis publicamente. Crie uma [fonte de dados sem credencial no escopo do banco de dados](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) para acessar arquivos publicamente disponíveis no Armazenamento do Azure.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

As credenciais no escopo do banco de dados são usadas em fontes externas para especificar qual método de autenticação será usado para acessar esse armazenamento:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Exemplos

### <a name="access-a-publicly-available-data-source"></a>**Acessar uma fonte de dados disponível publicamente**

Use o script a seguir para criar uma tabela que acessa uma fonte de dados disponível publicamente.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

O usuário do banco de dados pode ler o conteúdo dos arquivos da fonte de dados usando a tabela externa ou a função [OPENROWSET](develop-openrowset.md) que faz referência à fonte de dados:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**Acessar uma fonte de dados usando credenciais**

Modifique o script a seguir para criar uma tabela externa que acessa o Armazenamento do Azure usando o token SAS, a identidade do usuário do Azure AD ou a identidade gerenciada do workspace.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

O usuário do banco de dados pode ler o conteúdo dos arquivos da fonte de dados usando a [tabela externa](develop-tables-external-tables.md) ou a função [OPENROWSET](develop-openrowset.md) que faz referência à fonte de dados:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>Próximas etapas

Os artigos listados abaixo ajudarão você a aprender a consultar diferentes tipos de pasta, tipos de arquivo e criar e usar modos de exibição:

- [Consultar um arquivo CSV individual](query-single-csv-file.md)
- [Consultar pastas e vários arquivos CSV](query-folders-multiple-csv-files.md)
- [Consultar arquivos específicos](query-specific-files.md)
- [Consultar arquivos Parquet](query-parquet-files.md)
- [Criar e usar modos de exibição](create-use-views.md)
- [Consultar arquivos JSON](query-json-files.md)
- [Consultar tipos aninhados Parquet](query-parquet-nested-types.md)
