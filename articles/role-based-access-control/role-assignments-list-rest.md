---
title: Listar atribuições de função do Azure usando a API REST – RBAC do Azure
description: Saiba como determinar quais recursos os usuários, grupos, entidades de serviço ou identidades gerenciadas têm acesso ao uso da API REST e do Azure RBAC (controle de acesso baseado em função).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.topic: how-to
ms.date: 02/27/2021
ms.author: rolyon
ms.openlocfilehash: 9780902a1c5f4a711e1abffa6b508c28efe269ac
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735873"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>Listar atribuições de função do Azure usando a API REST

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Este artigo descreve como listar as atribuições de função usando a API REST.

> [!NOTE]
> Se sua organização tiver funções de gerenciamento terceirizadas para um provedor de serviços que usa o [Gerenciamento de recursos delegado do Azure](../lighthouse/concepts/azure-delegated-resource-management.md), as atribuições de função autorizadas por esse provedor de serviços não serão mostradas aqui.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="list-role-assignments"></a>Listar atribuições de função

No RBAC do Azure, para listar o acesso, você lista as atribuições de função. Para listar as atribuições de função, use uma das APIs REST [Atribuições de Função - Listar](/rest/api/authorization/roleassignments/list). Para refinar seus resultados, especifique um escopo e um filtro opcional.

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo para o qual você deseja listar as funções de atribuição.

    > [!div class="mx-tableFixed"]
    > | Escopo | Tipo |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gerenciamento |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |

    No exemplo anterior, Microsoft. Web é um provedor de recursos que se refere a uma instância do serviço de aplicativo. Da mesma forma, você pode usar qualquer outro provedor de recursos e especificar o escopo. Para obter mais informações, consulte [provedores de recursos do Azure e tipos](../azure-resource-manager/management/resource-providers-and-types.md) e [operações do provedor de recursos do Azure](resource-provider-operations.md)com suporte.  
     
1. Substitua *{filter}* pela condição que você deseja aplicar a fim de filtrar a lista de atribuições de função.

    > [!div class="mx-tableFixed"]
    > | Filtrar | Descrição |
    > | --- | --- |
    > | `$filter=atScope()` | Lista as atribuições de função somente para o escopo especificado, não incluindo as atribuições de função em Subescopos. |
    > | `$filter=assignedTo('{objectId}')` | Lista as atribuições de função para um usuário ou entidade de serviço especificada.<br/>Se o usuário for membro de um grupo que tem uma atribuição de função, essa atribuição de função também será listada. Esse filtro é transitivo para grupos, o que significa que, se o usuário for membro de um grupo e esse grupo for membro de outro grupo que tenha uma atribuição de função, essa atribuição de função também será listada.<br/>Esse filtro só aceita uma ID de objeto para um usuário ou uma entidade de serviço. Não é possível passar uma ID de objeto para um grupo. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Lista as atribuições de função para o usuário ou a entidade de serviço especificada e no escopo especificado. |
    > | `$filter=principalId+eq+'{objectId}'` | Lista as atribuições de função para um usuário, grupo ou entidade de serviço especificado. |

A solicitação a seguir lista todas as atribuições de função para o usuário especificado no escopo da assinatura:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()+and+assignedTo('{objectId1}')
```

O texto a seguir mostra um exemplo da saída:

```json
{
    "value": [
        {
            "properties": {
                "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
                "principalId": "{objectId1}",
                "scope": "/subscriptions/{subscriptionId1}",
                "createdOn": "2019-01-15T21:08:45.4904312Z",
                "updatedOn": "2019-01-15T21:08:45.4904312Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "{roleAssignmentId1}"
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções do Azure usando a API REST](role-assignments-rest.md)
- [Referência de API REST do Azure](/rest/api/azure/)
