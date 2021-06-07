---
title: Gerenciar a atribuição de usuário para um aplicativo no Azure Active Directory
description: Saiba como atribuir e cancelar a atribuição de usuários e grupos para um aplicativo usando Azure Active Directory para gerenciamento de identidade.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: 50dcde478b708cd53d8229d70a9ddf4b1ff271be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259727"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Gerenciar a atribuição de usuário para um aplicativo no Azure Active Directory

Este artigo mostra como atribuir usuários e grupos a aplicativos empresariais no Azure Active Directory (Azure AD), seja de dentro do portal do Azure ou usando o PowerShell. Quando você atribui um usuário a um aplicativo, o aplicativo é exibido nos [meus aplicativos](https://myapps.microsoft.com/) do usuário para facilitar o acesso. Se o aplicativo expõe funções, você também pode atribuir uma função específica ao usuário.

Para maior controle, determinados tipos de aplicativos empresariais podem ser configurados para [exigir a atribuição de usuário](#configure-an-application-to-require-user-assignment). 

> [!IMPORTANT]
> Quando você atribuir um grupo a um aplicativo, somente os usuários no grupo terão acesso. A atribuição não se propaga para grupos aninhados.

> [!NOTE]
> A atribuição baseada em grupo exige o Azure Active Directory Premium edição P1 ou P2. A atribuição baseada em grupo é compatível somente com os grupos de segurança. Atualmente, não há compatibilidade com associações de grupo aninhado e grupos do Microsoft 365. Para ver mais requisitos de licenciamento dos recursos discutidos neste artigo, confira a [página de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Configurar um aplicativo para exigir a atribuição de usuários

Com os seguintes tipos de aplicativos, você tem a opção de exigir que os usuários sejam atribuídos ao aplicativo antes que eles possam acessá-lo:

- Aplicativos configurados para SSO (logon único) federado com autenticação baseada em SAML
- Aplicativos de proxy de aplicativo que usam a pré-autenticação do Azure Active Directory
- Aplicativos criados na plataforma de aplicativos do Azure AD que usam a autenticação OAuth 2.0/OpenID Connect depois que um usuário ou administrador autoriza o aplicativo.

Quando a atribuição de usuário é necessária, somente os usuários que você atribui explicitamente ao aplicativo (por meio da atribuição de usuário direta ou com base na associação de grupo) poderão entrar. Eles podem acessar o aplicativo na página Meus Aplicativos ou usando um link direto. 

Quando a atribuição *não é necessária*, seja porque você definiu essa opção como **Não** ou porque o aplicativo usa outro modo SSO, qualquer usuário poderá acessar o aplicativo se tiver um link direto para ele ou a **URL de acesso do usuário** na página **Propriedades** do aplicativo. 

Essa configuração não afeta se um aplicativo aparece ou não em meus aplicativos. Os aplicativos aparecem nos painéis de acesso Meus Aplicativos dos usuários depois que você atribuiu um usuário ou grupo ao aplicativo. Para saber mais, confira [Gerenciar o acesso a aplicativos](what-is-access-management.md).

Para exigir a atribuição de usuários a um aplicativo:
1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador ou proprietário do aplicativo.
2. Selecione **Azure Active Directory**. No menu de navegação esquerdo, selecione **Aplicativos empresariais**.
3. Selecione o aplicativo na lista. Se você não encontrar o aplicativo, comece a digitar o nome dele na caixa de pesquisa. Outra opção é usar os controles de filtro para selecionar o tipo, o status ou a visibilidade do aplicativo e, em seguida, selecionar **Aplicar**.
4. No menu de navegação esquerdo, selecione **Propriedades**.
5. Defina o botão de alternância **Atribuição de usuário necessária?** para **Sim**.
   > [!NOTE]
   > Se o botão de alternância **Atribuição de usuário necessária?** não estiver disponível, use o PowerShell para definir a propriedade appRoleAssignmentRequired na entidade de serviço.
6. Clique no botão **Salvar** na parte superior da tela.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Atribuir ou cancelar a atribuição de usuários e grupos para um aplicativo usando o portal do Azure
Para saber como atribuir ou cancelar a atribuição de um usuário ou grupo usando o portal do Azure, consulte a [série de guias de início rápido sobre gerenciamento de aplicativos](add-application-portal-assign-users.md).

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Atribuir ou cancelar a atribuição de usuários e grupos para um aplicativo usando o API do Graph
Você pode usar a API do Graph para atribuir ou cancelar a atribuição de usuários e grupos para um aplicativo. Para saber mais, confira [atribuições de função de aplicativo](/graph/api/resources/approleassignment).

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Atribuir usuários e grupos a um aplicativo usando o PowerShell
1. Abra um prompt elevado do Windows PowerShell.
   > [!NOTE]
   > Você precisa instalar o módulo do Azure AD (use o comando `Install-Module -Name AzureAD`). Se solicitado a instalar um módulo do NuGet ou o novo módulo do Azure Active Directory V2 PowerShell, digite Y e pressione ENTER.
2. Execute `Connect-AzureAD` e entre com uma conta de usuário de Administrador Global.
3. Use o script abaixo para atribuir um usuário e uma função a um aplicativo:

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```
Para saber mais sobre como atribuir um usuário a uma função de aplicativo, confira a documentação sobre [New-AzureADUserAppRoleAssignment](/powershell/module/azuread/new-azureaduserapproleassignment).

Para atribuir um grupo a um aplicativo empresarial, é preciso substituir `Get-AzureADUser` por `Get-AzureADGroup` e `New-AzureADUserAppRoleAssignment` por `New-AzureADGroupAppRoleAssignment`.

Para saber mais sobre como atribuir um grupo a uma função de aplicativo, confira a documentação sobre [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment).

### <a name="example"></a>Exemplo

Este exemplo atribui a usuária Brenda Fernandes ao aplicativo [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) usando o PowerShell.

1. No PowerShell, atribua os valores correspondentes às variáveis $username, app_name $ e $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. Neste exemplo, não sabemos o nome exato da função de aplicativo que desejamos atribuir à Brenda Fernandes. Execute os comandos a seguir para obter o usuário ($user) e a entidade de serviço ($sp) usando o nome UPN do usuário e o nome de exibição da entidade de serviço.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. Execute o comando `$sp.AppRoles` para exibir as funções disponíveis para o aplicativo Workplace Analytics. Neste exemplo, queremos atribuir a Brenda Fernandes a função de Analista (acesso limitado).
   ![Mostra as funções disponíveis para um usuário que usa a função de análise](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Atribua o nome de função à variável `$app_role_name`.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. Execute o seguinte comando para atribuir o usuário à função de aplicativo:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Cancelar a atribuição de usuários e grupos de um aplicativo usando o PowerShell

1. Abra um prompt elevado do Windows PowerShell.
   > [!NOTE]
   > Você precisa instalar o módulo do Azure AD (use o comando `Install-Module -Name AzureAD`). Se solicitado a instalar um módulo do NuGet ou o novo módulo do Azure Active Directory V2 PowerShell, digite Y e pressione ENTER.
2. Execute `Connect-AzureAD` e entre com uma conta de usuário de Administrador Global.
3. Use o script a seguir para remover um usuário e uma função de um aplicativo:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```


## <a name="related-articles"></a>Artigos relacionados

- [Saiba mais sobre o acesso do usuário final a aplicativos](end-user-experiences.md)
- [Planejar uma implantação de meus aplicativos do Azure AD](my-apps-deployment-plan.md)
- [Gerenciamento do acesso aos aplicativos](what-is-access-management.md)
 
## <a name="next-steps"></a>Próximas etapas

- [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial]()
- [Desabilitar as entradas de usuário em um aplicativo empresarial](disable-user-sign-in-portal.md)
- [Alterar o nome ou logotipo de um aplicativo empresarial](./add-application-portal-configure.md)
