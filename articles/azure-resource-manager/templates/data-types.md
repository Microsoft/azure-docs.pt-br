---
title: Tipos de dados em modelos
description: Descreve os tipos de dados que estão disponíveis em modelos de Azure Resource Manager.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 7d3f15c8852e6e25c621baad9bc6f20c303ffdb9
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125037"
---
# <a name="data-types-in-arm-templates"></a>Tipos de dados em modelos ARM

Este artigo descreve os tipos de dados com suporte em modelos de Azure Resource Manager (modelos ARM). Ele abrange os tipos de dados JSON e bicep.

## <a name="supported-types"></a>Tipos com suporte

Em um modelo do ARM, você pode usar esses tipos de dados:

* array
* bool
* INT
* objeto
* secureobject – indicado por modificador em bicep
* secureString-indicado por modificador em bicep
* string

## <a name="arrays"></a>Matrizes

As matrizes começam com um colchete esquerdo ( `[` ) e terminam com um colchete direito ( `]` ).

No JSON, uma matriz pode ser declarada em uma única linha ou em várias linhas. Cada elemento é separado por uma vírgula.

No bicep, uma matriz deve ser declarada em várias linhas. Não use vírgulas entre valores.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleArray array = [
  1
  2
  3
]
```

---

Os elementos de uma matriz podem ser do mesmo tipo ou de tipos diferentes.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

---

## <a name="booleans"></a>Boolianos

Ao especificar valores Boolianos, use `true` ou `false` . Não coloque o valor entre aspas.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleBool bool = true
```

---

## <a name="integers"></a>Inteiros

Ao especificar valores inteiros, não use aspas.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleInt int = 1
```

---

Para números inteiros passados como parâmetros embutidos, o intervalo de valores pode ser limitado pelo SDK ou pela ferramenta de linha de comando usada para implantação. Por exemplo, ao usar o PowerShell para implantar um modelo, os tipos de inteiros podem variar de-2147483648 a 2147483647. Para evitar essa limitação, especifique valores inteiros grandes em um [arquivo de parâmetro](parameter-files.md). Os tipos de recurso aplicam seus próprios limites para propriedades de inteiros.

## <a name="objects"></a>Objetos

Os objetos começam com uma chave esquerda ( `{` ) e terminam com uma chave direita ( `}` ). Cada propriedade em um objeto consiste em chave e valor. A chave e o valor são separados por dois-pontos ( `:` ).

No JSON, a chave é colocada entre aspas duplas. Cada propriedade é separada por uma vírgula.

No bicep, a chave não está entre aspas. Não use vírgulas entre as propriedades.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

---

## <a name="strings"></a>Cadeias de caracteres

No JSON, as cadeias de caracteres são marcadas com aspas duplas. No bicep, as cadeias de caracteres são marcadas com aspas simples.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleString string = 'test value'
```
---

## <a name="secure-strings-and-objects"></a>Proteger cadeias de caracteres e objetos

A cadeia de caracteres segura usa o mesmo formato que a cadeia de caracteres, e o objeto seguro usa o mesmo formato que o objeto. Quando você define um parâmetro como uma cadeia de caracteres segura ou um objeto seguro, o valor do parâmetro não é salvo no histórico de implantação e não é registrado. No entanto, se você definir esse valor seguro para uma propriedade que não está esperando um valor seguro, o valor não será protegido. Por exemplo, se você definir uma cadeia de caracteres segura para uma marca, esse valor será armazenado como texto sem formatação. Use cadeias de caracteres seguras para senhas e segredos.

Com bicep, você adiciona o `@secure()` modificador a uma cadeia de caracteres ou objeto.

O exemplo a seguir mostra dois parâmetros seguros:

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param password string

@secure()
param configValues object
```

---

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a sintaxe do modelo, consulte [entender a estrutura e a sintaxe de modelos do ARM](template-syntax.md).
