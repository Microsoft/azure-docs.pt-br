---
title: "Conceder permissões de usuário para políticas específicas do laboratório | Microsoft Docs"
description: "Saiba como conceder permissões de usuário para políticas específicas dos Laboratórios de Desenvolvimento/Teste com base nas necessidades de cada usuário"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.openlocfilehash: fa7c1e4435106d0fc25cb7f77f195eb48705772d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Conceder permissões de usuário para políticas específicas do laboratório
## <a name="overview"></a>Visão geral
Este artigo ilustra como usar o PowerShell para conceder aos usuários permissões para uma determinada política de laboratório. Dessa forma, as permissões podem ser aplicadas com base nas necessidades de cada usuário. Por exemplo, talvez você queira conceder a um usuário específico a capacidade de alterar as configurações de política de VM, mas não as políticas de custo.

## <a name="policies-as-resources"></a>Políticas como recursos
Como discutido no artigo [Controle de acesso baseado em função do Azure](../active-directory/role-based-access-control-configure.md) , o RBAC permite o gerenciamento de acesso refinado de recursos do Azure. Com o RBAC, você pode separar as tarefas dentro de sua equipe de operação de desenvolvimento e conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos.

Nos Laboratórios de Desenvolvimento/Teste, uma política é um tipo de recurso que habilita a ação de RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Cada política do laboratório é um recurso do tipo de recurso Política e pode ser atribuída como um escopo a uma função RBAC.

Por exemplo, para conceder a permissão de leitura/gravação aos usuários para a política **Tamanhos de VM Permitidos**, você criaria uma função personalizada que funcionasse com a ação **Microsoft.DevTestLab/labs/policySets/policies/*** e, em seguida, atribuiria os usuários adequados a essa função personalizada no escopo de **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Para saber mais sobre as funções personalizadas no RBAC, consulte o [Controle de acesso das funções personalizadas](../active-directory/role-based-access-control-custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Criar uma função personalizada do laboratório usando o PowerShell
Para começar, você precisará ler o seguinte artigo, que explica como instalar e configurar os cmdlets do Azure PowerShell: [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Depois de configurar os cmdlets do Azure PowerShell, você pode executar as seguintes tarefas:

* Listar todas as operações/ações para um provedor de recursos
* Listar ações em uma função específica:
* Criar uma função personalizada

O seguinte script do PowerShell apresenta exemplos de como executar essas tarefas:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Atribuir permissões a um usuário para uma política específica usando funções personalizadas
Depois de definir suas funções personalizadas, você pode atribuí-las aos usuários. Para atribuir uma função personalizada a um usuário, você deve primeiro obter o **ObjectId** que representa esse usuário. Para fazer isso, use o cmdlet **Get-AzureRmADUser** .

No exemplo a seguir, o **ObjectId** do usuário *SomeUser* é 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Depois de você ter o **ObjectId** para o usuário e um nome de função personalizado, poderá atribuir essa função ao usuário com o cmdlet **New-AzureRmRoleAssignment**:

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

No exemplo anterior, a política **AllowedVmSizesInLab** é usada. Você pode usar qualquer uma das políticas a seguir:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
Após você tiver concedido permissões de usuário para políticas específicas do laboratório, estas são algumas das próximas etapas a serem consideradas:

* [Proteger o acesso a um laboratório](devtest-lab-add-devtest-user.md).
* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md).
* [Criar um modelo de laboratório](devtest-lab-create-template.md).
* [Criar artefatos personalizados para suas VMs](devtest-lab-artifact-author.md).
* [Adicionar uma VM com artefatos a um laboratório](devtest-lab-add-vm-with-artifacts.md).

