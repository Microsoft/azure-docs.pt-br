---
title: Entender as atribuições de negação do Azure-RBAC do Azure
description: Saiba mais sobre as atribuições de negação do Azure no Azure RBAC (controle de acesso baseado em função).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: a5f17f009caa9306631debf511f2c890f8f2a450
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82733764"
---
# <a name="understand-azure-deny-assignments"></a>Entender as atribuições de negação do Azure

Semelhante a uma atribuição de função, uma *negação de atribuição* anexa um conjunto de ações de negação a um usuário, grupo ou entidade de serviço em um escopo específico com o objetivo de negar acesso. As atribuições de negação impedem que os usuários executem ações específicas, mesmo que uma atribuição de função conceda o acesso a eles.

Este artigo descreve como as atribuições de negação são definidas.

## <a name="how-deny-assignments-are-created"></a>Como as atribuições de negação são criadas

As atribuições de negação são criadas e gerenciadas pelo Azure para proteger os recursos. Os planos gráficos do Azure e os aplicativos gerenciados do Azure usam atribuições de negação para proteger os recursos gerenciados pelo sistema. Os planos gráficos do Azure e os aplicativos gerenciados do Azure são a única maneira de que as atribuições de negação podem ser criadas. Você não pode criar suas próprias atribuições de negação diretamente. Para obter mais informações sobre como plantas usam atribuições de negação para bloquear recursos, consulte [entender o bloqueio de recursos nas plantas do Azure](../governance/blueprints/concepts/resource-locking.md).

> [!NOTE]
> Você não pode criar suas próprias atribuições de negação diretamente.

## <a name="compare-role-assignments-and-deny-assignments"></a>Comparar atribuições de função e atribuições de negação

As atribuições de negação seguem um padrão semelhante como atribuições de função, mas também têm algumas diferenças.

| Funcionalidade | Atribuição de função | Atribuição de negação |
| --- | --- | --- |
| Conceder acesso | :heavy_check_mark: |  |
| Negar acesso |  | :heavy_check_mark: |
| Pode ser criado diretamente | :heavy_check_mark: |  |
| Aplicar em um escopo | :heavy_check_mark: | :heavy_check_mark: |
| Excluir entidades |  | :heavy_check_mark: |
| Impedir a herança para escopos filho |  | :heavy_check_mark: |
| Aplicar a atribuições de [administrador de assinatura clássica](rbac-and-directory-admin-roles.md) |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Propriedades de atribuição de negação

 Uma atribuição de negação tem as seguintes propriedades:

> [!div class="mx-tableFixed"]
> | Propriedade | Obrigatório | Type | Descrição |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Sim | String | O nome de exibição da atribuição de negação. Os nomes devem ser exclusivos para um determinado escopo. |
> | `Description` | Não | String | A descrição da atribuição de negação. |
> | `Permissions.Actions` | Pelo menos um Actions ou um DataActions | String[] | Uma matriz de cadeias de caracteres que especificam as operações de gerenciamento para as quais o acesso é bloqueado pela atribuição de negação. |
> | `Permissions.NotActions` | Não | String[] | Uma matriz de cadeias de caracteres que especificam as operações de gerenciamento a excluir da atribuição de negação. |
> | `Permissions.DataActions` | Pelo menos um Actions ou um DataActions | String[] | Uma matriz de cadeias de caracteres que especificam as operações de dados para as quais o acesso é bloqueado pela atribuição de negação. |
> | `Permissions.NotDataActions` | Não | String[] | Uma matriz de cadeias de caracteres que especificam as operações de dados a excluir da atribuição de negação. |
> | `Scope` | Não | String | Uma cadeia de caracteres que especifica o escopo ao qual a atribuição de negação se aplica. |
> | `DoNotApplyToChildScopes` | Não | Booliano | Especifica se a atribuição de negação se aplica a escopos filho. O valor padrão é false. |
> | `Principals[i].Id` | Sim | String[] | Uma matriz de IDs de objeto principal do Microsoft Azure Active Directory (usuário, grupo, entidade de serviço ou identidade gerenciada) à qual a atribuição de negação se aplica. Defina como um GUID vazio `00000000-0000-0000-0000-000000000000` para representar todos os principais. |
> | `Principals[i].Type` | Não | String[] | Uma matriz de tipos de objeto representada por entidades de segurança [i]. ID. definido como `SystemDefined` para representar todas as entidades de segurança. |
> | `ExcludePrincipals[i].Id` | Não | String[] | Uma matriz de IDs de objeto principal do Microsoft Azure Active Directory (usuário, grupo, entidade de serviço ou identidade gerenciada) à qual a atribuição de negação não se aplica. |
> | `ExcludePrincipals[i].Type` | Não | String[] | Uma matriz de tipos de objeto representados por ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Não | Booliano | Especifica se esta atribuição de negação foi ou não criada pelo Azure e não pode ser editada ou excluída. Atualmente, todas as atribuições de negação são protegidas pelo sistema. |

## <a name="the-all-principals-principal"></a>A entidade de segurança todos os principais

Para dar suporte a atribuições de negação, uma entidade definida pelo sistema denominada *todas as entidades de segurança* foi introduzida. Essa entidade representa todos os usuários, grupos, entidades de serviço e identidades gerenciadas em um diretório do Microsoft Azure Active Directory. Se o ID principal for um zero GUID `00000000-0000-0000-0000-000000000000` e o tipo principal for `SystemDefined`, o principal representará todos os principais. Na saída Azure PowerShell, todas as entidades de segurança são parecidas com as seguintes:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Todas as entidades de segurança podem ser combinadas com o `ExcludePrincipals` para negar a todas as entidades de segurança, exceto alguns usuários. Todas as entidades de segurança têm as seguintes restrições:

- Pode ser usada apenas em `Principals` e não pode ser usada em `ExcludePrincipals`.
- `Principals[i].Type` deve ser definido como `SystemDefined`.

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Proteger recursos novos com bloqueios de recursos do Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md)
* [Listar atribuições de negação do Azure usando o portal do Azure](deny-assignments-portal.md)
