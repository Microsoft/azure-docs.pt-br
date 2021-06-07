---
title: Como consultar previsões usando um navegador – LUIS
description: Neste artigo, use um aplicativo LUIS público disponível para determinar a intenção do usuário com base em textos de conversa em um navegador.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 4542f85ab562e65777fb8570873305523c76aee6
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276817"
---
# <a name="how-to-query-the-prediction-runtime-with-user-text"></a>Como consultar o runtime de previsão com o texto do usuário

Para entender o que um ponto de extremidade de previsão do LUIS retorna, exiba um resultado de previsão em um navegador da Web.

## <a name="prerequisites"></a>Pré-requisitos

Para consultar um aplicativo público, você precisará de:

* Suas informações de recurso do LUIS (Reconhecimento vocal):
    * **Chave de previsão**, que pode ser obtida do [Portal do LUIS](https://www.luis.ai/). Caso você ainda não tenha uma assinatura para criar uma chave, registre-se em uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services).
    * **Subdomínio do ponto de extremidade de previsão** – o subdomínio também é o **nome** do recurso LUIS.
* Uma ID do aplicativo LUIS – use a ID do aplicativo IoT público de `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. A consulta de usuário usada no código de início rápido é específica para esse aplicativo.

## <a name="use-the-browser-to-see-predictions"></a>Usar o navegador para ver as previsões

1. Abra um navegador da Web.
1. Use as URLs completas abaixo, substituindo `YOUR-KEY` por sua própria chave de Previsão do LUIS. As solicitações são solicitações GET e incluem a autorização, com a chave de Previsão do LUIS, como um parâmetro de cadeia de consulta.

    #### <a name="v3-prediction-request"></a>[Solicitação de previsão V3](#tab/V3-1-1)


    O formato da URL V3 para uma solicitação de ponto de extremidade **GET** (por slots) é:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[Solicitação de previsão V2](#tab/V2-1-2)

    O formato da URL V2 para uma solicitação de ponto de extremidade **GET** é:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
    `

1. Cole a URL em uma janela do navegador e pressione Enter. O navegador exibe um resultado JSON que indica que o LUIS detecta a intenção `HomeAutomation.TurnOn` como a intenção principal e a entidade `HomeAutomation.Operation` com o valor `on`.

    #### <a name="v3-prediction-response"></a>[Resposta de previsão V3](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-response"></a>[Resposta de previsão V2](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Para ver todas as intenções, adicione o parâmetro de cadeia de consulta apropriado.

    #### <a name="v3-prediction-endpoint"></a>[Ponto de extremidade de previsão V3](#tab/V3-3-1)

    Adicione `show-all-intents=true` ao final da cadeia de consulta para **mostrar todas as intenções**:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpoint"></a>[Ponto de extremidade de previsão V2](#tab/V2)

    Adicione `verbose=true` ao final da cadeia de consulta para **mostrar todas as intenções**:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:
* [Ponto de extremidade de previsão V3](luis-migration-api-v3.md)
* [Subdomínios personalizados](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Usar as bibliotecas de cliente ou a API REST](client-libraries-rest-api.md)
