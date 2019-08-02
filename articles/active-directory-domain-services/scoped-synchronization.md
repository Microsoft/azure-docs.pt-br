---
title: 'Azure Active Directory Domain Services: Sincronização no escopo | Microsoft Docs'
description: Configurar a sincronização no escopo do Microsoft Azure Active Directory para seus domínios gerenciados
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: bb96e7ccbc38a71d24178c31f8eb9a077c06b8f0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472517"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Configurar a sincronização no escopo do Microsoft Azure Active Directory para seu domínio gerenciado
Este artigo mostra como configurar apenas contas de usuários específicos para serem sincronizadas entre seu diretório do Azure AD e o domínio gerenciado do Azure AD Domain Services.


## <a name="group-based-scoped-synchronization"></a>Sincronização no escopo baseado em grupo
Por padrão, todos os usuários e grupos no diretório do Microsoft Azure Active Directory são sincronizados com seu domínio gerenciado. Se apenas alguns usuários usam o domínio gerenciado, você pode sincronizar apenas essas contas de usuário. A sincronização no escopo baseada em grupo permite que você faça isso. Quando configurada, apenas contas de usuário que pertencem aos grupos que você especificou são sincronizadas com o domínio gerenciado.

A tabela a seguir ajuda a determinar como usar a sincronização no escopo:

| **Estado atual** | **Estado desejado** | **Configuração necessária** |
| --- | --- | --- |
| O domínio gerenciado existente é configurado para sincronizar todas as contas de usuário e grupos. | Você deseja sincronizar somente as contas de usuário que pertencem a grupos específicos para seu domínio gerenciado. | [Exclua o domínio gerenciado existente](delete-aadds.md). Depois, siga as instruções neste artigo para recriá-lo com a sincronização no escopo configurada. |
| Você não tem um domínio gerenciado existente. | Você deseja criar um novo domínio gerenciado e sincronizar apenas as contas de usuário que pertencem a grupos específicos. | Siga as instruções neste artigo para criar um novo domínio gerenciado com a sincronização no escopo configurada. |
| Seu domínio gerenciado existente é configurado para sincronizar apenas as contas que pertencem a grupos específicos. | Você deseja modificar a lista de grupos cujos usuários deverão ser sincronizados para o domínio gerenciado. | Siga as instruções neste artigo para modificar o escopo de sincronização. |

> [!WARNING]
> **A alteração do escopo de sincronização faz com que o seu domínio gerenciado percorra a ressincronização.**
> 
>  * Quando você altera o escopo de sincronização para um domínio gerenciado, ocorre uma ressincronização completa.
>  * Os objetos que não forem mais necessários no domínio gerenciado serão excluídos. Novos objetos são criados no domínio gerenciado.
>  * A ressincronização pode levar muito tempo para ser concluída, dependendo do número de objetos (usuários, grupos e associações de grupo) no domínio gerenciado e no diretório do Microsoft Azure Active Directory. Para diretórios grandes com várias centenas de milhares de objetos, a ressincronização pode levar alguns dias.


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Criar um novo domínio gerenciado e habilitar a sincronização com escopo baseada em grupo usando o portal do Azure

1. Siga a [Guia Primeiros passos](create-instance.md) para criar um domínio gerenciado.
2. Escolha com **escopo** durante a seleção do estilo de sincronização no assistente de criação dos Serviços de Domínio do Azure AD.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>Criar um novo domínio gerenciado e habilitar a sincronização com escopo baseada em grupo usando o PowerShell
Use o PowerShell para concluir esse conjunto de etapas. Confira as instruções para [Habilitar o Azure Active Directory Domain Services usando o PowerShell](powershell-create-instance.md). Algumas das etapas neste artigo foram ligeiramente modificadas para configurar a sincronização no escopo.

Conclua as seguintes etapas para configurar a sincronização de escopo com base em grupo para seu domínio gerenciado:

