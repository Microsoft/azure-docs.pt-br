---
title: Remover o acesso a uma delegação
description: Saiba como remover o acesso a recursos que foram delegados a um provedor de serviços para o Azure Lighthouse.
ms.date: 12/03/2020
ms.topic: how-to
ms.openlocfilehash: 7218c8829c9b2500e8311fbb3d5e9ff4eb986529
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791349"
---
# <a name="remove-access-to-a-delegation"></a>Remover o acesso a uma delegação

Depois que a assinatura ou o grupo de recursos de um cliente tiver sido delegado a um provedor de serviços para o [Azure Lighthouse](../overview.md), a delegação poderá ser removida se necessário. Depois que uma delegação é removida, o acesso de [Gerenciamento de recursos delegado do Azure](../concepts/azure-delegated-resource-management.md) que foi concedido anteriormente aos usuários no locatário do provedor de serviços não será mais aplicado.

A remoção de uma delegação pode ser feita por um usuário no locatário do cliente ou no locatário do provedor de serviços, desde que o usuário tenha as permissões apropriadas.

> [!TIP]
> Embora possamos nos referimos a provedores de serviços e clientes neste tópico, as [empresas que gerenciam vários locatários](../concepts/enterprise.md) podem usar os mesmos processos.

## <a name="customers"></a>Clientes

Os usuários no locatário do cliente que têm a [função interna de proprietário](../../role-based-access-control/built-in-roles.md#owner) para uma assinatura podem remover o acesso do provedor de serviço a essa assinatura (ou aos grupos de recursos nessa assinatura). Para fazer isso, um usuário no locatário do cliente pode ir para a [página provedores de serviço](view-manage-service-providers.md#add-or-remove-service-provider-offers) do portal do Azure, localizar a oferta na tela **ofertas do provedor de serviço** e selecionar o ícone de lixeira na linha dessa oferta.

Depois de confirmar a exclusão, nenhum usuário no locatário do provedor de serviços poderá acessar os recursos que foram previamente delegados.

## <a name="service-providers"></a>Provedores de serviço

Os usuários em um locatário de gerenciamento podem remover o acesso a recursos delegados se eles tiverem recebido a [função de exclusão de atribuição de registro de serviços gerenciados](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) para os recursos do cliente. Se essa função não tiver sido atribuída a nenhum usuário do provedor de serviços, a delegação só poderá ser removida por um usuário no locatário do cliente.

O exemplo a seguir mostra uma atribuição que concede a **função de exclusão de atribuição de registro de serviços gerenciados** que pode ser incluída em um arquivo de parâmetro durante o processo de [integração](onboard-customer.md):

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Essa função também pode ser selecionada em uma **autorização** ao [criar uma oferta de serviço gerenciado](../../marketplace/plan-managed-service-offer.md) para publicar no Azure Marketplace.

Um usuário com essa permissão pode remover uma delegação de uma das maneiras a seguir.

### <a name="azure-portal"></a>Portal do Azure

1. Navegue até a [página Meus clientes](view-manage-customers.md).
2. Selecione **delegações**.
3. Localize a delegação que você deseja remover e, em seguida, selecione o ícone de lixeira que aparece em sua linha.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Gerenciamento de recursos delegados do Azure](../concepts/azure-delegated-resource-management.md).
- [Exiba e gerencie clientes](view-manage-customers.md) acessando **Meus clientes** no portal do Azure.
- Saiba como [atualizar uma delegação anterior](update-delegation.md).
