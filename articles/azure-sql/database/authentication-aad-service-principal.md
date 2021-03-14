---
title: Entidade de serviço do Azure Active Directory com o SQL do Azure
description: Os aplicativos do Azure AD (entidades de serviço) dão suporte à criação de usuário do Azure AD no banco de dados SQL do Azure, Azure SQL Instância Gerenciada e Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 68267cdedd2f0b64549791866e8750cf42928ab4
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201235"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Entidade de serviço do Azure Active Directory com o SQL do Azure

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

O suporte para a criação de usuário do Azure Active Directory (Azure AD) no banco de dados SQL do Azure (BD SQL) e no [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) em nome de aplicativos do Azure AD (entidades de serviço) estão atualmente em **Visualização pública**.

> [!NOTE]
> Essa funcionalidade já tem suporte para o SQL Instância Gerenciada.

## <a name="service-principal-azure-ad-applications-support"></a>Suporte à entidade de serviço (aplicativos do Azure AD)

Este artigo se aplica a aplicativos que são integrados ao Azure AD e fazem parte do registro do Azure AD. Esses aplicativos geralmente precisam de acesso de autenticação e autorização ao SQL do Azure para executar várias tarefas. Esse recurso na **Visualização pública** agora permite que as entidades de serviço criem usuários do Azure AD no banco de dados SQL e no Azure Synapse. Houve uma limitação para impedir a criação de objetos do Azure AD em nome de aplicativos do Azure AD que foram removidos.

Quando um aplicativo do Azure AD é registrado usando o portal do Azure ou um comando do PowerShell, dois objetos são criados no locatário do Azure AD:

- Um objeto de aplicativo
- Um objeto de entidade de serviço

