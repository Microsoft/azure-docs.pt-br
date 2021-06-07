---
title: Atribuir a função de Leitores de diretório a um grupo do Azure AD e gerenciar atribuições de função
description: Este artigo orienta você sobre como habilitar a função de Leitores de diretório usando grupos do Azure AD para gerenciar atribuições de função do Azure AD com o Banco de Dados SQL do Azure, a Instância Gerenciada de SQL do Azure e o Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: bc809cf02b827b7498890cb7d929c44bd360ab53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99094702"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>Tutorial: Atribuir a função de Leitores de diretório a um grupo do Azure AD e gerenciar atribuições de função

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> A atribuição da função de **Leitores de diretório** a um grupo neste artigo está em **visualização pública**. 

Este artigo orienta você sobre como criar um grupo no Azure AD (na Azure Active Directory) e como atribuir a esse grupo a função de [**Leitores de diretório**](../../active-directory/roles/permissions-reference.md#directory-readers). As permissões de Leitores de diretório permitem que os proprietários do grupo adicionem mais membros ao grupo, como uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) do [Banco de Dados SQL do Azure](sql-database-paas-overview.md), da [Instância Gerenciada de SQL do Azure](../managed-instance/sql-managed-instance-paas-overview.md) e do [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Isso contorna a necessidade de ter um [Administrador global](../../active-directory/roles/permissions-reference.md#global-administrator) ou um [Administrador de funções com privilégios](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) para atribuir a função de Leitores de diretório diretamente a cada identidade do servidor lógico do Azure SQL no locatário.

Este tutorial usa o recurso apresentado em [Usar grupos de nuvem para gerenciar atribuições de função no Azure Active Directory (versão prévia)](../../active-directory/roles/groups-concept.md). 

Para obter mais informações sobre os benefícios de atribuir a função de Leitores de diretório a um grupo do Azure AD para o Azure SQL, confira [Função de Leitores de diretório no Azure Active Directory para o Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância do Azure AD. Para obter mais informações, confira [Configurar e gerenciar a autenticação do Azure AD com o Azure SQL](authentication-aad-configure.md).
- Um Banco de Dados SQL, uma Instância Gerenciada de SQL ou o Azure Synapse.

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>Atribuição da função de Leitores de diretório usando o portal do Azure

### <a name="create-a-new-group-and-assign-owners-and-role"></a>Criar um grupo e atribuir proprietários e funções

1. Um usuário com permissões de [Administrador Global](../../active-directory/roles/permissions-reference.md#global-administrator) ou [Administrador de Funções com Privilégios](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) é necessária para essa configuração inicial.
1. Faça com que o usuário com privilégios entre no [portal do Azure](https://portal.azure.com).
1. Vá até o recurso do **Azure Active Directory**. Em **Gerenciado**, vá para **Grupos**. Selecione **Novo grupo** para criar um grupo.
1. Selecione **Segurança** como o tipo de grupo e preencha o restante dos campos. Verifique se a configuração **Funções do Azure AD podem ser atribuídas ao grupo (versão prévia)** está configurada como **Sim**. Em seguida, atribua a função de **Leitores de diretório** do Azure AD ao grupo.
1. Atribua usuários do Azure AD como proprietário(s) ao grupo criado. Um proprietário do grupo pode ser um usuário comum do AD, sem nenhuma função administrativa do Azure AD atribuída. O proprietário deve ser um usuário que esteja gerenciando seu Banco de Dados SQL, sua Instância Gerenciada de SQL ou seu Azure Synapse.

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="aad-new-group":::

1. Escolha **Criar**

### <a name="checking-the-group-that-was-created"></a>Verificando o grupo criado

> [!NOTE]
> Verifique se o **Tipo de Grupo** é **Segurança**. Não há suporte para grupos do *Microsoft 365* para o Azure SQL.

Para verificar e gerenciar o grupo criado, volte para o painel **Grupos** no portal do Azure e pesquise pelo nome do grupo. Proprietários e membros adicionais podem ser adicionados no menu **Proprietários** e **Membros** da configuração **Gerenciar** depois de selecionar o grupo. Você também pode examinar as **Funções atribuídas** para o grupo.

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="Captura de tela de um painel de Grupo com os links que abrem os menus de Configurações para Membros, Proprietários e Funções atribuídas (versão prévia) realçados.":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>Adicionar identidade gerenciada do Azure SQL ao grupo

> [!NOTE]
> Estamos usando a Instância Gerenciada de SQL para este exemplo, mas etapas semelhantes podem ser aplicadas ao Banco de Dados SQL ou ao Azure Synapse para obter os mesmos resultados.

Para as etapas posteriores, o usuário com permissões de Administrador Global ou Administrador de Funções com Privilégios não é mais necessário.

1. Faça logon na portal do Azure como o usuário que está gerenciando a Instância Gerenciada de SQL e é proprietário do grupo criado anteriormente.

1. Localize o nome do recurso da **instância gerenciada de SQL** no portal do Azure.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="Captura da tela de instâncias gerenciadas do SQL com o nome da instância SQL ssomitest e o nome da Sub-rede ManagedInstance realçados.":::

   Durante a criação de sua Instância Gerenciada de SQL, uma identidade do Azure foi criada para a instância. A identidade criada tem o mesmo nome que o prefixo de seu nome da Instância Gerenciada de SQL. Encontre a entidade de serviço de sua identidade da Instância Gerenciada de SQL que foi criada como um aplicativo do Azure AD seguindo estas etapas:

    - Vá até o recurso do **Azure Active Directory**. Na configuração **Gerenciar**, selecione **Aplicativos empresariais**. A **ID do Objeto** é a identidade da instância.
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="Captura de tela da página Aplicativos empresariais de um recurso do Azure Active Directory com a ID de objeto da Instância Gerenciada de SQL realçada.":::

1. Vá até o recurso do **Azure Active Directory**. Em **Gerenciado**, vá para **Grupos**. Selecione o grupo criado. Na configuração **Gerenciado** do grupo, selecione **Membros**. Selecione **Adicionar membros** e adicione a entidade de serviço da Instância Gerenciada de SQL como um membro do grupo pesquisando pelo nome encontrado acima.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="Captura de tela da página Membros de um recurso do Azure Active Directory com as opções realçadas para adicionar uma Instância Gerenciada de SQL como um novo membro.":::

> [!NOTE]
> Podem ser necessários alguns minutos para que as permissões da entidade de serviço sejam propagadas no sistema do Azure e o acesso à API do Graph do Azure AD seja permitido. Talvez seja necessário aguardar alguns minutos antes de provisionar um administrador do Azure AD para a Instância Gerenciada de SQL.

### <a name="remarks"></a>Comentários

Para o Banco de Dados SQL e o Azure Synapse, a identidade do servidor pode ser criada durante a criação do servidor lógico do Azure SQL ou após a criação do servidor. Para obter mais informações sobre como criar ou definir a identidade do servidor no Banco de Dados SQL ou no Azure Synapse, confira [Habilitar entidades de serviço para criar usuários do Azure AD](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

Para a Instância Gerenciada de SQL, atribua a função **Leitores de diretório** à identidade da instância gerenciada para [configurar um administrador do Azure AD para a instância gerenciada](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

A atribuição da função de **Leitores de diretório** à identidade do servidor não é necessária para o Banco de Dados SQL nem para o Azure Synapse ao configurar um administrador do Azure AD para o servidor lógico. No entanto, para habilitar a criação de um objeto do Azure AD no Banco de Dados SQL ou no Azure Synapse em nome de um aplicativo do Azure AD, a função **Leitores de diretório** é necessária. Se a função não for atribuída à identidade do servidor lógico do SQL, a criação de usuários do Azure AD no Azure SQL falhará. Para obter mais informações, confira [Entidade de serviço do Azure Active Directory com o SQL do Azure](authentication-aad-service-principal.md).

## <a name="directory-readers-role-assignment-using-powershell"></a>Atribuição da função de Leitores de diretório usando o PowerShell

> [!IMPORTANT]
> Um [Administrador Global](../../active-directory/roles/permissions-reference.md#global-administrator) ou [Administrador de Funções com Privilégios](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) precisará executar essas etapas iniciais. Além do PowerShell, o Azure AD oferece a API do Microsoft Graph para [Criar um grupo com funções atribuíveis no Azure AD](../../active-directory/roles/groups-create-eligible.md#using-microsoft-graph-api).

1. Baixe o módulo do PowerShell da versão prévia do Azure AD usando os comandos a seguir. Talvez você precise executar o PowerShell como administrador.

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. Conecte-se ao seu locatário do Azure AD.

    ```powershell
    Connect-AzureAD
    ```

1. Crie um grupo de segurança para atribuir a função **Leitores de diretório**.

    - `DirectoryReaderGroup`, `Directory Reader Group` e `DirRead` podem ser alterados de acordo com sua preferência.

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. Atribua a função de **Leitores de diretório** ao grupo.

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. Atribua proprietários ao grupo.

    - Substitua `<username>` pelo usuário que você deseja que seja proprietário do grupo. Vários proprietários podem ser adicionados repetindo essas etapas.

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    Verifique os proprietários do grupo usando o seguinte comando:

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    Você também pode verificar os proprietários do grupo no [portal do Azure](https://portal.azure.com). Siga as etapas em [Verificando o grupo criado](#checking-the-group-that-was-created).

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>Atribuindo a entidade de serviço como um membro do grupo

Para as etapas posteriores, o usuário com permissões de Administrador Global ou Administrador de Funções com Privilégios não é mais necessário.

1. Usando um proprietário do grupo que também gerencia o recurso do Azure SQL, execute o comando a seguir para se conectar ao Azure AD.

    ```powershell
    Connect-AzureAD
    ```

1. Atribua a entidade de serviço como um membro do grupo criado.

    - Substitua `<ServerName>` pelo nome do servidor lógico do Azure SQL ou pelo nome da Instância Gerenciada. Para obter mais informações, confira a seção [Adicionar identidade do serviço do Azure SQL ao grupo](#add-azure-sql-managed-identity-to-the-group)

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    O seguinte comando retornará a ID de Objeto da entidade de serviço indicando que ela foi adicionada ao grupo:

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>Próximas etapas

- [Função Leitores de Diretório no Azure Active Directory para o SQL do Azure](authentication-aad-directory-readers-role.md)
- [Tutorial: Criar usuários do Azure AD usando aplicativos do Azure AD](authentication-aad-service-principal-tutorial.md)
- [Configurar e gerenciar a autenticação do Azure AD com o Azure SQL](authentication-aad-configure.md)