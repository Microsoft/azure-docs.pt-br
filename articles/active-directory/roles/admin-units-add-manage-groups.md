---
title: Adicionar, remover e listar grupos em uma unidade administrativa-Azure Active Directory | Microsoft Docs
description: Gerencie grupos e suas permissões de função em uma unidade administrativa no Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 03/10/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: f88a0818d93c33b6265cc8c695479d2a42678ada
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011027"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Adicionar e gerenciar grupos em uma unidade administrativa no Azure Active Directory

No Azure Active Directory (AD do Azure), você pode adicionar grupos a uma unidade administrativa para um escopo administrativo mais granular do controle.

Para se preparar para usar o PowerShell e Microsoft Graph para o gerenciamento de unidade administrativa [, consulte Introdução](admin-units-manage.md#get-started).

## <a name="add-groups-to-an-administrative-unit"></a>Adicionar grupos a uma unidade administrativa

Você pode adicionar grupos a uma unidade administrativa usando o portal do Azure, o PowerShell ou o Microsoft Graph.

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

Você pode atribuir apenas grupos individuais a uma unidade administrativa. Não há nenhuma opção para atribuir grupos como uma operação em massa. No portal do Azure, você pode atribuir um grupo a uma unidade administrativa de uma das duas maneiras:

* No painel **grupos** :

  1. Na portal do Azure, vá para **Azure ad**.
  1. Selecione **grupos** e, em seguida, selecione o grupo que você deseja atribuir à unidade administrativa. 
  1. No painel esquerdo, selecione **unidades administrativas** para exibir uma lista das unidades administrativas às quais o grupo está atribuído. 

     ![Captura de tela do link "atribuir à unidade administrativa" no painel "unidades administrativas".](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. Selecione **atribuir à unidade administrativa**.
  1. No painel direito, selecione a unidade administrativa.

* No painel **unidades administrativas**  >  **todos os grupos** :

  1. Na portal do Azure, vá para **Azure ad**.
  
  1. No painel esquerdo, selecione **unidades administrativas** e, em seguida, selecione **todos os grupos**. 
     Todos os grupos que já estão atribuídos à unidade administrativa são exibidos no painel direito. 

  1. No painel **grupos** , selecione **Adicionar**.
    O painel direito lista todos os grupos disponíveis na sua organização do Azure AD. 

     ![Captura de tela do botão "Adicionar" para adicionar um grupo a uma unidade administrativa.](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Selecione um ou mais grupos a serem atribuídos à unidade administrativa e, em seguida, selecione o botão **selecionar** .

### <a name="use-powershell"></a>Usar o PowerShell

No exemplo a seguir, use o `Add-AzureADMSAdministrativeUnitMember` cmdlet para adicionar o grupo à unidade administrativa. A ID de objeto da unidade administrativa e a ID de objeto do grupo a ser adicionado são executadas como argumentos. Altere a seção realçada conforme necessário para seu ambiente específico.


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>Usar Microsoft Graph

Execute os seguintes comandos:

Solicitação

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Corpo

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{group-id}"
}
```

Exemplo

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>Exibir uma lista de grupos em uma unidade administrativa

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

1. Na portal do Azure, vá para **Azure ad**.

1. No painel esquerdo, selecione **unidades administrativas** e, em seguida, selecione a unidade administrativa cujos grupos você deseja exibir. Por padrão, **todos os usuários** estão selecionados no painel esquerdo. 

1. No painel esquerdo, selecione **grupos**. O painel direito exibe uma lista de grupos que são membros da unidade administrativa selecionada.

   ![Captura de tela do painel "grupos" exibindo uma lista de grupos em uma unidade administrativa.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Usar o PowerShell

Para exibir uma lista de todos os membros da unidade administrativa, execute o seguinte comando: 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId
```

Para exibir todos os grupos que são membros da unidade administrativa, use o seguinte trecho de código:

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>Usar Microsoft Graph

Execute o seguinte comando:

Solicitação

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

Corpo

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Exibir uma lista de unidades administrativas para um grupo

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

1. Na portal do Azure, vá para **Azure ad**.

1. No painel esquerdo, selecione **grupos** para exibir uma lista de grupos.

1. Selecione um grupo para abrir o perfil do grupo. 

1. No painel esquerdo, selecione **unidades administrativas** para listar todas as unidades administrativas em que o grupo é membro.

   ![Captura de tela do painel "unidades administrativas", exibindo uma lista de unidades administrativas às quais um grupo é atribuído.](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>Usar o PowerShell

Execute o seguinte comando:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>Usar Microsoft Graph

Execute o seguinte comando:

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Remover um grupo de uma unidade administrativa

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

Você pode remover um grupo de uma unidade administrativa na portal do Azure de uma das duas maneiras:

- Remova-o de uma visão geral do Grupo:

  1. Na portal do Azure, vá para **Azure ad**.
  1. No painel esquerdo, selecione **grupos** e, em seguida, abra o perfil do grupo que você deseja remover de uma unidade administrativa.
  1. No painel esquerdo, selecione **unidades administrativas** para listar todas as unidades administrativas às quais o grupo está atribuído. 
  1. Selecione a unidade administrativa da qual você deseja remover o grupo e, em seguida, selecione **Remover da unidade administrativa**.

     ![Captura de tela do painel "unidades administrativas", exibindo uma lista de grupos atribuídos à unidade administrativa selecionada.](./media/admin-units-add-manage-groups/group-au-remove.png)

- Remova-o de uma unidade administrativa:

  1. Na portal do Azure, vá para **Azure ad**.
  1. No painel esquerdo, selecione **unidades administrativas** e, em seguida, selecione a unidade administrativa à qual o grupo está atribuído.
  1. No painel esquerdo, selecione **grupos** para listar todos os grupos atribuídos à unidade administrativa.
  1. Selecione o grupo que você deseja remover e, em seguida, selecione **remover grupos**.

    ![Captura de tela do painel "grupos", exibindo uma lista dos grupos em uma unidade administrativa.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Usar o PowerShell

Execute o seguinte comando:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>Usar Microsoft Graph

Execute o seguinte comando:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>Próximas etapas

- [Atribuir uma função a uma unidade administrativa](admin-units-assign-roles.md)
- [Gerenciar usuários em uma unidade administrativa](admin-units-add-manage-users.md)
