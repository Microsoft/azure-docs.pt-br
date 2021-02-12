---
title: Entender a ordem da sequência de implantação
description: Saiba mais sobre a ordem padrão em que os artefatos de Blueprint são implantados durante uma atribuição de Blueprint e como personalizar a ordem de implantação.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: ada7e7771e44e6f43c8546e8307fd5d2b218c009
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918221"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Entenda a sequência de implantação nos Blueprints do Azure

Os planos gráficos do Azure usam uma **ordem de sequenciamento** para determinar a ordem de criação de recursos ao processar a atribuição de uma definição de plano gráfico. Este artigo o guiará pelos seguintes conceitos:

- A ordem de sequenciamento padrão usada
- Como personalizar o pedido
- Como o pedido personalizado é processado

Existem variáveis nos exemplos JSON que você precisa substituir por seus próprios valores:

- `{YourMG}`: substitua pelo nome do seu grupo de gerenciamento

## <a name="default-sequencing-order"></a>Ordem de sequenciamento padrão

Se a definição de Blueprint não contiver nenhuma diretiva para a ordem de implantação de artefatos ou se a diretiva for nula, a seguinte ordem será usada:

- Designação de função de **nível de assinatura** artefatos classificados por nome de artefato
- Designação de política do **nível de assinatura** artefatos classificados pelo nome do artefato
- Artefatos do **modelo de Azure Resource Manager** de nível de assinatura (modelos ARM), classificadas por nome de artefato
- **Artefatos do grupo de recursos** (incluindo artefatos derivados) classificados por nome de espaço reservado

Em cada artefato do **grupo de recursos**, a seguinte ordem de sequência é usada para artefatos a serem criados dentro desse grupo de recursos:

- Filho de grupo de recursos **atribuição de função** artefatos classificados por nome de artefato
- Filho de grupo de recursos **atribuição de política** artefatos classificados por nome de artefato
- Artefatos do grupo de recursos **Azure Resource Manager modelo** (modelos do ARM), classificadas por nome do artefato

> [!NOTE]
> O uso de [artefatos ()](../reference/blueprint-functions.md#artifacts) cria uma dependência implícita no artefato que está sendo referenciado.

## <a name="customizing-the-sequencing-order"></a>Personalizando o pedido de sequenciamento

Ao compor grandes definições de Blueprint, pode ser necessário que os recursos sejam criados em uma ordem específica. O padrão de uso mais comum desse cenário é quando uma definição de Blueprint inclui vários modelos de ARM. Os planos gráficos do Azure lidam com esse padrão, permitindo que a ordem de sequenciamento seja definida.

A ordenação é realizada definindo uma propriedade `dependsOn` no JSON. A definição do Blueprint, para grupos de recursos e objetos de artefato dão suporte a essa propriedade. `dependsOn` é uma matriz de cadeia de caracteres de nomes de artefatos que o artefato específico precisa ser criado antes de ser criado.

> [!NOTE]
> Ao criar objetos BluePrints, cada recurso de artefato obtém seu nome do nome de arquivo, se estiver usando o [PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact)ou o ponto de extremidade de URL, se estiver usando a [API REST](/rest/api/blueprints/artifacts/createorupdate). as referências de _resourcegroup_ em artefatos devem corresponder àquelas definidas na definição do Blueprint.

### <a name="example---ordered-resource-group"></a>Exemplo-grupo de recursos ordenados

Este exemplo de definição de Blueprint tem um grupo de recursos que definiu uma ordem de sequenciamento personalizada declarando um valor para `dependsOn` , junto com um grupo de recursos padrão. Nesse caso, o artefato denominado **assignPolicyTags** será processado antes do grupo de recursos **ordered-rg**.
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
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Exemplo - artefato com ordem personalizada

Este exemplo é um artefato de política que depende de um modelo de ARM. Por padrão, a ordenação de um artefato de política seria criado antes do modelo ARM. Essa ordenação permite que o artefato da política aguarde até que o modelo ARM seja criado.

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
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Exemplo-artefato de modelo de nível de assinatura dependendo de um grupo de recursos

Este exemplo é para um modelo de ARM implantado no nível de assinatura para depender de um grupo de recursos. Na ordenação padrão, os artefatos de nível de assinatura seriam criados antes de qualquer grupo de recursos e artefatos filho nesses grupos de recursos. O grupo de recursos é definido na definição do Blueprint da seguinte maneira:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

O artefato do modelo de nível de assinatura, dependendo do grupo de recursos **Wait-for-me,** é definido da seguinte maneira:

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
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>Processando a sequência customizada

Durante o processo de criação, uma classificação topológica é usada para criar o gráfico de dependência dos artefatos de blueprints. A verificação assegura que cada nível de dependência entre grupos de recursos e artefatos seja suportado.

Se uma dependência de artefato for declarada que não alteraria a ordem padrão, nenhuma alteração será feita.
Um exemplo é um grupo de recursos que depende de uma política no nível da assinatura. Outro exemplo é uma atribuição de política filho 'standard-rg' do grupo de recursos que depende da atribuição de função filho 'standard-rg' do grupo de recursos. Em ambos os casos, o `dependsOn` não teria alterado a ordem de sequenciamento padrão e nenhuma alteração seria feita.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [ciclo de vida do blueprint](./lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](./parameters.md).
- Saiba como usar o [bloqueio de recurso de blueprint](./resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).