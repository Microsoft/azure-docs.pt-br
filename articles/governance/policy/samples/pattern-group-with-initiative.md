---
title: 'Padrão: Definições de política de grupo com iniciativas'
description: Este padrão do Azure Policy fornece um exemplo de como agrupar definições de política em uma iniciativa.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: aa09cafe636a4665dba6a2e746c13b95ff304895
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072910"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Padrão do Azure Policy: definições de política de grupo

Uma iniciativa é um grupo de definições de política. Ao agrupar definições de política relacionadas em um único objeto, você pode criar uma única atribuição que teria sido várias atribuições.

## <a name="sample-initiative-definition"></a>Definição de iniciativa de exemplo

Essa iniciativa implanta duas definições de política, cada uma com os parâmetros **tagName** e **tagValue**. A própria iniciativa tem dois parâmetros: **costCenterValue** e **productNamevalue**.
Esses parâmetros de iniciativa são fornecidos para cada uma das definições de política agrupadas. Esse design maximiza a reutilização das definições de política existentes, limitando o número de atribuições criadas para implementá-las conforme necessário.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Explicação

#### <a name="initiative-parameters"></a>Parâmetros de iniciativa

Uma iniciativa pode definir seus próprios parâmetros que são passados para as definições de política agrupadas.
Neste exemplo, **costCenterValue** e **productNamevalue** são definidos como parâmetros de iniciativa. Os valores são fornecidos quando a iniciativa é atribuída.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Inclui definições de política

Cada definição de política incluída deve fornecer o **policyDefinitionId** e uma matriz de **parameters** se a definição de política aceitar parâmetros. No trecho de código abaixo, a definição de política incluída usa dois parâmetros: **tagName** e **tagValue**. **tagName** é definido com um literal, mas **tagValue** usa o parâmetro **costCenterValue** definido pela iniciativa. Essa passagem de valores melhora a reutilização.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Próximas etapas

- Revise outros [padrões e definições internas](./index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).