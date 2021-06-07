---
title: 'Padrão: Parâmetros em uma definição de política'
description: Esse padrão do Azure Policy fornece um exemplo de como usar parâmetros de cadeia de caracteres e de matriz em uma definição de política, além de como parametrizar o efeito.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: b742aaaf950e2b5670edbaa1f0134da144e675b6
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092765"
---
# <a name="azure-policy-pattern-parameters"></a>Padrão do Azure Policy: parâmetros

Uma definição de política pode ser dinamizada para reduzir o número de definições de política necessárias usando [parâmetros](../concepts/definition-structure.md#parameters). O parâmetro é definido durante a atribuição da política. Os parâmetros têm um conjunto de propriedades predefinidas que descrevem o parâmetro e como ele é usado.

## <a name="sample-1-string-parameters"></a>Exemplo 1: Parâmetros de cadeia de caracteres

Essa definição de política usa dois parâmetros, **tagName** e **tagValue**, para definir o que a atribuição de política está procurando nos recursos. Esse formato permite que a definição de política seja usada para qualquer número de combinações de nome e valor de marca, mas mantém apenas uma única definição de política.

> [!NOTE]
> Para ver um exemplo de marca que usa o **modo** _Todos_ e funciona com um grupo de recursos, confira [Padrão: Marcas – Exemplo #1](./pattern-tags.md#sample-1-parameterize-tags).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Exemplo 1: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

Nesta parte da definição de política, o parâmetro **tagName** é definido como uma _sequência_ e uma descrição é fornecida para seu uso.

O parâmetro é então usado no bloco **policyRule.if** para tornar a política dinâmica. Aqui, ele é usado para definir o campo que é avaliado, que é uma marca com o valor de **tagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Exemplo 2: Parâmetros de matriz

Essa definição de política usa um único parâmetro, **listOfBandwidthinMbps**, para verificar se o recurso Express Route Circuit configurou a configuração de largura de banda para um dos valores aprovados. Se não corresponder, a criação ou atualização do recurso será [negada](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Exemplo 2: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

Nesta parte da definição de política, o parâmetro **listOfBandwidthinMbps** é definido como uma _matriz_ e uma descrição é fornecida para seu uso. Como uma _matriz_, ela tem vários valores para corresponder.

O parâmetro é então usado no bloco **policyRule.if**. Com um parâmetro de _matriz_, a [condição](../concepts/definition-structure.md#conditions) de _matriz_
 **in** ou **notIn** deve ser usada.
Aqui, é usado no alias **serviceProvider.bandwidthInMbps** como um dos valores definidos.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="sample-3-parameterized-effect"></a>Exemplo 3: Efeito parametrizado

Uma forma comum de tornar as definições de política reutilizáveis é parametrizar o próprio efeito. Este exemplo usa um só parâmetro, **effect**. A parametrização do efeito possibilita atribuir a mesma definição a escopos diferentes com efeitos diferentes.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json":::

### <a name="sample-3-explanation"></a>Exemplo 3: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="11-25":::

Nessa parte da definição da política, o parâmetro **effect** é definido como uma _cadeia de caracteres_. A definição da política define o valor padrão de uma atribuição como _audit_ e limita as outras opções a _disabled_ e _deny_.

O parâmetro, então, é usado no bloco **policyRule.if** para o _effect_.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="38-40" highlight="2":::

## <a name="next-steps"></a>Próximas etapas

- Revise outros [padrões e definições internas](./index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).