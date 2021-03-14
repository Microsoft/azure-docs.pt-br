---
title: Permissões de função personalizada para atribuições de acesso do aplicativo empresarial-Azure Active Directory | Microsoft Docs
description: Criar e atribuir funções personalizadas do Azure AD para acesso de aplicativos empresariais no Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb04616fb442f4a6c000e11919638231e3ddf64c
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012166"
---
# <a name="assign-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Atribuir funções personalizadas para gerenciar aplicativos empresariais no Azure Active Directory

Este artigo explica como criar uma função personalizada com permissões para gerenciar atribuições de aplicativo empresarial para usuários e grupos no Azure Active Directory (AD do Azure). Para os elementos das atribuições de funções e o significado de termos como subtipo, permissão e conjunto de propriedades, consulte a [visão geral das funções personalizadas](custom-overview.md).

## <a name="enterprise-app-role-permissions"></a>Permissões de função de aplicativo empresarial

Há duas permissões de aplicativo empresarial discutidas neste artigo. Todos os exemplos usam a permissão Atualizar.

* Para ler as atribuições de usuário e de grupo no escopo, conceda a `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` permissão
* Para gerenciar as atribuições de usuário e grupo no escopo, conceda a `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` permissão

A concessão dos resultados de permissão de atualização no destinatário é capaz de gerenciar atribuições de usuários e grupos a aplicativos empresariais. O escopo de atribuições de usuário e/ou grupo pode ser concedido para um único aplicativo ou concedido para todos os aplicativos. Se concedidas em nível de toda a organização, o destinatário pode gerenciar atribuições para todos os aplicativos. Se for feito em um nível de aplicativo, o destinatário poderá gerenciar atribuições somente para o aplicativo especificado.

A concessão da permissão Atualizar é feita em duas etapas:

1. Criar uma função personalizada com permissão `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Conceda permissões a usuários ou grupos para gerenciar atribuições de usuário e de grupo para aplicativos empresariais. Isso ocorre quando você pode definir o escopo para o nível de toda a organização ou para um único aplicativo.

## <a name="use-the-azure-ad-admin-center"></a>Usar o centro de administração do Azure AD

### <a name="create-a-new-custom-role"></a>Criar uma nova função personalizada

>[!NOTE]
> As funções personalizadas são criadas e gerenciadas em um nível de toda a organização e estão disponíveis somente na página de visão geral da organização.

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com permissões de administrador de função privilegiada ou de administrador global em sua organização.
1. Selecione **Azure Active Directory**, selecione **Funções e administradores** e, em seguida, selecione **Nova função personalizada**.

    ![Adicionar uma nova função personalizada da lista de funções no Azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. Na guia **noções básicas** , forneça "gerenciar atribuições de usuário e grupo" para o nome da função e "conceder permissões para gerenciar atribuições de usuário e de grupo" para a descrição da função e, em seguida, selecione **Avançar**.

    ![Forneça um nome e uma descrição para a função personalizada](./media/custom-enterprise-apps/role-name-and-description.png)

1. Na guia **permissões** , digite "Microsoft. Directory/servicePrincipalName/appRoleAssignedTo/Update" na caixa de pesquisa e marque as caixas de seleção ao lado das permissões desejadas e, em seguida, selecione **Avançar**.

    ![Adicionar as permissões à função personalizada](./media/custom-enterprise-apps/role-custom-permissions.png)

1. Na guia **Examinar + criar**, examine as permissões e selecione **Criar**.

    ![Agora você pode criar a função personalizada](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Atribuir a função a um usuário usando o portal do Azure AD

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com permissões de função de administrador de função privilegiada.
1. Selecione **Azure Active Directory** e, em seguida, selecione **Funções e administradores**.
1. Selecione a função **conceder permissões para gerenciar usuários e atribuições de grupo** .

    ![Abra funções e administradores e pesquise a função personalizada](./media/custom-enterprise-apps/select-custom-role.png)

1. Selecione **Adicionar atribuição**, selecione o usuário desejado e, em seguida, clique em **selecionar** para adicionar a atribuição de função ao usuário.

    ![Adicionar uma atribuição para a função personalizada ao usuário](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Dicas de atribuição

* Para conceder permissões a grupos para gerenciar usuários e acesso de grupo para todos os aplicativos empresariais, inicie na lista de **funções e administradores** de toda a organização na página de **visão geral** do Azure ad da sua organização.
* Para conceder permissões a usuários para gerenciarem o acesso de um aplicativo empresarial, para o usuário e o grupo, acesse o aplicativo no Azure AD e abra na lista **funções e administradores** do aplicativo. Selecione a nova função personalizada e conclua a atribuição de usuário ou grupo. Os atribuições podem gerenciar usuários e acesso de grupo somente para o aplicativo específico.
* Para testar sua atribuição de função personalizada, entre como o destinatário e abra a página **usuários e grupos** de um aplicativo para verificar se a opção **Adicionar usuário** está habilitada.

    ![Verificar as permissões de usuário](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Usar o PowerShell do Azure AD

Para obter mais detalhes, consulte [criar e atribuir uma função personalizada](custom-create.md) e [atribuir funções personalizadas com o escopo de recurso usando o PowerShell](custom-assign-powershell.md).

Primeiro, instale o módulo do PowerShell do Azure AD do [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Em seguida, importe o módulo de versão prévia do Azure AD PowerShell usando o seguinte comando:

```powershell
Import-Module -Name AzureADPreview
```

Para verificar se o módulo está pronto para uso, a versão retornada pelo seguinte comando deve coincidir com o comando listado aqui:

```powershell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="create-a-custom-role"></a>Criar uma função personalizada

Crie uma função usando o seguinte script do PowerShell:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =@( "microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Atribuir a função personalizada

Atribua a função usando este script do PowerShell.

```powershell
PowerShell
# Basic role information

$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="use-the-microsoft-graph-api"></a>Usar a API do Microsoft Graph

Crie uma função personalizada usando o exemplo fornecido na API Microsoft Graph. Para obter mais detalhes, consulte [criar e atribuir uma função personalizada](custom-create.md) e [atribuir funções de administrador personalizadas usando a API de Microsoft Graph](custom-assign-graph.md).

Solicitação HTTP para criar a função personalizada.

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Atribuir a função personalizada usando a API do Microsoft Graph

A atribuição de função combina uma ID de entidade de segurança (que pode ser um usuário ou uma entidade de serviço), uma ID de definição de função e um escopo de recursos do Azure AD. Para obter mais informações sobre os elementos de uma atribuição de função, consulte a [visão geral das funções personalizadas](custom-overview.md)

Solicitação HTTP à qual atribuir uma função personalizada.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Próximas etapas

* [Explorar as permissões de função personalizada disponíveis para aplicativos empresariais](custom-enterprise-app-permissions.md)
