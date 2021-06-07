---
title: Executar a análise de sentimento e a mineração de opiniões com a API REST de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como detectar sentimentos e minerar opiniões em um texto com a API de Análise de Texto dos Serviços Cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 03/29/2021
ms.author: aahi
ms.openlocfilehash: 7cd2b0a6b943ceb32420ef119a7fc5eddefa2e19
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276987"
---
# <a name="how-to-sentiment-analysis-and-opinion-mining"></a>Como usar a análise de sentimento e a mineração de opiniões

O recurso Análise de Sentimento da API de Análise de Texto fornece duas maneiras de detectar um sentimento positivo e negativo. Se você enviar uma solicitação de Análise de Sentimento, a API retornará rótulos de sentimento (como "negativo", "neutro" e "positivo") e pontuações de confiança no nível da frase e do documento. Você também pode enviar solicitações de mineração de opiniões usando o ponto de extremidade da Análise de Sentimento, que fornece informações granulares sobre as opiniões relacionadas a palavras (como os atributos de produtos ou serviços) no texto.

Os modelos de IA usados pela API são fornecidos pelo serviço; basta enviar o conteúdo para análise.

## <a name="sentiment-analysis-versions-and-features"></a>Versões e recursos da Análise de Sentimento

| Recurso                                   | Análise de Sentimento v3 | Análise de Sentimento v3.1 (versão prévia) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Métodos para solicitações únicas e em lote    | X                     | X                                 |
| Pontuações e rotulagem da Análise de Sentimento             | X                     | X                                 |
| [Contêiner do Docker](text-analytics-how-to-install-containers.md) baseado em Linux | X  |  |
| Mineração de opiniões                            |                       | X                                 |

## <a name="sentiment-analysis"></a>Análise de Sentimento

A Análise de Sentimento na versão 3.x aplica rótulos de sentimento a um texto, que são retornados em um nível da frase e do documento, com uma pontuação de confiança para cada um. 

Os rótulos são *positivos*, *negativos* e *neutros*. No nível do documento, o rótulo de sentimento *misto* também pode ser retornado. O sentimento do documento é determinado abaixo:

| Sentimento da frase                                                                            | Rótulo do documento retornado |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Há, pelo menos, uma frase `positive` no documento. O restante das frases é `neutral`. | `positive`              |
| Há, pelo menos, uma frase `negative` no documento. O restante das frases é `neutral`. | `negative`              |
| Há, pelo menos, uma frase `negative` e uma frase `positive` no documento.    | `mixed`                 |
| Todas as frases do documento são `neutral`.                                                  | `neutral`               |

As pontuações de confiança vão de 1 a 0. As pontuações mais próximas a 1 indicam uma maior confiança na classificação do rótulo, enquanto as pontuações inferiores indicam uma menor confiança. Para cada documento ou cada frase, as pontuações previstas associadas aos rótulos (positivo, negativo e neutro) somam 1. Para obter mais informações, confira a [observação sobre a transparência da Análise de Texto](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context). 

## <a name="opinion-mining"></a>Mineração de opiniões

A mineração de opiniões é um recurso da Análise de Sentimento introduzido da versão prévia da versão 3.1 em diante. Também conhecida como Análise de Sentimento baseada em aspecto no NLP (processamento de idioma natural), esse recurso fornece informações mais granulares sobre as opiniões relacionadas a atributos de produtos ou serviços no texto. A API revela opiniões como um destino (substantivo ou verbo) e uma avaliação (adjetivo).

Por exemplo, se um cliente deixar comentários sobre um hotel, como "O quarto era excelente, mas a equipe foi antipática", a mineração de opiniões localizará destinos (aspectos) no texto e as respectivas avaliações (opiniões) e sentimentos associados. A Análise de Sentimento só pode relatar um sentimento negativo.

:::image type="content" source="../media/how-tos/opinion-mining.png" alt-text="Um diagrama do exemplo de mineração de opiniões" lightbox="../media/how-tos/opinion-mining.png":::

