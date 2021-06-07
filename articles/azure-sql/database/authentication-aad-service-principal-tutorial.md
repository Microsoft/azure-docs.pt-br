---
title: Criar usuários do Azure AD usando entidades de serviço
description: Este tutorial orienta você na criação de um usuário do Azure AD com aplicativos do Azure AD (entidades de serviço) no Banco de Dados SQL do Azure e no Azure Synapse Analytics
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 13e049d3e7e0c87bd0a214a92491e10d652a3619
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380603"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>Tutorial: Criar usuários do Azure AD usando aplicativos do Azure AD

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> Esse artigo está em **versão prévia pública**. Para obter mais informações, confira [Entidade de serviço do Azure Active Directory com o SQL do Azure](authentication-aad-service-principal.md). Este artigo usará o Banco de Dados SQL do Azure para demonstrar as etapas de tutorial necessárias, mas pode ser aplicado de maneira semelhante ao [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Este artigo orienta você pelo processo de criação de usuários do Azure AD no Banco de Dados SQL do Azure, usando entidades de serviço do Azure (aplicativos do Azure AD). Essa funcionalidade já existe na Instância Gerenciada de SQL do Azure, mas agora está sendo introduzida no Banco de Dados SQL do Azure e no Azure Synapse Analytics. Para dar suporte a esse cenário, uma identidade do Azure AD precisa ser gerada e atribuída ao servidor lógico do SQL do Azure.

Para obter mais informações sobre a autenticação do Azure AD para o SQL do Azure, confira o artigo [Usar a autenticação do Azure Active Directory](authentication-aad-overview.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> - Atribuir uma identidade ao servidor lógico do SQL do Azure
> - Atribuir permissão de Leitores de diretório à identidade do servidor lógico do SQL
> - Criar uma entidade de serviço (um aplicativo do Azure AD) no Azure AD
> - Criar um usuário de entidade de serviço no Banco de Dados SQL do Azure
> - Criar um usuário diferente do Azure AD no Banco de Dados SQL usando um usuário da entidade de serviço do Azure AD

## <a name="prerequisites"></a>Pré-requisitos

- Uma implantação existente do [Banco de Dados SQL do Azure](single-database-create-quickstart.md) ou do [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para este tutorial, supomos que você tenha um Banco de Dados SQL em funcionamento.
- Acesso a um Azure Active Directory já existente.
- Se você usar o PowerShell para configurar um aplicativo individual do Azure AD na condição de administrador do Azure AD para o SQL do Azure, um módulo [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) ou posterior será necessário. Verifique se você atualizou para o módulo mais recente.

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>Atribuir uma identidade ao servidor lógico do SQL do Azure

1. Conecte-se ao Azure Active Directory. Você precisará localizar a sua ID de Locatário. Ela pode ser encontrada acessando o [portal do Azure](https://portal.azure.com) e o recurso **Azure Active Directory**. No painel de **Visão geral**, você precisa ver sua **ID de Locatário**. Execute o seguinte comando do PowerShell:

    - Substitua `<TenantId>` pela **ID de Locatário**.

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    Registre o `TenantId` para uso futuro neste tutorial.

1. Gere e atribua uma identidade do Azure AD ao servidor lógico do SQL do Azure. Execute o seguinte comando do PowerShell:

    - Substitua `<resource group>` e `<server name>` pelos seus recursos. Se o nome do servidor for `myserver.database.windows.net`, substitua `<server name>` por `myserver`.

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    Para obter mais informações, confira o comando [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver).

    > [!IMPORTANT]
    > Se uma identidade do Azure AD estiver configurada para o servidor lógico do SQL do Azure, a permissão de [**Leitores de diretório**](../../active-directory/roles/permissions-reference.md#directory-readers) precisará ser concedida à identidade. Veremos essa etapa na seção a seguir. **Não** ignore essa etapa, pois se o fizer, a autenticação do Azure AD deixará de funcionar.

    - Se você tiver usado o comando [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) com o parâmetro `AssignIdentity` para uma criação do SQL Server no passado, será necessário executar o comando [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) posteriormente como um comando separado para habilitar essa propriedade na malha do Azure.

1. Verifique se a identidade do servidor foi atribuída com êxito. Execute o seguinte comando do PowerShell:

    - Substitua `<resource group>` e `<server name>` pelos seus recursos. Se o nome do servidor for `myserver.database.windows.net`, substitua `<server name>` por `myserver`.
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    Sua saída agora deve mostrar `PrincipalId`, `Type` e `TenantId`. A identidade atribuída é a `PrincipalId`.

1. Também é possível verificar a identidade acessando o [portal do Azure](https://portal.azure.com).

    - No recurso **Azure Active Directory**, acesse **Aplicativos empresariais**. Digite o nome do seu servidor lógico do SQL. Você verá que ele tem uma **ID de Objeto** anexada ao recurso.
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="object-id":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>Atribuir permissão de Leitores de diretório à identidade do servidor lógico do SQL

Para permitir que a identidade atribuída do Azure AD funcione corretamente para o SQL do Azure, a permissão dos `Directory Readers` do Azure AD precisa ser concedida à identidade do servidor.

Para conceder essa permissão necessária, execute o script a seguir.

> [!NOTE] 
> Esse script precisa ser executado por um `Global Administrator` ou um `Privileged Roles Administrator` do Azure AD.
>
> Na **versão prévia pública**, você pode atribuir a função `Directory Readers` a um grupo do Azure AD. Os proprietários do grupo podem então adicionar a identidade gerenciada como um membro desse grupo, o que poderá ignorar a necessidade de um `Global Administrator` ou um `Privileged Roles Administrator` para conceder a função `Directory Readers`. Para obter mais informações sobre esse recurso, confira [Função Leitores de Diretório no Azure Active Directory para o SQL do Azure](authentication-aad-directory-readers-role.md).

- Substitua `<TenantId>` pelo `TenantId` coletado anteriormente.
- Substitua `<server name>` pelo seu nome do servidor lógico do SQL. Se o nome do servidor for `myserver.database.windows.net`, substitua `<server name>` por `myserver`.

```powershell
# This script grants Azure "Directory Readers" permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or "Privileged Roles Administrator" type of user.
# To check if the "Directory Readers" permission was granted, execute this script again

Import-Module AzureAD
Connect-AzureAD -TenantId "<TenantId>"    #Enter your actual TenantId
$AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}
 
# Get service principal for managed instance
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$($AssignIdentityName)', make sure that AssignIdentityName parameter was entered correctly."
    exit
}

if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
 
if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($AssignIdentityName)' to 'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to 'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
} else {
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> A saída do script acima indicará se a permissão de Leitores de diretório foi concedida à identidade. O script poderá ser executado novamente se você não tiver certeza de que a permissão foi concedida.

Para obter uma abordagem semelhante sobre como definir a permissão de **Leitores de diretório** para a Instância Gerenciada de SQL, confira [Provisionar o administrador do Azure AD (Instância Gerenciada de SQL)](authentication-aad-configure.md#powershell).

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>Criar uma entidade de serviço (um aplicativo do Azure AD) no Azure AD

1. Siga o guia disponibilizado aqui para [registrar seu aplicativo e definir permissões](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions).

    Certifique-se de adicionar as **permissões de aplicativo**, bem como as **permissões delegadas**.

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="Captura de tela que mostra a página de Registros de aplicativo do Azure Active Directory. Um aplicativo com o Nome de exibição AppSP é realçado.":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="api-permissions":::

2. Você também precisará criar um segredo do cliente para entrar. Siga o guia disponibilizado aqui para [carregar um certificado ou criar um segredo para entrar](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options).

3. Registre as informações a seguir, que serão obtidas do registro de aplicativo. Elas devem estar disponíveis em seu painel **Visão geral**:
    - **ID do Aplicativo**
    - **ID do Locatário** – deve ser igual ao anterior

Neste tutorial, usaremos *AppSP* como nossa entidade de serviço principal e *MyApp* como o segundo usuário da entidade de serviço, que será criado no SQL do Azure por *AppSP*. Você precisará criar dois aplicativos, *AppSP* e *myapp*.

Para obter mais informações sobre como criar um aplicativo do Azure AD, confira o artigo [Como Usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="permissions-required-to-set-or-unset-the-azure-ad-admin"></a>Permissões necessárias para definir ou remover a definição do administrador do Azure AD

Para que a entidade de serviço defina ou remova a definição de um administrador do Azure AD para o Azure SQL, é necessária uma permissão adicional de API. A permissão de API do aplicativo [Directory.Read.All](/graph/permissions-reference#application-permissions-18) precisará ser adicionada ao seu aplicativo no Azure AD.

:::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-directory-reader-all-permissions.png" alt-text="Permissões Directory.Reader.All no Azure AD":::

A entidade de serviço também precisará da função [**Colaborador do SQL Server**](../../role-based-access-control/built-in-roles.md#sql-server-contributor) para o Banco de Dados SQL ou a função [**Colaborador da Instância Gerenciada de SQL**](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) para a Instância Gerenciada de SQL.

> [!NOTE]
> Embora a API do Graph do Azure AD esteja sendo preterida, a permissão **Directory.Reader.All** ainda será aplicável a este tutorial. A API do Microsoft Graph não se aplica a este tutorial.

## <a name="create-the-service-principal-user-in-azure-sql-database"></a>criar o usuário da entidade de serviço no Banco de Dados SQL do Azure

Depois que uma entidade de serviço for criada no Azure AD, crie o usuário no Banco de Dados SQL. Você precisará se conectar ao Banco de Dados SQL com um logon válido com permissões para criar usuários no banco de dados.

1. Crie o usuário *AppSP* no Banco de Dados SQL usando o seguinte comando T-SQL:

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. Conceda a permissão `db_owner` para *AppSP*, a qual permite ao usuário criar outros usuários do Azure AD no banco de dados.

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    Para obter mais informações, confira [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    Como alternativa, a permissão `ALTER ANY USER` pode ser concedida em vez da função de `db_owner`. Isso permitirá que a entidade de serviço adicione outros usuários do Azure AD.

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > A configuração acima não é necessária quando *AppSP* é definido como um administrador do Azure AD para o servidor. Para definir a entidade de serviço como um administrador do AD para o servidor lógico do SQL, use os comandos do portal do Azure, do PowerShell ou da CLI do Azure. Para obter mais informações, confira [Provisionar um administrador do Azure AD (Banco de Dados SQL)](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse).

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>Criar um usuário diferente do Azure AD no Banco de Dados SQL usando uma entidade de serviço do Azure AD

> [!IMPORTANT]
> A entidade de serviço usada para fazer logon no Banco de Dados SQL precisa ter um segredo do cliente. Se ela não tiver um, siga a etapa 2 de [Criar uma entidade de serviço (um aplicativo do Azure AD) no Azure AD](#create-a-service-principal-an-azure-ad-application-in-azure-ad). Esse segredo do cliente precisa ser adicionado como um parâmetro de entrada no script abaixo.

1. Use o script a seguir para criar um usuário da entidade de serviço do Azure AD *myapp* usando a entidade de serviço *AppSP*.

    - Substitua `<TenantId>` pelo `TenantId` coletado anteriormente.
    - Substitua `<ClientId>` pelo `ClientId` coletado anteriormente.
    - Substitua `<ClientSecret>` pelo segredo do cliente criado anteriormente.
    - Substitua `<server name>` pelo seu nome do servidor lógico do SQL. Se o nome do servidor for `myserver.database.windows.net`, substitua `<server name>` por `myserver`.
    - Substitua `<database name>` pelo nome do Banco de Dados SQL.

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret
    # AppSP is part of an Azure AD admin for the Azure SQL server below
    
    # Download latest  MSAL  - https://www.powershellgallery.com/packages/MSAL.PS
    Import-Module MSAL.PS
    
    $tenantId = "<TenantId>"   # tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   # AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   # Client secret for AppSP 
    $scopes = "https://database.windows.net/.default" # The end-point
    
    $result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -ClientSecret (ConvertTo-SecureString $clientSecret -AsPlainText -Force) -TenantId $tenantId -Scopes $scopes
    
    $Tok = $result.AccessToken
    #Write-host "token"
    $Tok
      
    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    $DatabaseName = "<database name>"     # Azure SQL database name
    
    Write-Host "Create SQL connection string"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()
    ``` 

    Como alternativa, você pode usar o exemplo de código no blog, [Autenticação de entidade de serviço do Azure AD para BD SQL – exemplo de código](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467). Modifique o script para executar uma instrução DDL `CREATE USER [myapp] FROM EXTERNAL PROVIDER`. É possível usar o mesmo script para criar um usuário comum do Azure AD em um grupo no Banco de Dados SQL.

    
2. Verifique se o usuário *myapp* existe no banco de dados, executando o seguinte comando:

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    Você deverá ver uma saída semelhante a:

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>Próximas etapas

- [Entidade de serviço do Azure Active Directory com o SQL do Azure](authentication-aad-service-principal.md)
- [O que são identidades gerenciadas para recursos do Azure?](../../active-directory/managed-identities-azure-resources/overview.md)
- [Como usar identidades gerenciadas para o Serviço de Aplicativo e o Azure Functions](../../app-service/overview-managed-identity.md)
- [Autenticação de entidade de serviço do Azure AD para o BD SQL – exemplo de código](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)
- [Objetos de entidade de serviço e aplicativo no Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)
- [Criar uma entidade de serviço do Azure com o Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
- [Função Leitores de Diretório no Azure Active Directory para o SQL do Azure](authentication-aad-directory-readers-role.md)