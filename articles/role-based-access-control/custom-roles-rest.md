---
title: Criar funções personalizadas para recursos do Azure usando a API REST – Microsoft Azure | Microsoft Docs
description: Saiba como criar funções personalizadas com o RBAC (controle de acesso baseado em função) para recursos do Azure usando a API REST. Isso inclui como listar, criar, atualizar e excluir funções personalizadas.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4024f6fdb40c752ef61f348d15f681e81d81c08c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596688"
---
# <a name="create-custom-roles-for-azure-resources-using-the-rest-api"></a>Criar funções personalizadas para recursos do Azure usando a API REST

Se as [funções internas dos recursos do Azure](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias funções personalizadas. Este artigo descreve como criar e gerenciar o acesso usando a API REST.

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar todas as funções personalizadas em um diretório, use o [definições de função – lista](/rest/api/authorization/roledefinitions/list) API REST.

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Substitua *{filter}* com o tipo de função.

    | Filter | DESCRIÇÃO |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrar com base no tipo CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Listar funções personalizadas em um escopo

Para listar as funções personalizadas em um escopo, use o [definições de função – lista](/rest/api/authorization/roledefinitions/list) API REST.

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro da URI, substitua *{scope}* pelo escopo para o qual você deseja listar as funções.

    | Escopo | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Substitua *{filter}* com o tipo de função.

    | Filter | DESCRIÇÃO |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrar com base no tipo CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Listar uma definição de função personalizada por nome

Para obter informações sobre uma função personalizada por seu nome de exibição, use o [definições de função – obter](/rest/api/authorization/roledefinitions/get) API REST.

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro da URI, substitua *{scope}* pelo escopo para o qual você deseja listar as funções.

    | Escopo | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Substitua *{filter}* com o nome de exibição para a função.

    | Filter | DESCRIÇÃO |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Use a forma codificada da URL do nome de exibição exato da função. Por exemplo, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Listar uma definição de função personalizada por ID

Para obter informações sobre uma função personalizada por seu identificador exclusivo, use o [definições de função – obter](/rest/api/authorization/roledefinitions/get) API REST.

1. Use [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) API REST para obter o identificador GUID para a função.

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro da URI, substitua *{scope}* pelo escopo para o qual você deseja listar as funções.

    | Escopo | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Substitua *{roleDefinitionId}* pelo identificador GUID de definição da função.

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar uma função personalizada, use [Definições de Função - Criar ou Atualizar](/rest/api/authorization/roledefinitions/createorupdate) API REST. Para chamar essa API, você deve estar conectado com um usuário que é atribuído a uma função que tenha o `Microsoft.Authorization/roleDefinitions/write` permissão em todos os `assignableScopes`. Das funções internas, somente [proprietário](built-in-roles.md#owner) e [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) incluir essa permissão.

1. Examine a lista de [operações do provedor de recursos](resource-provider-operations.md) que estão disponíveis para criar as permissões para a função personalizada.

1. Use uma ferramenta GUID para gerar um identificador exclusivo que será usado para o identificador de função personalizada. O identificador tem o formato: `00000000-0000-0000-0000-000000000000`

1. Comece com a solicitação e corpo a seguir:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Dentro da URI, substita *{scope}* pelo primeiro `assignableScopes` da função personalizada.

    | Escopo | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Substitua *{roleDefinitionId}* pelo identificador GUID de definição da função personalizada.

1. Dentro do corpo de solicitação, na `assignableScopes` propriedade, substitua *{roleDefinitionId}* pelo identificador GUID.

1. Substitua *{subscriptionId}* pelo identificador da assinatura.

1. Em `actions` propriedade, adicione as operações que permitem que a função seja executada.

1. Em `notActions` propriedade, adicione as operações que são excluídas do permitidos `actions`.

1. Em `roleName` e `description` propriedades, especifique um nome exclusivo de função e uma descrição. Para obter informações sobre as propriedades, confira [Funções personalizadas](custom-roles.md).

    O exemplo a seguir mostra um exemplo de um corpo de solicitação:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar uma função personalizada, use [Definições de Função - Criar ou Atualizar](/rest/api/authorization/roledefinitions/createorupdate) API REST. Para chamar essa API, você deve estar conectado com um usuário que é atribuído a uma função que tenha o `Microsoft.Authorization/roleDefinitions/write` permissão em todos os `assignableScopes`. Das funções internas, somente [proprietário](built-in-roles.md#owner) e [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) incluir essa permissão.

1. Use [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) ou [Definições de Função - Obter](/rest/api/authorization/roledefinitions/get) API REST para obter informações sobre a função personalizada. Para obter mais informações, consulte cedo [listar funções personalizadas](#list-custom-roles) seção.

1. Comece com a solicitação a seguir:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro da URI, substita *{scope}* pelo primeiro `assignableScopes` da função personalizada.

    | Escopo | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Substitua *{roleDefinitionId}* pelo identificador GUID de definição da função personalizada.

1. Com base nas informações sobre a função personalizada, crie um corpo de solicitação com o seguinte formato:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Atualize o corpo de solicitação com as alterações que você deseja fazer a função personalizada.

    O exemplo a seguir mostra um exemplo de um corpo de solicitação com uma nova ação de configurações de diagnóstico adicionada:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Excluir uma função personalizada

Para excluir uma função personalizada, use [Definições de Função - Excluir](/rest/api/authorization/roledefinitions/delete) API REST. Para chamar essa API, você deve estar conectado com um usuário que é atribuído a uma função que tenha o `Microsoft.Authorization/roleDefinitions/delete` permissão em todos os `assignableScopes`. Das funções internas, somente [proprietário](built-in-roles.md#owner) e [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) incluir essa permissão.

1. Use [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) ou [Definições de Função - Obter](/rest/api/authorization/roledefinitions/get) API REST para obter o identificador GUI da função personalizada. Para obter mais informações, consulte cedo [listar funções personalizadas](#list-custom-roles) seção.

1. Comece com a solicitação a seguir:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro da URI, substitua *{scope}* pelo escopo para o qual você deseja excluir a função personalizada.

    | Escopo | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Substitua *{roleDefinitionId}* pelo identificador GUID de definição da função personalizada.

## <a name="next-steps"></a>Próximas etapas

- [Funções personalizadas para recursos do Azure](custom-roles.md)
- [Gerenciar o acesso aos recursos do Azure usando o RBAC e a API REST](role-assignments-rest.md)
- [Referência de API REST do Azure](/rest/api/azure/)