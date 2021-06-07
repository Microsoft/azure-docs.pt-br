---
title: 'Padrão: Usando marcas em uma definição de política'
description: Esse padrão do Azure Policy fornece exemplos de como adicionar marcas parametrizadas ou herdar marcas de um grupo de recursos em uma definição de política.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: c748eb9b8ea795f9725082ec0aa0b8065ada8c65
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093360"
---
# <a name="azure-policy-pattern-tags"></a>Padrão do Azure Policy: marcas

As [marcas](../../..//azure-resource-manager/management/tag-resources.md) são uma parte importante do gerenciamento, da organização e do controle de seus recursos do Azure. O Azure Policy possibilita configurar marcas em seus recursos novos e existentes em escala com o efeito [modificar](../concepts/effects.md#modify) e as [tarefas de correção](../how-to/remediate-resources.md).

## <a name="sample-1-parameterize-tags"></a>Exemplo 1: Parametrizar marcas

Essa definição de política usa dois parâmetros, **tagName** e **tagValue**, para definir o que a atribuição de política está procurando em grupos de recursos. Esse formato permite que a definição de política seja usada para qualquer número de combinações de nome e valor de marca, mas mantém apenas uma única definição de política.

> [!NOTE]
> Embora esse padrão de definição de política seja semelhante àquele em [Padrão: Parâmetros – Exemplo #1](./pattern-parameters.md#sample-1-string-parameters), este exemplo usa o **modo** _Todos_ e tem como destino os grupos de recursos.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>Exemplo 1: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

Neste exemplo, **modo** está definido como _Todos_, pois ele se destina a um grupo de recursos. Na maioria dos casos, o **modo** deve ser definido como _Indexado_ ao trabalhar com marcas. Para obter mais informações, confira [modos](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

Nessa parte da definição de política, `concat` combina o parâmetro **tagName** com parâmetros e o formato `tags['name']` para instruir o **campo** a avaliar essa marca para o parâmetro **tagValue**.
Como **notEquals** é usar, se **tags\[tagName\]** não for igual **tagValue**, o efeito **modificar** será disparado.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

Aqui, o mesmo formato para usar os valores de marca com parâmetros é usado pela operação **addOrReplace** para criar ou atualizar a marca para o valor desejado no grupo de recursos avaliado.

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>Exemplo 2: Herdar o valor da marca do grupo de recursos

Essa definição de política usa o parâmetro **tagName** para determinar qual valor da marca deve ser herdado do grupo de recursos pai.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>Exemplo 2: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

Neste exemplo, o **modo** é definido como _Indexado_, pois não tem como destino um grupo de recursos ou uma assinatura, embora obtenha o valor de um grupo de recursos. Para obter mais informações, confira [modos](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

A **policyRule.if** usa `concat`, assim como o [Exemplo #1](#sample-1-parameterize-tags), para avaliar o valor de **tagName**, mas usa a função `resourceGroup()` para compará-lo com o valor da mesma marca no grupo de recursos pai. A segunda cláusula aqui verifica se a marca no grupo de recursos tem um valor e não é nula.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

Aqui, o valor que está sendo atribuído à marca **tagName** no recurso também usa a função `resourceGroup()` para obter o valor do grupo de recursos pai. Dessa forma, você pode herdar marcas de grupos de recursos pai. Se você já criou o recurso, mas não adicionou a marca, essa mesma definição de política e uma [tarefa de correção](../how-to/remediate-resources.md) poderão atualizar os recursos existentes.

## <a name="next-steps"></a>Próximas etapas

- Revise outros [padrões e definições internas](./index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).