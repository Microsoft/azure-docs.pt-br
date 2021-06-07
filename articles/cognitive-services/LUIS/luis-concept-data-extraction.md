---
title: Extração de dados-LUIS
description: Extraia dados de texto expressão com intenções e entidades. Saiba que tipo de dados pode ser extraído de Reconhecimento vocal (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: e6f01354bb5aa2b78d3c9962bac49be39dd2c81f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025986"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrair dados de um texto do enunciado com intenções e entidades
O LUIS oferece a capacidade de obter informações de declarações de idioma natural de um usuário. As informações são extraídas de forma que possam ser usadas por um programa, aplicativo ou chat bot para executar uma ação. Nas seções a seguir, saiba quais dados são retornados de intenções e entidades com exemplos de JSON.

Os dados mais difíceis de extrair são os dados de aprendizado de máquina porque não é uma correspondência exata de texto. A extração de dados das [entidades](luis-concept-entity-types.md) de aprendizado de máquina precisa fazer parte do [ciclo de criação](luis-concept-app-iteration.md) até que você tenha certeza de que você receberá os dados esperados.

## <a name="data-location-and-key-usage"></a>Local dos dados e uso da chave
LUIS extrai dados do expressão do usuário no [ponto de extremidade](luis-glossary.md#endpoint)publicado. A **solicitação HTTPS** (POST ou GET) contém a declaração, assim como algumas configurações opcionais, como ambientes de preparo ou de produção.

**Solicitação de ponto de extremidade de previsão V2**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**Solicitação de ponto de extremidade de previsão V3**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

O `appID` estará disponível na página **Configurações** do aplicativo LUIS, assim como parte da URL (após `/apps/`) quando você estiver editando esse aplicativo do LUIS. A `subscription-key` é a chave do ponto de extremidade usada para consultar seu aplicativo. Embora seja possível usar a chave início/criação gratuita enquanto estiver treinando o LUIS, é importante alterar a chave de ponto de extremidade para uma chave que dê suporte ao [uso esperado do LUIS](luis-limits.md#key-limits). A unidade `timezoneOffset` é de minutos.

A **resposta HTTPS** contém todas as informações de intenção e de entidade que o LUIS pode determinar com base no modelo publicado atual do ponto de extremidade de preparo ou de produção. A URL de ponto de extremidade é encontrada no site [LUIS](luis-reference-regions.md), na seção **Gerenciar**, na página **Chaves e os pontos de extremidade**.

## <a name="data-from-intents"></a>Dados de intenções
Os dados primários são o **nome da intenção** da pontuação mais alta. A resposta do ponto de extremidade é:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V2](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V3](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

* * *

|Objeto de dados|Tipo de Dados|Local dos dados|Valor|
|--|--|--|--|
|Intencional|String|topScoringIntent.intent|"GetStoreInfo"|

Se seu aplicativo de chamada de chatbot ou LUIS tomar uma decisão com base em mais de uma pontuação de intenção, retornará todas as pontuações de intenções.


#### <a name="v2-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V2](#tab/V2)

Defina o parâmetro QueryString, `verbose=true` . A resposta do ponto de extremidade é:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V3](#tab/V3)

Defina o parâmetro QueryString, `show-all-intents=true` . A resposta do ponto de extremidade é:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

* * *

As intenções são ordenadas da pontuação mais alta para a mais baixa.

|Objeto de dados|Tipo de Dados|Local dos dados|Valor|Pontuação|
|--|--|--|--|:--|
|Intencional|String|intents[0].intent|"GetStoreInfo"|0,984749258|
|Intencional|String|intents[1].intent|"None"|0,0168218873|

Se você adicionar domínios predefinidos, o nome da intenção indicará o domínio, como `Utilties` ou `Communication`, assim como a intenção:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V2](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V3](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

* * *

|Domínio|Objeto de dados|Tipo de Dados|Local dos dados|Valor|
|--|--|--|--|--|
|Utilitários|Intencional|String|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|Comunicação|Intencional|String|intents[1].intent|<b>Communication</b>.StartOver"|
||Intencional|String|intents[2].intent|"None"|


## <a name="data-from-entities"></a>Dados de entidades
A maioria dos bots e aplicativos de bate-papo precisa de mais do que o nome da intenção. Esses dados adicionais e opcionais são provenientes de entidades descobertas na declaração. Cada tipo de entidade retorna diferentes informações sobre a correspondência.

Uma única palavra ou frase em uma declaração pode corresponder a mais de uma entidade. Nesse caso, cada entidade de correspondência é retornada com sua pontuação.

Todas as entidades são retornadas na matriz de **entidades** da resposta do ponto de extremidade

## <a name="tokenized-entity-returned"></a>Entidade indexada retornada

Examine o [suporte de token](luis-language-support.md#tokenization) em Luis.


## <a name="prebuilt-entity-data"></a>Dados de entidade predefinida
Entidades [predefinidas](luis-concept-entity-types.md) são descobertas com base em uma correspondência de expressão regular usando o projeto [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) de software livre. Entidades predefinidas são retornadas na matriz de entidades e usam o nome do tipo que começa com `builtin::`.

## <a name="list-entity-data"></a>Dados da entidade Lista

As [entidades de lista](reference-entity-list.md) representam um conjunto fixo e fechado de palavras relacionadas junto com seus sinônimos. O LUIS não descobre valores adicionais para entidades de lista. Use o recurso **Recomendado** para consultar sugestões de novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade será retornada na consulta de ponto de extremidade.

## <a name="regular-expression-entity-data"></a>Dados de entidade de expressão regular

Uma [entidade de expressão regular](reference-entity-regular-expression.md) extrai uma entidade com base em uma expressão regular que você fornece.

## <a name="extracting-names"></a>Extraindo nomes
Obter nomes de uma declaração é difícil, porque um nome pode ser quase qualquer combinação de letras e palavras. Dependendo de qual tipo de nome você está extraindo, haverá várias opções. As sugestões a seguir não são regras, mas mais diretrizes.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Adicionar entidades PersonName e GeographyV2 predefinidas

As entidades [PersonName](luis-reference-prebuilt-person.md) e [GeographyV2](luis-reference-prebuilt-geographyV2.md) estão disponíveis em algumas [culturas de linguagem](luis-reference-prebuilt-entities.md).

### <a name="names-of-people"></a>Nomes de pessoas

Os nomes de pessoas podem ter um formato pequeno dependendo do idioma e da cultura. Use uma entidade **[PersonName](luis-reference-prebuilt-person.md)** predefinida ou uma **[entidade simples](luis-concept-entity-types.md)** com funções de First e Last Name.

Se você usar a entidade simples, certifique-se de fornecer exemplos que usam o nome e o sobrenome em diferentes partes do expressão, em declarações de comprimentos diferentes e declarações em todas as intenções, incluindo a intenção de nenhum. [Examine](./luis-how-to-review-endpoint-utterances.md) declarações de ponto de extremidade regularmente para rotular nomes que não foram previstos corretamente.

### <a name="names-of-places"></a>Nomes de locais

Os nomes de local são definidos e conhecidos como cidades, municípios, Estados, províncias e países/regiões. Use a entidade predefinida **[geographyV2](luis-reference-prebuilt-geographyv2.md)** para extrair informações de localização.

### <a name="new-and-emerging-names"></a>Nomes novos e emergentes

Alguns aplicativos precisam poder encontrar nomes novos e emergentes, como produtos ou empresas. Esses tipos de nomes são o tipo mais difícil de extração de dados. Comece com uma **[entidade simples](luis-concept-entity-types.md#simple-entity)** e adicione uma [lista de frases](luis-concept-feature.md). [Examine](./luis-how-to-review-endpoint-utterances.md) declarações de ponto de extremidade regularmente para rotular nomes que não foram previstos corretamente.

## <a name="patternany-entity-data"></a>Dados de entidade pattern.any

[Padrão. any](reference-entity-pattern-any.md) é um espaço reservado de comprimento variável usado somente no modelo de um padrão expressão para marcar onde a entidade começa e termina. A entidade usada no padrão deve ser encontrada para que o padrão seja aplicado.

## <a name="sentiment-analysis"></a>Análise de sentimento
Se a análise de sentimentos estiver configurada durante a [publicação](luis-how-to-publish-app.md#sentiment-analysis), a resposta JSON Luis incluirá a análise de sentimentos. Saiba mais sobre a análise de sentimento na documentação [Análise de Texto](../text-analytics/index.yml).

## <a name="key-phrase-extraction-entity-data"></a>Dados de entidade de extração de frases-chave
A [entidade de extração de frases-chave](luis-reference-prebuilt-keyphrase.md) retorna frases-chave no expressão, fornecido por [análise de texto](../text-analytics/index.yml).

## <a name="data-matching-multiple-entities"></a>Dados que correspondem a várias entidades

O LUIS retorna todas as entidades descobertas na declaração. Como resultado, seu bot de chat pode precisar tomar uma decisão com base nos resultados.

## <a name="data-matching-multiple-list-entities"></a>Dados que correspondem a várias entidades de lista

Se uma palavra ou frase for correspondente a mais de uma entidade de lista, a consulta de ponto de extremidade retornará cada entidade de lista.

Para a consulta `when is the best time to go to red rock?` , e o aplicativo tem a palavra `red` em mais de uma lista, Luis reconhece todas as entidades e retorna uma matriz de entidades como parte da resposta do ponto de extremidade JSON.

## <a name="next-steps"></a>Próximas etapas

Confira [Adicionar entidades](luis-how-to-add-entities.md) para saber como adicionar entidades ao seu aplicativo LUIS.
