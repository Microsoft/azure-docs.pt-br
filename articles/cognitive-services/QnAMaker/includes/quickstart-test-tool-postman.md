---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 11/09/2020
ms.openlocfilehash: fa497b69b067d5556f11effdb52505895ecc3bdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94386637"
---
Este Início Rápido baseado no Postman fornece uma orientação para obtenção de uma resposta de uma base de dados de conhecimento.

## <a name="prerequisites"></a>Pré-requisitos

* [**Postman**](https://www.getpostman.com/) mais recente.
* Você precisará ter
    * Um [serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md)
    * Uma [base de dados de conhecimento treinada e publicada com perguntas e respostas](../Quickstarts/add-question-metadata-portal.md) criada com base no início rápido é configurada com metadados e bate-papo.

> [!NOTE]
> Quando você estiver pronto para gerar uma resposta a uma pergunta da base de dados de conhecimento, precisará [treinar](../Quickstarts/create-publish-knowledge-base.md#save-and-train) e [publicar](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) a base de dados de conhecimento. Quando sua base de dados de conhecimento for publicada, a página **Publicar** exibirá as configurações de solicitação HTTP para gerar uma resposta. A guia **Postman** mostra as configurações necessárias para gerar uma resposta.

## <a name="set-up-postman-for-requests"></a>Configurar o Postman para solicitações

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Este início rápido usará as mesmas configurações para a solicitação **POST** do Postman e, em seguida, vai configurar para executar POST do JSON do corpo enviado ao serviço com base no que você está tentando consultar.

Use este procedimento para configurar o Postman e, em seguida, leia cada seção seguinte para configurar o POST do JSON do corpo.

1. Na página **Configurações** da base de dados de conhecimento, selecione a guia **Postman** para ver a configuração usada para gerar uma resposta usando a base de dados de conhecimento. Copie as informações a seguir a serem usadas no Postman.

    |Nome|Configuração|Finalidade e valor|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Esse é o método HTTP e a rota para a URL.|
    |`Host`|`https://YOUR-RESOURCE_NAME.azurewebsites.net/qnamaker`|Este é o host da URL. Concatene os valores de Host e POST para obter a URL completa de generateAnswer.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|O valor do cabeçalho para autorizar a sua solicitação ao Azure. |
    |`Content-type`|`application/json`|O valor do cabeçalho para o conteúdo.|
    ||`{"question":"<Your question>"}`|O corpo da solicitação POST como um objeto JSON. Esse valor será alterado em cada seção a seguir, dependendo do que a consulta pretender fazer.|

1. Abra o Postman e crie uma solicitação **POST** básica com as configurações da base de dados de conhecimento publicada. Nas seções a seguir, altere o JSON do corpo do POST para alterar a consulta à base de dados de conhecimento.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

Este início rápido usará as mesmas configurações para a solicitação **POST** do Postman e, em seguida, vai configurar para executar POST do JSON do corpo enviado ao serviço com base no que você está tentando consultar.

Use este procedimento para configurar o Postman e, em seguida, leia cada seção seguinte para configurar o POST do JSON do corpo.

1. Na página **Configurações** da base de dados de conhecimento, selecione a guia **Postman** para ver a configuração usada para gerar uma resposta usando a base de dados de conhecimento. Copie as informações a seguir a serem usadas no Postman.

    |Nome|Configuração|Finalidade e valor|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Esse é o método HTTP e a rota para a URL.|
    |`Host`|`https://YOUR-RESOURCE_NAME.cognitiveservices.azure.com/qnamaker`|Este é o host da URL. Concatene os valores de Host e POST para obter a URL completa de generateAnswer.|
    |`Ocp-Apim-Subscription-Key`|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|O valor do cabeçalho para autorizar a solicitação. |
    |`Content-type`|`application/json`|O valor do cabeçalho para o conteúdo.|
    ||`{"question":"<Your question>"}`|O corpo da solicitação POST como um objeto JSON. Esse valor será alterado em cada seção a seguir, dependendo do que a consulta pretender fazer.|

1. Abra o Postman e crie uma solicitação **POST** básica com as configurações da base de dados de conhecimento publicada. Nas seções a seguir, altere o JSON do corpo do POST para alterar a consulta à base de dados de conhecimento.
---

## <a name="use-metadata-to-filter-answer"></a>Usar metadados para filtrar a resposta

Em um início rápido anterior, os metadados foram adicionados a dois pares de QnA para fazer a distinção entre duas perguntas diferentes. Adicione os metadados à consulta para restringir o filtro apenas ao par de QnA relevante.

1. No Postman, altere apenas o JSON da consulta adicionando a propriedade `strictFilters` com o par nome/valor `service:qna_maker`. O JSON do corpo deverá ser:

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    A pergunta é apenas uma só palavra, `size`, que pode retornar um dos dois pares de perguntas e respostas. A matriz `strictFilters` instrui a resposta a reduzir para apenas as respostas `qna_maker`.

1. A resposta inclui apenas a resposta que atende aos critérios de filtro.

    A seguinte resposta foi formatada para facilitar a leitura:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Se houver um par de perguntas e respostas que não correspondeu ao termo de pesquisa, mas que correspondeu ao filtro, ele não será retornado. Em vez disso, a resposta geral `No good match found in KB.` será retornada.

## <a name="use-debug-query-property"></a>Usar a propriedade de consulta de depuração

> [!NOTE]
>Não recomendamos usar a propriedade Debug para nenhuma dependência. Ela foi adicionada para ajudar a equipe do produto com a solução de problemas.

As informações de depuração ajudam você a entender como a resposta retornada foi determinada. Embora isso seja útil, não é necessário. Para gerar uma resposta com informações de depuração, adicione a propriedade `debug`:

1. No Postman, altere apenas o JSON do corpo adicionando a propriedade `debug`. O JSON deverá ser:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. A resposta inclui as informações relevantes sobre a resposta. Na saída JSON a seguir, alguns detalhes de depuração foram substituídos por reticências.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Usar a base de dados de conhecimento de teste

Caso deseje obter uma resposta da base de dados de conhecimento de teste, use a propriedade de corpo `isTest`.

No Postman, altere apenas o JSON do corpo adicionando a propriedade `isTest`. O JSON deverá ser:

```json
{
    'question':'size',
    'isTest': true
}
```

A resposta JSON usa o mesmo esquema da consulta da base de dados de conhecimento publicada.

> [!NOTE]
> Se as bases de dados de conhecimento de teste e publicada forem exatamente iguais, ainda poderá haver uma pequena variação, pois o índice de teste será compartilhado entre todas as bases de dados de conhecimento no recurso.

## <a name="query-for-a-chit-chat-answer"></a>Consultar uma resposta de bate-papo

1. No Postman, altere apenas o JSON do corpo para uma instrução de fim de conversa feita pelo usuário. O JSON deverá ser:

    ```json
    {
        'question':'thank you'
    }
    ```

1. A resposta inclui a pontuação e a resposta.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Como a pergunta `Thank you` correspondeu exatamente a uma pergunta do bate-papo, o QnA Maker está completamente seguro com a pontuação de 100. O QnA Maker também retornou todas as perguntas relacionadas e a propriedade de metadados que contém as informações de marca de metadados de Bate-papo.

## <a name="use-threshold-and-default-answer"></a>Usar o limite e a resposta padrão

Você pode solicitar um limite mínimo para a resposta. Se o limite não for atendido, a resposta padrão será retornada.

1. No Postman, altere apenas o JSON do corpo para uma instrução de fim de conversa feita pelo usuário. O JSON deverá ser:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    A base de dados de conhecimento não deverá encontrar essa resposta, porque a pontuação da pergunta é de 71% e, em vez disso, retornará a resposta padrão que você forneceu quando criou a base de dados de conhecimento.

    A resposta JSON retornada, incluindo a pontuação e a resposta, é:

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    O QnA Maker retornou uma pontuação igual a `0`, o que significa nenhuma confiança. Ele também retornou uma resposta padrão.

1. Altere o valor do limite para 60% e solicite a consulta novamente:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    O JSON retornado encontrou a resposta.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```