---
title: Padrão. qualquer tipo de entidade-LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any é um espaço reservado de tamanho variável usado apenas em um enunciado de modelo para marcar onde a entidade começa e termina.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: cda6c724a36a73dc34c2bf8e7158e3e3ec92d46b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563215"
---
# <a name="patternany-entity"></a>Entidade pattern.any 

Pattern.any é um espaço reservado de tamanho variável usado apenas em um enunciado de modelo para marcar onde a entidade começa e termina.  

As entidades Pattern.any devem ser marcadas nos exemplos de modelo [Padrão](luis-how-to-model-intent-pattern.md), não exemplos de usuários de intenção.

**A entidade é uma boa opção quando:**

* O final da entidade pode ser confundido com o texto restante do enunciado. 

## <a name="usage"></a>Uso

Em um determinado aplicativo cliente que pesquisa livros com base no título, o pattern.any extrai o título completo. Um enunciado de modelo usando pattern.any para essa busca de livro é `Was {BookTitle} written by an American this year[?]`. 

Na tabela a seguir, cada linha tem duas versões do enunciado. A principal expressão é como a LUIS inicialmente vê o expressão. Não fica claro onde o título do livro começa e termina. O expressão inferior usa um padrão. qualquer entidade para marcar o início e o fim da entidade. 

|Expressão com a entidade em negrito|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>**The Man Who Mistook His Wife for a Hat and Other Clinical Tales** foi escrito por um americano este ano?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>**Half Asleep in Frog Pajamas** foi escrito por uma americano este ano?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>**The Particular Sadness of Lemon Cake: foi um romance escrito** por um americano este ano?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**There's A Wocket In My Pocket!** foi escrito por um americano este ano?|
||

## <a name="example-json"></a>JSON de exemplo

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Neste [tutorial](luis-tutorial-pattern-any.md), use o **padrão. qualquer** entidade para extrair dados de declarações em que o declarações está bem formatado e onde o final dos dados pode ser facilmente confundido com as palavras restantes do expressão.