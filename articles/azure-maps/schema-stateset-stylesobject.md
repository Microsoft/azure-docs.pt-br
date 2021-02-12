---
title: Guia de referência de esquema styleobject para mapas dinâmicos do Azure
description: Guia de referência para o esquema e a sintaxe dinâmicos Styles do Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 08379e66c97d34eea53410190475e90e156a58e2
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903336"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>Guia de referência de esquema styleobject para mapas dinâmicos

> [!IMPORTANT]
> Os serviços do Azure Maps Creator estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 O `StylesObject` é uma `StyleObject` matriz que representa os estilos de estadoset. Use o [serviço de estado de recurso](/rest/api/maps/featurestate) do Azure Maps Creator (versão prévia) para aplicar seus estilos de stateset a recursos de dados de mapa em destaque. Depois de criar seus estilos de estado e os associados aos recursos de mapa interno, você pode usá-los para criar mapas de interno dinâmico. Para obter mais informações sobre a criação de mapas de interno dinâmico, consulte [implementar o estilo dinâmico para mapas de criador interno](indoor-map-dynamic-styling.md).

## <a name="styleobject"></a>Estilo de

Uma `StyleObject` é uma das seguintes regras de estilo:

 * [`BooleanTypeStyleRule`](#booleantypestylerule)
 * [`NumericTypeStyleRule`](#numerictypestylerule)
 * [`StringTypeStyleRule`](#stringtypestylerule)

O JSON abaixo mostra o uso de exemplo de cada um dos três tipos de estilo.  O `BooleanTypeStyleRule` é usado para determinar o estilo dinâmico para os recursos cuja `occupied` propriedade é true e false.  O `NumericTypeStyleRule` é usado para determinar o estilo de recursos cuja `temperature` propriedade está dentro de um determinado intervalo. Por fim, o `StringTypeStyleRule` é usado para fazer a correspondência de estilos específicos `meetingType` .



```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    },
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 Um `NumericTypeStyleRule` é um  [`StyleObject`](#styleobject) e consiste nas seguintes propriedades:

| Propriedade | Type | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `keyName` | string | O *estado* ou o nome da propriedade dinâmica. Um `keyName` deve ser exclusivo dentro da `StyleObject` matriz.| Sim |
| `type` | string | O valor é "numeric". | Sim |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Uma matriz de intervalos de estilo numérico com cores associadas. Cada intervalo define uma cor a ser usada quando o valor do *estado* satisfizer o intervalo.| Sim |

### <a name="numberruleobject"></a>NumberRuleObject

Um `NumberRuleObject` consiste em uma [`RangeObject`](#rangeobject) e uma `color` propriedade. Se o valor de *estado* cair no intervalo, sua cor de exibição será a cor especificada na `color` propriedade.

Se você definir vários intervalos sobrepostos, a cor escolhida será a cor definida no primeiro intervalo que é satisfeita.

No exemplo JSON a seguir, os dois intervalos terão true quando o valor de *estado* estiver entre 50-60. No entanto, a cor que será usada é `#343deb` o primeiro intervalo na lista que foi satisfeita.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| Propriedade | Type | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `range` | [Intervalo de](#rangeobject) | O [rangeobject](#rangeobject) define um conjunto de condições de intervalo lógico, que, se `true` , altera a cor de exibição do *estado* para a cor especificada na `color` propriedade. Se `range` não for especificado, a cor definida na `color` propriedade sempre será usada.   | Não |
| `color` | string | A cor a ser usada quando o valor do estado se enquadrar no intervalo. A `color` propriedade é uma cadeia de caracteres JSON em qualquer um dos seguintes formatos: <ul><li> Valores hexadecimais de estilo HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Nomes predefinidos de cores HTML, como amarelo e azul.</li></ul> | Sim |

### <a name="rangeobject"></a>Intervalo de

O `RangeObject` define um valor de intervalo numérico de um [`NumberRuleObject`](#numberruleobject) . Para que o valor de *estado* se enquadrar no intervalo, todas as condições definidas devem ser verdadeiras.

| Propriedade | Type | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `minimum` | double | Todo o número x que x ≥ `minimum` .| Não |
| `maximum` | double | Todo o número x que x ≤ `maximum` . | Não |
| `exclusiveMinimum` | double | Todo o número x > x `exclusiveMinimum` .| Não |
| `exclusiveMaximum` | double | Todo o número x < x `exclusiveMaximum` .| Não |

### <a name="example-of-numerictypestylerule"></a>Exemplo de NumericTypeStyleRule

O JSON a seguir ilustra um `NumericTypeStyleRule` *estado* chamado `temperature` . Neste exemplo, o [`NumberRuleObject`](#numberruleobject) contém dois intervalos de temperatura definidos e seus estilos de cor associados. Se o intervalo de temperatura for 50-69, a exibição deverá usar a cor `#343deb` .  Se o intervalo de temperatura for 31-70, a exibição deverá usar a cor `#eba834` .

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="stringtypestylerule"></a>StringTypeStyleRule

Um `StringTypeStyleRule` é um [`StyleObject`](#styleobject) e consiste nas seguintes propriedades:

| Propriedade | Type | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `keyName` | string |  O *estado* ou o nome da propriedade dinâmica.  Um `keyName` deve ser exclusivo dentro da  `StyleObject` matriz.| Sim |
| `type` | string |O valor é "String". | Sim |
| `rules` | [`StringRuleObject`](#stringruleobject)[]| Uma matriz de N números de valores de *estado* .| Sim |

### <a name="stringruleobject"></a>StringRuleObject

Um `StringRuleObject` consiste em até N números de valores de estado que são os possíveis valores de cadeia de caracteres da propriedade de um recurso. Se o valor da Propriedade do recurso não corresponder a nenhum dos valores de estado definidos, esse recurso não terá um estilo dinâmico. Se forem fornecidos valores de estado duplicados, o primeiro terá precedência.

A correspondência de valor de cadeia de caracteres diferencia maiúsculas de minúsculas.

| Propriedade | Type | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `stateValue1` | string | A cor quando o valor String é stateValue1. | Não |
| `stateValue2` | string | A cor quando o valor da cadeia de caracteres é estadovalue. | Não |
| `stateValueN` | string | A cor quando o valor String é stateValueN. | Não |

### <a name="example-of-stringtypestylerule"></a>Exemplo de StringTypeStyleRule

O JSON a seguir ilustra um `StringTypeStyleRule` que define os estilos associados a tipos específicos de reuniões.

```json
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }

```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

Um `BooleanTypeStyleRule` é um [`StyleObject`](#styleobject) e consiste nas seguintes propriedades:

| Propriedade | Type | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `keyName` | string |  O *estado* ou o nome da propriedade dinâmica.  Um `keyName` deve ser exclusivo dentro da `StyleObject`  matriz.| Sim |
| `type` | string |O valor é "booliano". | Sim |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)uma| Um par booliano com cores `true` para `false` valores de *estado* e.| Sim |

### <a name="booleanruleobject"></a>BooleanRuleObject

Um `BooleanRuleObject` define as cores `true` para `false` valores e.

| Propriedade | Type | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `true` | string | A cor a ser usada quando o valor do *estado* é `true` . A `color` propriedade é uma cadeia de caracteres JSON em qualquer um dos seguintes formatos: <ul><li> Valores hexadecimais de estilo HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Nomes predefinidos de cores HTML, como amarelo e azul.</li></ul>| Sim |
| `false` | string | A cor a ser usada quando o valor do *estado* é `false` . | Sim |

### <a name="example-of-booleantypestylerule"></a>Exemplo de BooleanTypeStyleRule

O JSON a seguir ilustra um `BooleanTypeStyleRule` *estado* chamado `occupied` . O [`BooleanRuleObject`](#booleanruleobject) define cores para `true` `false` valores e.

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```