Para incluir a mineração de opiniões nos resultados, inclua o sinalizador `opinionMining=true` em uma solicitação de análise de sentimento. Os resultados da mineração de opiniões serão incluídos na resposta da análise de sentimento. A mineração de opiniões é uma extensão da Análise de Sentimento e está incluída no seu [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) atual.


## <a name="sending-a-rest-api-request"></a>Como enviar uma solicitação da API REST 

### <a name="preparation"></a>Preparação

A Análise de Sentimento produz um resultado de maior qualidade quando você fornece partes menores de texto nas quais ela trabalhará. Este é o oposto da extração de frase-chave que executa melhor em grandes blocos de texto. Para obter os melhores resultados de ambas as operações, considere a reestruturação de entradas adequadamente.

Você deve ter documentos JSON neste formato: ID, texto e idioma. A Análise de Sentimento dá suporte a uma ampla variedade de idiomas, com mais opções na versão prévia. Para obter mais informações, consulte [Linguagens com suporte](../language-support.md).

O tamanho do documento deve ser inferior a 5.120 caracteres. Para obter o número máximo de documentos permitidos em uma coleção, confira o artigo [Limites de dados](../concepts/data-limits.md?tabs=version-3) em Conceitos. A coleção é enviada no corpo da solicitação.

## <a name="structure-the-request"></a>Estruturar a solicitação

Crie uma solicitação POST. Você pode [usar o Postman](text-analytics-how-to-call-api.md) ou o **console de teste da API** nos links de referência a seguir para estruturar e enviar uma rapidamente. 

#### <a name="version-31-preview"></a>[Versão 3.1 – versão prévia](#tab/version-3-1)

[Referência da Análise de Sentimento v3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

[Referência da Análise de Sentimento v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>Pontos de extremidade de solicitação

Defina o ponto de extremidade HTTPS para a Análise de Sentimento usando um recurso da Análise de Texto no Azure ou um [contêiner da Análise de Texto](text-analytics-how-to-install-containers.md) instanciado. É necessário incluir a URL correta para a versão que você deseja usar. Por exemplo:

> [!NOTE]
> Encontre a chave e o ponto de extremidade do recurso da Análise de Texto no portal do Azure. Eles estarão localizados na página de **Início rápido** do recurso, em **Gerenciamento de recursos**. 

#### <a name="version-31-preview"></a>[Versão 3.1 – versão prévia](#tab/version-3-1)

**Análise de Sentimento**

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/sentiment`

**Mineração de opiniões**

Para obter os resultados da mineração de opiniões, inclua o parâmetro `opinionMining=true`. Por exemplo:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/sentiment?opinionMining=true`

Esse parâmetro é definido como `false` por padrão. 

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

**Análise de Sentimento**

Na v3.0, o único ponto de extremidade disponível destina-se à Análise de Sentimento.
 
`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

Defina um cabeçalho de solicitação para incluir sua chave de API de Análise de Texto. No corpo da solicitação, forneça a coleção de documentos JSON preparada para esta análise

### <a name="example-request-for-sentiment-analysis-and-opinion-mining"></a>Exemplo de solicitação de Análise de Sentimento e mineração de opiniões  

Este é um exemplo de conteúdo que você pode enviar para a detecção de sentimento. O formato da solicitação é o mesmo na `v3.0` e na `v3.1-preview`.
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>Postar a solicitação

A análise é executada após o recebimento da solicitação. Para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo, confira a seção [Limites de dados](../overview.md#data-limits) na visão geral.

A API de Análise de Texto é sem estado. Nenhum dado é armazenado em sua conta e os resultados são retornados imediatamente na resposta.


### <a name="view-the-results"></a>Exibir os resultados

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local. Em seguida, importe a saída para um aplicativo que você possa usar para classificar, pesquisar e manipular os dados. Devido ao suporte multilíngue e a emojis, a resposta pode conter deslocamentos de texto. Confira [como processar deslocamentos](../concepts/text-offsets.md) para obter mais informações.

#### <a name="version-31-preview"></a>[Versão 3.1 – versão prévia](#tab/version-3-1)

### <a name="sentiment-analysis-and-opinion-mining-example-response"></a>Exemplo de resposta da Análise de Sentimento e da mineração de opiniões

> [!IMPORTANT]
> Veja a seguir um exemplo de JSON do uso da mineração de opiniões com a Análise de Sentimento, oferecida na v3.1 da API. Se você não solicitar a mineração de opiniões, a resposta da API será a mesma mostrada na guia da **Versão 3.0**.  

A Análise de Sentimento v3.1 pode retornar objetos de resposta para a Análise de Sentimento e a mineração de opiniões.
  
A análise de sentimento retorna um rótulo de sentimento e uma pontuação de confiança para todo o documento e cada frase dentro dele. As pontuações mais próximas a 1 indicam uma maior confiança na classificação do rótulo, enquanto as pontuações inferiores indicam uma menor confiança. Um documento pode ter várias frases, e as pontuações de confiança dentro de cada documento ou frase somam 1.

A mineração de opiniões localizará destinos (substantivos ou verbos) no texto e as avaliações (adjetivos) associadas a eles. Na resposta abaixo, a frase *O restaurante oferecia uma ótima comida e nosso garçom foi simpático* tem dois destinos: *comida* e *garçom*. A propriedade `relations` de cada destino contém um valor `ref` com a referência de URI aos objetos `documents`, `sentences` e `assessments` associados.

A API retorna opiniões como um destino (substantivo ou verbo) e uma avaliação (adjetivo).

```json
{
  "documents": [
    {
      "id": "1",
      "sentiment": "positive",
      "confidenceScores": {
        "positive": 1,
        "neutral": 0,
        "negative": 0
      },
      "sentences": [
        {
          "sentiment": "positive",
          "confidenceScores": {
            "positive": 1,
            "neutral": 0,
            "negative": 0
          },
          "offset": 0,
          "length": 58,
          "text": "The restaurant had great food and our waiter was friendly.",
          "targets": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 25,
              "length": 4,
              "text": "food",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/0"
                }
              ]
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 38,
              "length": 6,
              "text": "waiter",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/1"
                }
              ]
            }
          ],
          "assessments": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 19,
              "length": 5,
              "text": "great",
              "isNegated": false
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 49,
              "length": 8,
              "text": "friendly",
              "isNegated": false
            }
          ]
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

