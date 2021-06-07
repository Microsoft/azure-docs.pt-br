---
title: 'Padrão: propriedades de campo em uma definição de política'
description: Esse padrão do Azure Policy fornece um exemplo de como usar propriedades de campo em uma definição de política.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: b0b998084c9acf664d4e3e8dd9483af1ee986677
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093326"
---
# <a name="azure-policy-pattern-field-properties"></a>Padrão do Azure Policy: propriedades de campo

O operador de [campo](../concepts/definition-structure.md#fields) avalia a propriedade especificada ou [alias](../concepts/definition-structure.md#aliases) para um valor fornecido para uma determinada [condição](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Definição de política de amostra

Essa definição de política permite definir regiões permitidas que atendem aos requisitos de localização geográfica da sua organização. Os recursos permitidos são definidos no parâmetro **listOfAllowedLocations** (_matriz_). Os recursos que correspondem à definição são [negados](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

O operador **campo** é usado três vezes dentro do [operador lógico](../concepts/definition-structure.md#logical-operators) **allOf**.

- O primeiro uso avalia a propriedade `location` com a condição de **notIn** para o parâmetro **listOfAllowedLocations**. **notIn** funciona como se espera de uma _matriz_ e o parâmetro é uma _matriz_. Se o `location` do recurso criado ou atualizado não estiver na lista aprovada, esse elemento será avaliado como true.
- O segundo uso também avalia a propriedade `location`, mas usa a condição de **notEquals** para ver se o recurso é _global_. Se o `location` do recurso criado ou atualizado não for _global_, esse elemento será avaliado como true.
- O último uso avalia a propriedade `type` e usa a condição **notEquals** para validar o tipo de recurso que não é _Microsoft.AzureActiveDirectory/b2cDirectories_. Se não for, esse elemento será avaliado como true.

Se todas as três instruções de condição no operador lógico **allOf** forem avaliadas como true, a criação ou atualização de recurso será bloqueada pela Política do Azure.

## <a name="next-steps"></a>Próximas etapas

- Revise outros [padrões e definições internas](./index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).