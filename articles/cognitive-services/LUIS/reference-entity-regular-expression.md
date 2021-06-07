---
title: Tipo de entidade de expressão regular-LUIS
description: Uma expressão regular é melhor para texto de enunciado bruto. Não diferencia maiúsculas de minúsculas e ignora a variante cultural.  A correspondência de expressão regular é aplicada após alterações ortográficas no nível do caractere, não no nível do token.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 18e44ec43e1169aa054e6e5b4591ccd8611a7f4d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025218"
---
# <a name="regular-expression-entity"></a>Entidade de expressão regular

Uma entidade de expressão regular extrai uma entidade com base em um padrão de expressão regular fornecido por você.

Uma expressão regular é melhor para texto de enunciado bruto. Não diferencia maiúsculas de minúsculas e ignora a variante cultural.  A correspondência de expressão regular é aplicada após alterações ortográficas no nível do caractere, não no nível do token. Se a expressão regular for muito complexa, como o uso de vários colchetes, não será possível adicionar a expressão ao modelo. Usa parte, mas não toda a biblioteca [Regex .net](/dotnet/standard/base-types/regular-expressions) .

**A entidade é uma boa opção quando:**

* Os dados são consistentemente formatados com qualquer variação que também seja consistente.
* A expressão regular não requer mais de 2 níveis de aninhamento.

![Entidade de expressão regular](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Considerações sobre o uso

Expressões regulares podem corresponder a mais do que você espera corresponder. Um exemplo disso é a correspondência de palavras numéricas, como `one` e `two` . Um exemplo é o seguinte Regex, que corresponde ao número `one` junto com outros números:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Essa expressão Regex também corresponde a quaisquer palavras que terminem com esses números, como `phone` . Para corrigir problemas como esse, verifique se as correspondências de Regex levam em conta limites de palavras. O Regex para usar limites de palavras para este exemplo é usado no seguinte Regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>JSON de exemplo

Ao usar `kb[0-9]{6}` , como a definição de entidade de expressão regular, a resposta JSON a seguir é um exemplo de expressão com as entidades de expressão regular retornadas para a consulta:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V3](#tab/V3)


Esse é o JSON se `verbose=false` estiver definido na cadeia de caracteres de consulta:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Esse é o JSON se `verbose=true` estiver definido na cadeia de caracteres de consulta:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre entidades:

* [Conceitos](luis-concept-entity-types.md)
* [Como criar](luis-how-to-add-entities.md)