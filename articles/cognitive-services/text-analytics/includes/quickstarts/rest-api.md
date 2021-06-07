---
title: 'Início Rápido: usar a cURL para chamar a API REST de Análise de Texto'
titleSuffix: Azure Cognitive Services
description: Este início rápido mostra como começar a usar rapidamente a API de Análise de Texto nos Serviços Cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/09/2021
ms.author: aahi
ms.openlocfilehash: 52c20a88c9a4a70b1608a7d050e4faccef9e2d3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599015"
---
# <a name="version-31-preview"></a>[Versão 3.1, versão prévia](#tab/version-3-1)

[Documentação de referência da v3.1](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-4/)

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

[Documentação de referência da v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0) 

---

## <a name="prerequisites"></a>Pré-requisitos

* A versão atual do [cURL](https://curl.haxx.se/).
* Após ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Crie um recurso de Análise de Texto"  target="_blank">criar um recurso de Análise de Texto </a> no portal do Azure para obter sua chave e seu ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API de Análise de Texto. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

> [!NOTE]
> * Os exemplos de BASH a seguir usam o caractere de continuação de linha `\`. Se o console ou o terminal usar um caractere de continuação de linha diferente, use aquele caractere.
> * Você pode encontrar exemplos específicos de cada linguagem de programação no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code).
> * Acesse o portal do Azure e localize a chave e o ponto de extremidade do recurso Análise de Texto criados nos pré-requisitos. Eles estarão localizados na página de **chave e ponto de extremidade** do recurso, em **gerenciamento de recursos**. Em seguida, substitua as cadeias de caracteres no código abaixo pela chave e o ponto de extremidade.
Para chamar a API de Análise de Texto, você precisará das seguintes informações:


|parâmetro  |Descrição  |
|---------|---------|
|`-X POST <endpoint>`     | Especifica o ponto de extremidade para acessar a API.        |
|`-H Content-Type: application/json`     | Tipo de conteúdo para enviar dados JSON.          |
|`-H "Ocp-Apim-Subscription-Key:<key>`    | Especifica a chave para acessar a API.        |
|`-d <documents>`     | JSON contendo os documentos que você deseja enviar.         |

Os comandos cURL a seguir são executados por meio de um shell BASH. Edite esses comandos com o nome do recurso, a chave do recurso e os valores de JSON desejados.

## <a name="sentiment-analysis"></a>Análise de Sentimento

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[versão 3.1-preview](#tab/version-3-1)

> [!NOTE]
> O exemplo abaixo inclui uma solicitação para o recurso de Mineração de Opiniões da Análise de Sentimento usando o parâmetro `opinionMining=true`, que fornece informações granulares sobre as avaliações (adjetivos) relacionadas aos destinos (substantivos) no texto.

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.4/sentiment?opinionMining=true \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "The customer service here is really good."}]}'
```

### <a name="json-response"></a>Resposta JSON

```json
{
   "documents":[
      {
         "id":"1",
         "sentiment":"positive",
         "confidenceScores":{
            "positive":1.0,
            "neutral":0.0,
            "negative":0.0
         },
         "sentences":[
            {
               "sentiment":"positive",
               "confidenceScores":{
                  "positive":1.0,
                  "neutral":0.0,
                  "negative":0.0
               },
               "offset":0,
               "length":41,
               "text":"The customer service here is really good.",
               "targets":[
                  {
                     "sentiment":"positive",
                     "confidenceScores":{
                        "positive":1.0,
                        "negative":0.0
                     },
                     "offset":4,
                     "length":16,
                     "text":"customer service",
                     "relations":[
                        {
                           "relationType":"assessment",
                           "ref":"#/documents/0/sentences/0/assessments/0"
                        }
                     ]
                  }
               ],
               "assessments":[
                  {
                     "sentiment":"positive",
                     "confidenceScores":{
                        "positive":1.0,
                        "negative":0.0
                     },
                     "offset":36,
                     "length":4,
                     "text":"good",
                     "isNegated":false
                  }
               ]
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-04-01"
}
``` 

#### <a name="version-30"></a>[versão 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/sentiment/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "I had the best day of my life. I wish you were there with me."}]}'
```

### <a name="json-response"></a>Resposta JSON

```json
{
  "documents":[
    {
      "id":"1",
      "sentiment":"positive",
      "documentScores":{
        "positive":1.0,
        "neutral":0.0,
        "negative":0.0
      },
      "sentences":[
        {
          "sentiment":"positive",
          "sentenceScores":{
            "positive":1.0,
            "neutral":0.0,
            "negative":0.0
          },
          "offset":0,
          "length":30
        }
      ]
    }
  ],
  "errors":[
  ],
  "modelVersion":"2019-10-01"
}
```

---


## <a name="language-detection"></a>Detecção de idioma

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[versão 3.1-preview](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.4/languages/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "This is a document written in English."}]}'
```

### <a name="json-response"></a>Resposta JSON

```json
{
   "documents":[
      {
         "id":"1",
         "detectedLanguage":{
            "name":"English",
            "iso6391Name":"en",
            "confidenceScore":1.0
         },
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2021-01-05"
}
```

#### <a name="version-30"></a>[versão 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/languages/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "This is a document written in English."}]}'
```

### <a name="json-response"></a>Resposta JSON

```json
{
   "documents":[
      {
         "id":"1",
         "detectedLanguage":{
            "name":"English",
            "iso6391Name":"en",
            "confidenceScore":0.99
         },
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-09-01"
}
```

---


## <a name="named-entity-recognition-ner"></a>NER (Reconhecimento de Entidade Nomeada)

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[versão 3.1-preview](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.4/entities/recognition/general \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```


### <a name="json-response"></a>Resposta JSON

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "text":"Seattle",
               "category":"Location",
               "subcategory":"GPE",
               "offset":26,
               "length":7,
               "confidenceScore":0.99
            },
            {
               "text":"last week",
               "category":"DateTime",
               "subcategory":"DateRange",
               "offset":34,
               "length":9,
               "confidenceScore":0.8
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2021-01-15"
}

```

### <a name="detecting-personally-identifying-information"></a>Detectando informações de identificação pessoal

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

```bash
curl -X POST https://your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.4/entities/recognition/pii \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Call our office at 312-555-1234, or send an email to support@contoso.com"}]}'
```

### <a name="json-response"></a>Resposta JSON

```json
{
   "documents":[
      {
         "redactedText":"Call our office at ************, or send an email to *******************",
         "id":"1",
         "entities":[
            {
               "text":"312-555-1234",
               "category":"PhoneNumber",
               "offset":19,
               "length":12,
               "confidenceScore":0.8
            },
            {
               "text":"support@contoso.com",
               "category":"Email",
               "offset":53,
               "length":19,
               "confidenceScore":0.8
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2021-01-15"
}
```

#### <a name="version-30"></a>[versão 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/entities/recognition/general \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```


### <a name="json-response"></a>Resposta JSON

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "text":"trip",
               "category":"Event",
               "offset":18,
               "length":4,
               "confidenceScore":0.61
            },
            {
               "text":"Seattle",
               "category":"Location",
               "subcategory":"GPE",
               "offset":26,
               "length":7,
               "confidenceScore":0.82
            },
            {
               "text":"last week",
               "category":"DateTime",
               "subcategory":"DateRange",
               "offset":34,
               "length":9,
               "confidenceScore":0.8
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-04-01"
}
```

---

## <a name="entity-linking"></a>Vinculação de entidade

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[versão 3.1-preview](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.4/entities/linking \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975."}]}'
```

### <a name="json-response"></a>Resposta JSON

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "name":"Microsoft",
               "matches":[
                  {
                     "text":"Microsoft",
                     "offset":0,
                     "length":9,
                     "confidenceScore":0.48
                  }
               ],
               "language":"en",
               "id":"Microsoft",
               "url":"https://en.wikipedia.org/wiki/Microsoft",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"0d47c987-0042-5576-15e8-97af601614fa",
               "name":"Bill Gates",
               "matches":[
                  {
                     "text":"Bill Gates",
                     "offset":25,
                     "length":10,
                     "confidenceScore":0.52
                  }
               ],
               "language":"en",
               "id":"Bill Gates",
               "url":"https://en.wikipedia.org/wiki/Bill_Gates",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"df2c4376-9923-6a54-893f-2ee5a5badbc7",
               "name":"Paul Allen",
               "matches":[
                  {
                     "text":"Paul Allen",
                     "offset":40,
                     "length":10,
                     "confidenceScore":0.54
                  }
               ],
               "language":"en",
               "id":"Paul Allen",
               "url":"https://en.wikipedia.org/wiki/Paul_Allen",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"52535f87-235e-b513-54fe-c03e4233ac6e",
               "name":"April 4",
               "matches":[
                  {
                     "text":"April 4",
                     "offset":54,
                     "length":7,
                     "confidenceScore":0.38
                  }
               ],
               "language":"en",
               "id":"April 4",
               "url":"https://en.wikipedia.org/wiki/April_4",
               "dataSource":"Wikipedia"
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-02-01"
}
```

#### <a name="version-30"></a>[versão 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/entities/linking \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975."}]}'
```


### <a name="json-response"></a>Resposta JSON

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "name":"Microsoft",
               "matches":[
                  {
                     "text":"Microsoft",
                     "offset":0,
                     "length":9,
                     "confidenceScore":0.48
                  }
               ],
               "language":"en",
               "id":"Microsoft",
               "url":"https://en.wikipedia.org/wiki/Microsoft",
               "dataSource":"Wikipedia"
            },
            {
               "name":"Bill Gates",
               "matches":[
                  {
                     "text":"Bill Gates",
                     "offset":25,
                     "length":10,
                     "confidenceScore":0.52
                  }
               ],
               "language":"en",
               "id":"Bill Gates",
               "url":"https://en.wikipedia.org/wiki/Bill_Gates",
               "dataSource":"Wikipedia"
            },
            {
               "name":"Paul Allen",
               "matches":[
                  {
                     "text":"Paul Allen",
                     "offset":40,
                     "length":10,
                     "confidenceScore":0.54
                  }
               ],
               "language":"en",
               "id":"Paul Allen",
               "url":"https://en.wikipedia.org/wiki/Paul_Allen",
               "dataSource":"Wikipedia"
            },
            {
               "name":"April 4",
               "matches":[
                  {
                     "text":"April 4",
                     "offset":54,
                     "length":7,
                     "confidenceScore":0.38
                  }
               ],
               "language":"en",
               "id":"April 4",
               "url":"https://en.wikipedia.org/wiki/April_4",
               "dataSource":"Wikipedia"
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-02-01"
}
```


---


## <a name="key-phrase-extraction"></a>Extração de frases-chave

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]


#### <a name="version-31-preview"></a>[versão 3.1-preview](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.4/keyPhrases \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```

```json
{
   "documents":[
      {
         "id":"1",
         "keyPhrases":[
            "wonderful trip",
            "Seattle",
            "week"
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-07-01"
}
```

#### <a name="version-30"></a>[versão 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/keyPhrases \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```

### <a name="json-response"></a>Resposta JSON

```json
{
   "documents":[
      {
         "id":"1",
         "keyPhrases":[
            "wonderful trip",
            "Seattle",
            "week"
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-07-01"
}
```

---
