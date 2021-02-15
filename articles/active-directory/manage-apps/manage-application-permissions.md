---
title: Gerenciar permissões de usuário e administrador-Azure Active Directory | Microsoft Docs
description: Saiba como examinar e gerenciar permissões para o aplicativo no Azure AD. Por exemplo, revogar todas as permissões concedidas a um aplicativo.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd2bc7b9ccc9efe0c6fdf8de02665160667cb8f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256925"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Tome medidas sobre aplicativos com privilégios elevados ou suspeitos no Azure Active Directory

Saiba como examinar e gerenciar permissões de aplicativo. Este artigo fornece ações diferentes que você pode tomar para proteger seu aplicativo de acordo com o cenário. Essas ações se aplicam a todos os aplicativos que foram adicionados ao seu locatário do Azure Active Directory (Azure AD) por meio do consentimento do usuário ou do administrador.

Para obter mais informações sobre o consentimento de aplicativos, consulte a [estrutura de consentimento do Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Pré-requisitos

Para executar as ações a seguir, você deve entrar como um administrador global, um administrador de aplicativos ou um administrador de aplicativos de nuvem.

Para restringir o acesso aos aplicativos, você precisará exigir a atribuição do usuário e, em seguida, atribuir usuários ou grupos ao aplicativo.  Para obter mais informações, confira [Métodos para atribuir usuários e grupos](./assign-user-or-group-access-portal.md).

Você pode acessar o portal do AD do Azure para obter scripts do PowerShell contextuais para executar as ações.
 
1. Faça login no [Portal do Azure](https://portal.azure.com) como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.
2. Selecione **Azure Active Directory**  >  **aplicativos empresariais**.
3. Selecione o aplicativo ao qual você deseja restringir o acesso.
4. Selecione **Permissões**. Na barra de comandos, selecione **revisar permissões**.

![Captura de tela da janela de permissões de revisão.](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Controlar o acesso a um aplicativo

Recomendamos que você restrinja o acesso ao aplicativo ativando a configuração de **atribuição de usuário** .

1. Faça login no [Portal do Azure](https://portal.azure.com) como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.
2. Selecione **Azure Active Directory**  >  **aplicativos empresariais**.
3. Selecione o aplicativo ao qual você deseja restringir o acesso.
4. Selecione **Propriedades** e defina requisito de **usuário necessário** para **Sim**.
5. Selecione **usuário e grupos** e, em seguida, remova os usuários indesejados atribuídos ao aplicativo.
6. Atribua usuários ou grupos ao aplicativo.

Opcionalmente, você pode remover todos os usuários atribuídos ao aplicativo usando o PowerShell.

## <a name="revoke-all-permissions-for-an-application"></a>Revogar todas as permissões para um aplicativo

O uso do script do PowerShell revoga todas as permissões concedidas a este aplicativo.

> [!NOTE]
> A revogação da permissão concedida atual não impedirá que os usuários retornem ao aplicativo. Se você quiser impedir que os usuários consentim, leia [configurar como os usuários consentirão com os aplicativos](configure-user-consent.md).

Opcionalmente, você pode desabilitar o aplicativo para impedir que os usuários acessem o aplicativo e impedir que o aplicativo acesse seus dados.

1. Faça login no [Portal do Azure](https://portal.azure.com) como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.
2. Selecione **Azure Active Directory**  >  **aplicativos empresariais**.
3. Selecione o aplicativo ao qual você deseja restringir o acesso.
4. Selecione **Propriedades** e, em seguida, defina **habilitado para que os usuários entrem?** para **não**.

## <a name="investigate-a-suspicious-application"></a>Investigar um aplicativo suspeito

Recomendamos que você restrinja o acesso ao aplicativo ativando a configuração de **atribuição de usuário** . Em seguida, revise as permissões que os usuários e administradores concederam ao aplicativo.

1. Faça login no [Portal do Azure](https://portal.azure.com) como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.
3. Selecione **Azure Active Directory**  >  **aplicativos empresariais**.
5. Selecione o aplicativo ao qual você deseja restringir o acesso.
6. Selecione **Propriedades** e defina requisito de **usuário necessário** para **Sim**.
7. Selecione **permissões** e examine as permissões de administrador e de usuário consentidas.

Opcionalmente, usando o PowerShell, você pode:

- Remova todos os usuários atribuídos para impedir que eles façam logon no aplicativo.
- Invalidar tokens de atualização para usuários que têm acesso ao aplicativo.
- Revogar todas as permissões para o aplicativo.

Ou você pode desabilitar o aplicativo para bloquear o acesso dos usuários e interromper o acesso do aplicativo aos seus dados.


## <a name="disable-a-malicious-application"></a>Desabilitar um aplicativo mal-intencionado 

Recomendamos que você desabilite o aplicativo para bloquear o acesso dos usuários e impedir que o aplicativo acesse seus dados. Se você excluir o aplicativo em vez disso, os usuários poderão dar uma nova autorização ao aplicativo e conceder acesso aos seus dados.

1. Faça login no [Portal do Azure](https://portal.azure.com) como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.
2. Selecione **Azure Active Directory**  >  **aplicativos empresariais**.
3. Selecione o aplicativo ao qual você deseja restringir o acesso.
4. Selecione **Propriedades** e, em seguida, copie a ID do objeto.

### <a name="powershell-commands"></a>Comandos do PowerShell


Recupere a ID de objeto da entidade de serviço.

1. Faça login no [Portal do Azure](https://portal.azure.com) como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.
2. Selecione **Azure Active Directory**  >  **aplicativos empresariais**.
3. Selecione o aplicativo ao qual você deseja restringir o acesso.
4. Selecione **Propriedades** e, em seguida, copie a ID do objeto.

```powershell
$sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
$sp.ObjectId
```
Remova todos os usuários atribuídos ao aplicativo.
 ```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectId of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

# Remove all users and groups assigned to the application
$assignments | ForEach-Object {
    if ($_.PrincipalType -eq "User") {
        Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    } elseif ($_.PrincipalType -eq "Group") {
        Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    }
}
 ```

Revogar permissões concedidas ao aplicativo.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

# Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}

# Get all application permissions for the service principal
$spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

# Remove all delegated permissions
$spApplicationPermissions | ForEach-Object {
    Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
}
```
Invalidar os tokens de atualização.
```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectID of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

# Revoke refresh token for all users assigned to the application
$assignments | ForEach-Object {
    Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
}
```
## <a name="next-steps"></a>Próximas etapas
- [Gerenciar o consentimento para aplicativos e avaliar a solicitação de consentimento](manage-consent-requests.md)
- [Configurar consentimento do usuário](configure-user-consent.md)
- [Configurar fluxo de trabalho de consentimento do administrador](configure-admin-consent-workflow.md)
