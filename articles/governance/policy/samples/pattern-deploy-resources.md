---
title: 'Padrão: Implantar recursos com uma definição de política'
description: Esse padrão do Azure Policy fornece um exemplo de como implantar recursos com uma definição de política deployIfNotExists.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: 1dda3d5f2d4bef57919931ec3c1635310723e0c8
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093393"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Padrão do Azure Policy: implantar recursos

Com o efeito [deployIfNotExists](../concepts/effects.md#deployifnotexists), é possível implantar um [modelo do ARM](../../../azure-resource-manager/templates/overview.md) (modelo do Azure Resource Manager) ao criar ou atualizar um recurso que não está em conformidade. Pode ser preferível usar essa abordagem do que o efeito [deny](../concepts/effects.md#deny), pois ela permite que os recursos continuem sendo criados, mas garante que as alterações sejam feitas para torná-los compatíveis.

## <a name="sample-policy-definition"></a>Definição de política de amostra

Esta definição de política usa o operador **field** para avaliar o `type` do recurso criado ou atualizado. Quando esse recurso é um _Microsoft.Network/virtualNetworks_, a política procura um observador de rede no local do recurso novo ou atualizado. Se um observador de rede correspondente não for localizado, o modelo do ARM será implantado para criar o recurso ausente.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Explicação

#### <a name="existencecondition"></a>existenceCondition

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

O bloco **properties.policyRule.then.details** informa ao Azure Policy o que procurar relacionado ao recurso criado ou atualizado no bloco **properties.policyRule.if**. Neste exemplo, um observador de rede no grupo de recursos **networkWatcherRG** deve existir com um **field** `location`igual ao local do recurso novo ou atualizado. O uso da função `field()` permite que **existenceCondition** acesse propriedades no recurso novo ou atualizado, especificamente na propriedade `location`.

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

A propriedade da matriz **roleDefinitionIds** _no bloco_ **properties.policyRule.then.details** informa à definição de política quais direitos a identidade gerenciada precisa para implantar o modelo do Resource Manager incluído. Essa propriedade deve ser configurada para incluir funções que tenham as permissões necessárias para a implantação do modelo, mas deve usar o conceito de "princípio do menor privilégio" e ter apenas as operações necessárias.

#### <a name="deployment-template"></a>Modelo de implantação

A parte **implantação** da definição de política possui um bloco **propriedades** que define os três componentes principais:

- **modo** – essa propriedade define o [modo de implantação](../../../azure-resource-manager/templates/deployment-modes.md) do modelo.

- **modelo** – essa propriedade inclui o próprio modelo. Neste exemplo, o parâmetro do modelo **local** define o local do novo recurso do observador de rede.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **parâmetros** – essa propriedade define os parâmetros que são fornecidos para o **modelo**. Os nomes dos parâmetros devem corresponder ao que está definido no **modelo**. Neste exemplo, o parâmetro é nomeado **local** para corresponder. O valor de **local** usa a função `field()` novamente para obter o valor do recurso avaliado, que é a rede virtual no bloco **policyRule.if**.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>Próximas etapas

- Revise outros [padrões e definições internas](./index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).