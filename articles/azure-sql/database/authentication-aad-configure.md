---
title: Configurar a autenticação do Azure Active Directory
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Saiba como se conectar ao banco de dados SQL, ao SQL Instância Gerenciada e ao Azure Synapse Analytics usando a autenticação Azure Active Directory, depois de configurar o Azure AD.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: 9e7b337d4358f9685d683c308d6df9110607207a
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643413"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Configurar e gerenciar a autenticação do Azure AD com o Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Este artigo mostra como criar e popular uma instância do Azure Active Directory (Azure AD) e, em seguida, usar o Azure AD com o [banco de dados SQL do Azure](sql-database-paas-overview.md), o [SQL instância gerenciada do Azure](../managed-instance/sql-managed-instance-paas-overview.md)e o [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para obter uma visão geral, consulte [Azure Active Directory autenticação](authentication-aad-overview.md).

## <a name="azure-ad-authentication-methods"></a>Métodos de autenticação do Azure AD

A autenticação do Azure AD dá suporte aos seguintes métodos de autenticação:

- Identidades somente na nuvem do Azure AD
- Identidades híbridas do Azure AD que dão suporte a:
  - Autenticação de nuvem com duas opções combinadas com SSO (logon único) contínuo
    - Autenticação de hash de senha do Azure AD
    - Autenticação de passagem do Azure AD
  - Autenticação federada

Para obter mais informações sobre os métodos de autenticação do Azure AD e qual deles escolher, consulte [escolher o método de autenticação correto para sua solução de identidade híbrida Azure Active Directory](../../active-directory/hybrid/choose-ad-authn.md).

Para obter mais informações sobre identidades, configuração e sincronização híbridas do Azure AD, consulte:

