---
title: Migrar usuários e grupos do Windows do SQL Server para a instância gerenciada de SQL usando o T-SQL
description: Aprender como migrar usuários e grupos do Windows em uma instância do SQL Server para a Instância Gerenciada de SQL do Azure
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: f2dd34ab7c6ee5be26836e4abb86960605ee44ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84708628"
---
# <a name="tutorial-migrate-windows-users-and-groups-in-a-sql-server-instance-to-azure-sql-managed-instance-using-t-sql-ddl-syntax"></a>Tutorial: Migrar usuários e grupos do Windows em uma instância do SQL Server para a Instância Gerenciada de SQL do Azure usando a sintaxe T-SQL DDL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

> [!NOTE]
> A sintaxe usada para migrar usuários e grupos para a instância gerenciada de SQL neste artigo está em **versão prévia pública**.

Este artigo orienta você ao longo do processo de migração de usuários e grupos locais do Windows no SQL Server para uma Instância Gerenciada de SQL do Azure usando a sintaxe T-SQL.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Criar logons para o SQL Server
> - Criar um banco de dados de teste para migração
> - Criar logons, usuários e funções
> - Fazer backup e restaurar o banco de dados para a MI (instância gerenciada) de SQL
> - Migrar os usuários manualmente para a MI usando a sintaxe ALTER USER
> - Testar a autenticação com os novos usuários mapeados

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, os seguintes pré-requisitos se aplicam:

- O domínio do Windows é federado com o Azure AD (Azure Active Directory).
- Acesso ao Active Directory para criar usuários/grupos.
- Um SQL Server existente no ambiente local.
- Uma instância gerenciada existente de SQL. Confira [Início Rápido: Criar uma Instância Gerenciada de SQL](instance-create-quickstart.md).
  - É preciso usar um `sysadmin` na Instância Gerenciada de SQL para criar logons do Azure AD.
- [Crie um administrador do Azure AD para a Instância Gerenciada de SQL](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).
- Você pode se conectar à Instância Gerenciada de SQL dentro da rede. Consulte os seguintes artigos para obter informações adicionais:
  - [Conectar seu aplicativo à Instância Gerenciada de SQL do Azure](connect-application-instance.md)
  - [Início Rápido: Configurar uma conexão ponto a site a uma Instância Gerenciada de SQL do Azure do local](point-to-site-p2s-configure.md)
  - [Configurar um ponto de extremidade público na Instância Gerenciada de SQL do Azure](public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>Sintaxe T-SQL DDL

Veja abaixo a sintaxe T-SQL DDL usada para dar suporte à migração de grupos e usuários locais do Windows de uma instância do SQL Server para uma Instância Gerenciada de SQL com autenticação do Azure AD.

```sql
-- For individual Windows users with logins
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com];

--For individual groups with logins
ALTER USER [domainName\groupName] WITH LOGIN=[groupName]
```

## <a name="arguments"></a>Argumentos

_domainName_</br>
Especifica o nome de domínio do usuário.

_userName_</br>
Especifica o nome do usuário identificado dentro do banco de dados.

_= loginName\@domainName.com_</br>
Remapeia um usuário para o logon do Azure AD

_groupName_</br>
Especifica o nome do grupo identificado dentro do banco de dados.

## <a name="part-1-create-logins-in-sql-server-for-windows-users-and-groups"></a>Parte 1: Criar logons no SQL Server para usuários e grupos do Windows

> [!IMPORTANT]
> A sintaxe a seguir cria um logon de usuário e grupo em seu SQL Server. Você precisará certificar-se de que o usuário e o grupo existam dentro de seu AD (Active Directory) antes de executar a sintaxe abaixo. </br> </br>
> Usuários: testUser1, testGroupUser </br>
> Grupo: migration - testGroupUser precisa pertencer ao grupo de migração no AD

O exemplo a seguir cria um logon no SQL Server para uma conta chamada _testUser1_ no domínio _aadsqlmi_.

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases

use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;
go;

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser].
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;
go;


-- Check logins were created
select * from sys.server_principals;
go;
```

Criar um banco de dados para este teste.

```sql
-- Create a database called [migration]
create database migration
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Parte 2: Criar usuários e grupos do Windows e adicionar funções e permissões

Use a sintaxe a seguir para criar o usuário de teste.

```sql
use migration;  
go

-- Create Windows user [aadsqlmi\testUser1] with login
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1];
go
```

Verifique as permissões do usuário:

```sql
-- Check the user in the Metadata
select * from sys.database_principals;
go

-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions;
go
```

Crie uma função e atribua o usuário de teste a ela:

```sql
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole;
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole;
go

alter role UserMigrationRole add member [aadsqlmi\testUser1];
go
```

Use a consulta a seguir para exibir nomes de usuário atribuídos a uma função específica:

```sql
-- Display user name assigned to a specific role
SELECT DP1.name AS DatabaseRoleName,
   isnull (DP2.name, 'No members') AS DatabaseUserName
 FROM sys.database_role_members AS DRM
 RIGHT OUTER JOIN sys.database_principals AS DP1
   ON DRM.role_principal_id = DP1.principal_id
 LEFT OUTER JOIN sys.database_principals AS DP2
   ON DRM.member_principal_id = DP2.principal_id
WHERE DP1.type = 'R'
ORDER BY DP1.name;
```

Use a sintaxe a seguir para criar um grupo. Em seguida, adicione o grupo à função `db_owner`.

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration];
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration';
go

--Check the db_owner role for 'aadsqlmi\migration' group
select is_rolemember('db_owner', 'aadsqlmi\migration')
go
-- Output  ( 1 means YES)
```

Crie uma tabela de teste e adicione alguns dados usando a seguinte sintaxe:

```sql
-- Create a table and add data
create table test ( a int, b int);
go

