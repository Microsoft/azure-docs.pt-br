---
title: Criar políticas para propriedades de matriz em recursos
description: Aprenda a trabalhar com parâmetros de matriz e expressões de linguagem de matriz, avaliar o alias [*] e acrescentar elementos com regras de definição do Azure Policy.
ms.date: 10/22/2020
ms.topic: how-to
ms.openlocfilehash: 650b2ec6bc1bbd12cd10abb1917ef5ea2d6029e9
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220738"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Criar políticas para propriedades de matriz em recursos do Azure

As propriedades do Azure Resource Manager são normalmente definidas como cadeias de caracteres e boolianos. Quando existe uma relação um-para-muitos, as propriedades complexas são definidas como matrizes. No Azure Policy, as matrizes são usadas de várias maneiras diferentes:

- O tipo de um [parâmetro de definição](../concepts/definition-structure.md#parameters), para fornecer várias opções
- Parte de uma [regra de política](../concepts/definition-structure.md#policy-rule) usando as condições **in** ou **notIn**
- Parte de uma regra de política que conta quantos membros da matriz atendem a uma condição
- Nos efeitos [acrescentar](../concepts/effects.md#append) e [Modificar](../concepts/effects.md#modify) para atualizar uma matriz existente

Este artigo aborda cada uso pelo Azure Policy e fornece várias definições de exemplo.

## <a name="parameter-arrays"></a>Matrizes de parâmetros

### <a name="define-a-parameter-array"></a>Definir uma matriz de parâmetros

A definição de um parâmetro como uma matriz permite a flexibilidade da política quando mais de um valor é necessário.
Essa definição de política permite qualquer local único para o parâmetro **allowedLocations** e usa _eastus2_ como padrão:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Como **type** era _string_, apenas um valor pode ser definido ao atribuir a política. Se essa política for atribuída, os recursos no escopo só serão permitidos em uma região do Azure. A maioria das definições de política precisa permitir uma lista de opções aprovadas, como permitir _eastus2_, _eastus_ e _westus2_.

Para criar a definição de política para permitir várias opções, use o **type** _array_. A mesma política pode ser reescrita da seguinte maneira:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Quando uma definição de política é salva, a propriedade **type** em um parâmetro não pode ser alterada.

Essa nova definição de parâmetro usa mais de um valor durante a atribuição de política. Com a propriedade de matriz **allowedValues** definida, os valores disponíveis durante a atribuição são limitados ainda mais à lista predefinida de opções. O uso de **allowedValues** é opcional.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passar valores para uma matriz de parâmetros durante a atribuição

Ao atribuir a política por meio do portal do Azure, um parâmetro de **type** _array_ é exibido como uma caixa de texto. A dica diz "Use ; para separar valores. (por exemplo, Londres; Nova York)". Para passar os valores de local permitidos de _eastus2_, _eastus_ e _westus2_ para o parâmetro, use a seguinte cadeia de caracteres:

`eastus2;eastus;westus2`

O formato do valor do parâmetro é diferente ao usar CLI do Azure, o Azure PowerShell ou a API REST. Os valores são passados por meio de uma cadeia de caracteres JSON que também inclui o nome do parâmetro.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Para usar essa cadeia de caracteres com cada SDK, use os seguintes comandos:

- CLI do Azure: Comando [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) com o parâmetro **params**
- Azure PowerShell: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) com o parâmetro **PolicyParameter**
- API REST: Na operação [create](/rest/api/resources/policyassignments/create) _PUT_ como parte do corpo da solicitação, como o valor da propriedade **Properties.Parameters**

## <a name="using-arrays-in-conditions"></a>Usando matrizes em condições

### <a name="in-and-notin"></a>`In` e `notIn`

As `in` `notIn` condições e só funcionam com valores de matriz. Eles verificam a existência de um valor em uma matriz. A matriz pode ser uma matriz JSON literal ou uma referência a um parâmetro de matriz. Por exemplo:

```json
{
      "field": "tags.environment",
      "in": [ "dev", "test" ]
}
```

```json
{
      "field": "location",
      "notIn": "[parameters('allowedLocations')]"
}
```

### <a name="value-count"></a>Contagem de valor

A expressão de [contagem de valor](../concepts/definition-structure.md#value-count) conta quantos membros de matriz atendem a uma condição. Ele fornece uma maneira de avaliar a mesma condição várias vezes, usando valores diferentes em cada iteração. Por exemplo, a condição a seguir verifica se o nome do recurso corresponde a qualquer padrão de uma matriz de padrões:

```json
{
    "count": {
        "value": [ "test*", "dev*", "prod*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Para avaliar a expressão, Azure Policy avalia a `where` condição 3 vezes, uma vez para cada membro de `[ "test*", "dev*", "prod*" ]` , contando quantas vezes ela foi avaliada `true` . Em cada iteração, o valor do membro da matriz atual é emparelhado com o `pattern` nome do índice definido por `count.name` . Esse valor pode então ser referenciado dentro da `where` condição chamando uma função de modelo especial: `current('pattern')` .

| Iteração | `current('pattern')` valor retornado |
|:---|:---|
| 1 | `"test*"` |
| 2 | `"dev*"` |
| 3 | `"prod*"` |

A condição será verdadeira somente se a contagem resultante for maior que 0.

Para tornar a condição acima de mais genérica, use a referência de parâmetro em vez de uma matriz literal:

 ```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Quando a expressão de **contagem de valor** não está sob nenhuma outra expressão de **contagem** , `count.name` é opcional e a `current()` função pode ser usada sem argumentos:

```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

A **contagem de valor** também oferece suporte a matrizes de objetos complexos, permitindo condições mais complexas. Por exemplo, a condição a seguir define um valor de marca desejado para cada padrão de nome e verifica se o nome do recurso corresponde ao padrão, mas não tem o valor de marca necessário:

```json
{
    "count": {
        "value": [
            { "pattern": "test*", "envTag": "dev" },
            { "pattern": "dev*", "envTag": "dev" },
            { "pattern": "prod*", "envTag": "prod" },
        ],
        "name": "namePatternRequiredTag",
        "where": {
            "allOf": [
                {
                    "field": "name",
                    "like": "[current('namePatternRequiredTag').pattern]"
                },
                {
                    "field": "tags.env",
                    "notEquals": "[current('namePatternRequiredTag').envTag]"
                }
            ]
        }
    },
    "greater": 0
}
```

Para obter exemplos úteis, consulte [exemplos de contagem de valor](../concepts/definition-structure.md#value-count-examples).

## <a name="referencing-array-resource-properties"></a>Referenciando Propriedades de recurso de matriz

Muitos casos de uso exigem o trabalho com propriedades de matriz no recurso avaliado. Alguns cenários exigem a referência a uma matriz inteira (por exemplo, verificando seu comprimento). Outros exigem a aplicação de uma condição a cada membro da matriz individual (por exemplo, certifique-se de que toda a regra de firewall bloqueie o acesso da Internet). Entender as diferentes maneiras Azure Policy pode fazer referência a propriedades de recurso e como essas referências se comportam quando se referem a propriedades de matriz é a chave para escrever condições que abrangem esses cenários.

### <a name="referencing-resource-properties"></a>Propriedades de recurso de referência

As propriedades de recurso podem ser referenciadas por Azure Policy usando [aliases](../concepts/definition-structure.md#aliases) há duas maneiras de fazer referência aos valores de uma propriedade de recurso dentro de Azure Policy:

- Use condição de [campo](../concepts/definition-structure.md#fields) para verificar se **todas** as propriedades de recurso selecionadas atendem a uma condição. Exemplo:

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- Use `field()` a função para acessar o valor de uma propriedade. Exemplo:

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

A condição de campo tem um comportamento "todos" implícito. Se o alias representar uma coleção de valores, ele verificará se todos os valores individuais atendem à condição. A `field()` função retorna os valores representados pelo alias como estão, que podem ser manipulados por outras funções de modelo.

### <a name="referencing-array-fields"></a>Referenciando campos de matriz

As propriedades de recurso de matriz geralmente são representadas por dois tipos diferentes de aliases. Um alias ' normal ' e [aliases de matriz](../concepts/definition-structure.md#understanding-the--alias) que foram `[*]` anexados a ele:

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>Referenciando a matriz

O primeiro alias representa um único valor, o valor da `stringArray` Propriedade do conteúdo da solicitação. Como o valor dessa propriedade é uma matriz, ele não é muito útil em condições de política. Por exemplo:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

Essa condição compara toda a `stringArray` matriz com um único valor de cadeia de caracteres. A maioria das condições, incluindo `equals` , aceita apenas valores de cadeia de caracteres, portanto, não há muito uso em comparar uma matriz a uma cadeia de caracteres. O cenário principal em que referenciar a propriedade de matriz é útil quando se verifica se existe:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

Com a `field()` função, o valor retornado é a matriz do conteúdo da solicitação, que pode ser usada com qualquer uma das [funções de modelo com suporte](../concepts/definition-structure.md#policy-functions) que aceitam argumentos de matriz. Por exemplo, a condição a seguir verifica se o comprimento de `stringArray` é maior que 0:

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>Referenciando a coleção de membros da matriz

Os aliases que usam a `[*]` sintaxe representam uma **coleção de valores de propriedade selecionados de uma propriedade de matriz**, que é diferente de selecionar a própria propriedade de matriz. No caso do `Microsoft.Test/resourceType/stringArray[*]` , ele retorna uma coleção que tem todos os membros de `stringArray` . Como mencionado anteriormente, uma `field` condição verifica se todas as propriedades de recurso selecionadas atendem à condição. portanto, a condição a seguir será verdadeira somente se **todos** os membros de `stringArray` forem iguais a ' "valor" '.

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

Se a matriz contiver objetos, um `[*]` alias poderá ser usado para selecionar o valor de uma propriedade específica de cada membro da matriz. Exemplo:

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

Essa condição será verdadeira se os valores de todas as `property` Propriedades no `objectArray` forem iguais a `"value"` . Para obter mais exemplos, [consulte \[ \* \] exemplos de alias adicionais](#appendix--additional--alias-examples).

Ao usar a `field()` função para fazer referência a um alias de matriz, o valor retornado é uma matriz de todos os valores selecionados. Esse comportamento significa que o caso de uso comum da `field()` função, a capacidade de aplicar funções de modelo aos valores de propriedade de recurso, é muito limitado. As únicas funções de modelo que podem ser usadas neste caso são aquelas que aceitam argumentos de matriz. Por exemplo, é possível obter o comprimento da matriz com `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` . No entanto, cenários mais complexos, como aplicar a função de modelo a cada membro da matriz e compará-lo a um valor desejado só são possíveis ao usar a `count` expressão. Para obter mais informações, consulte [expressão de contagem de campos](#field-count-expressions).

Para resumir, consulte o seguinte conteúdo de recurso de exemplo e os valores selecionados retornados por vários aliases:

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

Ao usar a condição de campo no conteúdo do recurso de exemplo, os resultados são os seguintes:

| Alias | Valores selecionados |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | Uma coleção vazia de valores. |
| `Microsoft.Test/resourceType/missingArray[*].property` | Uma coleção vazia de valores. |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

Ao usar a `field()` função no conteúdo do recurso de exemplo, os resultados são os seguintes:

| Expression | Valor retornado |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

### <a name="field-count-expressions"></a>Expressões de contagem de campo

As expressões de [contagem de campos](../concepts/definition-structure.md#field-count) contam com quantos membros de matriz atendem a uma condição e comparam a contagem a um valor de destino. `Count` é mais intuitivo e versátil para avaliar matrizes em comparação com `field` condições. A sintaxe do é:

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

Quando usado sem uma `where` condição, `count` simplesmente retorna o comprimento de uma matriz. Com o conteúdo de recurso de exemplo da seção anterior, a `count` expressão a seguir é avaliada como `true` `stringArray` tem três membros:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

Esse comportamento também funciona com matrizes aninhadas. Por exemplo, a expressão a seguir `count` é avaliada como `true` porque há quatro membros de matriz nas `nestedArray` matrizes:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

A potência do `count` está na `where` condição. Quando especificado, Azure Policy enumera os membros da matriz e avalia cada um em relação à condição, contando quantos membros de matriz foram avaliados `true` . Especificamente, em cada iteração da `where` avaliação da condição, Azure Policy seleciona um único membro da matriz ***i** _ e avaliamos o conteúdo do recurso com a `where` condição _* como se **_eu_*_ fosse o único membro do array_ *. Ter apenas um membro de matriz disponível em cada iteração fornece uma maneira de aplicar condições complexas em cada membro da matriz individual.

Exemplo:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```
Para avaliar a `count` expressão, Azure Policy avalia a `where` condição 3 vezes, uma vez para cada membro de `stringArray` , contando quantas vezes ela foi avaliada `true` . Quando a `where` condição se refere aos `Microsoft.Test/resourceType/stringArray[*]` membros da matriz, em vez de selecionar todos os membros de `stringArray` , ele selecionará apenas um único membro da matriz a cada vez:

| Iteração | `Microsoft.Test/resourceType/stringArray[*]`Valores selecionados | `where` Resultado da avaliação |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

E, portanto, o retornará `count` `1` .

Aqui está uma expressão mais complexa:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| Iteração | Valores selecionados | `where` Resultado da avaliação |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

E, portanto, os `count` retornos `1` .

O fato de que a `where` expressão é avaliada em relação a **todo** o conteúdo da solicitação (com alterações somente no membro da matriz que está sendo enumerado atualmente) significa que a `where` condição também pode se referir a campos fora da matriz:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  }
}
```

| Iteração | Valores selecionados | `where` Resultado da avaliação |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

As expressões de contagem aninhadas também são permitidas:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "count": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "where": {
              "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
              "equals": 3
            },
            "greater": 0
          }
        }
      ]
    }
  }
}
```
 
| Iteração de loop externo | Valores selecionados | Iteração de loop interno | Valores selecionados |
|:---|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1` |
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `2` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `4` |

#### <a name="accessing-current-array-member-with-template-functions"></a>Acessando o membro da matriz atual com funções de modelo

Ao usar funções de modelo, use a `current()` função para acessar o valor do membro da matriz atual ou os valores de qualquer uma de suas propriedades. Para acessar o valor do membro da matriz atual, passe o alias definido em `count.field` ou qualquer um de seus aliases filho como um argumento para a `current()` função. Por exemplo:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
        "value": "[current('Microsoft.Test/resourceType/objectArray[*].property')]",
        "like": "value*"
    }
  },
  "equals": 2
}

```

| Iteração | `current()` valor retornado | `where` Resultado da avaliação |
|:---|:---|:---|
| 1 | O valor de `property` no primeiro membro de `objectArray[*]` : `value1` | `true` |
| 2 | O valor de `property` no primeiro membro de `objectArray[*]` : `value2` | `true` |

#### <a name="the-field-function-inside-where-conditions"></a>A função de campo dentro das condições de onde

A `field()` função também pode ser usada para acessar o valor do membro da matriz atual, desde que a expressão de **contagem** não esteja dentro de uma **condição de existência** ( `field()` a função sempre faz referência ao recurso avaliado na condição **If** ).
O comportamento de `field()` quando se refere à matriz avaliada é baseado nos seguintes conceitos:
1. Os aliases de matriz são resolvidos em uma coleção de valores selecionados de todos os membros da matriz.
1. `field()` as funções que fazem referência a aliases de matriz retornam uma matriz com os valores selecionados.
1. Referenciar o alias de matriz contado dentro da `where` condição retorna uma coleção com um único valor selecionado do membro da matriz que é avaliado na iteração atual.

Esse comportamento significa que, ao fazer referência ao membro da matriz contado com uma `field()` função dentro da `where` condição, **ele retorna uma matriz com um único membro**. Embora isso possa não ser intuitivo, é consistente com a ideia de que os aliases de matriz sempre retornam uma coleção de propriedades selecionadas. Veja um exemplo:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| Iteração | Valores de expressão | `where` Resultado da avaliação |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

Portanto, quando houver a necessidade de acessar o valor do alias de matriz contado com uma `field()` função, a maneira de fazer isso é encapsulá-la com uma função de `first()` modelo:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| Iteração | Valores de expressão | `where` Resultado da avaliação |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

Para obter exemplos úteis, consulte [exemplos de contagem de campos](../concepts/definition-structure.md#field-count-examples).

## <a name="modifying-arrays"></a>Modificando matrizes

As propriedades [Append](../concepts/effects.md#append) e [Modify](../concepts/effects.md#modify) ALTER em um recurso durante a criação ou atualização. Ao trabalhar com propriedades de matriz, o comportamento desses efeitos depende se a operação está tentando modificar o  **\[\*\]** alias ou não:

> [!NOTE]
> O uso do `modify` efeito com aliases está atualmente em **Visualização**.

|Alias |Efeito | Resultado |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | Azure Policy acrescenta toda a matriz especificada nos detalhes do efeito, se estiver ausente. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` com `add` operação | Azure Policy acrescenta toda a matriz especificada nos detalhes do efeito, se estiver ausente. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` com `addOrReplace` operação | Azure Policy acrescenta toda a matriz especificada nos detalhes do efeito, se ausente ou substitui a matriz existente. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure Policy acrescenta o membro da matriz especificado nos detalhes do efeito. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` com `add` operação | Azure Policy acrescenta o membro da matriz especificado nos detalhes do efeito. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` com `addOrReplace` operação | Azure Policy remove todos os membros da matriz existentes e acrescenta o membro da matriz especificado nos detalhes do efeito. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure Policy acrescenta um valor à `action` propriedade de cada membro da matriz. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` com `add` operação | Azure Policy acrescenta um valor à `action` propriedade de cada membro da matriz. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` com `addOrReplace` operação | Azure Policy acrescenta ou substitui a propriedade existente `action` de cada membro da matriz. |

Para obter mais informações, consulte os [exemplos de append](../concepts/effects.md#append-examples).

## <a name="appendix--additional--alias-examples"></a>Apêndice-exemplos de alias adicionais [*]

É recomendável usar as [expressões de contagem de campo](#field-count-expressions) para verificar se ' todos ' ou ' qualquer de ' os membros de uma matriz no conteúdo da solicitação atendem a uma condição. No entanto, para algumas condições simples, é possível obter o mesmo resultado usando um acessador de campo com um alias de matriz (conforme descrito em [referenciando a coleção de membros da matriz](#referencing-the-array-members-collection)). Isso pode ser útil em regras de política que excedem o limite de expressões de **contagem** permitidas. Aqui estão exemplos de casos de uso comuns:

A regra de política de exemplo para a tabela de cenário abaixo:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Para o cenário de tabela mostrado abaixo, a matriz **ipRules** é da seguinte maneira:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Para cada exemplo de condição abaixo, substitua `<field>` por `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Os seguintes resultados são o resultado da combinação entre a condição, da regra de política de exemplo e da matriz de valores existentes acima:

|Condição |Resultado | Cenário |Explicação |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nada |Nenhuma correspondência |Um elemento de matriz é avaliado como false (127.0.0.1 != 127.0.0.1) e outro como true (127.0.0.1 != 192.168.1.1), portanto, a condição **notEquals** é _false_ e o efeito não é disparado. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efeito de política |Nenhuma correspondência |Ambos os elementos da matriz são avaliados como true (10.0.4.1 != 127.0.0.1 e 10.0.4.1 != 192.168.1.1), então a condição **notEquals** é _true_ e o efeito é disparado. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efeito de política |Uma ou mais correspondências |Um elemento de matriz é avaliado como false (127.0.0.1 != 127.0.0.1) e outro como true (127.0.0.1 != 192.168.1.1), então a condição **notEquals** é _false_. O operador lógico é avaliado como true (**not** _false_), então o efeito é disparado. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nada |Uma ou mais correspondências |Ambos os elementos da matriz são avaliados como true (10.0.4.1 != 127.0.0.1 e 10.0.4.1 != 192.168.1.1), então a condição **notEquals** é _true_. O operador lógico é avaliado como falso (**not** _true_), então o efeito não é disparado. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efeito de política |Nem todos correspondem |Um elemento de matriz é avaliado como true (127.0.0.1 == 127.0.0.1) e outro como false (127.0.0.1 == 192.168.1.1), então a condição **Equals** é _false_. O operador lógico é avaliado como true (**not** _false_), então o efeito é disparado. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efeito de política |Nem todos correspondem |Ambos os elementos da matriz são avaliados como false (10.0.4.1 == 127.0.0.1 e 10.0.4.1 == 192.168.1.1), então a condição **Equals** é _false_. O operador lógico é avaliado como true (**not** _false_), então o efeito é disparado. |
|`{<field>,"Equals":"127.0.0.1"}` |Nada |Todos correspondem |Um elemento de matriz é avaliado como true (127.0.0.1 == 127.0.0.1) e outro como false (127.0.0.1 == 192.168.1.1), portanto, a condição **Equals** é _false_ e o efeito não é disparado. |
|`{<field>,"Equals":"10.0.4.1"}` |Nada |Todos correspondem |Ambos os elementos da matriz são avaliados como true (10.0.4.1 == 127.0.0.1 e 10.0.4.1 == 192.168.1.1), então a condição **Equals** é _false_ e o efeito não é disparado. |

## <a name="next-steps"></a>Próximas etapas

- Revise os exemplos em [Exemplos do Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas de maneira programática](programmatically-create.md).
- Saiba como [corrigir recursos fora de conformidade](remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
