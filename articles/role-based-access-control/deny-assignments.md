---
title: Compreender atribuições de negação dos recursos do Azure | Microsoft Docs
description: Saiba mais sobre atribuições de negação no RBAC (controle de acesso baseado em função) para recursos do Azure.
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
ms.date: 06/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: f15d6fd81337aa4a859539e86f37a516848c9370
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165994"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Compreender atribuições de negação dos recursos do Azure

Semelhante a uma atribuição de função, uma *negação de atribuição* anexa um conjunto de ações de negação a um usuário, grupo ou entidade de serviço em um escopo específico com o objetivo de negar acesso. As atribuições de negação impedem que os usuários executem ações específicas, mesmo que uma atribuição de função conceda o acesso a eles.

Este artigo descreve como as atribuições de negação são definidas.

## <a name="how-deny-assignments-are-created"></a>Como negar as atribuições são criadas

Nega as atribuições são criadas e gerenciadas pelo Azure para proteger recursos. Por exemplo, especificações técnicas do Azure e o Azure aplicativos gerenciados use negar atribuições para proteger os recursos gerenciados pelo sistema. Para obter mais informações, consulte [Proteger novos recursos com bloqueios de recurso do Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="compare-role-assignments-and-deny-assignments"></a>Comparar as atribuições de função e atribuições de negação

Nega atribuições seguem um padrão semelhante como atribuições de função, mas também têm algumas diferenças.

| Recurso | Atribuição de função | Negar atribuição |
| --- | --- | --- |
| Conceder acesso | :heavy_check_mark: |  |
| Negar acesso |  | :heavy_check_mark: |
| Pode ser criado diretamente | :heavy_check_mark: |  |
| Aplicar a um escopo | :heavy_check_mark: | :heavy_check_mark: |
| Excluir entidades de segurança |  | :heavy_check_mark: |
| Impedir a herança de escopos filho |  | :heavy_check_mark: |
| Aplicar a [administrador de assinatura clássicos](rbac-and-directory-admin-roles.md) atribuições |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Propriedades de atribuição de negação

 Uma atribuição de negação tem as seguintes propriedades:

> [!div class="mx-tableFixed"]
> | Propriedade | Obrigatório | Type | DESCRIÇÃO |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Sim | Cadeia de caracteres | O nome de exibição da atribuição de negação. Os nomes devem ser exclusivos para um determinado escopo. |
> | `Description` | Não | Cadeia de caracteres | A descrição da atribuição de negação. |
> | `Permissions.Actions` | Pelo menos um Actions ou um DataActions | String[] | Uma matriz de cadeias de caracteres que especificam as operações de gerenciamento para as quais o acesso é bloqueado pela atribuição de negação. |
> | `Permissions.NotActions` | Não | String[] | Uma matriz de cadeias de caracteres que especificam as operações de gerenciamento a excluir da atribuição de negação. |
> | `Permissions.DataActions` | Pelo menos um Actions ou um DataActions | String[] | Uma matriz de cadeias de caracteres que especificam as operações de dados para as quais o acesso é bloqueado pela atribuição de negação. |
> | `Permissions.NotDataActions` | Não | String[] | Uma matriz de cadeias de caracteres que especificam as operações de dados a excluir da atribuição de negação. |
> | `Scope` | Não | Cadeia de caracteres | Uma cadeia de caracteres que especifica o escopo ao qual a atribuição de negação se aplica. |
> | `DoNotApplyToChildScopes` | Não | Boolean | Especifica se a atribuição de negação se aplica a escopos filho. O valor padrão é falso. |
> | `Principals[i].Id` | Sim | String[] | Uma matriz de IDs de objeto principal do Microsoft Azure Active Directory (usuário, grupo, entidade de serviço ou identidade gerenciada) à qual a atribuição de negação se aplica. Defina como um GUID vazio `00000000-0000-0000-0000-000000000000` para representar todos os principais. |
> | `Principals[i].Type` | Não | String[] | Uma matriz de tipos de objeto representados por Principals[i].Id. Defina como `SystemDefined` para representar todos os principais. |
> | `ExcludePrincipals[i].Id` | Não | String[] | Uma matriz de IDs de objeto principal do Microsoft Azure Active Directory (usuário, grupo, entidade de serviço ou identidade gerenciada) à qual a atribuição de negação não se aplica. |
> | `ExcludePrincipals[i].Type` | Não | String[] | Uma matriz de tipos de objeto representados por ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Não | Boolean | Especifica se esta atribuição de negação foi ou não criada pelo Azure e não pode ser editada ou excluída. Atualmente, todas as atribuições de negação são protegidas pelo sistema. |

## <a name="the-all-principals-principal"></a>A entidade de segurança de todas as entidades de segurança

Para suporte a negar as atribuições de uma entidade definida pelo sistema denominada *todas as entidades de* foi introduzido. Essa entidade representa todos os usuários, grupos, entidades de serviço e identidades gerenciadas em um diretório do Microsoft Azure Active Directory. Se o ID principal for um zero GUID `00000000-0000-0000-0000-000000000000` e o tipo principal for `SystemDefined`, o principal representará todos os principais. Na saída do PowerShell do Azure, todas as entidades é semelhante ao seguinte:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Todas as entidades podem ser combinadas com `ExcludePrincipals` para negar todas as entidades, exceto alguns usuários. Todas as entidades de segurança tem as seguintes restrições:

- Pode ser usada apenas em `Principals` e não pode ser usada em `ExcludePrincipals`.
- `Principals[i].Type` deve ser definido como `SystemDefined`.

## <a name="next-steps"></a>Próximas etapas

* [Lista Negar as atribuições de recursos do Azure usando o portal do Azure](deny-assignments-portal.md)
* [Noções básicas sobre definições de função para recursos do Azure](role-definitions.md)
