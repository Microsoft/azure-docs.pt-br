---
title: Atribuir um usuário ou grupo a um aplicativo empresarial no Azure Active Directory | Microsoft Docs
description: Como selecionar um aplicativo empresarial par atribuir um usuário ou um grupo a ele no Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 249dfeeb231c61b05af2e89f0dc02822cc18e627
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702186"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Atribuir um usuário ou um grupo a um aplicativo empresarial no Azure Active Directory

Para atribuir um usuário ou grupo a um aplicativo corporativo, você deverá ter as permissões apropriadas para gerenciar o aplicativo empresarial, além de ser um administrador global do diretório. Para aplicativos da Microsoft (como aplicativos do Office 365), use o PowerShell para atribuir usuários a um aplicativo empresarial.

> [!NOTE]
> Para requisitos de licenciamento para os recursos discutidos neste artigo, consulte a [do Azure Active Directory, página de preços](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="assign-a-user-to-an-app---portal"></a>Atribuir um usuário a um aplicativo - portal

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **Todos os serviços**, insira Azure Active Directory na caixa de texto e, em seguida, selecione **Enter**.
1. Selecione **Aplicativos empresariais**.
1. Sobre o **aplicativos empresariais – todos os aplicativos** painel, você ver uma lista de aplicativos você pode gerenciar. Selecione um aplicativo.
1. Sobre o ***appname*** painel (ou seja, o painel com o nome do aplicativo selecionado no título), selecione **usuários e grupos**.
1. Sobre o ***appname*** **-grupos e usuários** painel, selecione **adicionar usuário**.
1. Sobre o **Adicionar atribuição** painel, selecione **usuários e grupos**.

   ![Atribuir um usuário ou um grupo ao aplicativo](./media/assign-user-or-group-access-portal/assign-users.png)

1. No **usuários e grupos** painel, selecione um ou mais usuários ou grupos na lista e, em seguida, escolha o **selecione** botão na parte inferior do painel.
1. Sobre o **Adicionar atribuição** painel, selecione **função**. Em seguida, na **Selecionar função** painel, selecione uma função para aplicar a usuários ou grupos selecionados, em seguida, selecione **Okey** na parte inferior do painel.
1. Sobre o **Adicionar atribuição** painel, selecione o **atribuir** botão na parte inferior do painel. Os usuários ou os grupos atribuídos têm as permissões definidas pela função selecionada para esse aplicativo empresarial.

## <a name="allow-all-users-to-access-an-app---portal"></a>Permitir que todos os usuários acessem um aplicativo - portal

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **Todos os serviços**, insira Azure Active Directory na caixa de texto e, em seguida, selecione **Enter**.
1. Selecione **Aplicativos empresariais**.
1. No painel **Aplicativos empresariais**, selecione **Todos os aplicativos**. Lista os aplicativos que você pode gerenciar.
1. No painel **Aplicativos empresariais – Todos os aplicativos**, selecione um aplicativo.
1. Sobre o ***appname*** painel, selecione **propriedades**.
1. Sobre o  ***appname* -propriedades** painel, defina o **atribuição de usuário necessária?** definindo como **não**.

**Atribuição de usuário obrigatória?** opção:

- Não afeta se um aplicativo é exibida no painel de acesso do aplicativo. Para mostrar o aplicativo no painel de acesso, você precisará atribuir um grupo ou usuário apropriado para o aplicativo.
- Funciona apenas com os aplicativos de nuvem que estão configurados para logon único SAML e aplicativos locais configurados com o Proxy do Aplicativo. Consulte [logon único para aplicativos](what-is-single-sign-on.md).
- Requer os usuários de consentimento para um aplicativo. Um administrador pode conceder consentimento para todos os usuários.  Consulte [Configurar a forma com que os usuários finais concedem um aplicativo](configure-user-consent.md).

## <a name="assign-a-user-to-an-app---powershell"></a>Atribuir um usuário a um aplicativo - PowerShell

1. Abra um prompt elevado do Windows PowerShell.

   > [!NOTE]
   > Você precisa instalar o módulo do Azure AD (use o comando `Install-Module -Name AzureAD`). Se solicitado a instalar um módulo do NuGet ou o novo módulo do Azure Active Directory V2 PowerShell, digite Y e pressione ENTER.

1. Execute `Connect-AzureAD` e entre com uma conta de usuário de Administrador Global.
1. Use o script abaixo para atribuir um usuário e uma função a um aplicativo:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

Para saber mais sobre como atribuir um usuário a uma função de aplicativo, consulte a documentação sobre [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Para atribuir um grupo a um aplicativo empresarial, é preciso substituir `Get-AzureADUser` por `Get-AzureADGroup`.

### <a name="example"></a>Exemplo

Este exemplo atribui a usuária Brenda Fernandes ao aplicativo [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) usando o PowerShell.

1. No PowerShell, atribua os valores correspondentes às variáveis $username, app_name $ e $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. Neste exemplo, não sabemos o nome exato da função de aplicativo que desejamos atribuir à Brenda Fernandes. Execute os comandos a seguir para obter o usuário ($user) e a entidade de serviço ($sp) usando o nome UPN do usuário e o nome de exibição da entidade de serviço.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Execute o comando `$sp.AppRoles` para exibir as funções disponíveis para o aplicativo Workplace Analytics. Neste exemplo, queremos atribuir a Brenda Fernandes a função de Analista (acesso limitado).

   ![Mostra as funções disponíveis para um usuário usando a função do Workplace Analytics](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Atribua o nome de função à variável `$app_role_name`.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Execute o seguinte comando para atribuir o usuário à função de aplicativo:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Próximas etapas

- [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Remover uma atribuição de usuário ou grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
- [Desabilitar as entradas de usuário em um aplicativo empresarial](disable-user-sign-in-portal.md)
- [Alterar o nome ou logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)