insert into test values (1,10)
go

-- Check the table values
select * from test;
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-sql-managed-instance"></a>Parte 3: Fazer backup e restaurar o banco de dados do usuário individual em uma Instância Gerenciada de SQL

Crie um backup do banco de dados de migração usando o artigo [Copiar bancos de dados com backup e restauração](/sql/relational-databases/databases/copy-databases-with-backup-and-restore) ou use a seguinte sintaxe:

```sql
use master;
go
backup database migration to disk = 'C:\Migration\migration.bak';
go
```

Siga nosso [Início Rápido: Restaurar um banco de dados em uma Instância Gerenciada de SQL](restore-sample-database-quickstart.md).

## <a name="part-4-migrate-users-to-sql-managed-instance"></a>Parte 4: Migrar usuários para a Instância Gerenciada de SQL

Execute o comando ALTER USER para concluir o processo de migração na Instância Gerenciada de SQL.

1. Entre em sua Instância Gerenciada de SQL usando a conta do administrador do Azure AD para a Instância Gerenciada do SQL. Em seguida, crie seu logon do Azure AD na Instância Gerenciada de SQL usando a sintaxe a seguir. Para saber mais, confira [Tutorial: Segurança da Instância Gerenciada de SQL no Banco de Dados SQL do Azure usando as entidades de segurança do servidor (logons) do Azure AD](aad-security-configure-tutorial.md).

    ```sql
    use master
    go

    -- Create login for AAD user [testUser1@aadsqlmi.net]
    create login [testUser1@aadsqlmi.net] from external provider
    go

    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net]
    create login [migration] from external provider
    go

    --Check the two new logins
    select * from sys.server_principals
    go
    ```

1. Verifique a migração quanto ao banco de dados, a tabela e as entidades de segurança corretas.

    ```sql
    -- Switch to the database migration that is already restored for MI
    use migration;
    go

    --Check if the restored table test exist and contain a row
    select * from test;
    go

    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals;
    go
    -- the old user aadsqlmi\testUser1 should be there
    -- the old group aadsqlmi\migration should be there
    ```

1. Use a sintaxe ALTER USER para mapear o usuário local para o logon do Azure AD.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net];
    go

    -- Check the principal
    select * from sys.database_principals;
    go
    -- New user testUser1@aadsqlmi.net should be there instead
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    -- Check a specific role
    -- Display Db user name assigned to a specific role
    SELECT DP1.name AS DatabaseRoleName,
    isnull (DP2.name, 'No members') AS DatabaseUserName
    FROM sys.database_role_members AS DRM
    RIGHT OUTER JOIN sys.database_principals AS DP1
    ON DRM.role_principal_id = DP1.principal_id
    LEFT OUTER JOIN sys.database_principals AS DP2
    ON DRM.member_principal_id = DP2.principal_id
    WHERE DP1.type = 'R'
    ORDER BY DP1.name;
    ```

1. Use a sintaxe ALTER USER para mapear o grupo local para o logon do Azure AD.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration];
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals;
    go

    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    --Check the db_owner role for 'aadsqlmi\migration' user
    select is_rolemember('db_owner', 'migration')
    go
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Parte 5: Testar a autenticação do usuário ou grupo do Azure AD

Teste a autenticação da Instância Gerenciada de SQL usando o usuário mapeado anteriormente para o logon do Azure AD usando a sintaxe ALTER USER.

1. Faça logon na VM federada usando sua assinatura da Instância Gerenciada de SQL do Azure como `aadsqlmi\testUser1`
1. Usando o SSMS (SQL Server Management Studio), entre na sua Instância Gerenciada de SQL usando a autenticação **Integrada do Active Directory**, conectando-se ao banco de dados `migration`.
    1. Você também pode entrar usando as credenciais testUser1@aadsqlmi.net com a opção do SSMS **Active Directory – Universal com suporte para MFA**. No entanto, nesse caso, você não pode usar o mecanismo de logon único e deve digitar uma senha. Você não precisará usar uma VM federada para fazer logon na Instância Gerenciada de SQL.
1. Como parte do membro da função **SELECT**, você pode selecionar na tabela `test`

    ```sql
    Select * from test  --  and see one row (1,10)
    ```

Teste a autenticação em uma Instância Gerenciada de SQL usando um membro de um grupo do Windows `migration`. O usuário `aadsqlmi\testGroupUser` deve ter sido adicionado ao grupo `migration` antes da migração.

1. Faça logon na VM federada usando sua assinatura da Instância Gerenciada de SQL do Azure como `aadsqlmi\testGroupUser`
1. Usando o SSMS com a autenticação **Integrada do Active Directory**, conecte-se ao servidor de Instância Gerenciada de SQL do Azure e ao banco de dados `migration`
    1. Você também pode entrar usando as credenciais testGroupUser@aadsqlmi.net com a opção do SSMS **Active Directory – Universal com suporte para MFA**. No entanto, nesse caso, você não pode usar o mecanismo de logon único e deve digitar uma senha. Você não precisará usar uma VM federada para fazer logon na Instância Gerenciada de SQL.
1. Como parte da função de `db_owner`, você pode criar uma nova tabela.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```

> [!NOTE]
> Devido a um problema de design conhecido no Banco de Dados SQL do Azure, uma instrução para criar uma tabela executada como membro de um grupo falhará com o seguinte erro: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> A solução alternativa atual é criar uma tabela com um esquema existente no caso acima <dbo.New>

## <a name="next-steps"></a>Próximas etapas

[Tutorial: Migrar o SQL Server para a Instância Gerenciada de SQL do Azure offline usando DMS](../../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)
