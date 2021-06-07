---
title: Usar o reconhecimento de entidade com a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Saiba como identificar e remover ambiguidades da identidade de uma entidade encontrada no texto com a API REST da Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 903147b6c9f102b83f65d67cf41d4df0c62c7b54
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280557"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Como usar o Reconhecimento de Entidade Nomeada na Análise de Texto

A API de Análise de Texto permite usar texto não estruturado e retorna uma lista de entidades sem ambiguidade, com links para mais informações na Web. A API dá suporte ao NER (Reconhecimento de Entidade Nomeada) para várias categorias de entidade e à vinculação de entidade.

## <a name="entity-linking"></a>Vinculação de Identidade

Vinculação de entidade é a capacidade de identificar e remover ambiguidades da identidade de uma entidade encontrada no texto (por exemplo, determinar se uma ocorrência da palavra "Marte" se refere ao planeta ou ao deus romano da guerra). Esse processo requer a presença de uma base de dados de conhecimento em um idioma apropriado para vincular as entidades reconhecidas no texto. A vinculação de entidade usa a [Wikipédia](https://www.wikipedia.org/) como base de dados de conhecimento.

## <a name="named-entity-recognition-ner"></a>NER (Reconhecimento de Entidade Nomeada)

O NER (Reconhecimento de Entidade Nomeada) é a capacidade de identificar diferentes entidades no texto e categorizá-las em classes ou tipos predefinidos, como: pessoa, local, evento, produto e organização.  

## <a name="personally-identifiable-information-pii"></a>PII (Informações de Identificação Pessoal)

O recurso de PII faz parte do NER e pode identificar e editar entidades confidenciais no texto associadas a uma pessoa individual, como: número de telefone, endereço de email, endereço para correspondência, número do passaporte.

## <a name="named-entity-recognition-features-and-versions"></a>Recursos e versões do Reconhecimento de Entidade Nomeada

| Recurso                                                         | NER v3.0 | NER v3.1-preview.4 |
|-----------------------------------------------------------------|--------|----------|
| Métodos para solicitações únicas e em lote                          | X      | X        |
| Reconhecimento de entidade expandido em várias categorias           | X      | X        |
| Pontos de extremidade separados para enviar solicitações de NER e vinculação de entidade. | X      | X        |
| Reconhecimento de entidades de informações pessoais (`PII`) e de saúde (`PHI`)        |        | X        |
| Edição de `PII`        |        | X        |

Confira [suporte a idiomas](../language-support.md) para obter mais informações.

O Reconhecimento de Entidade Nomeada v3 fornece detecção expandida em vários tipos. Atualmente, o NER v3.0 pode reconhecer entidades na [categoria de entidades geral](../named-entity-types.md).

O Reconhecimento de Entidade Nomeada v3.1-preview.4 inclui os recursos de detecção do v3.0 e: 
* A capacidade de detectar informações pessoais (`PII`) usando o ponto de extremidade `v3.1-preview.4/entities/recognition/pii`. 
* Um parâmetro `domain=phi` opcional para detectar informações de saúde confidenciais (`PHI`).
* [Operação assíncrona](text-analytics-how-to-call-api.md) usando o ponto de extremidade `/analyze`.

Para obter mais informações, confira o artigo [categorias de entidade](../named-entity-types.md) e a seção [pontos de extremidade de solicitação](#request-endpoints) abaixo. Para obter mais informações sobre as pontuações de confiança, confira a [observação sobre a transparência da Análise de Texto](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context). 

## <a name="sending-a-rest-api-request"></a>Como enviar uma solicitação da API REST

### <a name="preparation"></a>Preparação

Você precisa ter documentos JSON neste formato: ID, texto, idioma.

Cada documento precisa ter menos de 5.120 caracteres, e você pode ter até mil itens (IDs) por coleção. A coleção é enviada no corpo da solicitação.

### <a name="structure-the-request"></a>Estruturar a solicitação

Crie uma solicitação POST. Você pode [usar o Postman](text-analytics-how-to-call-api.md) ou o **console de teste da API** nos links a seguir para estruturar e enviar uma rapidamente. 

> [!NOTE]
> Você pode encontrar sua chave e ponto de extremidade para o recurso da Análise de Texto no portal do Azure. Eles estarão localizados na página de **Início rápido** do recurso, em **Gerenciamento de recursos**. 


### <a name="request-endpoints"></a>Pontos de extremidade de solicitação

#### <a name="version-31-preview"></a>[Versão 3.1 – versão prévia](#tab/version-3-preview)

O Reconhecimento de Entidade Nomeada `v3.1-preview.4` usa pontos de extremidade separados para solicitações de NER, PII e vinculação de entidade. Use um formato de URL abaixo com base em sua solicitação.

**Vinculação de entidade**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/linking`

[Referência do Reconhecimento de Entidade Nomeada versão 3.1-preview para `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-4/operations/EntitiesLinking)

**Reconhecimento de entidade nomeada**
* Entidades gerais – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/recognition/general`

[Referência do Reconhecimento de Entidade Nomeada versão 3.1-preview para `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-4/operations/EntitiesRecognitionGeneral)

