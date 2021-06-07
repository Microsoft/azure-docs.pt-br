---
title: Criar um grupo para atribuir funções no Azure Active Directory | Microsoft Docs
description: Saiba como criar um grupo de funções atribuíveis no Azure AD. Gerencie funções do Azure no portal do Azure, no PowerShell ou na API do Graph.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bf0a9ca193be9fd61d0b284338c0f581c9f91e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012047"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Criar um grupo de funções atribuídas no Azure Active Directory

Você só pode atribuir uma função a um grupo que foi criado com a propriedade ' isAssignableToRole ' definida como true, ou foi criado no portal do Azure AD com as **funções do Azure AD que podem ser atribuídas ao grupo** ativado. Esse atributo de grupo torna o grupo um que pode ser atribuído a uma função no Azure Active Directory (Azure AD). Este artigo descreve como criar esse tipo especial de grupo. **Observação:** Um grupo com a propriedade isAssignableToRole definida como true não pode ser do tipo de associação dinâmica. Para obter mais informações, consulte [usando um grupo para gerenciar atribuições de função do Azure ad](groups-concept.md).

## <a name="using-azure-ad-admin-center"></a>Usando o centro de administração do Azure AD

1. Entre no centro de [Administração do Azure ad](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com permissões de administrador de função privilegiada ou de administrador global na organização do Azure AD.
1. Selecione **grupos**  >  **todos os grupos**  >  **novo grupo**.

    [![Abra Azure Active Directory e crie um novo grupo.](./media/groups-create-eligible/new-group.png "Abra Azure Active Directory e crie um novo grupo.")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. Na guia **novo grupo** , forneça o tipo de grupo, o nome e a descrição.
1. A ativação de **funções do Azure AD pode ser atribuída ao grupo**. Essa opção é visível somente para administradores de função com privilégios e administradores globais porque essas são apenas duas funções que podem definir a opção.

    [![Tornar o novo grupo qualificado para atribuição de função](./media/groups-create-eligible/eligible-switch.png "Tornar o novo grupo qualificado para atribuição de função")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. Selecione os membros e proprietários do grupo. Você também tem a opção de atribuir funções ao grupo, mas a atribuição de uma função não é necessária aqui.

    [![Adicione membros ao grupo de funções atribuídas e atribua funções.](./media/groups-create-eligible/specify-members.png "Adicione membros ao grupo de funções atribuídas e atribua funções.")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. Depois que os membros e proprietários forem especificados, selecione **criar**.

    [![O botão criar está na parte inferior da página.](./media/groups-create-eligible/create-button.png "O botão criar está na parte inferior da página.")](./media/groups-create-eligible/create-button.png#<lightbox>)

O grupo é criado com qualquer função que você tenha atribuído a ele.

## <a name="using-powershell"></a>Usando o PowerShell

### <a name="install-the-azure-ad-preview-module"></a>Instalar o módulo de visualização do Azure AD

```powershell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

Para verificar se o módulo está pronto para uso, emita o seguinte comando:

```powershell
Get-Module -Name AzureADPreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Criar um grupo que possa ser atribuído à função

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

Para esse tipo de grupo, `isPublic` sempre será false e `isSecurityEnabled` será sempre verdadeiro.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>Copiar os usuários e as entidades de serviço de um grupo em um grupo de função atribuível

```powershell
#Basic set up
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
Get-Module -Name AzureADPreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>Usando a API Microsoft Graph

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Criar um grupo de funções atribuíveis no Azure AD

```http
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

Para esse tipo de grupo, `isPublic` sempre será false e `isSecurityEnabled` será sempre verdadeiro.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir uma função a um grupo de nuvem](groups-assign-role.md)
- [Usar grupos de nuvem para gerenciar atribuições de função](groups-concept.md)
- [Solução de problemas de funções atribuídas a grupos de nuvem](groups-faq-troubleshooting.md)
