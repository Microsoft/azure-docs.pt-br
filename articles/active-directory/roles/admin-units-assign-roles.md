---
title: Atribuir e listar funções com escopo de unidade administrativa-Azure Active Directory | Microsoft Docs
description: Use unidades administrativas para restringir o escopo de atribuições de função no Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fc0c4bf9f71a8fe7e8cf49b83d32ac594dbe062
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011368"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Atribuir funções a uma unidade administrativa

No Azure Active Directory (AD do Azure), para controle administrativo mais granular, você pode atribuir usuários a uma função do Azure AD com um escopo limitado a uma ou mais unidades administrativas.

Para se preparar para usar o PowerShell e Microsoft Graph para o gerenciamento de unidade administrativa [, consulte Introdução](admin-units-manage.md#get-started).

## <a name="available-roles"></a>Funções disponíveis

Função  |  Descrição
----- |  -----------
Administrador de Autenticação  |  Tem acesso para exibir, definir e redefinir informações do método de autenticação para qualquer usuário não administrador somente na unidade administrativa atribuída.
Administrador de Grupos  |  Pode gerenciar todos os aspectos de configurações de grupos e grupos, como políticas de nomenclatura e expiração, somente na unidade administrativa atribuída.
Administrador de assistência técnica  |  O pode redefinir senhas para não administradores e administradores de assistência técnica somente na unidade administrativa atribuída.
Administrador de Licenças  |  Pode atribuir, remover e atualizar atribuições de licença somente dentro da unidade administrativa.
Administrador de senha  |  O pode redefinir senhas para não administradores e administradores de senha somente dentro da unidade administrativa atribuída.
Administrador de usuários  |  Pode gerenciar todos os aspectos de usuários e grupos, incluindo a redefinição de senhas para administradores limitados somente na unidade administrativa atribuída.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Entidades de segurança que podem ser atribuídas a uma função com escopo

As entidades de segurança a seguir podem ser atribuídas a uma função com um escopo de unidade administrativa:

* Usuários
* Grupos de nuvem de atribuição de função (visualização)
* SPN (Nome da Entidade de Serviço)

## <a name="assign-a-scoped-role"></a>Atribuir uma função com escopo

Você pode atribuir uma função com escopo usando o portal do Azure, o PowerShell ou o Microsoft Graph.

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

1. Na portal do Azure, vá para **Azure ad**.

1. Selecione **unidades administrativas** e, em seguida, selecione a unidade administrativa à qual você deseja atribuir um escopo de função de usuário. 

1. No painel esquerdo, selecione **funções e administradores** para listar todas as funções disponíveis.

   ![Captura de tela do painel "função e administradores" para selecionar uma unidade administrativa cujo escopo de função você deseja atribuir.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Selecione a função a ser atribuída e, em seguida, selecione **Adicionar atribuições**. 

1. No painel **Adicionar atribuições** , selecione um ou mais usuários a serem atribuídos à função.

   ![Selecione a função para o escopo e, em seguida, selecione Adicionar atribuições](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Para atribuir uma função em uma unidade administrativa usando Azure AD Privileged Identity Management (PIM), consulte [atribuir funções do Azure AD no PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="use-powershell"></a>Usar o PowerShell

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$roleMember.ObjectId = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $adminUnitObj.ObjectId -RoleObjectId $role.ObjectId -RoleMemberInfo $roleMember
```

Você pode alterar a seção realçada conforme necessário para o ambiente específico.

### <a name="use-microsoft-graph"></a>Usar Microsoft Graph

Solicitação

```http
POST /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```
    
Corpo

```http
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>Exibir uma lista de administradores com escopo em uma unidade administrativa

Você pode exibir uma lista de administradores com escopo usando o portal do Azure, o PowerShell ou o Microsoft Graph.

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

Você pode exibir todas as atribuições de função criadas com um escopo de unidade administrativa na [seção unidades administrativas do Azure ad](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). 

1. Na portal do Azure, vá para **Azure ad**.

1. No painel esquerdo, selecione **unidades administrativas** e, em seguida, selecione a unidade administrativa para a lista de atribuições de função que você deseja exibir. 

1. Selecione **funções e administradores** e, em seguida, abra uma função para exibir as atribuições na unidade administrativa.

### <a name="use-powershell"></a>Usar o PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $adminUnitObj.ObjectId | fl *
```

Você pode alterar a seção realçada conforme necessário para seu ambiente específico.

### <a name="use-microsoft-graph"></a>Usar Microsoft Graph

Solicitação

```http
GET /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```

Corpo

```http
{}
```

## <a name="next-steps"></a>Próximas etapas

- [Usar grupos de nuvem para gerenciar atribuições de função](groups-concept.md)
- [Solucionar problemas de funções atribuídas a grupos de nuvem](groups-faq-troubleshooting.md)
