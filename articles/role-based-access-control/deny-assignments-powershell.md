---
title: Listar atribuições de negação do Azure usando Azure PowerShell-RBAC do Azure
description: Saiba como listar os usuários, grupos, entidades de serviço e identidades gerenciadas que tiveram o acesso negado a ações específicas de recursos do Azure em determinados escopos usando o Azure PowerShell e o controle de acesso baseado em função do Azure (RBAC do Azure).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6cf379b051ba42be2d7df7d288f07cdc2a0002a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94657628"
---
# <a name="list-azure-deny-assignments-using-azure-powershell"></a>Listar atribuições de negação do Azure usando Azure PowerShell

As [atribuições de negação do Azure](deny-assignments.md) bloqueiam os usuários de executarem ações específicas do Azure Resource, mesmo se uma atribuição de função conceder a eles acesso Este artigo descreve como listar atribuições de negação usando Azure PowerShell.

> [!NOTE]
> Você não pode criar suas próprias atribuições de negação diretamente. Para obter informações sobre como as atribuições de negação são criadas, consulte [atribuições de negação do Azure](deny-assignments.md).

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma atribuição de negação, você deve ter:

- `Microsoft.Authorization/denyAssignments/read` permissão, que está incluída na maioria das [funções internas do Azure](built-in-roles.md)
- [PowerShell em Azure cloud Shell](../cloud-shell/overview.md) ou [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Listar atribuições de negação

### <a name="list-all-deny-assignments"></a>Listar todas as atribuições de negação

Para listar todas as atribuições de negação para a assinatura atual, use [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Listar atribuições de negação em um escopo de grupo de recursos

Para listar todas as atribuições de negação em um escopo de grupo de recursos, use [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Listar atribuições de negação em um escopo de assinatura

Para listar todas as atribuições de negação em um escopo de assinatura, use [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Para obter a ID da assinatura, você pode encontrá-la na folha **assinaturas** no portal do Azure ou pode usar [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Próximas etapas

- [Entender as atribuições de negação do Azure](deny-assignments.md)
- [Listar atribuições de negação do Azure usando o portal do Azure](deny-assignments-portal.md)
- [Listar atribuições de negação do Azure usando a API REST](deny-assignments-rest.md)