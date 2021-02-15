---
title: Criar usuários convidados do Azure AD
description: Como criar usuários convidados do Azure AD e defini-los como administrador do Azure AD sem usar grupos do Azure AD no banco de dados SQL do Azure, Azure SQL Instância Gerenciada e Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 7a4d9fb9f803a497e84fa189d9a89c2d9097bb70
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675048"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>Criar usuários convidados do Azure AD e definir como um administrador do Azure AD

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Esse artigo está em **versão prévia pública** .

Os usuários convidados no Azure Active Directory (AD do Azure) são usuários que foram importados para o Azure AD atual de outros diretórios ativos do Azure ou fora dele. Por exemplo, os usuários convidados podem incluir usuários de outros diretórios ativos do Azure ou de contas como *\@ Outlook.com* , *\@ hotmail.com* , *\@ Live.com* ou *\@ gmail.com* . Este artigo demonstrará como criar um usuário convidado do Azure AD e definirá esse usuário como um administrador do Azure AD para o servidor lógico do SQL do Azure, sem a necessidade de fazer com que o usuário convidado faça parte de um grupo dentro do Azure AD.

## <a name="feature-description"></a>Descrição do recurso

Esse recurso levanta a limitação atual que só permite que usuários convidados se conectem ao banco de dados SQL do Azure, ao SQL Instância Gerenciada ou ao Azure Synapse Analytics quando são membros de um grupo criado no Azure AD. O grupo precisava ser mapeado para um usuário manualmente usando a instrução [Create User (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) em um determinado banco de dados. Depois que um usuário de banco de dados tiver sido criado para o grupo do Azure AD que contém o usuário convidado, o usuário convidado poderá entrar no banco de dados usando Azure Active Directory com a autenticação MFA. Como parte dessa **Visualização pública** , os usuários convidados podem ser criados e se conectar diretamente ao banco de dados SQL, SQL instância gerenciada ou Azure Synapse sem a necessidade de adicioná-los a um grupo do Azure ad primeiro e, em seguida, criar um usuário de banco de dados para esse grupo do Azure AD.

Como parte desse recurso, você também pode definir o usuário convidado do Azure AD diretamente como um administrador do AD para o servidor lógico do SQL do Azure. A funcionalidade existente em que o usuário convidado pode fazer parte de um grupo do Azure AD e esse grupo pode ser definido como o administrador do Azure AD para o servidor lógico do Azure SQL não é afetado. Os usuários convidados no banco de dados que fazem parte de um grupo do Azure AD também não são afetados por essa alteração.

Para obter mais informações sobre o suporte existente para usuários convidados usando grupos do Azure AD, consulte [usando a autenticação de Azure Active Directory multifator](authentication-mfa-ssms-overview.md).

## <a name="prerequisite"></a>Pré-requisito

- [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) module ou superior é necessário ao usar o PowerShell para definir um usuário convidado como um administrador do Azure ad para o servidor lógico do SQL do Azure.

## <a name="create-database-user-for-azure-ad-guest-user"></a>Criar usuário de banco de dados para usuário convidado do Azure AD 

Siga estas etapas para criar um usuário de banco de dados usando um usuário convidado do Azure AD.

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>Criar usuário convidado no banco de dados SQL e Azure Synapse

1. Verifique se o usuário convidado (por exemplo, `user1@gmail.com` ) já foi adicionado ao Azure AD e se um administrador do Azure ad foi definido para o servidor de banco de dados. Ter um administrador do Azure AD é necessário para a autenticação Azure Active Directory.

1. Conecte-se ao banco de dados SQL como o administrador do Azure AD ou um usuário do Azure AD com permissões SQL suficientes para criar usuários e execute o comando abaixo no banco de dados em que o usuário convidado precisa ser adicionado:

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Agora deve haver um usuário de banco de dados criado para o usuário convidado `user1@gmail.com` .

1. Execute o comando abaixo para verificar se o usuário do banco de dados foi criado com êxito:

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. Desconecte e entre no banco de dados como o usuário convidado `user1@gmail.com` usando o [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) usando o método de autenticação **Azure Active Directory-universal com MFA** . Para obter mais informações, consulte [usando autenticação multifator Azure Active Directory](authentication-mfa-ssms-overview.md).

### <a name="create-guest-user-in-sql-managed-instance"></a>Criar usuário convidado no SQL Instância Gerenciada

> [!NOTE]
> O SQL Instância Gerenciada dá suporte a logons para usuários do Azure AD, bem como usuários de banco de dados independente do Azure AD. As etapas a seguir mostram como criar um logon e um usuário para um usuário convidado do Azure AD no SQL Instância Gerenciada. Você também pode optar por criar um [usuário de banco de dados independente](/sql/relational-databases/security/contained-database-users-making-your-database-portable) no SQL instância gerenciada usando o método na seção [criar usuário convidado no banco de dados SQL e no Azure Synapse](#create-guest-user-in-sql-database-and-azure-synapse) .

1. Verifique se o usuário convidado (por exemplo, `user1@gmail.com` ) já foi adicionado ao Azure AD e se um administrador do Azure ad foi definido para o SQL instância gerenciada Server. Ter um administrador do Azure AD é necessário para a autenticação Azure Active Directory.

1. Conecte-se ao SQL Instância Gerenciada Server como o administrador do Azure AD ou um usuário do Azure AD com permissões SQL suficientes para criar usuários e execute o seguinte comando no `master` banco de dados para criar um logon para o usuário convidado:

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Agora deve haver um logon criado para o usuário convidado `user1@gmail.com` no banco de `master` dados.

1. Execute o comando abaixo para verificar se o logon foi criado com êxito:

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. Execute o comando abaixo no banco de dados em que o usuário convidado precisa ser adicionado: 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. Agora deve haver um usuário de banco de dados criado para o usuário convidado `user1@gmail.com` .

1. Desconecte e entre no banco de dados como o usuário convidado `user1@gmail.com` usando o [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) usando o método de autenticação **Azure Active Directory-universal com MFA** . Para obter mais informações, consulte [usando autenticação multifator Azure Active Directory](authentication-mfa-ssms-overview.md).

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>Configurando um usuário convidado como um administrador do Azure AD

Siga estas etapas para definir um usuário convidado do Azure AD como o administrador do Azure AD para o servidor lógico do SQL.

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>Definir o administrador do Azure AD para o banco de dados SQL e o Azure Synapse

1. Verifique se o usuário convidado (por exemplo, `user1@gmail.com` ) já foi adicionado ao Azure AD.

1. Execute o seguinte comando do PowerShell para adicionar o usuário convidado como o administrador do Azure AD para seu servidor lógico do SQL do Azure:

    - Substitua `<ResourceGroupName>` pelo nome do grupo de recursos do Azure que contém o servidor lógico do SQL do Azure.
    - Substitua `<ServerName>` pelo nome do servidor lógico do SQL do Azure. Se o nome do servidor for `myserver.database.windows.net`, substitua `<Server Name>` por `myserver`.
    - Substitua `<DisplayNameOfGuestUser>` pelo seu nome de usuário convidado.

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    Você também pode usar o comando CLI do Azure [AZ SQL Server ad-admin](/cli/azure/sql/server/ad-admin) para definir o usuário convidado como um administrador do Azure ad para seu servidor lógico do SQL do Azure.

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>Definir o administrador do Azure AD para o SQL Instância Gerenciada

1. Verifique se o usuário convidado (por exemplo, `user1@gmail.com` ) já foi adicionado ao Azure AD.

1. Vá para a [portal do Azure](https://portal.azure.com)e vá para o recurso de **Azure Active Directory** . Em **gerenciar** , vá para o painel **usuários** . Selecione o usuário convidado e registre o `Object ID` . 

1. Execute o seguinte comando do PowerShell para adicionar o usuário convidado como o administrador do Azure AD para seu Instância Gerenciada do SQL:

    - Substitua `<ResourceGroupName>` pelo nome do grupo de recursos do Azure que contém o instância gerenciada do SQL.
    - Substitua `<ManagedInstanceName>` pelo seu nome de instância gerenciada do SQL.
    - Substitua `<DisplayNameOfGuestUser>` pelo seu nome de usuário convidado.
    - Substitua `<AADObjectIDOfGuestUser>` pelo `Object ID` reunido anteriormente.

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    Você também pode usar o comando CLI do Azure [AZ SQL mi ad-admin](/cli/azure/sql/mi/ad-admin) para definir o usuário convidado como um administrador do Azure ad para o SQL instância gerenciada.

## <a name="limitations"></a>Limitações

Há uma limitação no portal do Azure que impede a seleção de um usuário convidado do Azure AD como o administrador do Azure AD para SQL Instância Gerenciada. Para contas de convidado fora do Azure AD, como *\@ Outlook.com* , *\@ hotmail.com* , *\@ Live.com* ou *\@ gmail.com* , o seletor de administrador do AD mostra essas contas, mas elas ficam esmaecidas e não podem ser selecionadas. Use os comandos do [PowerShell ou da CLI](#setting-a-guest-user-as-an-azure-ad-admin) listados acima para definir o administrador do Azure AD. Como alternativa, um grupo do Azure AD que contém o usuário convidado pode ser definido como o administrador do Azure AD para o Instância Gerenciada do SQL.

Essa funcionalidade será habilitada para o SQL Instância Gerenciada antes da disponibilidade geral desse recurso.

## <a name="next-steps"></a>Próximas etapas

- [Configurar e gerenciar a autenticação do Azure AD com o Azure SQL](authentication-aad-configure.md)
- [Usando a autenticação de Azure Active Directory multifator](authentication-mfa-ssms-overview.md)
- [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)