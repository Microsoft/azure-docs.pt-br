---
title: Verificar a instância de contêiner de Detecção de Idioma
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância de contêiner de Detecção de Idioma.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 543a4d85982adadc86435819679351c8ffaa9814
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009836"
---
### <a name="verify-the-language-detection-container-instance"></a>Verificar a instância de contêiner de Detecção de Idioma

1. Selecione a guia **visão geral** e copie o endereço IP.
1. Abra uma nova guia do navegador e insira o endereço IP. Por exemplo, digite `http://<IP-address>:5000 (http://55.55.55.55:5000` ). O home page do contêiner é exibido, o que permite que você saiba que o contêiner está em execução.

    ![Exibir o home page do contêiner para verificar se ele está em execução](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecione o link de **Descrição da API de serviço** para ir para a página do Swagger do contêiner.

1. Escolha qualquer uma das APIs **post** e selecione **experimentar**. Os parâmetros são exibidos, o que inclui esta entrada de exemplo:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Defina **o** de status como `true` .

1. Selecione **executar** para determinar a suopinião do texto.

    O modelo que é empacotado no contêiner gera uma pontuação que varia de 0 a 1, em que 0 é uma opinião negativa e 1 é uma opinião positiva.

    A resposta JSON retornada inclui um sentimentos para a entrada de texto atualizada:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

Agora podemos correlacionar os documentos dos dados JSON da carga de resposta aos documentos de carga de solicitação originais por seus correspondentes `id` . Cada documento é tratado de forma independente, contendo várias estatísticas, como `characterCount` e `transactionCount` . Além disso, cada documento resultante tem a `detectedLanguages` matriz com o `name` , `iso6391Name` e `score` para cada idioma detectado. Quando vários idiomas são detectados, o `score` é usado para determinar a linguagem mais provável.