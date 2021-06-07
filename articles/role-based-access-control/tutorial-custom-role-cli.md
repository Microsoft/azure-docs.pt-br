---
title: 'Tutorial: Criar uma função personalizada do Azure com a CLI do Azure – RBAC do Azure'
description: Introdução à criação de uma função personalizada do Azure usando a CLI do Azure e o RBAC do Azure (controle de acesso baseado em função do Azure) neste tutorial.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.custom: devx-track-azurecli
ms.openlocfilehash: e3743697d58d0f5b167b123df59bc5638aa60489
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771669"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-cli"></a>Tutorial: Criar uma função personalizada do Azure usando a CLI do Azure

Se as [funções internas do Azure](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar funções personalizadas próprias. Para este tutorial, crie uma função personalizada chamada Tíquetes de suporte do leitor usando a CLI do Azure. A função personalizada permite ao usuário exibir tudo no plano de gerenciamento de uma assinatura e também abrir tíquetes de suporte.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma função personalizada
> * Listar funções personalizadas
> * Atualizar uma função personalizada
> * Excluir uma função personalizada

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, você precisará de:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de acesso do usuário](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) ou [CLI do Azure](/cli/azure/install-azure-cli)

## <a name="sign-in-to-azure-cli"></a>Entrar na CLI do Azure

Entrar na [CLI do Azure](/cli/azure/authenticate-azure-cli).

## <a name="create-a-custom-role"></a>Criar uma função personalizada

A maneira mais fácil de criar uma função personalizada é começar com um modelo JSON, adicionar suas alterações e criar uma nova função.

1. Examine a lista de operações para o [provedor de recursos Microsoft.Support](resource-provider-operations.md#microsoftsupport). É útil conhecer as operações disponíveis para criar as permissões.

    | Operação | Descrição |
    | --- | --- |
    | Microsoft.Support/register/action | Registrar para dar suporte ao provedor de recursos |
    | Microsoft.Support/supportTickets/read | Obter detalhes de tíquete de suporte (incluindo status, severidade, detalhes de contato e comunicações) ou obtém a lista de tíquetes de suporte entre assinaturas. |
    | Microsoft.Support/supportTickets/write | Criar ou atualizar um tíquete de suporte. Você pode criar um tíquete de suporte para problemas técnicos, de cotas, de cobrança ou de gerenciamento de assinaturas. Você pode atualizar severidade, detalhes de contato e comunicações de tíquetes de suporte existentes. |
    
1. Criar um novo arquivo chamado **ReaderSupportRole.json**.

1. Abra ReaderSupportRole.json em um editor e adicione o JSON a seguir.

    Para obter informações sobre as diversas propriedades, confira [Funções personalizadas do Azure](custom-roles.md).

    ```json
    {
      "Name": "",
      "IsCustom": true,
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId1}"
      ]
    }
    ```
    
1. Adicione as seguintes operações à `Actions` propriedade. Essas ações permitem que o usuário veja tudo na assinatura e crie os tíquetes de suporte.

    ```
    "*/read",
    "Microsoft.Support/*"
    ```

1. Obtenha a ID da sua assinatura usando o comando [az account list](/cli/azure/account#az_account_list).

    ```azurecli
    az account list --output table
    ```

1. Em `AssignableScopes`, substitua `{subscriptionId1}` pela sua ID da assinatura.

    Você deve adicionar IDs de assinatura explícitas; caso contrário, não será possível importar a função para a assinatura.

1. Altere as propriedades `Name` e `Description` para "Tíquetes de suporte do leitor" e "Exibir tudo na assinatura e também abrir tíquetes de suporte".

    Seu arquivo JSON deverá ter a seguinte aparência:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. Para criar a nova função personalizada, use o comando [az role definition create](/cli/azure/role/definition#az_role_definition_create) e especifique o arquivo de definição da função JSON.

    ```azurecli
    az role definition create --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

    A nova função personalizada agora está disponível e pode ser atribuída a usuários, grupos ou entidades de serviço da mesma forma que as funções internas.

## <a name="list-custom-roles"></a>Listar funções personalizadas

- Para listar todas as suas funções personalizadas, use o comando [az role definition list](/cli/azure/role/definition#az_role_definition_list) com o parâmetro `--custom-role-only`.

    ```azurecli
    az role definition list --custom-role-only true
    ```
    
    ```Output
    [
      {
        "additionalProperties": {},
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ],
        "description": "View everything in the subscription and also open support tickets.",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
        "name": "22222222-2222-2222-2222-222222222222",
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Support/*",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Insights/diagnosticSettings/*/read"
            ],
            "additionalProperties": {},
            "dataActions": [],
            "notActions": [],
            "notDataActions": []
          }
        ],
        "roleName": "Reader Support Tickets",
        "roleType": "CustomRole",
        "type": "Microsoft.Authorization/roleDefinitions"
      }
    ]
    ```
    
    Você também pode ver a função personalizada no portal do Azure.

    ![captura de tela de função personalizada importada no Portal do Azure](./media/tutorial-custom-role-cli/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar a função personalizada, atualize o arquivo JSON e, em seguida, atualize a função personalizada.

1. Abra o arquivo ReaderSupportRole.json.

1. Em `Actions`, adicione a operação de criar e gerenciar implantações de grupos de recursos `"Microsoft.Resources/deployments/*"`. Inclua uma vírgula após a operação anterior.

    O arquivo JSON atualizado deve ficar mais ou menos assim:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. Para atualizar a função personalizada, use o comando [az role definition update](/cli/azure/role/definition#az_role_definition_update) e especifique o arquivo JSON atualizado.

    ```azurecli
    az role definition update --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*",
            "Microsoft.Resources/deployments/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```
    
## <a name="delete-a-custom-role"></a>Excluir uma função personalizada

- Use o comando [az role definition delete](/cli/azure/role/definition#az_role_definition_delete) e especifique o nome da função ou a ID de função para excluir a função personalizada.

    ```azurecli
    az role definition delete --name "Reader Support Tickets"
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar ou atualizar funções personalizadas do Azure usando a CLI do Azure](custom-roles-cli.md)
