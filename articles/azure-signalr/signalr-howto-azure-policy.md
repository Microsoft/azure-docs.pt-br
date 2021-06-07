---
title: Conformidade usando o Azure Policy
description: Atribua políticas internas no Azure Policy para auditar a conformidade dos recursos do serviço de Signaler do Azure.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: 018033d3a6123948191a7261f5a1ee2ae526e25a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89295005"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Auditar a conformidade dos recursos do serviço de Signaler do Azure usando o Azure Policy

[Azure Policy](../governance/policy/overview.md) é um serviço no Azure que você usa para criar, atribuir e gerenciar políticas. Essas políticas impõem diferentes regras e efeitos sobre os recursos para que esses recursos permaneçam em conformidade com seus padrões corporativos e contratos de nível de serviço.

Este artigo apresenta políticas internas (versão prévia) para o serviço de Signaler do Azure. Use essas políticas para auditar recursos de Signaler novos e existentes para fins de conformidade.

Não há encargos para usar Azure Policy.

## <a name="built-in-policy-definitions"></a>Definições de política internas

As definições de política internas a seguir são específicas do serviço de Signaler do Azure:

[!INCLUDE [azure-policy-reference-policies-signalr](../../includes/policy/reference/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>Atribuir definições de política

* Atribua definições de política usando o [portal do Azure](../governance/policy/assign-policy-portal.md), [CLI do Azure](../governance/policy/assign-policy-azurecli.md), um [modelo do Resource Manager](../governance/policy/assign-policy-template.md)ou os SDKs do Azure Policy.
* Escopo uma atribuição de política para um grupo de recursos, uma assinatura ou um [grupo de gerenciamento do Azure](../governance/management-groups/overview.md). As atribuições de política do signalr aplicam-se a recursos de Signaler novos e existentes dentro do escopo.
* Habilite ou desabilite a [imposição de política](../governance/policy/concepts/assignment-structure.md#enforcement-mode) a qualquer momento.

> [!NOTE]
> Depois de atribuir ou atualizar uma política, levará algum tempo para que a atribuição seja aplicada aos recursos no escopo definido. Consulte informações sobre [gatilhos de avaliação de política](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Examinar a conformidade da política

Acesse informações de conformidade geradas por suas atribuições de política usando o portal do Azure, as ferramentas de linha de comando do Azure ou os SDKs de Azure Policy. Para obter detalhes, consulte [obter dados de conformidade dos recursos do Azure](../governance/policy/how-to/get-compliance-data.md).

Quando um recurso não está em conformidade, há muitos motivos possíveis. Para determinar o motivo ou para localizar a alteração responsável, consulte [determinar a não conformidade](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Conformidade de política no Portal:

1. Selecione **todos os serviços** e procure **política**.
1. Selecione **conformidade**.
1. Use os filtros para limitar os Estados de conformidade ou para pesquisar políticas
   
    [![Conformidade de política no portal ](./media/signalr-howto-azure-policy/azure-policy-compliance.png)](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Selecione uma política para examinar os detalhes de conformidade agregada e eventos. Se desejar, selecione um Signalr específico para conformidade de recursos.

### <a name="policy-compliance-in-the-azure-cli"></a>Conformidade de política no CLI do Azure

Você também pode usar o CLI do Azure para obter dados de conformidade. Por exemplo, use o comando de [lista de atribuição de política AZ](/cli/azure/policy/assignment#az-policy-assignment-list) na CLI para obter as IDs de política das políticas de serviço de signaler do Azure que são aplicadas:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Saída de exemplo:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Em seguida, execute a [lista de estado de política AZ](/cli/azure/policy/state#az-policy-state-list) para retornar o estado de conformidade formatado em JSON para todos os recursos em um grupo de recursos específico:

```azurecli
az policy state list --g <resourceGroup>
```

Ou execute a [lista de Estados de política AZ](/cli/azure/policy/state#az-policy-state-list) para retornar o estado de conformidade formatado em JSON de um recurso de signalr específico:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [definições](../governance/policy/concepts/definition-structure.md) e [efeitos](../governance/policy/concepts/effects.md) de Azure Policy

* Criar uma [definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md)

* Saiba mais sobre os [recursos de governança](../governance/index.yml) no Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/