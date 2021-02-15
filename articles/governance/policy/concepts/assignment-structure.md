---
title: Detalhes da estrutura de atribuição de política
description: Descreve a definição de atribuição de política usada por Azure Policy para relacionar definições de política e parâmetros a recursos para avaliação.
ms.date: 01/29/2021
ms.topic: conceptual
ms.openlocfilehash: 12acbe368c9ccd6fa5654d3394e0fecb286984bf
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219559"
---
# <a name="azure-policy-assignment-structure"></a>Estrutura de atribuição do Azure Policy

As atribuições de política são usadas pelo Azure Policy para definir quais recursos são atribuídos a quais políticas ou iniciativas. A atribuição de política pode determinar os valores dos parâmetros para esse grupo de recursos no momento da atribuição, possibilitando a reutilização de definições de política que abordam as mesmas propriedades de recurso com necessidades diferentes de conformidade.

Você usa JSON para criar uma atribuição de política. A atribuição de política contém elementos para:

- nome de exibição
- descrição
- metadata
- modo de imposição
- escopos excluídos
- definição de política
- mensagens de não conformidade
- parâmetros

Por exemplo, o JSON a seguir mostra uma atribuição de política no modo _DoNotEnforce_ com parâmetros dinâmicos:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Todas as amostras do Azure Policy estão em [Amostras do Azure Policy](../samples/index.md).

## <a name="display-name-and-description"></a>Nome de exibição e descrição

Use **DisplayName** e **Description** para identificar a atribuição de política e fornecer contexto para seu uso com o conjunto específico de recursos. **displayName** tem um comprimento máximo de _128_ caracteres e **description** tem um comprimento máximo de _512_ caracteres.

## <a name="enforcement-mode"></a>Modo de imposição

A **Propriedade** impolicymode fornece aos clientes a capacidade de testar o resultado de uma política em recursos existentes sem iniciar o efeito da política ou disparar entradas no [log de atividades do Azure](../../../azure-monitor/platform/platform-logs-overview.md). Esse cenário é conhecido como "What If" e alinha-se às práticas de implantação seguras. **imposiçãomode** é diferente do efeito [desabilitado](./effects.md#disabled) , pois esse efeito impede que a avaliação de recursos aconteça.

Essa propriedade tem os seguintes valores:

|Modo |Valor JSON |Tipo |Corrigir manualmente |Entrada do log de atividades |Descrição |
|-|-|-|-|-|-|
|Habilitada |Padrão |string |Sim |Sim |O efeito de política é imposto durante a criação ou atualização de recursos. |
|Desabilitado |DoNotEnforce |string |Sim |Não | O efeito de política não é imposto durante a criação ou atualização de recursos. |

Se **imposiçãomode** não for especificado em uma definição de política ou iniciativa, o valor _padrão_ será usado. [As tarefas de correção](../how-to/remediate-resources.md) podem ser iniciadas para políticas de [deployIfNotExists](./effects.md#deployifnotexists) , **mesmo quando** é definido como _DoNotEnforce_.

## <a name="excluded-scopes"></a>Escopos excluídos

O **escopo** da atribuição inclui todos os contêineres de recursos filho e recursos filho. Se um contêiner de recursos filho ou um recurso filho não deve ter a definição aplicada, cada um pode ser _excluído_ da avaliação definindo não **escopos**. Essa propriedade é uma matriz para habilitar a exclusão de um ou mais contêineres de recursos ou recursos da avaliação. os não **escopos** podem ser adicionados ou atualizados após a criação da atribuição inicial.

> [!NOTE]
> Um recurso _excluído_ é diferente de um recurso _isento_ . Para obter mais informações, consulte [entender o escopo em Azure Policy](./scope.md).

## <a name="policy-definition-id"></a>ID de definição de política

Este campo deve ser o nome do caminho completo de uma definição de política ou uma definição de iniciativa.
`policyDefinitionId` é uma cadeia de caracteres e não uma matriz. É recomendável que, em vez disso, várias políticas sejam atribuídas juntas, para usar uma [iniciativa](./initiative-definition-structure.md) .

## <a name="non-compliance-messages"></a>Mensagens de não conformidade

Para definir uma mensagem personalizada que descreve por que um recurso não está em conformidade com a definição de política ou iniciativa, defina `nonComplianceMessages` na definição de atribuição. Esse nó é uma matriz de `message` entradas. Essa mensagem personalizada é além da mensagem de erro padrão para não conformidade e é opcional.

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Se a atribuição for para uma iniciativa, diferentes mensagens poderão ser configuradas para cada definição de política na iniciativa. As mensagens usam o `policyDefinitionReferenceId` valor configurado na definição da iniciativa. Para obter detalhes, consulte [Propriedades de definições de propriedade](./initiative-definition-structure.md#policy-definition-properties).

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>Parâmetros

Esse segmento da atribuição de política fornece os valores para os parâmetros definidos na definição de [política ou definição de iniciativa](./definition-structure.md#parameters). Esse design torna possível reutilizar uma definição de política ou iniciativa com recursos diferentes, mas verificar valores comerciais ou resultados diferentes.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

Neste exemplo, os parâmetros definidos anteriormente na definição de política são `prefix` e `suffix` . Essa atribuição de política específica define `prefix` como **depta** e `suffix` to **-LC**. A mesma definição de política é reutilizável com um conjunto diferente de parâmetros para um departamento diferente, reduzindo a duplicação e a complexidade das definições de política, oferecendo flexibilidade.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [estrutura da definição de política](./definition-structure.md).
- Entenda como [criar políticas de forma programática](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).