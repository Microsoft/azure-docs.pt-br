---
title: 'Padrão: O operador count em uma definição de política'
description: Esse padrão do Azure Policy fornece um exemplo de como usar o operador value em uma definição de política.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 8392c69ff3d63ff4ecad2a26d5d914b4766147b8
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072876"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Padrão do Azure Policy: o operador value

O operador [value](../concepts/definition-structure.md#value) avalia [parâmetros](../concepts/definition-structure.md#parameters), [funções de modelo com suporte](../concepts/definition-structure.md#policy-functions), ou literais para um valor fornecido para uma determinada [condição](../concepts/definition-structure.md#conditions).

> [!WARNING]
> Se o resultado de uma _função de modelo_ for um erro, a avaliação da política falhará. Uma avaliação com falha é um **deny** implícito. Para mais informações, confira [Evitar falhas de modelo](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Definição de política de amostra

Essa definição de política adiciona ou substitui a marca especificada no parâmetro **tagName** (_string_) nos recursos e herda o valor para **tagName** do grupo de recursos em que o recurso está. Essa avaliação ocorre quando o recurso é criado ou atualizado. Como um efeito [modify](../concepts/effects.md#modify) efeito, a correção pode ser executada em recursos existentes por meio de uma [tarefa de correção](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

O operador **valor** é usado dentro do bloco **policyRule.if** nas **propriedades**. Neste exemplo, o [operador lógico](../concepts/definition-structure.md#logical-operators) **allOf** é usado para afirmar que ambas as instruções condicionais devem ser verdadeiras para que o efeito **modify** ocorra.

**valor** avalia o resultado da função de modelo [resourceGroup()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) à condição **notEquals** de um valor em branco. Se o nome da marca fornecido em **tagName** no grupo de recursos pai existir, o condicional será avaliado como true.

## <a name="next-steps"></a>Próximas etapas

- Revise outros [padrões e definições internas](./index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).