---
title: Exclusões de histórico de implantações
description: Descreve como Azure Resource Manager exclui automaticamente implantações do histórico de implantação. As implantações são excluídas quando o histórico está próximo de exceder o limite de 800.
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 13c65f3311e308708034bb5befb7e3c3ee158d38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652475"
---
# <a name="automatic-deletions-from-deployment-history"></a>Exclusões automáticas do histórico de implantação

Toda vez que você implanta um modelo, as informações sobre a implantação são gravadas no histórico de implantação. Cada grupo de recursos é limitado a 800 implantações em seu histórico de implantação.

Azure Resource Manager exclui automaticamente as implantações do seu histórico conforme você próximo ao limite. A exclusão automática é uma alteração do comportamento passado. Anteriormente, era necessário excluir manualmente as implantações do histórico de implantação para evitar a obtenção de um erro. Essa alteração foi implementada em 6 de agosto de 2020.

**As exclusões automáticas têm suporte para implantações de grupo de recursos. Atualmente, as implantações no histórico de [assinatura](deploy-to-subscription.md), [grupo de gerenciamento](deploy-to-management-group.md)e implantações de [locatário](deploy-to-tenant.md) não são excluídas automaticamente.**

> [!NOTE]
> A exclusão de uma implantação do histórico não afeta nenhum dos recursos que foram implantados.

## <a name="when-deployments-are-deleted"></a>Quando as implantações são excluídas

As implantações são excluídas do seu histórico quando você excede 775 implantações. Azure Resource Manager exclui as implantações até que o histórico seja reduzido para 750. As implantações mais antigas são sempre excluídas primeiro.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Exclusões do histórico de implantação":::

> [!NOTE]
> O número inicial (775) e o número final (750) estão sujeitos a alterações.
>
> Se o grupo de recursos já estiver no limite de 800, a próxima implantação falhará com um erro. O processo de exclusão automática é iniciado imediatamente. Você pode tentar a implantação novamente após uma breve espera.

Além das implantações, você também dispara exclusões quando executa a [operação What-If](template-deploy-what-if.md) ou valida uma implantação.

Quando você dá a uma implantação o mesmo nome que uma no histórico, você redefine seu local no histórico. A implantação é movida para o local mais recente no histórico. Você também redefine o local de uma implantação ao [reverter para essa implantação](rollback-on-error.md) após um erro.

## <a name="remove-locks-that-block-deletions"></a>Remover bloqueios que bloqueiam exclusões

Se você tiver um [bloqueio CanNotDelete](../management/lock-resources.md) em um grupo de recursos, as implantações desse grupo de recursos não poderão ser excluídas. Você deve remover o bloqueio para aproveitar as exclusões automáticas no histórico de implantação.

Para usar o PowerShell para excluir um bloqueio, execute os seguintes comandos:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Para usar CLI do Azure para excluir um bloqueio, execute os seguintes comandos:

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="opt-out-of-automatic-deletions"></a>Recusar exclusões automáticas

Você pode recusar exclusões automáticas do histórico. **Use essa opção somente quando quiser gerenciar o histórico de implantação por conta própria.** O limite de implantações de 800 no histórico ainda é imposto. Se você exceder 800 implantações, receberá um erro e a implantação falhará.

Para desabilitar as exclusões automáticas, registre o `Microsoft.Resources/DisableDeploymentGrooming` sinalizador de recurso. Ao registrar o sinalizador de recurso, você recusa as exclusões automáticas de toda a assinatura do Azure. Você não pode recusar apenas um determinado grupo de recursos. Para reabilitar as exclusões automáticas, cancele o registro do sinalizador de recurso.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para o PowerShell, use [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Para ver o status atual da sua assinatura, use:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Para reabilitar exclusões automáticas, use a API REST do Azure ou CLI do Azure.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para CLI do Azure, use [o registro de recurso AZ](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Para ver o status atual da sua assinatura, use:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Para reabilitar exclusões automáticas, use [o recurso AZ cancelar o registro](/cli/azure/feature#az-feature-unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Para a API REST, use [recursos-registrar](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Para ver o status atual da sua assinatura, use:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Para reabilitar exclusões automáticas, usar [recursos-cancelar registro](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como exibir o histórico de implantação, consulte [Exibir o histórico de implantação com Azure Resource Manager](deployment-history.md).