### <a name="sentiment-analysis-example-response"></a>Exemplo de resposta da Análise de Sentimento

A análise de sentimento retorna um rótulo de sentimento e uma pontuação de confiança para todo o documento e cada frase dentro dele. As pontuações mais próximas a 1 indicam uma maior confiança na classificação do rótulo, enquanto as pontuações inferiores indicam uma menor confiança. Um documento pode ter várias frases, e as pontuações de confiança dentro de cada documento ou frase somam 1.

As respostas da Análise de Sentimento v3 contêm rótulos e pontuações de sentimentos para cada frase e documento analisado.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>Resumo

Neste artigo, você conheceu os conceitos e o fluxo de trabalho para a análise de sentimento usando a API de Análise de Texto. Em resumo:

+ A Análise de Sentimento e a mineração de opiniões está disponível para os idiomas selecionados.
+ Documentos JSON no corpo da solicitação incluem um código de idioma, texto e ID.
+ A solicitação POST é feita para um ponto de extremidade `/sentiment` usando uma [chave de acesso personalizada e um ponto de extremidade](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que é válido para sua assinatura.
+ Use `opinionMining=true` nas solicitações de Análise de Sentimento para obter os resultados da mineração de opiniões.
+ Saída de resposta, que pontuação de sentimento para cada ID do documento, pode ser transmitida para qualquer aplicativo que aceita JSON. Por exemplo, Excel e Power BI.

## <a name="see-also"></a>Confira também

* [Visão geral da Análise de Texto](../overview.md)
* [Como usar a biblioteca de clientes da Análise de Texto](../quickstarts/client-libraries-rest-api.md)
* [Novidades](../whats-new.md)
