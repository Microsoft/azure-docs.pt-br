---
title: Listar atribuições de função do Azure AD
description: Agora você pode ver e gerenciar membros de uma função de administrador Azure Active Directory no centro de administração do Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: de546ef091b1a8e996f286b0c9af45e93488b5b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467639"
---
# <a name="list-azure-ad-role-assignments"></a>Listar atribuições de função do Azure AD

Este artigo descreve como listar funções que você atribuiu no Azure Active Directory (Azure AD). No Azure Active Directory (AD do Azure), as funções podem ser atribuídas em um escopo de toda a organização ou com um escopo de aplicativo único.

- As atribuições de função no escopo de toda a organização são adicionadas e podem ser vistas na lista de atribuições de função de aplicativo único.
- As atribuições de função no escopo do aplicativo único não são adicionadas e não podem ser vistas na lista de atribuições no escopo de toda a organização.

## <a name="list-role-assignments-in-the-azure-portal"></a>Listar atribuições de função no portal do Azure

Este procedimento descreve como listar as atribuições de função com escopo de toda a organização.

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com permissões de administrador de função privilegiada ou de administrador global na organização do Azure AD.
1. Selecione **Azure Active Directory**, selecione **funções e administradores** e, em seguida, selecione uma função para abri-la e exibir suas propriedades.
1. Selecione **atribuições** para listar as atribuições de função.

    ![Listar atribuições de função e permissões ao abrir uma função da lista](./media/view-assignments/role-assignments.png)

## <a name="list-my-role-assignments"></a>Listar minhas atribuições de função

Também é fácil listar suas próprias permissões. Selecione **Sua função** na página **Funções e administradores** para ver as funções atribuídas atualmente a você.

## <a name="download-role-assignments"></a>Baixar atribuições de função

Para baixar todas as atribuições de uma função específica, na página **funções e administradores** , selecione uma função e, em seguida, selecione **baixar atribuições de função**. Um arquivo CSV que lista as atribuições em todos os escopos para essa função é baixado.

![baixar todas as atribuições de uma função](./media/view-assignments/download-role-assignments.png)

## <a name="list-role-assignments-using-azure-ad-powershell"></a>Listar atribuições de função usando o PowerShell do Azure AD

Esta seção descreve a exibição de atribuições de uma função com escopo de toda a organização. Este artigo usa o módulo [Azure Active Directory PowerShell versão 2](/powershell/module/azuread/#directory_roles) . Para exibir atribuições de escopo de aplicativo único usando o PowerShell, você pode usar os cmdlets em [atribuir funções personalizadas com o PowerShell](custom-assign-powershell.md).

### <a name="prepare-powershell"></a>Preparar o PowerShell

Primeiro, você deve [baixar o módulo PowerShell de visualização do Azure ad](https://www.powershellgallery.com/packages/AzureAD/).

Para instalar o módulo PowerShell do Azure AD, use os seguintes comandos:

``` PowerShell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

Para verificar se o módulo está pronto para ser usado, use o seguinte comando:

``` PowerShell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="list-role-assignments"></a>Listar atribuições de função

Exemplo de listagem das atribuições de função.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="list-role-assignments-using-microsoft-graph-api"></a>Listar atribuições de função usando Microsoft Graph API

Esta seção descreve como listar as atribuições de função com escopo de toda a organização.  Para listar atribuições de função de escopo de aplicativo único usando API do Graph, você pode usar as operações em [atribuir funções personalizadas com API do Graph](custom-assign-graph.md).

Solicitação HTTP para obter uma atribuição de função para uma determinada definição de função.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Resposta

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="list-role-assignments-with-single-application-scope"></a>Listar atribuições de função com escopo de aplicativo único

Esta seção descreve como listar atribuições de função com escopo de aplicativo único. Esse recurso está atualmente em visualização pública.

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com permissões de administrador de função privilegiada ou de administrador global na organização do Azure AD.
1. Selecione **registros de aplicativo** e, em seguida, selecione o registro do aplicativo para exibir suas propriedades. Talvez seja necessário selecionar **Todos os aplicativos** para ver a lista completa de registros de aplicativo em sua organização do Azure AD.

    ![Criar ou editar registros de aplicativo na página Registros de aplicativo](./media/view-assignments/app-reg-all-apps.png)

1. No registro do aplicativo, selecione **funções e administradores** e, em seguida, selecione uma função para exibir suas propriedades.

    ![Listar atribuições de função de registro de aplicativo na página Registros de aplicativo](./media/view-assignments/app-reg-assignments.png)

1. Selecione **atribuições** para listar as atribuições de função. Abrir a página atribuições de dentro do registro do aplicativo mostra as atribuições de função que têm o escopo deste recurso do Azure AD.

    ![Listar atribuições de função de registro de aplicativo das propriedades de um registro de aplicativo](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>Próximas etapas

* Fique à vontade para compartilhar seus comentários conosco no [fórum de funções administrativas do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Para obter mais informações sobre funções e a atribuição de função de Administrador, confira [Atribuir funções de administrador](permissions-reference.md).
* Para obter as permissões de usuário padrão, confira uma [comparação entre as permissões de usuário membro e convidado padrão](../fundamentals/users-default-permissions.md).
