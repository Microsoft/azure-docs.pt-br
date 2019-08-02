---
title: Tipo de entidade de expressão regular-LUIS
titleSuffix: Azure Cognitive Services
description: Uma expressão regular é melhor para texto de enunciado bruto. Não diferencia maiúsculas de minúsculas e ignora a variante cultural.  A correspondência de expressão regular é aplicada após alterações ortográficas no nível do caractere, não no nível do token.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 82cce359f2161800c53ccce7cdb0342bba759d43
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559938"
---
# <a name="regular-expression-entity"></a>Entidade de expressão regular 

Uma entidade de expressão regular extrai uma entidade com base em um padrão de expressão regular fornecido por você.

Uma expressão regular é melhor para texto de enunciado bruto. Não diferencia maiúsculas de minúsculas e ignora a variante cultural.  A correspondência de expressão regular é aplicada após alterações ortográficas no nível do caractere, não no nível do token. Se a expressão regular for muito complexa, como o uso de vários colchetes, não será possível adicionar a expressão ao modelo. Usa parte, mas não toda a biblioteca [Regex .net](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) . 

**A entidade é uma boa opção quando:**

* Os dados são consistentemente formatados com qualquer variação que também seja consistente.
* A expressão regular não requer mais de 2 níveis de aninhamento. 

![Entidade de expressão regular](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Considerações de uso

Expressões regulares podem corresponder a mais do que você espera corresponder. Um exemplo disso é a correspondência de palavras numéricas `one` , `two`como e. Um exemplo é o seguinte Regex, que corresponde ao número `one` junto com outros números:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

Essa expressão Regex também corresponde a quaisquer palavras que terminem com esses números, `phone`como. Para corrigir problemas como esse, verifique se as correspondências de Regex levam em conta limites de palavras. O Regex para usar limites de palavras para este exemplo é usado no seguinte Regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>JSON de exemplo

Ao usar `kb[0-9]{6}`, como a definição de entidade de expressão regular, a resposta JSON a seguir é um exemplo de expressão com as entidades de expressão `When was kb123456 published?`regular retornadas para a consulta:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Neste [tutorial](luis-quickstart-intents-regex-entity.md), crie um aplicativo para extrair dados formatados de forma consistente de um expressão usando a entidade **expressão regular** .