---
title: Exibir atribuições de função personalizadas no portal de Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: c13167706c2794253108b04eaa2dcc46e0e6c09a
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740679"
---
# <a name="view-custom-role-assignments-using-azure-active-directory"></a>Exibir atribuições de função personalizadas usando Azure Active Directory

Este artigo descreve como exibir funções personalizadas que você atribuiu no Azure Active Directory (Azure AD). No Azure Active Directory (AD do Azure), as funções podem ser atribuídas em um escopo de toda a organização ou com um escopo de aplicativo único.

- As atribuições de função no escopo de toda a organização são adicionadas e podem ser vistas na lista de atribuições de função de aplicativo único.
- As atribuições de função no escopo do aplicativo único não são adicionadas e não podem ser vistas na lista de atribuições no escopo de toda a organização.

## <a name="view-role-assignments-in-the-azure-portal"></a>Exibir atribuições de função no portal do Azure

Este procedimento descreve a exibição de atribuições de uma função com escopo de toda a organização.

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com permissões de administrador de função privilegiada ou de administrador global na organização do Azure AD.
1. Selecione **Azure Active Directory**, selecione **funções e administradores** e, em seguida, selecione uma função para abri-la e exibir suas propriedades.
1. Selecione **atribuições** para exibir as atribuições da função.

    ![Exibir atribuições de função e permissões ao abrir uma função da lista](./media/view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Exibir atribuições de função usando o PowerShell do Azure AD

Esta seção descreve a exibição de atribuições de uma função com escopo de toda a organização. Este artigo usa o módulo [Azure Active Directory PowerShell versão 2](/powershell/module/azuread/#directory_roles) . Para exibir atribuições de escopo de aplicativo único usando o PowerShell, você pode usar os cmdlets em [atribuir funções personalizadas com o PowerShell](custom-assign-powershell.md).

### <a name="prepare-powershell"></a>Preparar o PowerShell

Primeiro, você deve [baixar o módulo PowerShell de visualização do Azure ad](https://www.powershellgallery.com/packages/AzureAD/).

Para instalar o módulo PowerShell do Azure AD, use os seguintes comandos:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Para verificar se o módulo está pronto para ser usado, use o seguinte comando:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>Exibir as atribuições de uma função

Exemplo de exibição das atribuições de uma função.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Exibir atribuições de função usando a API Microsoft Graph

Esta seção descreve a exibição de atribuições de uma função com escopo de toda a organização.  Para exibir atribuições de escopo de aplicativo único usando API do Graph, você pode usar as operações em [atribuir funções personalizadas com API do Graph](custom-assign-graph.md).

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

## <a name="view-assignments-of-single-application-scope"></a>Exibir atribuições de escopo de aplicativo único

Esta seção descreve a exibição de atribuições de uma função com escopo de aplicativo único. Esse recurso está atualmente em visualização pública.

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com permissões de administrador de função privilegiada ou de administrador global na organização do Azure AD.
1. Selecione **registros de aplicativo** e, em seguida, selecione o registro do aplicativo para exibir suas propriedades. Talvez seja necessário selecionar **Todos os aplicativos** para ver a lista completa de registros de aplicativo em sua organização do Azure AD.

    ![Criar ou editar registros de aplicativo na página Registros de aplicativo](./media/view-assignments/app-reg-all-apps.png)

1. No registro do aplicativo, selecione **funções e administradores** e, em seguida, selecione uma função para exibir suas propriedades.

    ![Exibir atribuições de função de registro de aplicativo na página Registros de aplicativo](./media/view-assignments/app-reg-assignments.png)

1. Selecione **atribuições** para exibir as atribuições da função. Abrir a exibição atribuições de dentro do registro do aplicativo mostra as atribuições que têm o escopo definido para esse recurso do Azure AD.

    ![Exibir atribuições de função de registro de aplicativo das propriedades de um registro de aplicativo](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>Próximas etapas

* Fique à vontade para compartilhar seus comentários conosco no [fórum de funções administrativas do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Para obter mais informações sobre funções e a atribuição de função de Administrador, confira [Atribuir funções de administrador](permissions-reference.md).
* Para obter as permissões de usuário padrão, confira uma [comparação entre as permissões de usuário membro e convidado padrão](../fundamentals/users-default-permissions.md).
