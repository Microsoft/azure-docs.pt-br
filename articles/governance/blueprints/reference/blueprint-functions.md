---
title: Funções dos Azure Blueprints
description: Descreve as funções disponíveis para uso com artefatos de blueprint em definições e atribuições do Azure Blueprints.
ms.date: 01/27/2021
ms.topic: reference
ms.openlocfilehash: 92cb906e87179073b7a69aa0bd4eab22c77087f9
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919267"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funções para uso com o Azure Blueprints

O Azure Blueprints fornece funções que tornam uma definição de blueprint mais dinâmica. Essas funções são para uso com definições de blueprint e artefatos de blueprint. Um artefato de modelo de Azure Resource Manager (modelo ARM) dá suporte ao uso completo de funções do Resource Manager, além de obter um valor dinâmico por meio de um parâmetro Blueprint.

As seguintes funções não são compatíveis:

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

Retorna um objeto das propriedades preenchidas com as saídas dos artefatos de blueprint.

> [!NOTE]
> A `artifacts()` função não pode ser usada de dentro de um modelo ARM. A função só pode ser usada no JSON da definição de blueprint ou no artefato JSON ao gerenciar o blueprint com o Azure PowerShell ou a API REST como parte de [Blueprints como código](https://github.com/Azure/azure-blueprints/blob/master/README.md).

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| artifactName |Sim |string |O nome de um artefato de blueprint. |

### <a name="return-value"></a>Valor retornado

Um objeto de propriedades de saída. As propriedades de **saídas** dependem do tipo de artefato de blueprint que está sendo referenciado. Todos os tipos seguem o formato:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefato de atribuição de política

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="arm-template-artifact"></a>Artefato do modelo ARM

As propriedades de **saídas** do objeto retornado são definidas dentro do modelo ARM e retornadas pela implantação.

#### <a name="role-assignment-artifact"></a>Artefato de atribuição de função

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Exemplo

Um artefato de modelo ARM com a ID _myTemplateArtifact_ que contém a seguinte propriedade de saída de exemplo:

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

Alguns exemplos de recuperação de dados do exemplo _myTemplateArtifact_ são:

| Expression | Type | Valor |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["primeiro", "segundo"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "primeiro" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "meu valor de cadeia de caracteres" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Objeto | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "meu valor" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combina vários valores de cadeia de caracteres e retorna o resultado concatenado.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| string1 |Sim |string |O primeiro valor de concatenação. |
| argumentos adicionais |Não |string |Valores adicionais em ordem sequencial para concatenação |

### <a name="return-value"></a>Valor retornado

Uma cadeia de caracteres de valores concatenados.

### <a name="remarks"></a>Comentários

A função Azure Blueprint difere da função de modelo ARM, pois só funciona com cadeias de caracteres.

### <a name="example"></a>Exemplo

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parâmetros

`parameters(parameterName)`

Retorna um valor de parâmetro de blueprint. O nome do parâmetro especificado deve ser definido na definição do blueprint ou em artefatos de blueprint.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| parameterName |Sim |string |O nome do parâmetro a retornar. |

### <a name="return-value"></a>Valor retornado

O valor do parâmetro de artefato de blueprint ou do blueprint especificado.

### <a name="remarks"></a>Comentários

A função Azure Blueprint difere da função de modelo ARM, pois só funciona com parâmetros de plano gráfico.

### <a name="example"></a>Exemplo

Defina o parâmetro _principalIds_ na definição do blueprint:

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
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the Azure role assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Em seguida, use o _principalIds_ como o argumento para `parameters()` em um artefato de blueprint:

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

### <a name="return-value"></a>Valor retornado

O objeto retornado está no seguinte formato:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Comentários

A função Azure Blueprint difere da função de modelo ARM. A função `resourceGroup()` não pode ser usada em um artefato de nível de assinatura ou na definição do blueprint. Ela só pode ser usada em artefatos de blueprint que fazem parte de um artefato de grupo de recursos.

Um uso comum da função `resourceGroup()` é criar recursos na mesma localização que o artefato do grupo de recursos.

### <a name="example"></a>Exemplo

Para usar a localização do grupo de recursos, configure na definição do blueprint ou durante a atribuição, como a localização de outro artefato, declare um objeto de espaço reservado do grupo de recursos na definição do blueprint. Neste exemplo, _NetworkingPlaceholder_ é o nome do espaço reservado do grupo de recursos.

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

Em seguida, use a função `resourceGroup()` no contexto de um artefato de blueprint destinado a um objeto de espaço reservado do grupo de recursos. Neste exemplo, o artefato do modelo é implantado no grupo de recursos _NetworkingPlaceholder_ e fornece o parâmetro _resourceLocation_ preenchido dinamicamente com a localização do grupo de recursos _NetworkingPlaceholder_ para o modelo. A localização do grupo de recursos do _NetworkingPlaceholder_ poderia ter sido estaticamente definido na definição do blueprint ou definido dinamicamente durante a atribuição. Em ambos os casos, o artefato do modelo recebe essas informações como um parâmetro e as usa para implantar os recursos na localização correta.

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

Retorna um objeto que representa o artefato do grupo de recursos especificado. Ao contrário de `resourceGroup()`, que requer o contexto do artefato, essa função é usada para obter as propriedades de um espaço reservado de grupo de recursos específico quando não está no contexto desse grupo de recursos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| placeholderName |Sim |string |O nome do espaço reservado do artefato do grupo de recursos a ser retornado. |

### <a name="return-value"></a>Valor retornado

O objeto retornado está no seguinte formato:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Exemplo

Para usar a localização do grupo de recursos, configure na definição do blueprint ou durante a atribuição, como a localização de outro artefato, declare um objeto de espaço reservado do grupo de recursos na definição do blueprint. Neste exemplo, _NetworkingPlaceholder_ é o nome do espaço reservado do grupo de recursos.

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

Em seguida, use a função `resourceGroups()` do contexto de qualquer artefato de blueprint para obter uma referência ao objeto do espaço reservado do grupo de recursos. Neste exemplo, o artefato do modelo é implantado fora do grupo de recursos _NetworkingPlaceholder_ e fornece o parâmetro _artifactLocation_ preenchido dinamicamente com a localização do grupo de recursos _NetworkingPlaceholder_ para o modelo. A localização do grupo de recursos do _NetworkingPlaceholder_ poderia ter sido estaticamente definido na definição do blueprint ou definido dinamicamente durante a atribuição. Em ambos os casos, o artefato do modelo recebe essas informações como um parâmetro e as usa para implantar os recursos na localização correta.

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

Retorna detalhes sobre a assinatura da atribuição de blueprint atual.

### <a name="return-value"></a>Valor retornado

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

Use o nome de exibição da assinatura e a função `concat()` para criar uma convenção de nomenclatura passada como parâmetro _resourceName_ para o artefato do modelo.

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

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).