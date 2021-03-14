---
title: Adicionar, remover e listar usuários em uma unidade administrativa-Azure Active Directory | Microsoft Docs
description: Gerenciar usuários e suas permissões de função em uma unidade administrativa no Azure Active Directory
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
ms.openlocfilehash: 7a9d80344a31023d174935e7f785e36102e99eba
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011504"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Adicionar e gerenciar usuários em uma unidade administrativa no Azure Active Directory

No Azure Active Directory (AD do Azure), você pode adicionar usuários a uma unidade administrativa para um escopo administrativo mais granular do controle.

Para se preparar para usar o PowerShell e Microsoft Graph para o gerenciamento de unidade administrativa [, consulte Introdução](admin-units-manage.md#get-started).

## <a name="add-users-to-an-administrative-unit"></a>Adicionar usuários a uma unidade administrativa

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

Você pode atribuir usuários a unidades administrativas individualmente ou como uma operação em massa.

- Atribuir usuários individuais de um perfil de usuário:

   1. Entre no centro de [Administração do Azure ad](https://portal.azure.com) com permissões de administrador de função privilegiada.

   1. Selecione **usuários** e, em seguida, para abrir o perfil do usuário, selecione o usuário a ser atribuído a uma unidade administrativa.
   
   1. Selecione **unidades administrativas**. 
   
   1. Para atribuir o usuário a uma ou mais unidades administrativas, selecione **atribuir à unidade administrativa** e, no painel direito, selecione as unidades administrativas às quais você deseja atribuir o usuário.

       ![Captura de tela do painel "unidades administrativas" para atribuir um usuário a uma unidade administrativa.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Atribuir usuários individuais de uma unidade administrativa:

   1. Entre no centro de [Administração do Azure ad](https://portal.azure.com) com permissões de administrador de função privilegiada.
   1. Selecione **unidades administrativas** e, em seguida, selecione a unidade administrativa na qual o usuário deve ser atribuído.
   1. Selecione **todos os usuários**, selecione **Adicionar membro** e, em seguida, no painel **Adicionar membro** , selecione um ou mais usuários que você deseja atribuir à unidade administrativa.

        ![Captura de tela do painel "usuários" da unidade administrativa para atribuir um usuário a uma unidade administrativa.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Atribuir usuários como uma operação em massa:

   1. Entre no centro de [Administração do Azure ad](https://portal.azure.com) com permissões de administrador de função privilegiada.

   1. Selecione **unidades administrativas**.

   1. Selecione a unidade administrativa à qual você deseja adicionar usuários.

   1. Selecione **usuários**  >  **atividades em massa**  >  **adicionar membros em massa**. Em seguida, você pode baixar o modelo de valores separados por vírgulas (CSV) e editar o arquivo. O formato é simples e precisa que um único nome principal de usuário seja adicionado em cada linha. Depois que o arquivo estiver pronto, salve-o em um local apropriado e, em seguida, carregue-o como parte desta etapa.

      ![Captura de tela do painel "usuários" para atribuir usuários a uma unidade administrativa como uma operação em massa.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>Usar o PowerShell

No PowerShell, use o `Add-AzureADAdministrativeUnitMember` cmdlet no exemplo a seguir para adicionar o usuário à unidade administrativa. A ID de objeto da unidade administrativa à qual você deseja adicionar o usuário e a ID de objeto do usuário que você deseja adicionar são usadas como argumentos. Altere a seção realçada conforme necessário para seu ambiente específico.

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$userObj = Get-AzureADUser -Filter "UserPrincipalName eq 'bill@example.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.ObjectId -RefObjectId $userObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Usar Microsoft Graph

Substitua o espaço reservado por informações de teste e execute o seguinte comando:

Solicitação

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Corpo

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{user-id}"
}
```

Exemplo

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/john@example.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>Exibir uma lista de unidades administrativas para um usuário

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

No portal do Azure, você pode abrir o perfil de um usuário fazendo o seguinte:

1. Vá para **Azure ad** e selecione **usuários**.

1. Selecione o usuário cujo perfil você deseja exibir.

1. Selecione **unidades administrativas** para exibir a lista de unidades administrativas às quais o usuário foi atribuído.

   ![Captura de tela de unidades administrativas para as quais um usuário foi atribuído.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>Usar o PowerShell

Execute o seguinte comando:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

> [!NOTE]
> Por padrão, `Get-AzureADAdministrativeUnitMember` retorna apenas 100 membros de uma unidade administrativa. Para recuperar mais membros, você pode adicionar `"-All $true"` .

### <a name="use-microsoft-graph"></a>Usar Microsoft Graph

Substitua o espaço reservado por informações de teste e execute o seguinte comando:

```http
https://graph.microsoft.com/v1.0/users/{user-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Remover um único usuário de uma unidade administrativa

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

Você pode remover um usuário de uma unidade administrativa de uma das duas maneiras: 

* Na portal do Azure, vá para **Azure ad** e, em seguida, selecione **usuários**. 
  1. Selecione o usuário para abrir o perfil do usuário. 
  1. Selecione a unidade administrativa da qual você deseja remover o usuário e, em seguida, selecione **remover da unidade administrativa**.

     ![Captura de tela mostrando como remover um usuário de uma unidade administrativa do painel de perfil do usuário.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* Na portal do Azure, vá para **Azure ad** e, em seguida, selecione **unidades administrativas**.
  1. Selecione a unidade administrativa da qual você deseja remover o usuário. 
  1. Selecione o usuário e, em seguida, selecione **Remover membro**.
  
     ![Captura de tela mostrando como remover um usuário no nível de unidade administrativa.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>Usar o PowerShell

Execute o seguinte comando:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $adminUnitId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Usar Microsoft Graph

Substitua os espaços reservados por informações de teste e execute o seguinte comando:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{user-id}/$ref
```

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Remover vários usuários como uma operação em massa

Para remover vários usuários de uma unidade administrativa, faça o seguinte:

1. Na portal do Azure, vá para **Azure ad**.

1. Selecione **unidades administrativas** e, em seguida, selecione a unidade administrativa da qual você deseja remover os usuários. 

1. Selecione **remover membros em massa** e, em seguida, baixe o modelo CSV que você usará para listar os usuários que deseja remover.

   ![Captura de tela mostrando o link "remover membros em massa" no painel "usuários".](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Edite o modelo CSV baixado com as entradas de usuário relevantes. Não remova as duas primeiras linhas do modelo. Adicione um nome principal de usuário (UPN) em cada linha.

   ![Captura de tela de um arquivo CSV editado para remover usuários de uma unidade administrativa em massa.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Salve as alterações, carregue o arquivo e, em seguida, selecione **Enviar**.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir uma função a uma unidade administrativa](admin-units-assign-roles.md)
- [Adicionar grupos a uma unidade administrativa](admin-units-add-manage-groups.md)