Para obter mais informações sobre os aplicativos do Azure AD, consulte [objetos de aplicativo e entidade de serviço no Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) e [criar uma entidade de serviço do Azure com Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

O banco de dados SQL, o Azure Synapse e o SQL Instância Gerenciada dão suporte aos seguintes objetos do Azure AD:

- Usuários do Azure AD (gerenciados, federados e convidados)
- Grupos do Azure AD (gerenciados e federados)
-  Aplicativos do Azure AD 

O comando T-SQL `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` em nome de um aplicativo do Azure ad agora tem suporte para o banco de dados SQL e o Azure Synapse.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Funcionalidade da criação de usuário do Azure AD usando entidades de serviço

O suporte a essa funcionalidade é útil nos processos de automação de aplicativo do Azure AD em que os objetos do Azure AD são criados e mantidos no banco de dados SQL e no Azure Synapse sem interação humana. As entidades de serviço podem ser um administrador do Azure AD para o servidor lógico do SQL, como parte de um grupo ou um usuário individual. O aplicativo pode automatizar a criação de objetos do Azure AD no banco de dados SQL e no Azure Synapse quando executado como administrador do sistema e não requer nenhum privilégio SQL adicional. Isso permite uma automação completa de uma criação de usuário de banco de dados. Esse recurso também tem suporte para identidade gerenciada atribuída pelo sistema e identidade gerenciada atribuída pelo usuário. Para obter mais informações, consulte [o que são identidades gerenciadas para recursos do Azure?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Habilitar entidades de serviço para criar usuários do Azure AD

Para habilitar uma criação de objeto do Azure AD no banco de dados SQL e no Azure Synapse em nome de um aplicativo do Azure AD, as seguintes configurações são necessárias:

1. Atribua a identidade do servidor. A identidade do servidor atribuído representa o Identidade de Serviço Gerenciada (MSI). Atualmente, a identidade do servidor para o SQL do Azure não oferece suporte a UMI (identidade gerenciada pelo usuário).
    - Para um novo servidor lógico do Azure SQL, execute o seguinte comando do PowerShell:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Para obter mais informações, consulte o comando [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) .

    - Para servidores lógicos do SQL Azure existentes, execute o seguinte comando:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Para obter mais informações, confira o comando [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver).

    - Para verificar se a identidade do servidor está atribuída ao servidor, execute o comando Get-AzSqlServer.

    > [!NOTE]
    > A identidade do servidor também pode ser atribuída usando comandos da CLI. Para obter mais informações, consulte [AZ SQL Server CREATE](/cli/azure/sql/server#az-sql-server-create) and [AZ SQL Server Update](/cli/azure/sql/server#az-sql-server-update).

2. Conceda permissão de [**leitores de diretório**](../../active-directory/roles/permissions-reference.md#directory-readers) do Azure ad à identidade do servidor criada ou atribuída ao servidor.
    - Para conceder essa permissão, siga a descrição usada para o SQL Instância Gerenciada que está disponível no seguinte artigo: [provisionar administrador do Azure AD (SQL instância gerenciada)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - O usuário do Azure AD que está concedendo essa permissão deve fazer parte do **administrador global** do Azure ad ou da função de **administrador de funções privilegiadas** .

> [!IMPORTANT]
> As etapas 1 e 2 devem ser executadas na ordem acima. Primeiro, crie ou atribua a identidade do servidor, depois de conceder a permissão [**leitores de diretório**](../../active-directory/roles/permissions-reference.md#directory-readers) . Omitir uma dessas etapas ou ambas causará um erro de execução durante a criação de um objeto do Azure AD no Azure SQL em nome de um aplicativo do Azure AD.
>
> Se você estiver usando a entidade de serviço para definir ou remover o administrador do Azure AD, o aplicativo também deverá ter a permissão [Directory. Read. All](/graph/permissions-reference#application-permissions-18) Application API no Azure AD. Para obter mais informações sobre [as permissões necessárias para definir um administrador do Azure ad](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin)e instruções passo a passo para criar um usuário do Azure AD em nome de um aplicativo do Azure AD, consulte [tutorial: criar usuários do Azure ad usando aplicativos do Azure ad](authentication-aad-service-principal-tutorial.md).
>
> Em **Visualização pública**, você pode atribuir a função de **leitores de diretório** a um grupo no Azure AD. Os proprietários do grupo podem então adicionar a identidade gerenciada como um membro desse grupo, o que pode ignorar a necessidade de um administrador **global** ou de **funções com privilégios Administrator** para conceder a função de **leitores de diretório** . Para obter mais informações sobre esse recurso, confira [Função Leitores de Diretório no Azure Active Directory para o SQL do Azure](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Solução de problemas e limitações para visualização pública

- Ao criar objetos do Azure AD no Azure SQL em nome de um aplicativo do Azure AD sem habilitar a identidade do servidor e conceder permissão aos **leitores de diretório** , a operação falhará com os seguintes erros possíveis. O exemplo de erro abaixo é para uma execução de comando do PowerShell para criar um usuário do banco de dados SQL `myapp` no artigo [tutorial: criar usuários do Azure ad usando aplicativos do Azure ad](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - Para o erro acima, siga as etapas para [atribuir uma identidade ao servidor lógico do SQL do Azure](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) e [atribuir permissão de leitores de diretório à identidade do servidor lógico do SQL](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity).
    > [!NOTE]
    > As mensagens de erro indicadas acima serão alteradas antes do recurso GA para identificar claramente o requisito de configuração ausente para o suporte a aplicativos do Azure AD.
- A configuração do aplicativo Azure AD como um administrador do Azure AD para SQL Instância Gerenciada só tem suporte usando o comando da CLI e o comando do PowerShell com [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) ou superior. Para obter mais informações, consulte os comandos [AZ SQL mi ad-admin Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) e [set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) . 
    - Se você quiser usar o portal do Azure para SQL Instância Gerenciada para definir o administrador do Azure AD, uma possível solução alternativa é criar um grupo do Azure AD. Em seguida, adicione a entidade de serviço (aplicativo do Azure AD) a esse grupo e defina esse grupo como um administrador do Azure AD para o SQL Instância Gerenciada.
    - Definir a entidade de serviço (aplicativo do Azure AD) como um administrador do Azure AD para o banco de dados SQL e o Azure Synapse tem suporte usando os comandos portal do Azure, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)e [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) .
- O uso de um aplicativo do Azure AD com a entidade de serviço de outro locatário do Azure AD falhará ao acessar o banco de dados SQL ou o SQL Instância Gerenciada criado em um locatário diferente. Uma entidade de serviço atribuída a esse aplicativo deve ser do mesmo locatário que o servidor lógico do SQL ou Instância Gerenciada.
- Se você usar o PowerShell para configurar um aplicativo individual do Azure AD na condição de administrador do Azure AD para o SQL do Azure, um módulo [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) ou posterior será necessário. Verifique se você atualizou para o módulo mais recente.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Criar usuários do Azure AD usando aplicativos do Azure AD](authentication-aad-service-principal-tutorial.md)