1. Conclua as seguintes tarefas:
   * [Tarefa 1: Instalar os módulos do PowerShell exigidos](powershell-create-instance.md#task-1-install-the-required-powershell-modules).
   * [Tarefa 2: Criar a entidade de serviço exigida em seu diretório do Azure AD](powershell-create-instance.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory).
   * [Tarefa 3: Criar e configurar o 'AAD DC Administrators"group]powershell-create-instance.md#task-3-create-and-configure-the-aad-dc-administrators-group).
   * [Tarefa 4: Registrar o provedor de recursos do Azure AD Domain Services](powershell-create-instance.md#task-4-register-the-azure-ad-domain-services-resource-provider).
   * [Tarefa 5: Criar um grupo de recursos](powershell-create-instance.md#task-5-create-a-resource-group).
   * [Tarefa 6: Criar e configurar a rede virtual](powershell-create-instance.md#task-6-create-and-configure-the-virtual-network).

2. Selecione os grupos que você deseja sincronizar e forneça o nome de exibição dos grupos que deseja que sejam sincronizados com o domínio gerenciado.

3. Salve o [script na seção a seguir](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) em um arquivo chamado ```Select-GroupsToSync.ps1```. Execute o script como indicado abaixo:

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **Não se esqueça de incluir o grupo 'Administradores do AAD DC'.**
   >
   > Você deve incluir o grupo 'Administradores do AAD DC' na lista de grupos configurados para sincronização no escopo. Se você não incluir esse grupo, o domínio gerenciado não poderá ser usado.
   >

4. Agora, crie o domínio gerenciado e habilite a sincronização no escopo com base no grupo para o domínio gerenciado. Inclua a propriedade ```"filteredSync" = "Enabled"``` no parâmetro ```Properties```. Por exemplo, veja o seguinte fragmento de script, copiado da [Tarefa 7: Provisionar o domínio gerenciado do Azure AD Domain Services](powershell-create-instance.md#task-7-provision-the-azure-ad-domain-services-managed-domain).

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso100.com"
   $ResourceGroupName = "ContosoAaddsRg"
   $VnetName = "DomainServicesVNet_WUS"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -ApiVersion 2017-06-01 -Force -Verbose
   ```

   > [!TIP]
   > Não se esqueça de incluir ```"filteredSync" = "Enabled"``` no parâmetro ```-Properties```, para que a sincronização no escopo seja habilitada para o domínio gerenciado.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Script para selecionar grupos para sincronizar o domínio gerenciado (selecione GroupsToSync.ps1)
Salve o script a seguir em um arquivo (```Select-GroupsToSync.ps1```). Esse script configura o Azure AD Domain Services para sincronizar os grupos selecionados para o domínio gerenciado. Todas as contas de usuário que pertencerem a grupos especificados serão sincronizadas para o domínio gerenciado.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```


## <a name="modify-group-based-scoped-synchronization"></a>Modificar sincronização no escopo baseada em grupo
Para modificar a lista de grupos cujos usuários devem ser sincronizados para o domínio gerenciado, execute novamente o [script do PowerShell](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) e especifique a nova lista de grupos. Lembre-se sempre de especificar o grupo 'Administradores do AAD DC' nessa lista.

> [!WARNING]
> **Não se esqueça de incluir o grupo 'Administradores do AAD DC'.**
>
> Você deve incluir o grupo 'Administradores do AAD DC' na lista de grupos configurados para sincronização no escopo. Se você não incluir esse grupo, o domínio gerenciado não poderá ser usado.
>


## <a name="disable-group-based-scoped-synchronization"></a>Desabilitar sincronização no escopo baseada em grupo
Use o seguinte script do PowerShell para desabilitar a sincronização no escopo baseada em grupo para o domínio gerenciado:

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>Próximas etapas
* [Compreender a sincronização no Azure AD Domain Services](synchronization.md)
* [Habilitar o Azure Active Directory Domain Services usando o PowerShell](powershell-create-instance.md)