**PII (Informações de Identificação Pessoal)**
* Informações pessoais (`PII`) – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/recognition/pii`

Você também pode usar o parâmetro `domain=phi` opcional para detectar informações de integridade (`PHI`) o texto. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/recognition/pii?domain=phi`

Começando no `v3.1-preview.4`, a resposta JSON inclui uma propriedade `redactedText`, que contém o texto de entrada modificado em que as entidades de PII detectadas são substituídas por um `*` para cada caractere nas entidades.

[Referência do Reconhecimento de Entidade Nomeada versão 3.1-preview para `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-4/operations/EntitiesRecognitionPii)

A API tentará detectar as [categorias de entidade listadas](../named-entity-types.md?tabs=personal) para um determinado idioma do documento. Se você quiser especificar quais entidades serão detectadas e retornadas, use o parâmetro pii-categories opcional com as categorias de entidade apropriadas. Esse parâmetro também pode permitir que você detecte entidades que não estão habilitadas por padrão para o idioma do documento. Por exemplo, um número de carteira de motorista em francês que pode ocorrer em um texto em inglês.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/recognition/pii?piiCategories=[FRDriversLicenseNumber]`

**Operação assíncrona**

Começando no `v3.1-preview.4`, você pode enviar solicitações de NER e vinculação de entidade de maneira assíncrona usando o ponto de extremidade `/analyze`.

* Operação assíncrona – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/analyze`

Confira [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md) para obter informações sobre como enviar solicitações assíncronas.

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

O Reconhecimento de Entidade Nomeada v3 usa pontos de extremidade separados para solicitações de NER e vinculação de entidade. Use um formato de URL abaixo com base em sua solicitação:

**Vinculação de entidade**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Referência do Reconhecimento de Entidade Nomeada versão 3.0 para `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

**Reconhecimento de entidade nomeada**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Referência do Reconhecimento de Entidade Nomeada versão 3.0 para `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

Defina um cabeçalho de solicitação para incluir sua chave de API de Análise de Texto. No corpo da solicitação, forneça os documentos JSON que você preparou.

## <a name="example-requests"></a>Exemplos de solicitações

#### <a name="version-31-preview"></a>[Versão 3.1 – versão prévia](#tab/version-3-preview)

### <a name="example-synchronous-ner-request"></a>Exemplo de solicitação de NER síncrona 

O JSON a seguir é um exemplo de conteúdo que você pode enviar à API. O formato da solicitação é o mesmo para as duas versões da API.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

### <a name="example-asynchronous-ner-request"></a>Exemplo de solicitação de NER assíncrona

Se usar o ponto de extremidade `/analyze` para a [operação assíncrona](text-analytics-how-to-call-api.md), você receberá uma resposta contendo as tarefas que enviou para a API.

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}
```

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

### <a name="example-synchronous-ner-request"></a>Exemplo de solicitação de NER síncrona 

A versão 3.0 inclui apenas a operação síncrona. O JSON a seguir é um exemplo de conteúdo que você pode enviar à API. O formato da solicitação é o mesmo para as duas versões da API.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

---

## <a name="post-the-request"></a>Postar a solicitação

A análise é executada após o recebimento da solicitação. Confira o artigo [Limites de dados](../overview.md#data-limits) para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo.

A API de Análise de Texto é sem estado. Nenhum dado é armazenado em sua conta e os resultados são retornados imediatamente na resposta.

## <a name="view-results"></a>Exibir os resultados

Todas as solicitações POST retornam uma resposta formatada em JSON com as IDs e as propriedades de entidade detectadas.

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local e, em seguida, importá-lo para um aplicativo que permite que você classifique, pesquise e manipule os dados. Devido ao suporte multilíngue e a emojis, a resposta pode conter deslocamentos de texto. Para obter mais informações, confira [como processar deslocamentos de texto](../concepts/text-offsets.md).

### <a name="example-responses"></a>Respostas de exemplo

A versão 3 fornece pontos de extremidade separados para NER, PII e vinculação de entidade geral. A versão 3.1-preview inclui um modo de análise assíncrona. As respostas dessas operações estão abaixo. 

#### <a name="version-31-preview"></a>[Versão 3.1 – versão prévia](#tab/version-3-preview)

### <a name="synchronous-example-results"></a>Resultados do exemplo síncrono

Exemplo de uma resposta de NER geral:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

Exemplo de uma resposta de PII:

```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

Exemplo de uma resposta de vinculação de entidade:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```

### <a name="example-asynchronous-result"></a>Resultado do exemplo assíncrono

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Exemplo de uma resposta de NER geral:
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
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

Neste artigo, você aprendeu os conceitos e fluxo de trabalho para detecção de idioma usando a Análise de Texto em Serviços Cognitivos. Em resumo:

* Documentos JSON no corpo da solicitação incluem um código de idioma, texto e ID.
* As solicitações POST são enviadas a um ou mais pontos de extremidade usando uma [chave de acesso personalizada e um ponto de extremidade](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que é válido para sua assinatura.
* A saída da resposta, composta por entidades vinculadas (incluindo pontuações de confiança, deslocamentos e links da Web, para cada ID de documento) pode ser usada em qualquer aplicativo

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da Análise de Texto](../overview.md)
* [Como usar a biblioteca de clientes da Análise de Texto](../quickstarts/client-libraries-rest-api.md)
* [Novidades](../whats-new.md)
