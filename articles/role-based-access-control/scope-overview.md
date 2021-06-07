---
title: Entender o escopo do RBAC do Azure
description: Saiba mais sobre o escopo do RBAC do Azure (controle de acesso baseado em função) do Azure e como determinar o escopo de um recurso.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/08/2020
ms.author: rolyon
ms.openlocfilehash: deee42c46c9b08bb265c972695b9319413d4fcb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555913"
---
# <a name="understand-scope-for-azure-rbac"></a>Entender o escopo do RBAC do Azure

*Escopo* é o conjunto de recursos ao qual o acesso se aplica. Quando você atribui uma função, é importante entender o escopo para que você possa conceder a uma entidade de segurança apenas o acesso de que ela realmente precisa. Ao limitar o escopo, você limita quais recursos estarão em risco se a entidade de segurança for comprometida.

## <a name="scope-levels"></a>Níveis de escopo

No Azure, você pode especificar um escopo em quatro níveis: [grupo de gerenciamento](../governance/management-groups/overview.md), assinatura, [grupo de recursos](../azure-resource-manager/management/overview.md#resource-groups) e recurso. Os escopos são estruturados em uma relação pai-filho. Cada nível de hierarquia torna o escopo mais específico. Você pode atribuir funções em qualquer um desses níveis de escopo. O nível que você seleciona determina o quão amplamente a função é aplicada. Os níveis inferiores herdam as permissões de função de níveis superiores. 

![Escopo para uma atribuição de função](./media/scope-overview/rbac-scope-no-label.png)

Os grupos de gerenciamento estão um nível de escopo acima das assinaturas, mas eles dão suporte a hierarquias mais complexas. O diagrama a seguir mostra um exemplo de uma hierarquia de grupos de gerenciamento e assinaturas que você pode definir. Para obter mais informações sobre grupos de gerenciamento, confira [O que são grupos de gerenciamento do Azure?](../governance/management-groups/overview.md).

![Grupo de gerenciamento e hierarquia de assinaturas](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>Formato do escopo

Se você atribuir funções usando a linha de comando, precisará especificar o escopo. Para as ferramentas da linha de comando, o escopo é uma cadeia de caracteres potencialmente longa que identifica o escopo exato da atribuição de função. No portal do Azure, esse escopo é normalmente listado como a *ID do recurso*.

O escopo consiste em uma série de identificadores separados pelo caractere de barra (/). Você pode considerar essa cadeia de caracteres como a hierarquia a seguir, em que o texto sem espaços reservados (`{}`) são identificadores fixos:

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` é a ID da assinatura a ser usada (um GUID).
- `{resourcesGroupName}` é o nome do grupo de recursos contentor.
- `{providerName}` é o nome do [provedor de recursos](../azure-resource-manager/management/azure-services-resource-providers.md) que manipula o recurso. `{resourceType}` e `{resourceSubType*}` identificam mais níveis dentro desse provedor de recursos.
- `{resourceName}` é a última parte da cadeia de caracteres que identifica um recurso específico.

Os grupos de gerenciamento estão um nível acima das assinaturas e têm o escopo mais amplo (menos específico). As atribuições de função nesse nível se aplicam a assinaturas dentro do grupo de gerenciamento. O escopo de um grupo de gerenciamento tem o seguinte formato:

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>Exemplos de escopo

> [!div class="mx-tableFixed"]
> | Escopo | Exemplo |
> | --- | --- |
> | Grupo de gerenciamento | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | Subscription | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Resource group | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | Recurso | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>Como determinar o escopo de um recurso

É bastante simples determinar o escopo de um grupo de gerenciamento, uma assinatura ou um grupo de recursos. Você só precisa saber o nome e a ID da assinatura. No entanto, determinar o escopo de um recurso dá um pouco mais de trabalho. Veja algumas formas de determinar o escopo de um recurso.

- No portal do Azure, abra o recurso e examine as propriedades. O recurso deve listar a **ID do recurso** em que você pode determinar o escopo. Por exemplo, aqui estão as IDs de recurso para uma conta de armazenamento.

    ![IDs de recurso para uma conta de armazenamento no portal do Azure](./media/scope-overview/scope-resource-id.png)

- Outra maneira é usar o portal do Azure para atribuir uma função temporariamente no escopo do recurso e usar o [Azure PowerShell](role-assignments-list-powershell.md) ou a [CLI do Azure](role-assignments-list-cli.md) para listar a atribuição de função. Na saída, o escopo será listado como uma propriedade.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="next-steps"></a>Próximas etapas

- [Etapas para atribuir funções no Azure](role-assignments-steps.md)
- [Provedores de recursos para serviços do Azure](../azure-resource-manager/management/azure-services-resource-providers.md)
- [O que são grupos de gerenciamento do Azure?](../governance/management-groups/overview.md)
