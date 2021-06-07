---
title: Excluir o grupo de recursos e os recursos
description: Descreve como excluir recursos e grupos de recursos. Ele descreve como o Azure Resource Manager ordena a exclusão de recursos quando uma exclusão de um grupo de recursos. Descreve os códigos de resposta e como o Resource Manager os manipula para determinar se a exclusão teve êxito.
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: seodec18
ms.openlocfilehash: 244d59ffc096b5e219e27fd376b07baecde3670e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587654"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager o grupo de recursos e a exclusão de recursos

Este artigo mostra como excluir recursos e grupos de recursos. Ele descreve como o Azure Resource Manager ordena a exclusão de recursos quando você exclui um grupo de recursos.

## <a name="how-order-of-deletion-is-determined"></a>Como a ordem de exclusão é determinada

Quando você exclui um grupo de recursos, o Gerenciador de Recursos determina a ordem para excluir recursos. Ele usa a seguinte ordem:

1. Todos os recursos filho (aninhados) são excluídos.

2. Recursos que gerenciam outros recursos são excluídos a seguir. Um recurso pode ter a propriedade `managedBy` definida para indicar que um recurso diferente a gerencia. Quando essa propriedade é configurada, o recurso que gerencia o outro recurso é excluído antes dos outros recursos.

3. Os recursos restantes são excluídos após as duas categorias anteriores.

Depois que o pedido é determinado, o Gerenciador de recursos emite uma operação DELETE para cada recurso. Aguarda que quaisquer dependências terminem antes de prosseguir.

Para operações síncronas, os códigos de resposta bem-sucedidos esperados são:

* 200
* 204
* 404

Para operações assíncronas, a resposta bem-sucedida esperada é 202. O Gerenciador de Recursos rastreia o cabeçalho do local ou o cabeçalho da operação azure-async para determinar o status da operação de exclusão assíncrona.
  
### <a name="deletion-errors"></a>Erros de exclusão

Quando uma operação de exclusão retorna um erro, o Gerenciador de Recursos repete a chamada DELETE. As novas tentativas ocorrem para os códigos de status 5xx, 429 e 408. Por padrão, o período de repetição é de 15 minutos.

## <a name="after-deletion"></a>Após a exclusão

Gerenciador de recursos emite uma chamada GET em cada recurso que ele tentou excluir. Espera-se que a resposta desta chamada GET seja 404. Quando o Gerenciador de Recursos recebe um erro 404, ele considera a exclusão concluída com êxito. O Gerenciador de recursos remove o recurso do cache.

No entanto, se a chamada GET no recurso retornar um 200 ou 201, o Gerenciador de recursos recria o recurso.

Se a operação GET retornar um erro, o Gerenciador de Recursos tentará novamente o GET para o seguinte código de erro:

* Menos de 100
* 408
* 429
* Maior que 500

Para outros códigos de erro, o Gerenciador de Recursos falha na exclusão do recurso.

> [!IMPORTANT]
> A exclusão do grupo de recursos é irreversível.

## <a name="delete-resource-group"></a>Excluir grupo de recursos

Use um dos métodos a seguir para excluir o grupo de recursos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal](https://portal.azure.com), selecione o grupo de recursos que você deseja excluir.

1. Selecione **Excluir grupo de recursos**.

   ![Excluir grupo de recursos](./media/delete-resource-group/delete-group.png)

1. Para confirmar a exclusão, digite o nome do grupo de recursos

---

## <a name="delete-resource"></a>Excluir recurso

Use um dos métodos a seguir para excluir um recurso.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal](https://portal.azure.com), selecione o recurso que você deseja excluir.

1. Selecione **Excluir**. A captura de tela a seguir mostra as opções de gerenciamento para uma máquina virtual.

   ![Excluir recurso](./media/delete-resource-group/delete-resource.png)

1. Quando receber a solicitação, confirme a exclusão.

---

## <a name="required-access"></a>Acesso necessário

Para excluir um grupo de recursos, você precisa ter acesso à ação Excluir para o recurso **Microsoft. Resources/subscriptions/resourceGroups** . Você também precisa excluir para todos os recursos no grupo de recursos.

Para obter uma lista de operações, consulte [operações do provedor de recursos do Azure](../../role-based-access-control/resource-provider-operations.md). Para obter uma lista de funções internas, consulte [funções internas do Azure](../../role-based-access-control/built-in-roles.md).

Se você tiver o acesso necessário, mas a solicitação de exclusão falhar, talvez seja porque há um [bloqueio](lock-resources.md) no grupo de recursos.

## <a name="next-steps"></a>Próximas etapas

* Para reconhecer os conceitos do Gerenciador de Recursos, consulte [Visão geral do Azure Resource Manager](overview.md).
* Para comandos de exclusão, consulte [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [CLI do Azure](/cli/azure/group#az-group-delete) e [API REST](/rest/api/resources/resourcegroups/delete).
