---
title: Entender a ordem da sequência de implantação
description: Saiba mais sobre o ciclo de vida que uma definição de planta percorre e os detalhes sobre cada fase.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: b05a7ce260e8cc1da4ac8a0c186694ae097a3b1e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766344"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Entenda a sequência de implantação nos Blueprints do Azure

O Azure usa plantas uma **pedido de sequenciamento** para determinar a ordem de criação de recursos ao processar a atribuição de uma definição de planta. Este artigo o guiará pelos seguintes conceitos:

- A ordem de sequenciamento padrão usada
- Como personalizar o pedido
- Como o pedido personalizado é processado

Existem variáveis nos exemplos JSON que você precisa substituir por seus próprios valores:

- `{YourMG}` - Substitua pelo nome do seu grupo de gerenciamento

## <a name="default-sequencing-order"></a>Ordem de sequenciamento padrão

Se a definição de planta não contém nenhuma diretiva para a ordem para implantar artefatos ou a diretiva for null, a ordem a seguir é usada:

- Designação de função de **nível de assinatura** artefatos classificados por nome de artefato
- Designação de política do **nível de assinatura** artefatos classificados pelo nome do artefato
- Nível de assinatura **Artefatos do modelo** do Azure Resource Manager classificados por nome de artefato
- **Artefatos do grupo de recursos** (incluindo artefatos derivados) classificados por nome de espaço reservado

Em cada artefato do **grupo de recursos**, a seguinte ordem de sequência é usada para artefatos a serem criados dentro desse grupo de recursos:

- Filho de grupo de recursos **atribuição de função** artefatos classificados por nome de artefato
- Filho de grupo de recursos **atribuição de política** artefatos classificados por nome de artefato
- Filho do grupo de recursos **Artefatos do modelo do Azure Resource Manager** classificados por nome de artefato

> [!NOTE]
> Uso de [artifacts()](../reference/blueprint-functions.md#artifacts) cria uma dependência implícita no artefato está sendo referenciado.

## <a name="customizing-the-sequencing-order"></a>Personalizando o pedido de sequenciamento

Ao redigir definições de plantas grande, pode ser necessário para os recursos a serem criados em uma ordem específica. O padrão de uso mais comum desse cenário é quando uma definição de planta inclui vários modelos do Azure Resource Manager. Blueprints lida com esse padrão permitindo que a ordem de sequenciamento seja definida.

A ordenação é realizada definindo uma propriedade `dependsOn` no JSON. A definição de planta, para grupos de recursos e objetos de artefato suporte a essa propriedade. `dependsOn` é uma matriz de cadeia de caracteres de nomes de artefatos que o artefato específico precisa ser criado antes de ser criado.

### <a name="example---ordered-resource-group"></a>Exemplo - ordenados de grupo de recursos

Essa definição de plano gráfico de exemplo tem um grupo de recursos que tenha definido uma ordem de sequenciamento personalizada, declarando um valor para `dependsOn`, juntamente com um grupo de recursos padrão. Nesse caso, o artefato denominado **assignPolicyTags** será processado antes do grupo de recursos **ordered-rg**.
**standard-rg** será processado de acordo com a ordem de sequenciamento padrão.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>Exemplo - artefato com ordem personalizada

Este exemplo é um artefato de política que depende de um modelo do Azure Resource Manager. Por padrão, um artefato de política seria criado antes do modelo do Azure Resource Manager. Essa ordenação permite que o artefato de política aguarde o modelo do Azure Resource Manager ser criado.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Exemplo - artefato de modelo de nível de assinatura, dependendo de um grupo de recursos

Este exemplo é para um modelo do Resource Manager implantado no nível da assinatura depende de um grupo de recursos. Ordenação padrão, os artefatos em nível de assinatura seriam criados antes de quaisquer grupos de recursos e os artefatos de filho desses grupos de recursos. O grupo de recursos é definido na definição de plano gráfico como este:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

O artefato de modelo de nível de assinatura, dependendo de **wait-para-me** grupo de recursos é definido como este:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/subtemplateWaitForRG",
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "name": "subtemplateWaitForRG"
}
```

## <a name="processing-the-customized-sequence"></a>Processando a sequência customizada

Durante o processo de criação, uma classificação topológica é usada para criar o gráfico de dependência dos artefatos de blueprints. A verificação assegura que cada nível de dependência entre grupos de recursos e artefatos seja suportado.

Se uma dependência de artefato for declarada que não alteraria a ordem padrão, nenhuma alteração será feita. Um exemplo é um grupo de recursos que depende de uma política no nível da assinatura. Outro exemplo é uma atribuição de política filho 'standard-rg' do grupo de recursos que depende da atribuição de função filho 'standard-rg' do grupo de recursos. Em ambos os casos, o `dependsOn` não teria alterado a ordem de sequenciamento padrão e nenhuma alteração seria feita.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [ciclo de vida do blueprint](lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](parameters.md).
- Saiba como usar o [bloqueio de recurso de blueprint](resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).