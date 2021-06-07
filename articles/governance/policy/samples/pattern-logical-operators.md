---
title: 'Padrão: Operadores lógicos em uma definição de política'
description: Esse padrão de Política do Azure fornece exemplos de como usar os operadores lógicos em uma definição de política.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: feb9e50b0c73c19027b747cf0f95fa1cb6fbd47c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093343"
---
# <a name="azure-policy-pattern-logical-operators"></a>Padrão do Azure Policy: operadores lógicos

Uma definição de política pode conter várias instruções condicionais. Talvez você precise que cada instrução seja verdadeira ou que apenas algumas delas sejam verdadeiras. Para dar suporte a essas necessidades, a linguagem tem [operadores lógicos](../concepts/definition-structure.md#logical-operators) para **not**, **allOf** e **anyOf**. Eles são opcionais e podem ser aninhados para criar cenários complexos.

## <a name="sample-1-one-logical-operator"></a>Exemplo 1: Um operador lógico

Essa definição de política avalia as contas do [Azure Cosmos DB](../../../cosmos-db/introduction.md) para verificar se failovers automáticos e vários locais de gravação estão configurados. Quando não estão, a [auditoria](../concepts/effects.md#audit) é acionada e cria uma entrada de log quando o recurso não compatível é criado ou atualizado.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Exemplo 1: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

O bloco **policyRule.if** usa um único **allOf** para garantir que todas as três condições sejam verdadeiras.
Somente quando todas essas condições são avaliadas como verdadeiras o efeito da **auditoria** é acionado.

## <a name="sample-2-multiple-logical-operators"></a>Exemplo 2: Vários operadores lógicos

Esta definição de política avalia recursos para um padrão de nomenclatura. Se um recurso não corresponder, será [negado](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Exemplo 2: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Esse bloco **policyRule.if** também inclui um único **allOf**, mas cada condição é agrupada com o operador lógico **not**. A condicional dentro do operador lógico **not** avalia primeiro e depois avalia o **not** para determinar se a cláusula inteira é verdadeira ou falsa. Se os dois operadores lógicos **not** forem avaliados como true, o efeito da política será acionado.

## <a name="sample-3-combining-logical-operators"></a>Exemplo 3: combinar operadores lógicos

Essa definição de política avalia as contas [Spring no Azure](/azure/developer/java/spring-framework) para ver se o rastreamento não está habilitado ou se ele não está em um estado bem-sucedido.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json":::

### <a name="sample-3-explanation"></a>Exemplo 3: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json" range="6-28" highlight="3,8":::

Esse bloco **policyRule.if** inclui os operadores lógicos **allOf** e **anyOf**. O operador lógico **anyOf** é avaliado como true, desde que uma condição incluída seja true. Como _type_ está no núcleo de **allOf**, ele sempre deve ser avaliado como true. Se _type_ e uma das condições em **anyOf** forem true, o efeito de política será disparado.

## <a name="next-steps"></a>Próximas etapas

- Revise outros [padrões e definições internas](./index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).