---
title: Funções de especificações técnicas do Azure
description: Descreve as funções para uso com atribuições e definições de especificações técnicas do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 0de3e0add804290cdfe27e2e97d8b1a0f240e0a6
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769295"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funções para uso com as especificações técnicas do Azure

Especificações técnicas do Azure fornece funções para criar uma definição de planta mais dinâmicos. Essas funções são para uso com definições de plantas e especificações técnicas de artefatos. Um artefato de modelo do Resource Manager suporta o uso completo de funções do Gerenciador de recursos além de obter um valor dinâmico por meio de um parâmetro de projeto.

Há suporte para as funções a seguir:

- [artifacts](#artifacts)
- [concat](#concat)
- [parâmetros](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>Artefatos

`artifacts(artifactName)`

Retorna que um objeto de propriedades preenchido com esse artefato do blueprint saídas.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- |
| artifactName |Sim |string |O nome de um artefato de blueprint. |

### <a name="return-value"></a>Valor de retorno

Um objeto de propriedades de saída. As propriedades de saída são dependentes do tipo de artefato de blueprint que está sendo referenciado. Todos os tipos de seguem o formato:

```json
{
  "output": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefato de atribuição de política

```json
{
    "output": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Artefato de modelo do Resource Manager

O **saída** propriedades do objeto retornado são definidas no modelo do Resource Manager e retornadas pela implantação.

#### <a name="role-assignment-artifact"></a>Artefato de atribuição de função

```json
{
    "output": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Exemplo

Um artefato de modelo do Resource Manager com a ID _myTemplateArtifact_ propriedade de saída que contém o exemplo a seguir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Alguns exemplos de recuperação de dados das _myTemplateArtifact_ exemplo são:

| Expression | Type | Value |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").output.myArray]` | Matriz | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").output.myArray[0]]` | Cadeia de caracteres | "first" |
|`[artifacts("myTemplateArtifact").output.myString]` | Cadeia de caracteres | "meu valor de cadeia de caracteres" |
|`[artifacts("myTemplateArtifact").output.myObject]` | Object | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").output.myObject.myProperty]` | Cadeia de caracteres | "meu valor" |
|`[artifacts("myTemplateArtifact").output.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combina vários valores de cadeia de caracteres e retorna o resultado concatenado.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- |
| string1 |Sim |string |O primeiro valor de concatenação. |
| argumentos adicionais |Não  |string |Valores adicionais em ordem sequencial para concatenação |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres de valores concatenados.

### <a name="remarks"></a>Comentários

A função de especificações técnicas do Azure difere da função do modelo do Azure Resource Manager em que ele só funciona com cadeias de caracteres.

### <a name="example"></a>Exemplo

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Retorna um valor de parâmetro de especificações técnicas. O nome do parâmetro especificado deve ser definido na definição de plano gráfico ou em artefatos do blueprint.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- |
| parameterName |Sim |string |O nome do parâmetro a retornar. |

### <a name="return-value"></a>Valor de retorno

O valor do parâmetro de artefato do blueprint ou projeto especificado.

### <a name="remarks"></a>Comentários

A função de especificações técnicas do Azure difere da função do modelo do Azure Resource Manager em que ele só funciona com parâmetros de plano gráfico.

### <a name="example"></a>Exemplo

Definir o parâmetro _principalIds_ na definição da especificação técnica:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Em seguida, use _principalIds_ como o argumento para `parameters()` em um artefato de blueprint:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Retorna um objeto que representa o grupo de recursos atual.

### <a name="return-value"></a>Valor de retorno

O objeto retornado está no seguinte formato:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Comentários

A função de especificações técnicas do Azure difere da função do modelo do Azure Resource Manager. O `resourceGroup()` função não pode ser usada em um artefato de nível de assinatura ou a definição de planta. Ele só pode ser usado em artefatos do blueprint que fazem parte de um artefato de grupo de recursos.

Um uso comum do `resourceGroup()` função é criar recursos no mesmo local que o artefato de grupo de recursos.

### <a name="example"></a>Exemplo

Para usar o local do grupo de recursos, definir uma definição de planta ou durante a atribuição, como o local para outro artefato, declarar um objeto de espaço reservado de grupo de recursos em sua definição de planta. Neste exemplo, _NetworkingPlaceholder_ é o nome do espaço reservado de grupo de recursos.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Em seguida, use o `resourceGroup()` função no contexto de um artefato de blueprint que tem como destino um objeto de espaço reservado de grupo de recursos. Neste exemplo, o artefato de modelo é implantado na _NetworkingPlaceholder_ grupo de recursos e fornece o parâmetro _resourceLocation_ dinamicamente preenchida com o  _NetworkingPlaceholder_ local do grupo de recursos para o modelo. O local do _NetworkingPlaceholder_ grupo de recursos ter sido definido estaticamente na definição de plano gráfico ou definido dinamicamente durante a atribuição. Em ambos os casos, o artefato de modelo é fornecido essas informações como um parâmetro e o utiliza para implantar os recursos para o local correto.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Retorna um objeto que representa o artefato de grupo de recursos especificado. Ao contrário de `resourceGroup()`, que requer o contexto do artefato, essa função é usada para obter as propriedades de um espaço reservado grupo de recursos específico quando não estiver no contexto do grupo de recursos.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- |
| placeholderName |Sim |string |O nome do espaço reservado do artefato de grupo de recursos para retornar. |

### <a name="return-value"></a>Valor de retorno

O objeto retornado está no seguinte formato:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Exemplo

Para usar o local do grupo de recursos, definir uma definição de planta ou durante a atribuição, como o local para outro artefato, declarar um objeto de espaço reservado de grupo de recursos em sua definição de planta. Neste exemplo, _NetworkingPlaceholder_ é o nome do espaço reservado de grupo de recursos.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Em seguida, use o `resourceGroups()` função a partir do contexto de qualquer artefato do blueprint para obter uma referência ao objeto de espaço reservado de grupo de recursos. Neste exemplo, o artefato de modelo é implantado fora do _NetworkingPlaceholder_ grupo de recursos e fornece o parâmetro _artifactLocation_ dinamicamente preenchida com o  _NetworkingPlaceholder_ local do grupo de recursos para o modelo. O local do _NetworkingPlaceholder_ grupo de recursos ter sido definido estaticamente na definição de plano gráfico ou definido dinamicamente durante a atribuição. Em ambos os casos, o artefato de modelo é fornecido essas informações como um parâmetro e o utiliza para implantar os recursos para o local correto.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>subscription

`subscription()`

Retorna detalhes sobre a assinatura para a atribuição de plano gráfico atual.

### <a name="return-value"></a>Valor de retorno

O objeto retornado está no seguinte formato:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Exemplo

Usar nome de exibição da assinatura e o `concat()` função para criar uma convenção de nomenclatura passada como parâmetro _resourceName_ para o artefato de modelo.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).