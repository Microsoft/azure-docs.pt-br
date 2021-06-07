---
title: Remover atribuições de função de um grupo no Azure Active Directory | Microsoft Docs
description: Visualize funções personalizadas do Azure AD para delegar o gerenciamento de identidades. Gerencie funções do Azure no portal do Azure, no PowerShell ou na API do Graph.
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
ms.openlocfilehash: 78ed23f563fce9760768a99e5bbf58f68500d665
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012013"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Remover atribuições de função de um grupo no Azure Active Directory

Este artigo descreve como um administrador de ti pode remover funções do Azure AD atribuídas a grupos. No portal do Azure, agora você pode remover as atribuições de função direta e indireta a um usuário. Se um usuário receber uma função por uma associação de grupo, remova o usuário do grupo para remover a atribuição de função.

## <a name="using-azure-admin-center"></a>Usando o centro de administração do Azure

1. Entre no centro de [Administração do Azure ad](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com permissões de administrador de função privilegiada ou de administrador global na organização do Azure AD.

1. Selecione **funções e administradores**  >  **_nome da função_**.

1. Selecione o grupo do qual você deseja remover a atribuição de função e selecione **remover atribuição**.

   ![Remover uma atribuição de função de um grupo selecionado.](./media/groups-remove-assignment/remove-assignment.png)

1. Quando solicitado a confirmar sua ação, selecione **Sim**.

## <a name="using-powershell"></a>Usando o PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Criar um grupo que possa ser atribuído à função

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>Obter a definição de função à qual você deseja atribuir o grupo

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>Criar uma atribuição de função

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>Remover a atribuição de função

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>Usando a API Microsoft Graph

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Criar um grupo que pode ser atribuído a uma função do Azure AD

```http
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
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

### <a name="get-the-role-definition"></a>Obter a definição de função

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter=displayName+eq+'Helpdesk Administrator'
```

### <a name="create-the-role-assignment"></a>Criar a atribuição de função

```http
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"{object-id-of-group}",
"roleDefinitionId":"{role-definition-id}",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>Excluir atribuição de função

```http
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/{role-assignment-id}
```

## <a name="next-steps"></a>Próximas etapas

- [Usar grupos de nuvem para gerenciar atribuições de função](groups-concept.md)
- [Solução de problemas de funções atribuídas a grupos de nuvem](groups-faq-troubleshooting.md)
