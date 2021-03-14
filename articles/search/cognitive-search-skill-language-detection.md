---
title: Habilidade cognitiva de detecção de idioma
titleSuffix: Azure Cognitive Search
description: Avalia o texto não estruturado e, para cada registro, retorna um identificador de idioma com uma pontuação indicando a força da análise em um pipeline de enriquecimento de ia no Azure Pesquisa Cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 078a9312a7ee1b3b0eafd000928ed74348a540c3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548046"
---
#   <a name="language-detection-cognitive-skill"></a>Habilidade cognitiva de detecção de idioma

A **detecção de idioma** habilidade detecta o idioma do texto de entrada e relata um único código de idioma para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a intensidade da análise. Essa habilidade usa os modelos de machine learning fornecidos pela [Análise de Texto](../cognitive-services/text-analytics/overview.md) nos Serviços Cognitivos.

Esse recurso é especialmente útil quando você precisa fornecer o idioma do texto como entrada para outras habilidades (por exemplo, a habilidade de [Análise de Sentimento](cognitive-search-skill-sentiment.md) ou [Habilidade de Divisão de Texto](cognitive-search-skill-textsplit.md)).

A detecção de idioma aproveita as bibliotecas de processamento de idioma natural do Bing, que excedem o número de [idiomas e regiões com suporte](../cognitive-services/text-analytics/language-support.md) listados para análise de texto. A lista exata de idiomas não é publicada, mas inclui todas as linguagens amplamente faladas, além de variantes, dialetos e algumas linguagens regionais e culturais. Se você tiver conteúdo expresso em uma linguagem usada com menos frequência, poderá [tentar a API detecção de idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) para ver se ela retorna um código. A resposta para idiomas que não pode ser detectada é `(Unknown)`.

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem, como parte do estágio de quebra de documento na Pesquisa Cognitiva do Azure. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço da extração de imagem é descrito na [página de preços da Pesquisa Cognitiva do Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 50.000 caracteres conforme medido por [`String.Length`](/dotnet/api/system.string.length). Se você precisar dividir seus dados antes de enviá-los para a habilidade de detecção de idioma, poderá usar a [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Entradas | Descrição |
|---------------------|-------------|
| `defaultCountryHint` | Adicional Um código de país da letra ISO 3166-1 alfa-2 2 pode ser fornecido para ser usado como uma dica para o modelo de detecção de idioma se não for possível eliminar a ambiguidade do idioma. Consulte [a documentação do análise de texto](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) neste tópico para obter mais detalhes. Especificamente, o `defaultCountryHint` parâmetro é usado com documentos que não especificam a `countryHint` entrada explicitamente.  |
| `modelVersion`   | Adicional A versão do modelo a ser usada ao chamar o serviço de Análise de Texto. O padrão será o mais recente disponível quando não for especificado. Recomendamos que você não especifique esse valor, a menos que seja absolutamente necessário. Consulte [controle de versão de modelo no API de análise de texto](../cognitive-services/text-analytics/concepts/model-versioning.md) para obter mais detalhes. |

## <a name="skill-inputs"></a>Entradas de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Entradas     | Descrição |
|--------------------|-------------|
| `text` | O texto a ser analisado.|
| `countryHint` | Um código de país de letra ISO 3166-1 alfa-2 2 para usar como uma dica para o modelo de detecção de idioma se não for possível desambiguar o idioma. Consulte [a documentação do análise de texto](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) neste tópico para obter mais detalhes. |

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída    | Descrição |
|--------------------|-------------|
| `languageCode` | O código de idioma ISO 6391 para o idioma identificado. Por exemplo, “in” |
| `languageName` | O nome do idioma. Por exemplo “inglês”. |
| `score` | Um valor entre 0 e 1. A probabilidade de que o idioma é identificado corretamente. A pontuação pode ser menor que 1, se a frase misturou idiomas.  |

##  <a name="sample-definition"></a>Definição de exemplo

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "countryHint",
        "source": "/document/countryHint"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>Entrada de exemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "impossible",
             "countryHint": "fr"
           }
      }
    ]
```


##  <a name="sample-output"></a>Saída de exemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      },
      {
        "recordId": "3",
        "data":
            {
              "languageCode": "fr",
              "languageName": "French",
              "score": 1,
            }
      }
    ]
}
```

## <a name="see-also"></a>Confira também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)