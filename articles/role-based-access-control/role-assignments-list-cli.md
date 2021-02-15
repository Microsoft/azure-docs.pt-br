---
title: Listar atribuições de função do Azure usando o CLI do Azure-RBAC do Azure
description: Saiba como determinar quais recursos os usuários, grupos, entidades de serviço ou identidades gerenciadas têm acesso ao uso de CLI do Azure e do Azure RBAC (controle de acesso baseado em função).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b6125252c22163306a79f5682a3a5fc4f0b55d4c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964381"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Listar atribuições de função do Azure usando CLI do Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Este artigo descreve como listar atribuições de função usando CLI do Azure.

> [!NOTE]
> Se sua organização tiver funções de gerenciamento terceirizadas para um provedor de serviços que usa o [Gerenciamento de recursos delegado do Azure](../lighthouse/concepts/azure-delegated-resource-management.md), as atribuições de função autorizadas por esse provedor de serviços não serão mostradas aqui.

## <a name="prerequisites"></a>Pré-requisitos

- [Bash em Azure cloud Shell](../cloud-shell/overview.md) ou [CLI do Azure](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Listar as atribuições de função de um usuário

Para listar as atribuições de função para um usuário específico, use a [lista de atribuições de função az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee {assignee}
```

Por padrão, somente as atribuições de função para a assinatura atual serão exibidas. Para exibir as atribuições de função para a assinatura atual e abaixo, adicione o `--all` parâmetro. Para exibir as atribuições de função herdadas, adicione o `--include-inherited` parâmetro.

O exemplo a seguir lista as atribuições de função atribuídas diretamente ao usuário *patlong \@ contoso.com* :

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  }
]
```

## <a name="list-role-assignments-for-a-resource-group"></a>Listar as atribuições de função para um grupo de recursos

Para listar as atribuições de função que existem em um escopo de grupo de recursos, use a [lista de atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group {resourceGroup}
```

O exemplo a seguir lista as atribuições de função para o grupo de recursos *Pharma-Sales* :

```azurecli
az role assignment list --resource-group pharma-sales --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  
  ...

]
```

## <a name="list-role-assignments-for-a-subscription"></a>Listar atribuições de função de um usuário

Para listar todas as atribuições de função em um escopo de assinatura, use a [lista de atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-list). Para obter a ID da assinatura, você pode encontrá-la na folha **assinaturas** no portal do Azure ou pode usar a lista de [contas AZ](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

Exemplo:

```azurecli
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "Subscription Admins",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },

  ...

]
```

## <a name="list-role-assignments-for-a-management-group"></a>Listar as atribuições de função para um grupo de gerenciamento

Para listar todas as atribuições de função em um escopo do grupo de gerenciamento, use a [lista de atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-list). Para obter a ID do grupo de gerenciamento, você pode encontrá-la na folha **grupos de gerenciamento** no portal do Azure ou pode usar [AZ Account Management-grupo List](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

Exemplo:

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/sales-group --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  }
]
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Listar atribuições de função para uma identidade gerenciada

1. Obtenha a ID da entidade de segurança da identidade gerenciada atribuída pelo sistema ou pelo usuário.

    Para obter a ID de entidade de segurança de uma identidade gerenciada atribuída pelo usuário, você pode usar [AZ ad SP List](/cli/azure/ad/sp#az-ad-sp-list) ou [AZ Identity List](/cli/azure/identity#az-identity-list).

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    Para obter a ID de entidade de segurança de uma identidade gerenciada atribuída pelo sistema, você pode usar [AZ ad SP List](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. Para listar as atribuições de função, use a [lista de atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-list).

    Por padrão, somente as atribuições de função para a assinatura atual serão exibidas. Para exibir as atribuições de função para a assinatura atual e abaixo, adicione o `--all` parâmetro. Para exibir as atribuições de função herdadas, adicione o `--include-inherited` parâmetro.

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>Próximas etapas

- [Adicionar ou remover atribuições de função do Azure usando a CLI do Azure](role-assignments-cli.md)