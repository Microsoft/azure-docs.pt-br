---
title: Atribuir uma identidade gerenciada a uma função de aplicativo usando o PowerShell-Azure AD
description: Instruções passo a passo para atribuir um acesso de identidade gerenciada à função de outro aplicativo, usando o PowerShell.
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2d844054e3744aafa01f43ded354a5b5d9f8b4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98732040"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Atribuir um acesso de identidade gerenciada a uma função de aplicativo usando o PowerShell

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade no Azure Active Directory. Eles funcionam sem a necessidade de credenciais em seu código. Os serviços do Azure usam essa identidade para se autenticar em serviços que dão suporte à autenticação do Azure AD. As funções de aplicativo fornecem uma forma de controle de acesso baseado em função e permitem que um serviço implemente regras de autorização.

Neste artigo, você aprende a atribuir uma identidade gerenciada a uma função de aplicativo exposta por outro aplicativo usando o PowerShell do Azure AD.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#managed-identity-types)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os scripts de exemplo, você tem duas opções:
    - Use o [Azure cloud Shell](../../cloud-shell/overview.md), que você pode abrir usando o botão **experimentar** no canto superior direito dos blocos de código.
    - Execute os scripts localmente instalando a versão mais recente do [PowerShell do Azure ad](/powershell/azure/active-directory/install-adv2).

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Atribuir um acesso de identidade gerenciada à função de aplicativo de outro aplicativo

1. Habilite a identidade gerenciada em um recurso do Azure, [como uma VM do Azure](qs-configure-powershell-windows-vm.md).

1. Localize a ID de objeto da entidade de serviço da identidade gerenciada.

   **Para uma identidade gerenciada atribuída pelo sistema**, você pode encontrar a ID de objeto no portal do Azure na página de **identidade** do recurso. Você também pode usar o seguinte script do PowerShell para localizar a ID do objeto. Você precisará da ID de recurso do recurso criado na etapa 1, que está disponível no portal do Azure na página de **Propriedades** do recurso.

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **Para uma identidade gerenciada atribuída pelo usuário**, você pode encontrar a ID de objeto da identidade gerenciada no portal do Azure na página **visão geral** do recurso. Você também pode usar o seguinte script do PowerShell para localizar a ID do objeto. Você precisará da ID de recurso da identidade gerenciada atribuída pelo usuário.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Crie um novo registro de aplicativo para representar o serviço ao qual sua identidade gerenciada enviará uma solicitação. Se a API ou o serviço que expõe a função de aplicativo Grant para a identidade gerenciada já tiver uma entidade de serviço em seu locatário do Azure AD, ignore esta etapa. Por exemplo, se você quiser conceder o acesso de identidade gerenciada à API de Microsoft Graph, poderá ignorar esta etapa.

1. Localize a ID de objeto da entidade de serviço do aplicativo de serviço. Você pode encontrá-lo usando o portal do Azure. Vá para Azure Active Directory e abra a página **aplicativos empresariais** , em seguida, localize o aplicativo e procure a **ID do objeto**. Você também pode encontrar a ID de objeto da entidade de serviço por seu nome de exibição usando o seguinte script do PowerShell:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Nomes de exibição para aplicativos não são exclusivos, portanto, você deve verificar se você obtém a entidade de serviço do aplicativo correto.

1. Adicione uma [função de aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) ao aplicativo que você criou na etapa 3. Você pode criar a função usando o portal do Azure ou usando Microsoft Graph. Por exemplo, você pode adicionar uma função de aplicativo como esta:

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Atribua a função de aplicativo à identidade gerenciada. Você precisará das seguintes informações para atribuir a função de aplicativo:
    * `managedIdentityObjectId`: a ID de objeto da entidade de serviço da identidade gerenciada, que você encontrou na etapa 2.
    * `serverServicePrincipalObjectId`: a ID de objeto da entidade de serviço do aplicativo de servidor, que você encontrou na etapa 4.
    * `appRoleId`: a ID da função de aplicativo exposta pelo aplicativo de servidor, que você gerou na etapa 5-no exemplo, a ID da função de aplicativo é `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   Execute o seguinte script do PowerShell para adicionar a atribuição de função:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>Script completo

Este script de exemplo mostra como atribuir uma identidade gerenciada do aplicativo Web do Azure a uma função de aplicativo.

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>Próximas etapas

- [Identidade gerenciada para visão geral dos recursos do Azure](overview.md)
- Para habilitar a identidade gerenciada em uma máquina virtual do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o PowerShell](qs-configure-powershell-windows-vm.md).