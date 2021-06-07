---
title: Sincronização com escopo usando o PowerShell para Azure AD Domain Services | Microsoft Docs
description: Saiba como usar o PowerShell do Azure AD para configurar a sincronização com escopo do Azure AD para um Azure Active Directory Domain Services domínio gerenciado
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: justinha
ms.openlocfilehash: f877a631fd3c89d74b9e3b47cf205bbcf173ebc0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453403"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Configurar a sincronização com escopo do Azure AD para Azure Active Directory Domain Services usando o PowerShell do Azure AD

Para fornecer serviços de autenticação, Azure Active Directory Domain Services (Azure AD DS) sincroniza usuários e grupos do Azure AD. Em um ambiente híbrido, os usuários e grupos de um ambiente de Active Directory Domain Services local (AD DS) podem ser sincronizados primeiro com o Azure AD usando Azure AD Connect e, em seguida, sincronizados com o Azure AD DS.

Por padrão, todos os usuários e grupos de um diretório do AD do Azure são sincronizados com um domínio gerenciado do Azure AD DS. Se você tiver necessidades específicas, poderá optar por sincronizar apenas um conjunto definido de usuários.

Este artigo mostra como criar um domínio gerenciado que usa a sincronização com escopo e, em seguida, alterar ou desabilitar o conjunto de usuários com escopo usando o PowerShell do Azure AD. Você também pode [concluir essas etapas usando o portal do Azure][scoped-sync].

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, conclua o tutorial para [criar e configurar um Azure Active Directory Domain Services domínio gerenciado][tutorial-create-instance].
* Você precisa de privilégios de *administrador global* em seu locatário do Azure ad para alterar o escopo de sincronização de AD DS do Azure.

## <a name="scoped-synchronization-overview"></a>Visão geral da sincronização com escopo

Por padrão, todos os usuários e grupos de um diretório do Azure AD são sincronizados com um domínio gerenciado. Se apenas alguns usuários precisarem acessar o domínio gerenciado, você poderá sincronizar somente as contas de usuário. Essa sincronização com escopo é baseada em grupo. Quando você configura a sincronização com escopo baseado em grupo, somente as contas de usuário que pertencem aos grupos que você especifica são sincronizadas com o domínio gerenciado. Grupos aninhados não são sincronizados, somente os grupos específicos que você selecionar.

Você pode alterar o escopo de sincronização antes ou depois de criar o domínio gerenciado. O escopo da sincronização é definido por uma entidade de serviço com o identificador de aplicativo 2565bd9d-DA50-47d4-8b85-4c97f669dc36. Para evitar a perda de escopo, não exclua nem altere a entidade de serviço. Se ele for excluído acidentalmente, o escopo de sincronização não poderá ser recuperado. 

Tenha em mente as seguintes advertências se você alterar o escopo de sincronização:

- Ocorre uma sincronização completa.
- Os objetos que não são mais necessários no domínio gerenciado são excluídos. Novos objetos são criados no domínio gerenciado.

Para saber mais sobre o processo de sincronização, consulte [entender a sincronização no Azure AD Domain Services][concepts-sync].

## <a name="powershell-script-for-scoped-synchronization"></a>Script do PowerShell para sincronização com escopo

Para configurar a sincronização com escopo usando o PowerShell, primeiro salve o script a seguir em um arquivo chamado `Select-GroupsToSync.ps1` .

Esse script configura o Azure AD DS para sincronizar os grupos selecionados do Azure AD. Todas as contas de usuário que fazem parte dos grupos especificados são sincronizadas com o domínio gerenciado.

Esse script é usado nas etapas adicionais neste artigo.

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

## <a name="enable-scoped-synchronization"></a>Habilitar sincronização com escopo

Para habilitar a sincronização com escopo baseado em grupo para um domínio gerenciado, conclua as seguintes etapas:

1. Primeiro, defina *"filteredSync" = "Enabled"* no recurso de AD DS do Azure e, em seguida, atualize o domínio gerenciado.

    Quando solicitado, especifique as credenciais para um *administrador global* para entrar em seu locatário do Azure ad usando o cmdlet [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    # Connect to your Azure AD tenant
    Connect-AzureAD

    # Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    # Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    # Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. Agora, especifique a lista de grupos cujos usuários devem ser sincronizados com o domínio gerenciado.

    Execute o `Select-GroupsToSync.ps1` script e especifique a lista de grupos a serem sincronizados. No exemplo a seguir, os grupos a serem sincronizados são *GroupName1* e *GroupName2*.

    > [!WARNING]
    > Você deve incluir o grupo de *Administradores de DC do AAD* na lista de grupos para sincronização com escopo. Se você não incluir esse grupo, o domínio gerenciado será inutilizável.

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

Alterar o escopo da sincronização faz com que o domínio gerenciado sincronize novamente todos os dados. Os objetos que não são mais necessários no domínio gerenciado são excluídos e a ressincronização pode levar muito tempo para ser concluída.

## <a name="modify-scoped-synchronization"></a>Modificar sincronização com escopo

Para modificar a lista de grupos cujos usuários devem ser sincronizados com o domínio gerenciado, execute `Select-GroupsToSync.ps1` o script e especifique a nova lista de grupos a serem sincronizados.

No exemplo a seguir, os grupos para sincronizar não incluem mais *GroupName2* e agora incluem *GroupName3*.

> [!WARNING]
> Você deve incluir o grupo de *Administradores de DC do AAD* na lista de grupos para sincronização com escopo. Se você não incluir esse grupo, o domínio gerenciado será inutilizável.

Quando solicitado, especifique as credenciais para um *administrador global* para entrar em seu locatário do Azure ad usando o cmdlet [Connect-AzureAD][Connect-AzureAD] :

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Alterar o escopo da sincronização faz com que o domínio gerenciado sincronize novamente todos os dados. Os objetos que não são mais necessários no domínio gerenciado são excluídos e a ressincronização pode levar muito tempo para ser concluída.

## <a name="disable-scoped-synchronization"></a>Desabilitar sincronização com escopo

Para desabilitar a sincronização com escopo baseado em grupo para um domínio gerenciado, defina *"filteredSync" = "Disabled"* no recurso de AD DS do Azure e, em seguida, atualize o domínio gerenciado. Ao concluir, todos os usuários e grupos serão definidos para sincronizar do Azure AD.

Quando solicitado, especifique as credenciais para um *administrador global* para entrar em seu locatário do Azure ad usando o cmdlet [Connect-AzureAD][Connect-AzureAD] :

```powershell
# Connect to your Azure AD tenant
Connect-AzureAD

# Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

# Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

# Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Alterar o escopo da sincronização faz com que o domínio gerenciado sincronize novamente todos os dados. Os objetos que não são mais necessários no domínio gerenciado são excluídos e a ressincronização pode levar muito tempo para ser concluída.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o processo de sincronização, consulte [entender a sincronização no Azure AD Domain Services](synchronization.md).

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
