---
title: Configurar Autenticação do Azure Active Directory – SQL| Microsoft Docs
description: Saiba como se conectar ao Banco de Dados SQL, à Instância Gerenciada e ao SQL Data Warehouse usando a Autenticação do Azure Active Directory – após a configuração do Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: eb96b60593dc660682c6376c0e0133be8f371176
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348688"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configurar e gerenciar autenticação do Azure Active Directory com SQL

Este artigo mostra como criar e popular o Azure AD e, em seguida, usar o Azure AD com [Banco de Dados SQL](sql-database-technical-overview.md), [Instância Gerenciada](sql-database-managed-instance.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para obter uma visão geral, consulte [Autenticação do Azure Active Directory](sql-database-aad-authentication.md).

> [!NOTE]
> Este artigo se aplica ao SQL Server do Azure e aos bancos de dados SQL Database e SQL Data Warehouse criados no servidor SQL do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse.
> [!IMPORTANT]  
> Não há suporte para conectar ao SQL Server em execução em uma VM do Azure usando uma conta do Azure Active Directory. Use um conta de domínio do Active Directory.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

## <a name="create-and-populate-an-azure-ad"></a>Criar e popular um Azure AD

Crie um Azure AD e popule-o com usuários e grupos. O Azure AD pode ser o domínio gerenciado pelo Azure AD inicial. O Azure AD também pode ser um Active Directory Domain Services local federado com o Azure AD.

Para obter mais informações, consulte [Integrando suas identidades locais no Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Adicionar seu próprio nome de domínio ao Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [O Microsoft Azure agora dá suporte à federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administrando seu diretório do Azure AD](../active-directory/fundamentals/active-directory-administer.md), [Gerenciar o Azure AD usando o Windows PowerShell](/powershell/azure/overview) e [Portas e protocolos necessários para a identidade híbrida](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associar ou adicionar uma assinatura do Azure ao Azure Active Directory

1. Associe sua assinatura do Azure ao Azure Active Directory tornando o diretório um diretório confiável para a assinatura do Azure que hospeda o banco de dados. Para mais detalhes, consulte [Como as assinaturas do Azure são associadas ao Microsoft Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
2. Use o seletor de diretório no portal do Azure para alternar para a assinatura associada ao domínio.

   **Informações adicionais:** Cada assinatura do Azure tem uma relação de confiança com uma instância do Azure AD. Isso significa que ela confia que esse diretório autentique usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas uma única assinatura confia em apenas um diretório. Essa relação de confiança que uma assinatura tem com um diretório é diferente da relação que uma assinatura tem com todos os outros recursos no Azure (sites, bancos de dados e assim por diante), que são mais similares a recursos filho de uma assinatura. Se uma assinatura expira, o acesso a esses outros recursos associados à assinatura também pára. Mas o diretório permanece no Azure e você pode associar outra assinatura a ele, além de continuar a gerenciar os usuários do diretório. Para obter mais informações sobre recursos, consulte [Noções básicas sobre o acesso a recursos no Azure](../active-directory/active-directory-b2b-admin-add-users.md). Para saber mais sobre essa relação de confiança, consulte relação [Como associar ou adicionar uma assinatura do Azure ao Microsoft Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Criar um administrador do Azure AD para o Azure SQL Server

Cada Servidor do Azure SQL Server (que hospeda um Banco de dados SQL ou SQL Data Warehouse) começa com uma única conta de administrador do servidor, que é o administrador de todo o Azure SQL Server. Um segundo administrador do SQL Server deve ser criado, que é uma conta do Azure AD. Essa entidade de segurança é criada como um usuário de banco de dados independente no banco de dados mestre. Como administradores, as contas de administrador do servidor são membros da função **db_owner** em todos os bancos de dados de usuários e inserem cada banco de dados de usuário como o usuário **dbo**. Para obter mais informações sobre as contas do administrador do servidor, veja [Gerenciando Bancos de Dados e Logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).

Ao usar o Azure Active Directory com a Replicação Geográfica, o administrador do Azure Active Directory deverá ser configurado para os servidores primários e secundários. Se um servidor não tiver um administrador do Azure Active Directory, os usuários e logons do Azure Active Directory obterão um erro de servidor "Não é possível conectar-se".

> [!NOTE]
> Usuários que não são baseados em uma conta do Azure AD (incluindo a conta de administrador do Azure SQL Server) não podem criar usuários baseados no Azure AD porque eles não têm permissão para validar os usuários do banco de dados proposto com o Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Provisionar um administrador do Azure Active Directory para a Instância Gerenciada

> [!IMPORTANT]
> Apenas siga estas etapas se estiver provisionando uma Instância Gerenciada. Esta operação só pode ser executada pelo Administrador Global/da Empresa no Azure AD. As etapas a seguir descrevem o processo de concessão de permissões para usuários com privilégios diferentes no diretório.

A Instância Gerenciada precisa de permissões de leitura no Azure AD para executar com êxito tarefas como autenticação de usuários por meio da associação a um grupo de segurança ou criação de novos usuários. Para que isso funcione, você precisa conceder permissões de leitura no Azure AD à Instância Gerenciada. Há duas maneiras de fazer isso: no Portal e no PowerShell. As etapas a seguir mostram os dois métodos.

1. No portal do Azure, no canto superior direito, selecione a conexão para exibir uma lista suspensa dos Active Directories possíveis.
2. Escolha o Active Directory correto como o AD do Azure padrão.

   Esta etapa vincula a assinatura associada ao Active Directory à Instância Gerenciada, garantindo que a mesma assinatura é usada para o Azure AD e a Instância Gerenciada.
3. Navegue para a Instância Gerenciada e selecione uma que você deseja usar para a integração do Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Selecione a faixa na parte superior da página de administração do Active Directory e conceda permissão ao usuário atual. Se você estiver conectado como Administrador de Empresa/Global no Azure AD, poderá fazê-lo no portal do Azure ou usando o PowerShell com o script abaixo.

    ![grant permissions-portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the Managed Instance.
    # Can be executed only by a "Company Administrator" or "Global Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

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
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null)
    {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1))
    {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null)
    {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else
    {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Depois que a operação for concluída com êxito, a seguinte notificação será exibida no canto superior direito:

    ![success](./media/sql-database-aad-authentication/success.png)

6. Agora você pode escolher o administrador do Azure AD para sua Instância Gerenciada. Para isso, na página de administração do Active Directory, selecione o comando **Definir administrador**.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Na página de administração do AAD, pesquise um usuário, selecione o usuário ou grupo para ser um administrador e, em seguida, escolha **Selecionar**.

   A página de administração do Active Directory mostra todos os membros e grupos do Active Directory. Usuários ou grupos que estejam esmaecidos não poderão ser selecionados porque não têm suporte como administradores do Azure AD. Consulte a lista de administradores com suporte em [Limitações e recursos do Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). O RBAC (controle de acesso baseado em função) aplica-se somente ao portal do Azure e não é propagado para o SQL Server.

    ![add-admin](./media/sql-database-aad-authentication/add-admin.png)

8. Na parte superior da página Administrador do Active Directory, selecione **Salvar**.

    ![salvar](./media/sql-database-aad-authentication/save.png)

    O processo de alteração do administrador pode levar vários minutos. Em seguida, o novo administrador é exibido na caixa de administração do Active Directory.

Após provisionar um administrador do Azure AD para a Instância Gerenciada, você poderá começar a criar entidades de segurança do servidor do Azure AD (logons) (**versão prévia pública**) com a sintaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>. Para obter mais informações, consulte [Visão geral da Instância Gerenciada](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Para remover um Administrador mais tarde, na parte superior da página Administrador do Active Directory, selecione **Remover administrador** e, em seguida, selecione **Salvar**.

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Provisionar um administrador do Azure Active Directory para o servidor do Banco de Dados SQL do Azure

> [!IMPORTANT]
> Apenas execute estas etapas se estiver provisionando um servidor do Banco de Dados SQL do Azure ou um Data Warehouse.

Os dois procedimentos a seguir mostram como provisionar um administrador do Azure Active Directory para seu servidor do Azure SQL Server no Portal do Azure e usando o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. No [portal do Azure](https://portal.azure.com/), no canto superior direito, selecione a conexão para exibir uma lista suspensa dos Active Directories possíveis. Escolha o Active Directory correto como o AD do Azure padrão. Esta etapa vincula o Active Directory associado à assinatura ao Azure SQL Server, verificando se a mesma assinatura é usada tanto para o Azure AD quanto para o SQL Server. (O SQL Server do Azure pode estar hospedando o Banco de Dados SQL do Azure ou SQL Data Warehouse do Microsoft Azure.) ![choose-ad][8]

2. Na faixa à esquerda, selecione **Todos os serviços**e no tipo de filtro no **SQL server**. Selecione **SQL Servers**.

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > Nessa página, antes você selecionar **SQL servers**, você pode selecionar a **estrela** ao lado do nome para estabelecer a categoria como *favorito* e adicionar **SQL servers**à barra de navegação à esquerda.

3. Na página **SQL Server**, selecione **Administrador do Active Directory**.
4. Na página **Administrador do Active Directory**, selecione **Definir administrador**.  ![selecionar Active Directory](./media/sql-database-aad-authentication/select-active-directory.png)  

5. Na página **Adicionar administrador**, pesquise um usuário, selecione o usuário ou grupo que será um administrador e, em seguida, selecione **Selecionar**. A página de administração do Active Directory mostra todos os membros e grupos do Active Directory. Usuários ou grupos que estão esmaecidos não podem ser selecionados porque eles não têm suporte como administradores do AD do Azure. (Consulte a lista de administradores com suporte na seção **Limitações e recursos do Azure AD** de [Usar o Azure Active Directory para autenticação com o Banco de Dados SQL ou o SQL Data Warehouse](sql-database-aad-authentication.md).) O RBAC (controle de acesso baseado em função) aplica-se somente ao portal e não é propagado para o SQL Server.
    ![selecionar administrador](./media/sql-database-aad-authentication/select-admin.png)  

6. Na parte superior da página **Administrador do Active Directory**, selecione **SALVAR**.
    ![salvar administrador](./media/sql-database-aad-authentication/save-admin.png)

O processo de alteração do administrador pode levar vários minutos. O novo administrador aparece na caixa **Administrador do Active Directory** .

   > [!NOTE]
   > Ao configurar o administrador do Azure AD, o novo nome de administrador (usuário ou grupo) não pode já estar presente no banco de dados mestre virtual como um usuário de autenticação do SQL Server. Se presente, a configuração de administração do AD do Azure falhará, revertendo sua criação e indicando que esse administrador (nome) já existe. Como esse usuário de autenticação do SQL Server não é parte do Azure AD, qualquer esforço para se conectar ao servidor usando a autenticação do Azure AD falhará.

Para remover um Administrador mais tarde, na parte superior da página **Administrador do Active Directory**, selecione **Remover administrador** e, em seguida, selecione **Salvar**.

### <a name="powershell"></a>PowerShell

Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview). Para provisionar um administrador do AD do Azure, execute os seguintes comandos do Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets usados para provisionar e gerenciar o administrador do AD do Azure:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Provisiona um administrador do Azure Active Directory para o Azure SQL Server ou o SQL Data Warehouse do Azure. (Precisa ser da assinatura atual.) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Remove um administrador do Azure Active Directory para o Azure SQL Server ou para o SQL Data Warehouse do Azure. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Retorna informações sobre um administrador do Azure Active Directory atualmente configurado para o Azure SQL Server ou para o SQL Data Warehouse do Azure. |

Use o comando get-help do PowerShell para ver mais informações sobre cada um desses comandos, por exemplo, ``get-help Set-AzSqlServerActiveDirectoryAdministrator``.

O seguinte script provisiona um grupo de administradores do Azure AD nomeado **DBA_Group** (ID de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para o servidor **demo_server** em um grupo de recursos nomeado **Group-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

O parâmetro de entrada **DisplayName** aceita o nome de exibição do Azure AD ou o Nome UPN. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Para grupos do AD do Azure, há suporte apenas para o nome de exibição do AD do Azure.

> [!NOTE]
> O comando ```Set-AzSqlServerActiveDirectoryAdministrator``` do Azure PowerShell não impede que você provisione administradores do Azure AD para usuários sem suporte. Um usuário para o qual não há suporte pode ser provisionado, mas não pode se conectar a um banco de dados.

O seguinte exemplo usa a **ObjectID**opcional:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> A **ObjectID** do Azure AD é necessária quando o **DisplayName** não é exclusivo. Para recuperar os valores de **ObjectID** e **DisplayName**, use a seção do Active Directory do Portal Clássico do Azure e exiba as propriedades de um usuário ou grupo.

O exemplo a seguir retorna informações sobre o administrador atual do Azure AD para o Azure SQL Server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

O seguinte exemplo remove um administrador do AD do Azure:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Você também pode provisionar um administrador do Azure Active Directory usando as APIs REST. Para obter mais informações, confira [Referência da API REST de Gerenciamento de Serviços e Operações do Banco de Dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/)

### <a name="cli"></a>CLI  

É possível provisionar um administrador do Azure AD chamando os seguintes comandos da CLI:

| Comando | Descrição |
| --- | --- |
|[az sql server ad-admin create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) |Provisiona um administrador do Azure Active Directory para o Azure SQL Server ou o SQL Data Warehouse do Azure. (Precisa ser da assinatura atual.) |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) |Remove um administrador do Azure Active Directory para o Azure SQL Server ou para o SQL Data Warehouse do Azure. |
|[az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) |Retorna informações sobre um administrador do Azure Active Directory atualmente configurado para o Azure SQL Server ou para o SQL Data Warehouse do Azure. |
|[az sql server ad-admin update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) |Atualiza o administrador do Active Directory para um Azure SQL Server ou SQL Data Warehouse do Azure. |

Para obter mais informações sobre comandos da CLI, consulte [SQL – az sql](https://docs.microsoft.com/cli/azure/sql/server).  

## <a name="configure-your-client-computers"></a>Configurar os computadores cliente

Em todos os computadores cliente, dos quais seus aplicativos ou usuários se conectam ao Banco de Dados SQL do Azure ou ao SQL Data Warehouse do Azure usando identidades do Azure AD, você deve instalar o software a seguir:

- .NET Framework 4.6 ou posterior de [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- A Biblioteca de Autenticação do Active Directory do Azure para SQL Server (**ADALSQL.DLL**) está disponível em vários idiomas (tanto x86 quanto amd64) no centro de download da [Biblioteca de Autenticação do Microsoft Active Directory para Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

Você pode atender a esses requisitos:

- A instalação do [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ou o [SQL Server Data Tools para Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) atende ao requisito do .NET Framework 4.6.
- O SSMS instala a versão x86 do **ADALSQL.DLL**.
- O SSDT instala a versão amd64 do **ADALSQL.DLL**.
- O Visual Studio mais recente dos [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) atende ao requisito do .NET Framework 4.6, mas não instala a versão amd64 necessária do **ADALSQL.DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure

>[!IMPORTANT]
>A Instância Gerenciada atualmente oferece suporte a entidades de segurança do servidor do Azure AD (logons) (**versão prévia pública**), que permite criar logons de usuários, grupos ou aplicativos do Azure AD. As entidades de segurança do servidor do Azure AD (logons) fornecem a capacidade de autenticação na Instância Gerenciada sem exigir que os usuários do banco de dados sejam criados como um usuário de banco de dados independente. Para obter mais informações, consulte [Visão geral da Instância Gerenciada](sql-database-managed-instance.md#azure-active-directory-integration). Para obter a sintaxe na criação de entidades de segurança do servidor do Azure AD (logons), consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

A autenticação do Active Directory do Azure exige que os usuários do banco de dados sejam criados como usuários do banco de dados independente. Um usuário de banco de dados independente com base em uma identidade do AD do Azure é um usuário de banco de dados que não tem um logon no banco de dados mestre e que mapeia para uma identidade no diretório do AD do Azure que está associada ao banco de dados. A identidade do AD do Azure pode ser uma conta de usuário individual ou um grupo. Para saber mais sobre usuários de bancos de dados independentes, veja [Usuários do bancos de dados independentes - Tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Os usuários do banco de dados (com exceção dos administradores) não podem ser criados usando o portal do Azure. Funções de RBAC não são propagadas para o SQL Server, para o Banco de Dados SQL ou para o SQL Data Warehouse. As funções RBAC do Azure são usadas para gerenciar Recursos do Azure e não se aplicam às permissões de banco de dados. Por exemplo, a função **Colaborador do SQL Server** não concede acesso para se conectar ao Banco de Dados SQL ou ao SQL Data Warehouse. A permissão de acesso deve ser concedida diretamente no banco de dados usando instruções Transact-SQL.
> [!WARNING]
> Não há suporte para caracteres especiais como dois-pontos `:` ou E comercial `&`, quando incluídos como nomes de usuário nas instruções T-SQL CREATE LOGIN e CREATE USER.

Para criar um usuário de banco de dados independente baseado no Azure AD (que não seja o administrador do servidor proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD como um usuário com, no mínimo, a permissão **ALTER ANY USER** . Em seguida, use a sintaxe Transact-SQL a seguir:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*nome_UPN_Azure_AD* pode ser o nome UPN de um usuário do Azure AD ou o nome de exibição de um grupo do Azure AD.

**Exemplos:** Para criar um usuário de banco de dados independente que representa um usuário de domínio federado ou gerenciado pelo Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Para criar um usuário de banco de dados independente que represente um grupo de domínio do AD do Azure AD ou federado, forneça o nome para exibição de um grupo de segurança:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Para criar um usuário de banco de dados independente que representa um aplicativo que se conectará usando um token do Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!TIP]
> Não é possível criar um usuário diretamente de um Azure Active Directory que não seja o Azure Active Directory associado à sua assinatura do Azure. No entanto, os membros de outros Active Directories que são os usuários importados no Active Directory associado (conhecidos como usuários externos) podem ser adicionados a um grupo do Active Directory no Active Directory locatário. Ao criar um usuário de banco de dados independente para esse grupo AD, os usuários do Active Directory externo podem obter acesso ao Banco de Dados SQL.

Para obter mais informações sobre como criar usuários de banco de dados independente baseados em identidades do Active Directory do Azure, consulte [CRIAR USUÁRIO (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Removendo o administrador do Azure Active Directory para o Azure SQL Server impede que qualquer usuário da autenticação do Azure AD se conecte ao servidor. Se for necessário, os usuários do Azure AD inutilizáveis poderão ser removidos manualmente por um administrador do Banco de Dados SQL.
> [!NOTE]
> Se você receber um **Tempo Limite de Conexão Expirado**, talvez seja necessário definir o parâmetro `TransparentNetworkIPResolution` da cadeia de conexão como falso. Para obter mais informações, consulte [Problema de tempo limite de conexão com o .NET Framework 4.6.1 – TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Quando você cria um usuário de banco de dados, o usuário recebe a permissão **CONNECT** e pode se conectar a esse banco de dados como membro da função **PUBLIC**. Inicialmente, as únicas permissões disponíveis para o usuário são as permissões concedidas à função **PUBLIC** ou as permissões concedidas a quaisquer grupos do Azure AD dos quais esse usuário é membro. A partir do momento que você provisionar um usuário de banco de dados independente baseado no AD do Azure, você pode conceder ao usuário permissões adicionais, do mesmo modo que você concede permissões para qualquer outro tipo de usuário. Normalmente, conceda permissões para funções de banco de dados e adicione usuários a funções. Para saber mais, confira [Noções básicas sobre permissões do Mecanismo de Banco de Dados](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obter mais informações sobre funções especiais do Banco de dados SQL, veja [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).
Uma conta de usuário de domínio federado que é importado para um domínio gerenciado como um usuário externo deve usar a identidade do domínio gerenciado.

> [!NOTE]
> Usuários do AD do Azure são marcados nos metadados do banco de dados com tipo E (EXTERNAL_USER) e para grupos com o tipo X (EXTERNAL_GROUPS). Para obter mais informações, consulte [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Conectar-se ao banco de dados do usuário ou data warehouse usando SSMS ou SSDT  

Para confirmar que o administrador do Azure AD está configurado corretamente, conecte-se ao banco de dados **mestre** usando a conta de administrador do Azure AD.
Para provisionar um usuário de banco de dados independente com base no Azure AD (que não seja o administrador do servidor que é o proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD que tenha acesso ao banco de dados.

> [!IMPORTANT]
> O suporte à autenticação do Azure Active Directory está disponível com o [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e o [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) no Visual Studio 2015. A versão de agosto de 2016 do SSMS também inclui suporte para Universal autenticação do Active Directory, que permite aos administradores exigir Autenticação Multifator utilizando uma chamada telefônica, mensagem de texto, os cartões inteligentes com PIN ou a notificação de aplicativo móvel.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Usando uma identidade do Azure AD para se conectar usando SSMS ou SSDT

Os procedimentos a seguir mostram como se conectar a um Banco de Dados SQL com uma identidade do Azure AD usando o SQL Server Management Studio ou as Ferramentas de Banco de Dados do SQL Server.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Use este método se você efetuou logon no Windows usando suas credenciais do Azure Active Directory por meio de um domínio federado.

1. Inicie o Management Studio ou o Data Tools e, na caixa de diálogo **Conectar ao Servidor** (ou **Conectar ao Mecanismo de Banco de Dados**), na caixa **Autenticação**, selecione **Active Directory - Integrado**. Nenhuma senha é necessária ou pode ser inserida porque suas credenciais existentes serão apresentadas para a conexão.

    ![Selecione Autenticação Integrada do AD][11]
2. Selecione o botão **Opções** e, na página **Propriedades de Conexão**, na caixa **Conectar ao banco de dados**, digite o nome do banco de dados de usuário ao qual você deseja se conectar. (A opção **ID de locatário ou o nome de domínio do AD**) tem suporte apenas para opções de **conexão Universal com MFA**, caso contrário, ela fica acinzentada).  

    ![Selecione o nome do banco de dados][13]

## <a name="active-directory-password-authentication"></a>Autenticação de senha no Active Directory

Use esse método ao se conectar com um nome de entidade do AD do Azure usando o domínio gerenciado pelo Azure AD. Você também pode usá-lo para contas federadas sem acesso ao domínio, por exemplo, ao trabalhar remotamente.

Use esse método para autenticação no BD SQL/DW com Azure AD para usuários do Azure AS nativo ou federado. Um usuário nativo é explicitamente criado no Azure AD e é autenticado usando nome de usuário e senha, enquanto um usuário federado é um usuário do Windows cujo domínio é federado com o Azure AD. O último método (usando usuário e senha) poderá ser usado quando um usuário quiser usar a credencial do Windows, mas o computador local não estiver associado ao domínio (por exemplo, usando um acesso remoto). Nesse caso, um usuário do Windows poderá indicar a conta de domínio e senha e poderá autenticar-se no BD SQL/DW usando credenciais federadas.

1. Inicie o Management Studio ou o Data Tools e, na caixa de diálogo **Conectar ao Servidor** (ou **Conectar ao Mecanismo de Banco de Dados**), na caixa **Autenticação**, selecione **Active Directory - Senha**.
2. Na caixa **nome de usuário** , digite o nome de usuário do Azure Active Directory no **formato\@nome**de usuário Domain.com. Os nomes de usuário devem ser uma conta do Azure Active Directory ou uma conta de um federado de domínio com o Azure Active Directory.
3. Na caixa **Senha** , digite sua senha de usuário para a conta do Azure Active Directory ou conta de domínio federado.

    ![Selecione Autenticação de Senha do AD][12]
4. Selecione o botão **Opções** e, na página **Propriedades de Conexão**, na caixa **Conectar ao banco de dados**, digite o nome do banco de dados de usuário ao qual você deseja se conectar. (Confira o gráfico na opção anterior.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Usando uma identidade do Azure AD para conectar-se de um aplicativo cliente

Os procedimentos a seguir mostram como se conectar a um Banco de Dados SQL com uma identidade do Azure AD de um aplicativo cliente.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Para usar a autenticação integrada do Windows, o Active Directory de seu domínio deve ser federado com o Azure Active Directory. Seu aplicativo cliente (ou um serviço) conectando-se ao banco de dados deve estar em execução em uma máquina ingressada no domínio com credenciais de domínio do usuário.

Para conectar-se a um banco de dados usando a autenticação integrada e uma identidade do AD do Azure, a palavra-chave de Autenticação na cadeia de conexão de banco de dados deve ser definida como Integrada ao Active Directory. O exemplo de código em C# a seguir usa ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Para a conexão ao Banco de Dados SQL do Azure, não há suporte para a palavra-chave de cadeia de conexão ``Integrated Security=True``. Ao realizar uma conexão ODBC, você precisará remover espaços e configurar a autenticação para 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticação de senha do Active Directory

Para conectar-se a um banco de dados usando a autenticação integrada e uma identidade do Azure AD, a palavra-chave de Autenticação deve ser definida como Senha do Active Directory. A cadeia de conexão deve conter valores e palavras-chave de ID/UID de Usuário e Senha/PWD. O exemplo de código em C# a seguir usa ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Saiba mais sobre métodos de autenticação do Azure AD usando os exemplos de código de demonstração disponíveis em [Demonstração do GitHub de Autenticação do Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token do Azure AD

Este método de autenticação permite que os serviços de camada intermediária se conectem ao Banco de Dados SQL do Azure ou ao SQL Data Warehouse do Azure obtendo um token do AAD (Azure Active Directory). Ele permite cenários sofisticados, incluindo a autenticação baseada em certificado. Você precisa concluir quatro etapas básicas para usar a autenticação de token do Azure AD:

1. Registrar seu aplicativo no Azure Active Directory e obter a ID de cliente para seu código.
2. Criar um usuário de banco de dados que representa o aplicativo. (Concluída anteriormente na etapa 6).
3. Crie um certificado no computador cliente que executa o aplicativo.
4. Adicionar o certificado como uma chave para seu aplicativo.

Exemplo de cadeia de conexão:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Para obter mais informações, confira [Blog de segurança do SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Para obter informações sobre como adicionar um certificado, consulte [Introdução à autenticação baseada em certificado no Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

As instruções a seguir, conexão usando a versão 13.1 do sqlcmd, que está disponível no [Centro de Download](https://go.microsoft.com/fwlink/?LinkID=825643).

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral de acesso e controle no Banco de Dados SQL, confira [Acesso e controle de Banco de Dados SQL](sql-database-control-access.md).
- Para obter uma visão geral de logons, usuários e funções de banco de dados no Banco de Dados SQL, confira [Logons, usuários e funções de banco de dados](sql-database-manage-logins.md).
- Para obter mais informações sobre objetos de banco de dados, confira [Entidades](https://msdn.microsoft.com/library/ms181127.aspx).
- Para obter mais informações sobre as funções de banco de dados, confira [Funções de banco de dados](https://msdn.microsoft.com/library/ms189121.aspx).
- Para obter mais informações sobre as regras de firewall no Banco de Dados SQL, confira [Regras de firewall de Banco de Dados SQL](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
