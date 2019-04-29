---
title: Método de avaliação – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Use o Método de avaliação para retornar um conjunto de entidades acadêmicas com base em uma expressão de consulta.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: d2e628fb7fc502ef9ba81d20680d66f24fd7d138
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61339073"
---
# <a name="evaluate-method"></a>Método Avaliar

A API REST **avaliar** é usada para retornar um conjunto de entidades acadêmicas com base em uma expressão de consulta.
<br>

**Ponto de extremidade de REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>

## <a name="request-parameters"></a>Parâmetros de solicitação  

NOME     | Value | Obrigatório?  | DESCRIÇÃO
-----------|-----------|---------|--------
**expr**       | Cadeia de caracteres de texto | Sim | Uma expressão de consulta que especifica quais entidades devem ser retornadas.
**modelo**      | Cadeia de caracteres de texto | Não   | Nome do modelo que você quer consultar.  Atualmente, o valor padrão é o *mais recente*.        
**atributos** | Cadeia de caracteres de texto | Não <br>padrão: ID | Uma lista delimitada por vírgulas que especifica os valores de atributo que são incluídos na resposta. Os nomes de atributo diferenciam maiúsculas de minúsculas.
**count**        | Número | Não <br>Padrão: 10 | Número de resultados para retornar.
**offset**     | Número |   Não <br>Padrão: 0    | Índice do primeiro resultado para retornar.
**orderby** |   Cadeia de caracteres de texto | Não <br>Padrão: diminuindo prob | Nome de um atributo que é usado para classificar as entidades. Opcionalmente, pode ser especificado ascendente/descendente. O formato é: *name:asc* ou *name:desc*.
  
 <br>

## <a name="response-json"></a>Resposta (JSON)

NOME | DESCRIÇÃO
-------|-----   
**expr** |  O parâmetro *expr* da solicitação.
**entidades** |  Uma matriz de 0 ou mais entidades que correspondem à expressão de consulta. Cada entidade contém um valor de probabilidade de log natural e os valores de outros atributos solicitados.
**anulado** | Verdadeiro se a solicitação atingiu o tempo limite.

<br>

#### <a name="example"></a>Exemplo:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Normalmente, uma expressão será obtida de uma resposta ao método **interpretar**.  Mas também, é possível compor expressões de consulta (consulte [Sintaxe de Expressão de Consulta](QueryExpressionSyntax.md)).  
  
Usando os parâmetros de *contagem* e *deslocamento*, um grande número de resultados pode ser obtido sem enviar uma única solicitação que resulte em uma resposta muito grande (e potencialmente lenta).  Neste exemplo, a solicitação usou a expressão para a primeira interpretação da resposta da API **interpretar** como o valor *expr*. O parâmetro *count=2* especifica que 2 resultados da entidade estão sendo solicitados. E o parâmetro *attributes=Ti,Y,CC,AA.AuN,AA.AuId* indica que o título, ano, contagem de citações, nome do autor e ID do autor são solicitados para cada resultado.  Consulte [Atributos de Entidade](EntityAttributes.md) para uma lista de atributos.
  
```JSON
{
  "expr": "Composite(AA.AuN=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -15.08,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "CC": 372,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1968481722
        },
        {
          "AuN": "susan t dumais",
          "AuId": 676500258
        },
        {
          "AuN": "eric horvitz",
          "AuId": 1470530979
        }
      ]
    },
    {
      "logprob": -15.389,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "CC": 237,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1982462162
        },
        {
          "AuN": "christine alvarado",
          "AuId": 2163512453
        },
        {
          "AuN": "mark s ackerman",
          "AuId": 2055132526
        },
        {
          "AuN": "david r karger",
          "AuId": 2012534293
        }
      ]
    }
  ]
}
 ```
 
