---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 67c95ffcdbdbcfbb9a86e15c91d984953d7bbffc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172235"
---
Para entender o que um ponto de extremidade de previsão do LUIS retorna, exiba um resultado de previsão em um navegador da Web. Para consultar um aplicativo público, você precisa de sua própria chave e a ID do aplicativo. A ID do aplicativo de IoT pública, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, é fornecida como parte da URL na etapa um.

O formato da URL para uma solicitação de ponto de extremidade **GET** é:

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. O ponto de extremidade do aplicativo de IoT público está no seguinte formato: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light`

    Copie a URL e substitua a sua chave pelo valor de `<YOUR_KEY>`.

2. Cole a URL em uma janela do navegador e pressione Enter. O navegador exibe um resultado JSON que indica que o LUIS detecta a intenção `HomeAutomation.TurnOn` como a intenção principal e a entidade `HomeAutomation.Room` com o valor `bedroom`.

    ```JSON
    {
      "query": "turn on the bedroom light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.809439957
      },
      "entities": [
        {
          "entity": "bedroom",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 18,
          "score": 0.8065475
        }
      ]
    }
    ```

3. Altere o valor do parâmetro `q=` na URL para `turn off the living room light`, e pressione enter. O resultado agora indica que o LUIS detectou a intenção `HomeAutomation.TurnOff` como a intenção principal e a entidade `HomeAutomation.Room` com o valor `living room`. 

    ```JSON
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.984057844
      },
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Room",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.9619945
        }
      ]
    }
    ```
