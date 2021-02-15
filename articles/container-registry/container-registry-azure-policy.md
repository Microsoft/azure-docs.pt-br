---
title: Conformidade usando o Azure Policy
description: Atribuir políticas internas no Azure Policy para auditar a conformidade de seus registros de contêiner do Azure
ms.topic: article
ms.date: 06/11/2020
ms.openlocfilehash: 26c56616bcc411063d0ebfda28ba1e6fdf44c7fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291001"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Auditar a conformidade de registros de contêiner do Azure usando o Azure Policy

[Azure Policy](../governance/policy/overview.md) é um serviço no Azure que você usa para criar, atribuir e gerenciar políticas. Essas políticas impõem diferentes regras e efeitos sobre os recursos para que esses recursos permaneçam em conformidade com seus padrões corporativos e contratos de nível de serviço.

Este artigo apresenta políticas internas para o registro de contêiner do Azure. Use essas políticas para auditar registros novos e existentes para fins de conformidade.

Não há encargos para usar Azure Policy.

## <a name="built-in-policy-definitions"></a>Definições de política internas

As definições de política internas a seguir são específicas do registro de contêiner do Azure:

[!INCLUDE [azure-policy-reference-policies-container-registry](../../includes/policy/reference/bycat/policies-container-registry.md)]

Consulte também a definição de política de rede interna: o [registro de contêiner deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78).

## <a name="assign-policies"></a>Atribuir políticas

* Atribua políticas usando o [portal do Azure](../governance/policy/assign-policy-portal.md), [CLI do Azure](../governance/policy/assign-policy-azurecli.md), um [modelo do Resource Manager](../governance/policy/assign-policy-template.md)ou os SDKs do Azure Policy.
* Escopo uma atribuição de política para um grupo de recursos, uma assinatura ou um [grupo de gerenciamento do Azure](../governance/management-groups/overview.md). As atribuições de política de registro de contêiner se aplicam a registros de contêiner novos e existentes dentro do escopo.
* Habilite ou desabilite a [imposição de política](../governance/policy/concepts/assignment-structure.md#enforcement-mode) a qualquer momento.

> [!NOTE]
> Depois de atribuir ou atualizar uma política, levará algum tempo para que a atribuição seja aplicada aos recursos no escopo definido. Consulte informações sobre [gatilhos de avaliação de política](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Examinar a conformidade da política

Acesse informações de conformidade geradas por suas atribuições de política usando o portal do Azure, as ferramentas de linha de comando do Azure ou os SDKs de Azure Policy. Para obter detalhes, consulte [obter dados de conformidade dos recursos do Azure](../governance/policy/how-to/get-compliance-data.md).

Quando um recurso não está em conformidade, há muitos motivos possíveis. Para determinar o motivo ou para localizar a alteração responsável, consulte [determinar a não conformidade](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Conformidade de política no Portal:

1. Selecione **todos os serviços**e procure **política**.
1. Selecione **conformidade**.
1. Use os filtros para limitar os Estados de conformidade ou para pesquisar políticas.

    ![Conformidade de política no portal](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. Selecione uma política para examinar os detalhes de conformidade agregada e eventos. Se desejar, selecione um registro específico para conformidade de recursos.

### <a name="policy-compliance-in-the-azure-cli"></a>Conformidade de política no CLI do Azure

Você também pode usar o CLI do Azure para obter dados de conformidade. Por exemplo, use o comando de [lista de atribuição de política AZ](/cli/azure/policy/assignment#az-policy-assignment-list) na CLI para obter as IDs de política das políticas de registro de contêiner do Azure que são aplicadas:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Saída de exemplo:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Em seguida, execute a [lista de estado de política AZ](/cli/azure/policy/state#az-policy-state-list) para retornar o estado de conformidade formatado em JSON para todos os recursos em uma ID de política específica:

```azurecli
az policy state list \
  --resource <policyID>
```

Ou execute a [lista de estado de política AZ](/cli/azure/policy/state#az-policy-state-list) para retornar o estado de conformidade formatado em JSON de um recurso de registro específico, como *myregistry*:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [definições](../governance/policy/concepts/definition-structure.md) e [efeitos](../governance/policy/concepts/effects.md)de Azure Policy.

* Crie uma [definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md).

* Saiba mais sobre os [recursos de governança](../governance/index.yml) no Azure.
