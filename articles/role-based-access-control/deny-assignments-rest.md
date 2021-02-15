---
title: Listar atribuições de negação do Azure usando a API REST – RBAC do Azure
description: Saiba como listar atribuições de negação do Azure para usuários, grupos e aplicativos usando a API REST e o controle de acesso baseado em função do Azure (RBAC do Azure).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2f835c270930734bf9963a7c7c3168b873eddaf6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84791904"
---
# <a name="list-azure-deny-assignments-using-the-rest-api"></a>Listar atribuições de negação do Azure usando a API REST

As [atribuições de negação do Azure](deny-assignments.md) bloqueiam os usuários de executarem ações específicas do Azure Resource, mesmo se uma atribuição de função conceder a eles acesso Este artigo descreve como listar atribuições de negação usando a API REST.

> [!NOTE]
> Você não pode criar suas próprias atribuições de negação diretamente. Para obter informações sobre como as atribuições de negação são criadas, consulte [atribuições de negação do Azure](deny-assignments.md).

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma atribuição de negação, você deve ter:

- `Microsoft.Authorization/denyAssignments/read` permissão, que está incluída na maioria das [funções internas do Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Lista uma única atribuição de negação

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo para o qual você deseja listar as atribuições de negação.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subscription |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{deny-assignment-id}* pelo identificador de atribuição de negação que você deseja recuperar.

## <a name="list-multiple-deny-assignments"></a>Listar várias atribuições de negação

1. Inicie com uma das seguintes solicitações:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Com parâmetros opcionais:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo para o qual você deseja listar as atribuições de negação.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subscription |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{filter}* pela condição que você deseja aplicar a fim de filtrar a lista de atribuição de negação.

    > [!div class="mx-tableFixed"]
    > | Filtrar | Descrição |
    > | --- | --- |
    > | (sem filtro) | Lista todas as atribuições de negação em, acima e abaixo do escopo especificado. |
    > | `$filter=atScope()` | Lista as atribuições de negação somente para o escopo especificado e acima. Não inclui as atribuições de negação em sub-escopos. |
    > | `$filter=assignedTo('{objectId}')` | Lista as atribuições de negação para o usuário ou a entidade de serviço especificada.<br/>Se o usuário for membro de um grupo que tem uma atribuição de negação, a atribuição negar também será listada. Esse filtro é transitivo para grupos, o que significa que se o usuário for membro de um grupo e esse grupo for membro de outro grupo que tenha uma atribuição de negação, a atribuição negar também será listada.<br/>Esse filtro só aceita uma ID de objeto para um usuário ou uma entidade de serviço. Não é possível passar uma ID de objeto para um grupo. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Lista as atribuições de negação para o usuário ou a entidade de serviço especificada e no escopo especificado. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Lista as atribuições de negação com o nome especificado. |
    > | `$filter=principalId+eq+'{objectId}'` | Lista as atribuições de negação para o usuário, grupo ou entidade de serviço especificado. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Listar atribuições de negação no escopo raiz (/)

1. Eleve seu acesso conforme descrito em [elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](elevate-access-global-admin.md).

1. Envie a seguinte solicitação:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Substitua *{filter}* pela condição que você deseja aplicar a fim de filtrar a lista de atribuição de negação. Um filtro é exigido.

    > [!div class="mx-tableFixed"]
    > | Filtrar | Descrição |
    > | --- | --- |
    > | `$filter=atScope()` | Listar atribuições de negação apenas no escopo raiz. Não inclui as atribuições de negação em sub-escopos. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Listar atribuições de negação com o nome especificado. |

1. Remover acesso elevado.

## <a name="next-steps"></a>Próximas etapas

- [Entender as atribuições de negação do Azure](deny-assignments.md)
- [Elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](elevate-access-global-admin.md)
- [Referência de API REST do Azure](/rest/api/azure/)