- Autenticação de hash de senha- [implementar a sincronização de hash de senha com sincronização de Azure ad Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Autenticação de passagem – [Azure Active Directory autenticação de passagem](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Autenticação federada – [Implantando serviços de Federação do Active Directory (AD FS) no Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) e [Azure ad Connect e Federação](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Criar e popular uma instância do Azure AD

Crie uma instância do Azure AD e preencha-a com usuários e grupos. O Azure AD pode ser o domínio gerenciado pelo Azure AD inicial. O Azure AD também pode ser um Active Directory Domain Services local federado com o Azure AD.

Para obter mais informações, consulte [Integrando suas identidades locais no Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [Adicionar seu próprio nome de domínio ao Azure AD](../../active-directory/fundamentals/add-custom-domain.md), [O Microsoft Azure agora dá suporte à federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administrando seu diretório do Azure AD](../../active-directory/fundamentals/active-directory-whatis.md), [Gerenciar o Azure AD usando o Windows PowerShell](/powershell/azure/) e [Portas e protocolos necessários para a identidade híbrida](../../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associar ou adicionar uma assinatura do Azure ao Azure Active Directory

1. Associe sua assinatura do Azure ao Azure Active Directory tornando o diretório um diretório confiável para a assinatura do Azure que hospeda o banco de dados. Para obter detalhes, consulte [associar ou adicionar uma assinatura do Azure ao seu locatário Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Use o seletor de diretório no portal do Azure para alternar para a assinatura associada ao domínio.

   > [!IMPORTANT]
   > Cada assinatura do Azure tem uma relação de confiança com uma instância do Azure AD. Isso significa que ela confia que esse diretório autentique usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas uma única assinatura confia em apenas um diretório. Essa relação de confiança que uma assinatura tem com um diretório é diferente da relação que uma assinatura tem com todos os outros recursos no Azure (sites, bancos de dados e assim por diante), que são mais similares a recursos filho de uma assinatura. Se uma assinatura expira, o acesso a esses outros recursos associados à assinatura também pára. Mas o diretório permanece no Azure e você pode associar outra assinatura a ele, além de continuar a gerenciar os usuários do diretório. Para obter mais informações sobre recursos, consulte [Noções básicas sobre o acesso a recursos no Azure](../../active-directory/external-identities/add-users-administrator.md). Para saber mais sobre essa relação de confiança, consulte relação [Como associar ou adicionar uma assinatura do Azure ao Microsoft Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>Administrador do Azure AD com um servidor no banco de dados SQL

Cada [servidor](logical-servers.md) no Azure (que hospeda o banco de dados SQL ou o Azure Synapse) começa com uma única conta de administrador de servidor que é o administrador de todo o servidor. Crie uma segunda conta de administrador como uma conta do Azure AD. Essa entidade de segurança é criada como um usuário de banco de dados independente no banco de dados mestre do servidor. As contas de administrador são membros da função de **db_owner** em cada banco de dados de usuário e inserem cada banco de dados de usuário como o usuário **dbo** . Para obter mais informações sobre contas de administrador, consulte [Gerenciando bancos de dados e logons](logins-create-manage.md).

Ao usar o Azure Active Directory com a Replicação Geográfica, o administrador do Azure Active Directory deverá ser configurado para os servidores primários e secundários. Se um servidor não tiver um administrador de Azure Active Directory, Azure Active Directory logons e usuários receberão um `Cannot connect` erro de servidor.

> [!NOTE]
> Os usuários que não se baseiam em uma conta do Azure AD (incluindo a conta de administrador do servidor) não podem criar usuários baseados no Azure AD, porque eles não têm permissão para validar os usuários de banco de dados proposto com o Azure AD.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Provisionar administrador do Azure AD (SQL Instância Gerenciada)

> [!IMPORTANT]
> Somente siga estas etapas se estiver provisionando um Instância Gerenciada de SQL do Azure. Esta operação só pode ser executada pelo administrador global ou por um administrador de função com privilégios no Azure AD.
>
> Em **Visualização pública**, você pode atribuir a função de **leitores de diretório** a um grupo no Azure AD. Os proprietários do grupo podem então adicionar a identidade da instância gerenciada como um membro desse grupo, o que permitiria provisionar um administrador do Azure AD para o SQL Instância Gerenciada. Para obter mais informações sobre esse recurso, confira [Função Leitores de Diretório no Azure Active Directory para o SQL do Azure](authentication-aad-directory-readers-role.md).

O SQL Instância Gerenciada precisa de permissões para ler o Azure AD para realizar tarefas com êxito, como a autenticação de usuários por meio da Associação de grupo de segurança ou da criação de novos usuários. Para que isso funcione, você precisa conceder a permissão SQL Instância Gerenciada para ler o Azure AD. Você pode fazer isso usando o portal do Azure ou o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

Para conceder ao SQL Instância Gerenciada permissão de leitura do AD do Azure usando o portal do Azure, faça logon como administrador global no Azure AD e siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), no canto superior direito, selecione sua conexão em uma lista suspensa de diretórios ativos possíveis.

2. Escolha o Active Directory correto como o AD do Azure padrão.

   Esta etapa vincula a assinatura associada ao Active Directory ao Instância Gerenciada SQL, certificando-se de que a mesma assinatura seja usada para a instância do Azure AD e o SQL Instância Gerenciada.

3. Navegue até o SQL Instância Gerenciada que você deseja usar para a integração do Azure AD.

   ![Captura de tela da portal do Azure mostrando a página de administração de Active Directory aberta para a instância gerenciada do SQL selecionada.](./media/authentication-aad-configure/aad.png)

4. Selecione a faixa na parte superior da página de administração do Active Directory e conceda permissão ao usuário atual.

    ![Captura de tela da caixa de diálogo para conceder permissões a uma instância gerenciada do SQL para acessar Active Directory. O botão conceder permissões está selecionado.](./media/authentication-aad-configure/grant-permissions.png)

5. Depois que a operação for concluída com sucesso, a notificação a seguir será exibida no canto superior direito:

    ![Captura de tela de uma notificação confirmando se as permissões de leitura do Active Directory foram atualizadas com êxito para a instância gerenciada.](./media/authentication-aad-configure/success.png)

6. Agora você pode escolher o administrador do Azure AD para seu Instância Gerenciada do SQL. Para isso, na página de administração do Active Directory, selecione o comando **Definir administrador**.

    ![Captura de tela mostrando o comando Set admin realçado na página de administração Active Directory para a instância gerenciada do SQL selecionada.](./media/authentication-aad-configure/set-admin.png)

7. Na página Administrador do Azure AD, procure um usuário, selecione o usuário ou grupo que será um administrador e selecione **selecionar**.

   A página de administração do Active Directory mostra todos os membros e grupos do Active Directory. Usuários ou grupos que estejam esmaecidos não poderão ser selecionados porque não têm suporte como administradores do Azure AD. Consulte a lista de administradores com suporte em [Limitações e recursos do Azure AD](authentication-aad-overview.md#azure-ad-features-and-limitations). O Azure RBAC (controle de acesso baseado em função) aplica-se somente ao portal do Azure e não é propagado para o banco de dados SQL, SQL Instância Gerenciada ou Azure Synapse.

    ![Adicionar Azure Active Directory administrador](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. Na parte superior da página Administrador do Active Directory, selecione **Salvar**.

    ![Captura de tela da página de administração Active Directory com o botão salvar na linha superior ao lado dos botões definir administrador e remover administrador.](./media/authentication-aad-configure/save.png)

    O processo de alteração do administrador pode levar vários minutos. Em seguida, o novo administrador é exibido na caixa de administração do Active Directory.

Depois de provisionar um administrador do Azure AD para seu Instância Gerenciada do SQL, você pode começar a criar entidades de segurança do servidor do Azure AD (logons) com a sintaxe [Create login](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) . Para obter mais informações, consulte [visão geral do SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration).

> [!TIP]
> Para remover um Administrador mais tarde, na parte superior da página Administrador do Active Directory, selecione **Remover administrador** e, em seguida, selecione **Salvar**.

### <a name="powershell"></a>PowerShell

Para conceder ao SQL Instância Gerenciada permissão de leitura do AD do Azure usando o PowerShell, execute este script:

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
# Can be executed only by a "Global Administrator" or "Privileged Role Administrator" type of user.

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

# Get service principal for your SQL Managed Instance
$roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}

# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

    #Write-Output "Dumping service principal '$($managedInstanceName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else {
    Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
}
```

### <a name="powershell-for-sql-managed-instance"></a>PowerShell para SQL Instância Gerenciada

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/).

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte do Azure SQL Instância Gerenciada, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber as correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para saber mais sobre a compatibilidade entre eles, confira [Apresentação do novo módulo Az do Azure PowerShell](/powershell/azure/new-azureps-module-az).

Para provisionar um administrador do AD do Azure, execute os seguintes comandos do Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Os cmdlets usados para provisionar e gerenciar o administrador do Azure AD para o SQL Instância Gerenciada estão listados na tabela a seguir:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Provisiona um administrador do Azure AD para o Instância Gerenciada do SQL na assinatura atual. (Deve ser da assinatura atual)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Remove um administrador do Azure AD para o Instância Gerenciada do SQL na assinatura atual. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Retorna informações sobre um administrador do Azure AD para o Instância Gerenciada do SQL na assinatura atual.|

O comando a seguir obtém informações sobre um administrador do Azure AD para um Instância Gerenciada do SQL chamado ManagedInstance01 que está associado a um grupo de recursos chamado ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

O comando a seguir provisiona um grupo de administradores do Azure AD chamado DBAs para o SQL Instância Gerenciada chamado ManagedInstance01. Este servidor está associado ao grupo de recursos ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

O comando a seguir remove o administrador do Azure AD para o SQL Instância Gerenciada chamado ManagedInstanceName01 associado ao grupo de recursos ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você também pode provisionar um administrador do Azure AD para o Instância Gerenciada do SQL chamando os seguintes comandos da CLI:

| Comando | Descrição |
| --- | --- |
|[AZ SQL mi ad-admin Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Provisiona um administrador de Azure Active Directory para o SQL Instância Gerenciada (deve ser da assinatura atual). |
|[AZ SQL mi ad-admin Delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Remove um administrador de Azure Active Directory para o Instância Gerenciada SQL. |
|[AZ SQL mi ad-lista de administradores](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Retorna informações sobre um administrador de Azure Active Directory configurado atualmente para o Instância Gerenciada SQL. |
|[AZ SQL mi-atualização de administração](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Atualiza o administrador de Active Directory para o Instância Gerenciada SQL. |

Para obter mais informações sobre comandos da CLI, consulte [AZ SQL mi](/cli/azure/sql/mi).

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Provisionar administrador do Azure AD (banco de dados SQL)

> [!IMPORTANT]
> Somente siga estas etapas se estiver provisionando um [servidor](logical-servers.md) para o banco de dados SQL ou o Azure Synapse.

Os dois procedimentos a seguir mostram como provisionar um administrador de Azure Active Directory para seu servidor no portal do Azure e usando o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. No [portal do Azure](https://portal.azure.com/), no canto superior direito, selecione a conexão para exibir uma lista suspensa dos Active Directories possíveis. Escolha o Active Directory correto como o AD do Azure padrão. Esta etapa vincula o Active Directory associado à assinatura ao servidor, certificando-se de que a mesma assinatura seja usada tanto para o Azure AD quanto para o servidor.

2. Pesquise e selecione **SQL Server**.

    ![Pesquisar e selecionar servidores SQL](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Nessa página, antes você selecionar **SQL servers**, você pode selecionar a **estrela** ao lado do nome para estabelecer a categoria como *favorito* e adicionar **SQL servers** à barra de navegação à esquerda.

3. Na página **SQL Server** , selecione **Active Directory admin**.

4. Na página **Administrador do Active Directory**, selecione **Definir administrador**.

    ![SQL Servers Set Active Directory admin](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. Na página **Adicionar Administrador** , procure um usuário, selecione o usuário ou grupo que será um administrador e selecione **selecionar**. A página de administração do Active Directory mostra todos os membros e grupos do Active Directory. Usuários ou grupos que estão esmaecidos não podem ser selecionados porque eles não têm suporte como administradores do AD do Azure. (Consulte a lista de administradores com suporte na seção **recursos e limitações do Azure ad** de [usar Azure Active Directory autenticação para autenticação com o banco de dados SQL ou o Azure Synapse](authentication-aad-overview.md).) O Azure RBAC (controle de acesso baseado em função) aplica-se somente ao portal e não é propagado para SQL Server.

    ![Selecionar administrador de Azure Active Directory](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. Na parte superior da página **Administrador do Active Directory**, selecione **Salvar**.

    ![salvar administrador](./media/authentication-aad-configure/save-admin.png)

O processo de alteração do administrador pode levar vários minutos. O novo administrador aparece na caixa **Administrador do Active Directory** .

   > [!NOTE]
   > Ao configurar o administrador do Azure AD, o novo nome de administrador (usuário ou grupo) não pode já estar presente no banco de dados mestre virtual como um usuário de autenticação de servidor. Se presente, a configuração de administração do AD do Azure falhará, revertendo sua criação e indicando que esse administrador (nome) já existe. Como esse usuário de autenticação de servidor não faz parte do Azure AD, qualquer esforço para se conectar ao servidor usando a autenticação do Azure AD falhará.

Para remover um Administrador mais tarde, na parte superior da página **Administrador do Active Directory**, selecione **Remover administrador** e, em seguida, selecione **Salvar**.

### <a name="powershell-for-sql-database-and-azure-synapse"></a>PowerShell para banco de dados SQL e Azure Synapse

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/). Para provisionar um administrador do AD do Azure, execute os seguintes comandos do Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets usados para provisionar e gerenciar o administrador do Azure AD para o banco de dados SQL e o Azure Synapse:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Provisiona um administrador de Azure Active Directory para o servidor que hospeda o banco de dados SQL ou o Azure Synapse. (Deve ser da assinatura atual) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Remove um administrador de Azure Active Directory do servidor que hospeda o banco de dados SQL ou o Azure Synapse.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Retorna informações sobre um administrador de Azure Active Directory configurado atualmente para o servidor que hospeda o banco de dados SQL ou o Azure Synapse. |

Use o comando Get-Help do PowerShell para ver mais informações para cada um desses comandos. Por exemplo, `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

O seguinte script provisiona um grupo de administradores do Azure AD nomeado **DBA_Group** (ID de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para o servidor **demo_server** em um grupo de recursos nomeado **Group-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

O parâmetro de entrada **DisplayName** aceita o nome de exibição do Azure AD ou o Nome UPN. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Para grupos do AD do Azure, há suporte apenas para o nome de exibição do AD do Azure.

> [!NOTE]
> O comando ```Set-AzSqlServerActiveDirectoryAdministrator``` do Azure PowerShell não impede que você provisione administradores do Azure AD para usuários sem suporte. Um usuário para o qual não há suporte pode ser provisionado, mas não pode se conectar a um banco de dados.

O seguinte exemplo usa a **ObjectID** opcional:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> A **ObjectID** do Azure AD é necessária quando o **DisplayName** não é exclusivo. Para recuperar os valores de **ObjectID** e **DisplayName**, use a seção do Active Directory do Portal Clássico do Azure e exiba as propriedades de um usuário ou grupo.

O exemplo a seguir retorna informações sobre o administrador atual do Azure AD para o servidor:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

O seguinte exemplo remove um administrador do AD do Azure:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode provisionar um administrador do Azure AD chamando os seguintes comandos da CLI:

| Comando | Descrição |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Provisiona um administrador de Azure Active Directory para o servidor que hospeda o banco de dados SQL ou o Azure Synapse. (Deve ser da assinatura atual) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Remove um administrador de Azure Active Directory do servidor que hospeda o banco de dados SQL ou o Azure Synapse. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Retorna informações sobre um administrador de Azure Active Directory configurado atualmente para o servidor que hospeda o banco de dados SQL ou o Azure Synapse. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Atualiza o administrador do Active Directory para o servidor que hospeda o banco de dados SQL ou o Azure Synapse. |

Para obter mais informações sobre comandos da CLI, consulte [AZ SQL Server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Você também pode provisionar um administrador do Azure Active Directory usando as APIs REST. Para obter mais informações, confira [Referência da API REST de Gerenciamento de Serviços e Operações do Banco de Dados SQL do Azure](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Configurar os computadores cliente

Em todos os computadores cliente, dos quais seus aplicativos ou usuários se conectam ao banco de dados SQL ou ao Azure Synapse usando identidades do Azure AD, você deve instalar o seguinte software:

- .NET Framework 4,6 ou posterior de [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) .
- Azure Active Directory biblioteca de autenticação para SQL Server (*ADAL.DLL*). Abaixo estão os links de download para instalar o driver SSMS, ODBC e OLE DB mais recente que contém a biblioteca *ADAL.DLL* .
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [ODBC Driver 17 for SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)
  - [Driver OLE DB 18 para SQL Server](/sql/connect/oledb/download-oledb-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)

Você pode atender a esses requisitos:

- A instalação da versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) atende aos requisitos .NET Framework 4,6.
  - O SSMS instala a versão x86 do *ADAL.DLL*.
  - SSDT instala a versão amd64 do *ADAL.DLL*.
  - O Visual Studio mais recente dos [downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) atende aos requisitos .NET Framework 4,6, mas não instala a versão de AMD64 necessária do *ADAL.DLL*.

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Criar usuários contidos mapeados para identidades do Azure AD

Como o SQL Instância Gerenciada dá suporte a entidades de segurança do Azure AD Server (logons), o uso de usuários de banco de dados independente não é necessário. As entidades de segurança do servidor do Azure AD (logons) permitem criar logons de usuários, grupos ou aplicativos do Azure AD. Isso significa que você pode autenticar com o seu Instância Gerenciada SQL usando o logon do servidor do Azure AD em vez de um usuário de banco de dados independente. Para obter mais informações, consulte [visão geral do SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration). Para obter a sintaxe na criação de entidades de segurança do servidor do Azure AD (logons), consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

No entanto, usar Azure Active Directory autenticação com o banco de dados SQL e o Azure Synapse requer o uso de usuários de banco de dados independente com base em uma identidade do Azure Um usuário de banco de dados independente não tem um logon no banco de dados mestre e é mapeado para uma identidade no Azure AD que está associada ao banco de dados. A identidade do AD do Azure pode ser uma conta de usuário individual ou um grupo. Para saber mais sobre usuários de bancos de dados independentes, veja [Usuários do bancos de dados independentes - Tornando seu banco de dados portátil](/sql/relational-databases/security/contained-database-users-making-your-database-portable).

> [!NOTE]
> Os usuários do banco de dados (com exceção dos administradores) não podem ser criados usando o portal do Azure. As funções do Azure não são propagadas para o banco de dados no banco de dados SQL, o SQL Instância Gerenciada ou o Azure Synapse. As funções do Azure são usadas para gerenciar recursos do Azure e não se aplicam a permissões de banco de dados. Por exemplo, a função **colaborador de SQL Server** não concede acesso para se conectar ao banco de dados no banco de dados SQL, o SQL instância gerenciada ou o Azure Synapse. A permissão de acesso deve ser concedida diretamente no banco de dados usando instruções Transact-SQL.

> [!WARNING]
> Caracteres especiais, como dois-pontos `:` ou e comercial `&` , quando incluídos como nomes de usuário nas instruções T-SQL `CREATE LOGIN` e `CREATE USER` não têm suporte.

Para criar um usuário de banco de dados independente baseado no Azure AD (que não seja o administrador do servidor proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD como um usuário com, no mínimo, a permissão **ALTER ANY USER** . Em seguida, use a sintaxe Transact-SQL a seguir:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*nome_UPN_Azure_AD* pode ser o nome UPN de um usuário do Azure AD ou o nome de exibição de um grupo do Azure AD.

**Exemplos:** para criar um usuário de banco de dados independente que representa um usuário de domínio federado ou gerenciado pelo Azure AD:

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

> [!NOTE]
> Esse comando requer que o SQL Access do Azure AD (o "provedor externo") em nome do usuário conectado. Às vezes, ocorrerão circunstâncias que fazem com que o Azure AD retorne uma exceção para o SQL. Nesses casos, o usuário verá o erro SQL 33134, que deve conter a mensagem de erro específica do Azure AD. Na maioria das vezes, o erro dirá que o acesso foi negado ou que o usuário deve se registrar no MFA para acessar o recurso ou que o acesso entre aplicativos primários deve ser tratado por meio de autorização. Nos dois primeiros casos, o problema geralmente é causado pelas políticas de acesso condicional que são definidas no locatário do Azure AD do usuário: elas impedem que o usuário acesse o provedor externo. A atualização das políticas de acesso condicional para permitir o acesso ao aplicativo ' 00000002-0000-0000-C000-000000000000 ' (a ID do aplicativo do API do Graph do Azure AD) deve resolver o problema. No caso de o erro dizer que o acesso entre os aplicativos primários deve ser manipulado por meio de autorização, o problema ocorre porque o usuário está conectado como uma entidade de serviço. O comando deverá ter sucesso se for executado por um usuário.

> [!TIP]
> Não é possível criar um usuário diretamente de um Azure Active Directory que não seja o Azure Active Directory associado à sua assinatura do Azure. No entanto, os membros de outros Active Directories que são os usuários importados no Active Directory associado (conhecidos como usuários externos) podem ser adicionados a um grupo do Active Directory no Active Directory locatário. Ao criar um usuário de banco de dados independente para esse grupo AD, os usuários do Active Directory externo podem obter acesso ao Banco de Dados SQL.

Para obter mais informações sobre como criar usuários de banco de dados independente baseados em identidades do Active Directory do Azure, consulte [CRIAR USUÁRIO (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql).

> [!NOTE]
> Remover o administrador de Azure Active Directory do servidor impede que qualquer usuário de autenticação do Azure AD se conecte ao servidor. Se for necessário, os usuários do Azure AD inutilizáveis poderão ser removidos manualmente por um administrador do Banco de Dados SQL.

> [!NOTE]
> Se você receber um **Tempo Limite de Conexão Expirado**, talvez seja necessário definir o parâmetro `TransparentNetworkIPResolution` da cadeia de conexão como falso. Para obter mais informações, consulte [Problema de tempo limite de conexão com o .NET Framework 4.6.1 – TransparentNetworkIPResolution](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution).

Quando você cria um usuário de banco de dados, o usuário recebe a permissão **CONNECT** e pode se conectar a esse banco de dados como membro da função **PUBLIC**. Inicialmente, as únicas permissões disponíveis para o usuário são as permissões concedidas à função **PUBLIC** ou as permissões concedidas a quaisquer grupos do Azure AD dos quais esse usuário é membro. A partir do momento que você provisionar um usuário de banco de dados independente baseado no AD do Azure, você pode conceder ao usuário permissões adicionais, do mesmo modo que você concede permissões para qualquer outro tipo de usuário. Normalmente, conceda permissões para funções de banco de dados e adicione usuários a funções. Para saber mais, confira [Noções básicas sobre permissões do Mecanismo de Banco de Dados](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obter mais informações sobre funções especiais do Banco de dados SQL, veja [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](logins-create-manage.md).
Uma conta de usuário de domínio federado que é importado para um domínio gerenciado como um usuário externo deve usar a identidade do domínio gerenciado.

> [!NOTE]
> Usuários do AD do Azure são marcados nos metadados do banco de dados com tipo E (EXTERNAL_USER) e para grupos com o tipo X (EXTERNAL_GROUPS). Para obter mais informações, consulte [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>Conectar-se ao banco de dados usando SSMS ou SSDT  

Para confirmar que o administrador do Azure AD está configurado corretamente, conecte-se ao banco de dados **mestre** usando a conta de administrador do Azure AD.
Para provisionar um usuário de banco de dados independente com base no Azure AD (que não seja o administrador do servidor que é o proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD que tenha acesso ao banco de dados.

> [!IMPORTANT]
> O suporte à autenticação do Azure Active Directory está disponível com o [SQL Server 2016 Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e o [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) no Visual Studio 2015. A versão de agosto de 2016 do SSMS também inclui suporte para Universal autenticação do Active Directory, que permite aos administradores exigir Autenticação Multifator utilizando uma chamada telefônica, mensagem de texto, os cartões inteligentes com PIN ou a notificação de aplicativo móvel.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Usando uma identidade do Azure AD para se conectar usando SSMS ou SSDT

Os procedimentos a seguir mostram como se conectar ao banco de dados SQL com uma identidade do Azure AD usando as ferramentas de banco de dados SQL Server Management Studio ou SQL Server.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Use esse método se você estiver conectado ao Windows usando suas credenciais de Azure Active Directory de um domínio federado ou um domínio gerenciado que está configurado para logon único contínuo para autenticação de passagem e de hash de senha. Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md).

1. Inicie o Management Studio ou as ferramentas de dados e, na caixa de diálogo **conectar ao servidor** (ou **conectar ao mecanismo de banco de dados**), na caixa **autenticação** , selecione **integrado ao Azure Active Directory**. Nenhuma senha é necessária ou pode ser inserida porque suas credenciais existentes serão apresentadas para a conexão.

   ![Selecione Autenticação Integrada do AD][11]

2. Selecione o botão **Opções** e, na página **Propriedades de Conexão**, na caixa **Conectar ao banco de dados**, digite o nome do banco de dados de usuário ao qual você deseja se conectar. Para obter mais informações, consulte o artigo [autenticação multifator do Azure ad](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter) nas diferenças entre as propriedades de conexão para o SSMS 17. x e 18. x.

   ![Selecione o nome do banco de dados][13]

### <a name="active-directory-password-authentication"></a>Autenticação de senha do Active Directory

Use esse método ao se conectar com um nome de entidade do AD do Azure usando o domínio gerenciado pelo Azure AD. Você também pode usá-lo para contas federadas sem acesso ao domínio, por exemplo, ao trabalhar remotamente.

Use esse método para autenticar o banco de dados no banco de dados SQL ou o SQL Instância Gerenciada com usuários de identidade somente na nuvem do Azure AD ou aqueles que usam identidades híbridas do Azure AD. Esse método dá suporte a usuários que desejam usar suas credenciais do Windows, mas sua máquina local não está unida ao domínio (por exemplo, usando acesso remoto). Nesse caso, um usuário do Windows pode indicar sua conta de domínio e senha e pode se autenticar no banco de dados no banco de dados SQL, no SQL Instância Gerenciada ou no Azure Synapse.

1. Inicie o Management Studio ou o Data Tools e, na caixa de diálogo **conectar ao servidor** (ou **conectar ao mecanismo de banco de dados**), na caixa **autenticação** , selecione **Azure Active Directory-senha**.

2. Na caixa **nome de usuário** , digite o nome de usuário do Azure Active Directory no formato nome de usuário **\@ Domain.com**. Os nomes de usuário devem ser uma conta de Azure Active Directory ou uma conta de um domínio gerenciado ou federado com Azure Active Directory.

3. Na caixa **senha** , digite sua senha de usuário para a conta de Azure Active Directory ou para a conta de domínio gerenciado/federado.

    ![Selecione Autenticação de Senha do AD][12]

4. Selecione o botão **Opções** e, na página **Propriedades de Conexão**, na caixa **Conectar ao banco de dados**, digite o nome do banco de dados de usuário ao qual você deseja se conectar. (Confira o gráfico na opção anterior.)

### <a name="active-directory-interactive-authentication"></a>Active Directory autenticação interativa

Use esse método para autenticação interativa com ou sem autenticação multifator (MFA), com a senha solicitada interativamente. Esse método pode ser usado para autenticar o banco de dados no banco de dados SQL, o SQL Instância Gerenciada e o Azure Synapse para usuários de identidade somente de nuvem do Azure AD ou aqueles que usam identidades híbridas do Azure AD.

Para obter mais informações, consulte [usando a autenticação multifator do Azure AD com o banco de dados SQL e o Azure Synapse (suporte do SSMS para MFA)](authentication-mfa-ssms-overview.md).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Usando uma identidade do Azure AD para conectar-se de um aplicativo cliente

Os procedimentos a seguir mostram como se conectar a um banco de dados SQL com uma identidade do Azure AD de um aplicativo cliente.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Para usar a autenticação integrada do Windows, o Active Directory do seu domínio deve ser federado com Azure Active Directory, ou deve ser um domínio gerenciado configurado para logon único contínuo para a autenticação de passagem ou de hash de senha. Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md).

> [!NOTE]
> [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) para a autenticação integrada do Windows não é compatível com o logon único contínuo para autenticação de passagem e de hash de senha.

Seu aplicativo cliente (ou um serviço) conectando-se ao banco de dados deve estar em execução em um computador ingressado no domínio sob as credenciais de domínio de um usuário.

Para se conectar a um banco de dados usando a autenticação integrada e uma identidade do Azure AD, a palavra-chave Authentication na cadeia de conexão do banco de dados deve ser definida como `Active Directory Integrated` . O exemplo de código em C# a seguir usa ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Para a conexão ao Banco de Dados SQL do Azure, não há suporte para a palavra-chave de cadeia de conexão `Integrated Security=True`. Ao realizar uma conexão ODBC, você precisará remover espaços e configurar a autenticação para 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticação de senha do Active Directory

Para se conectar a um banco de dados usando contas de usuário de identidade somente na nuvem do Azure AD ou aqueles que usam identidades híbridas do Azure AD, a palavra-chave de autenticação deve ser definida como `Active Directory Password` . A cadeia de conexão deve conter valores e palavras-chave de ID/UID de Usuário e Senha/PWD. O exemplo de código em C# a seguir usa ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Saiba mais sobre métodos de autenticação do Azure AD usando os exemplos de código de demonstração disponíveis em [Demonstração do GitHub de Autenticação do Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token do Azure AD

Esse método de autenticação permite que os serviços de camada intermediária obtenham [JWT (tokens Web JSON)](../../active-directory/develop/id-tokens.md) para se conectar ao banco de dados no banco de dados SQL, ao SQL instância gerenciada ou ao Azure Synapse, obtendo um token do Azure AD. Esse método permite vários cenários de aplicativo, incluindo identidades de serviço, entidades de serviço e aplicativos usando a autenticação baseada em certificado. Você precisa concluir quatro etapas básicas para usar a autenticação de token do Azure AD:

1. Registre seu aplicativo com Azure Active Directory e obtenha a ID do cliente para seu código.
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

Para obter mais informações, confira [Blog de segurança do SQL Server](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth). Para obter informações sobre como adicionar um certificado, consulte [Introdução à autenticação baseada em certificado no Azure Active Directory](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

As instruções a seguir, conexão usando a versão 13.1 do sqlcmd, que está disponível no [Centro de Download](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` com o `-G` comando não funciona com as identidades do sistema e requer um logon principal do usuário.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Solucionar problemas de autenticação do Azure AD

A orientação sobre solução de problemas com a autenticação do Azure AD pode ser encontrada no seguinte blog: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral de logons, usuários, funções de banco de dados e permissões no banco de dados SQL, consulte [logons, usuários, funções de banco de dados e contas de usuário](logins-create-manage.md).
- Para obter mais informações sobre objetos de banco de dados, confira [Entidades](/sql/relational-databases/security/authentication-access/principals-database-engine).
- Para obter mais informações sobre as funções de banco de dados, confira [Funções de banco de dados](/sql/relational-databases/security/authentication-access/database-level-roles).
- Para obter mais informações sobre as regras de firewall no Banco de Dados SQL, confira [Regras de firewall de Banco de Dados SQL](firewall-configure.md).
- Para obter informações sobre como definir um usuário convidado do Azure AD como o administrador do Azure AD, consulte [criar usuários convidados do Azure AD e definir como um administrador do Azure ad](authentication-aad-guest-users.md).
- Para obter informações sobre como usar entidades de serviço com o SQL do Azure, consulte [criar usuários do Azure ad usando aplicativos do Azure ad](authentication-aad-service-principal-tutorial.md)

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
