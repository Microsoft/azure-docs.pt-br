---
title: Entrega segura de webhook com o Azure AD na grade de eventos do Azure
description: Descreve como entregar eventos a pontos de extremidade HTTPS protegidos por Azure Active Directory usando a grade de eventos do Azure
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: be38edfb4edfee5b3a3b42d7b1c5a44c9d72ceef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035438"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publicar eventos para os pontos de extremidade protegidos do Azure Active Directory

Este artigo descreve como aproveitar Azure Active Directory para proteger a conexão entre sua assinatura de evento e seu ponto de extremidade do webhook. Para obter uma visão geral dos aplicativos do Azure AD e das entidades de serviço, confira a [Visão geral da plataforma de identidade da Microsoft (v2.0)](../active-directory/develop/v2-overview.md).

Este artigo usa a portal do Azure para demonstração, no entanto, o recurso também pode ser habilitado usando a CLI, o PowerShell ou os SDKs.


## <a name="create-an-azure-ad-application"></a>Criar um aplicativo do Azure AD

Comece criando um aplicativo do Azure AD para seu ponto de extremidade protegido. Consulte https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configure sua API protegida para ser chamada por um aplicativo daemon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Habilitar a grade de eventos para usar seu aplicativo do Azure AD
Esta seção mostra como habilitar a grade de eventos para usar seu aplicativo do Azure AD. 

> [!NOTE]
> Você deve ser membro da [função Administrador do aplicativo Azure AD](../active-directory/roles/permissions-reference.md#all-roles) para executar esse script.

### <a name="connect-to-your-azure-tenant"></a>Conectar-se ao seu locatário do Azure
Primeiro, conecte-se ao seu locatário do Azure usando o `Connect-AzureAD` comando. 

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"
Connect-AzureAD -TenantId $myTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Criar entidade de serviço Microsoft. EventGrid
Execute o script a seguir para criar a entidade de serviço para **Microsoft. EventGrid** , caso ela ainda não exista. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Criar uma função para seu aplicativo   
Execute o script a seguir para criar uma função para seu aplicativo do Azure AD. Neste exemplo, o nome da função é: **AzureEventGridSecureWebhook**. Modifique o script do PowerShell `$myTenantId` para usar sua ID de locatário do Azure AD e `$myAzureADApplicationObjectId` com a ID de objeto do seu aplicativo do Azure AD

```PowerShell
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}

# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
} else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles
```

### <a name="add-event-grid-service-principal-to-the-role"></a>Adicionar entidade de serviço da grade de eventos à função    
Agora, execute o `New-AzureADServiceAppRoleAssignment` comando para atribuir a entidade de serviço da grade de eventos à função que você criou na etapa anterior. 

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Execute os comandos a seguir para gerar informações de saída que usarão as próximas etapas. 

```powershell    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
```
    
## <a name="configure-the-event-subscription"></a>Configurar a assinatura de evento

No fluxo de criação para sua assinatura de evento, selecione tipo de ponto de extremidade ' gancho da Web '. Depois de receber o URI do ponto de extremidade, clique na guia recursos adicionais na parte superior da folha criar assinaturas de evento.

![Selecione o tipo de ponto de extremidade webhook](./media/secure-webhook-delivery/select-webhook.png)

Na guia recursos adicionais, marque a caixa "usar autenticação do AAD" e configure a ID do locatário e a ID do aplicativo:

* Copie a ID de locatário do Azure AD da saída do script e insira-a no campo ID de locatário do AAD.
* Copie a ID do aplicativo do Azure AD da saída do script e insira-a no campo ID do aplicativo do AAD.

    ![Proteger a ação de webhook](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o monitoramento de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Para saber mais sobre a chave de autenticação, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
