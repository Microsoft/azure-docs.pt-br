---
title: Restringir o acesso do usuário a operações de dados somente com Azure Cosmos DB
description: Saiba como restringir o acesso a operações de dados somente com Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 61e8ee2cbcd1252704b2d1e220fcfaaff17c2f28
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93085982"
---
# <a name="restrict-user-access-to-data-operations-in-azure-cosmos-db"></a>Restringir o acesso do usuário a operações de dados no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

No Azure Cosmos DB, há duas maneiras de autenticar suas interações com o serviço de banco de dados:

- usando sua identidade de Azure Active Directory ao interagir com o portal do Azure,
- usar [chaves](database-security.md#primary-keys) de Azure Cosmos DB ou [tokens de recurso](secure-access-to-data.md#resource-tokens) ao emitir chamadas de APIs e SDKs.

Cada método de autenticação dá acesso a diferentes conjuntos de operações, com alguma sobreposição:

:::image type="content" source="./media/how-to-restrict-user-data/operations.png" alt-text="Divisão de operações por tipo de autenticação" border="false":::

Em alguns cenários, talvez você queira restringir alguns usuários da sua organização para executar operações de dados (que são solicitações e consultas CRUD) apenas. Normalmente, esse é o caso para os desenvolvedores que não precisam criar ou excluir recursos, ou alterar a taxa de transferência provisionada dos contêineres em que estão trabalhando.

Você pode restringir o acesso aplicando as seguintes etapas:
1. Criar uma função de Azure Active Directory personalizada para os usuários aos quais você deseja restringir o acesso. A função de Active Directory personalizada deve ter um nível de acesso refinado para operações usando [ações granulares](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)Azure Cosmos DB.
1. Desautorizando a execução de operações que não são de dados com chaves. Você pode conseguir isso restringindo essas operações somente a chamadas Azure Resource Manager.

As próximas seções deste artigo mostram como executar essas etapas.

> [!NOTE]
> Para executar os comandos nas próximas seções, você precisa instalar Azure PowerShell módulo 3.0.0 ou posterior, bem como a [função de proprietário do Azure](../role-based-access-control/built-in-roles.md#owner) na assinatura que você está tentando modificar.

Nos scripts do PowerShell nas próximas seções, substitua os seguintes espaços reservados por valores específicos do seu ambiente:
- `$MySubscriptionId` -A ID da assinatura que contém a conta do Azure Cosmos em que você deseja limitar as permissões. Por exemplo: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName` -O grupo de recursos que contém a conta do Azure Cosmos. Por exemplo: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName` -O nome da sua conta do Azure Cosmos. Por exemplo: `mycosmosdbsaccount`.
- `$MyUserName` -O logon ( username@domain ) do usuário para o qual você deseja limitar o acesso. Por exemplo: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Selecionar sua assinatura do Azure

Azure PowerShell comandos exigem que você faça logon e selecione a assinatura para executar os comandos:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Criar a função de Azure Active Directory personalizada

O script a seguir cria uma atribuição de função Azure Active Directory com o acesso "chave somente" para contas do Azure Cosmos. A função é baseada em [funções personalizadas do Azure](../role-based-access-control/custom-roles.md) e [ações granulares para Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). Essas funções e ações fazem parte do `Microsoft.DocumentDB` namespace Azure Active Directory.

1. Primeiro, crie um documento JSON chamado `AzureCosmosKeyOnlyAccess.json` com o seguinte conteúdo:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Execute os seguintes comandos para criar a atribuição de função e atribuí-la ao usuário:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Não permitir a execução de operações que não são de dados

Os comandos a seguir removem a capacidade de usar chaves para:
- criar, modificar ou excluir recursos
- atualizar configurações de contêiner (incluindo políticas de indexação, taxa de transferência, etc.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [controle de acesso baseado em função do cosmos DB](role-based-access-control.md)
- Obtenha uma visão geral de [acesso seguro aos dados no cosmos DB](secure-access-to-data.md)
