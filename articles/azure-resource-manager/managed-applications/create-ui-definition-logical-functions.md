---
title: Criar funções lógicas de definição de interface do usuário
description: Descreve as funções para executar operações lógicas.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 00d2f0eeb5d353c8ebd7ad30f6866f890d6cb42e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095724"
---
# <a name="createuidefinition-logical-functions"></a>Funções lógicas CreateUiDefinition

Essas funções podem ser usadas em expressões condicionais. Algumas funções podem não dar suporte a todos os tipos de dados JSON.

## <a name="and"></a>e

Retorna `true` se todos os parâmetros são avaliados como `true`. Essa função dá suporte apenas a dois ou mais parâmetros do tipo booliano.

O exemplo a seguir retorna `true`:

```json
"[and(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

O exemplo a seguir retorna `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

## <a name="coalesce"></a>coalesce

Retorna o valor do primeiro parâmetro não nulo. Essa função dá suporte a todos os tipos de dados JSON.

Suponha que `element1` e `element2` são indefinidos. O exemplo a seguir retorna `"Contoso"`:

```json
"[coalesce(steps('demoStep').element1, steps('demoStep').element2, 'Contoso')]"
```

Essa função é especialmente útil no contexto de invocação opcional que ocorre devido a uma ação do usuário após o carregamento da página. Um exemplo é se as restrições colocadas em um campo na interface do usuário dependem do valor selecionado atualmente de outro campo **inicialmente não visível** . Nesse caso, `coalesce()` o pode ser usado para permitir que a função seja sintaticamente válida no tempo de carregamento da página e tenha o efeito desejado quando o usuário interage com o campo.

Considere isso `DropDown` , que permite ao usuário escolher entre vários tipos diferentes de banco de dados:

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

Para condição da ação de outro campo no valor atual escolhido deste campo, use `coalesce()` , conforme mostrado aqui:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

Isso é necessário porque o `databaseType` inicialmente não está visível e, portanto, não tem um valor. Isso faz com que a expressão inteira não seja avaliada corretamente.

## <a name="equals"></a>equals

Retorna `true` se ambos os parâmetros têm o mesmo tipo e valor. Essa função dá suporte a todos os tipos de dados JSON.

O exemplo a seguir retorna `true`:

```json
"[equals(0, 0)]"
```

O exemplo a seguir retorna `true`:

```json
"[equals('web', 'web')]"
```

O exemplo a seguir retorna `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

## <a name="greater"></a>greater

Retorna `true` se o primeiro parâmetro é estritamente maior que o segundo parâmetro. Essa função dá suporte apenas a parâmetros dos tipos número e cadeia de caracteres.

O exemplo a seguir retorna `false`:

```json
"[greater(1, 2)]"
```

O exemplo a seguir retorna `true`:

```json
"[greater('9', '10')]"
```

## <a name="greaterorequals"></a>greaterOrEquals

Retorna `true` se o primeiro parâmetro é maior ou igual ao segundo parâmetro. Essa função dá suporte apenas a parâmetros dos tipos número e cadeia de caracteres.

O exemplo a seguir retorna `true`:

```json
"[greaterOrEquals(2, 2)]"
```

## <a name="if"></a>if

Retorna um valor com base em se uma condição é verdadeira ou falsa. O primeiro parâmetro é a condição a ser testada. O segundo parâmetro é o valor a ser retornado se a condição for verdadeira. O terceiro parâmetro é o valor a ser retornado se a condição for falsa.

O exemplo a seguir retorna `yes` .

```json
"[if(equals(42, mul(6, 7)), 'yes', 'no')]"
```

## <a name="less"></a>less

Retorna `true` se o primeiro parâmetro é estritamente menor que o segundo parâmetro. Essa função dá suporte apenas a parâmetros dos tipos número e cadeia de caracteres.

O exemplo a seguir retorna `true`:

```json
"[less(1, 2)]"
```

O exemplo a seguir retorna `false`:

```json
"[less('9', '10')]"
```

## <a name="lessorequals"></a>lessOrEquals

Retorna `true` se o primeiro parâmetro é menor ou igual ao segundo parâmetro. Essa função dá suporte apenas a parâmetros dos tipos número e cadeia de caracteres.

O exemplo a seguir retorna `true`:

```json
"[lessOrEquals(2, 2)]"
```

## <a name="not"></a>not

Retorna `true` se o parâmetro é avaliado como `false`. Essa função dá suporte apenas a parâmetros do tipo booliano.

O exemplo a seguir retorna `true`:

```json
"[not(false)]"
```

O exemplo a seguir retorna `false`:

```json
"[not(equals(0, 0))]"
```

## <a name="or"></a>ou

Retorna `true` se, pelo menos, um dos parâmetros é avaliado como `true`. Essa função dá suporte apenas a dois ou mais parâmetros do tipo booliano.

O exemplo a seguir retorna `true`:

```json
"[or(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

O exemplo a seguir retorna `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução ao Azure Resource Manager, consulte [Visão geral do Azure Resource Manager](../management/overview.md).
