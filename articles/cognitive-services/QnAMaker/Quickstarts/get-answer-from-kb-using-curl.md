---
title: 'Início Rápido: usar cURL para obter respostas da base de dados de conhecimento – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este Início Rápido fornece uma orientação para obtenção de uma resposta de uma base de dados de conhecimento usando cURL.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: ad4b40d649d4e4cbc17d6aec5d8bc7308012b927
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60913728"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Início Rápido: obter uma resposta da base de dados de conhecimento usando cURL

Este início rápido baseado em cURL fornece uma orientação para obtenção de uma resposta de base de dados de conhecimento.

## <a name="prerequisites"></a>Pré-requisitos

* [**cURL**](https://curl.haxx.se/) mais recente.
* Você deve ter um [serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md) e uma [base de dados de conhecimento com perguntas e respostas](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Publicar para obter ponto de extremidade

Quando você estiver pronto para gerar uma resposta a uma pergunta da sua base de dados de conhecimento, [publique](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) sua base de dados de conhecimento.

## <a name="use-production-endpoint-with-curl"></a>Usar o ponto de extremidade de produção com o cURL

Quando sua base de dados de conhecimento for publicada, a página **Publicar** exibirá as configurações de solicitação HTTP para gerar uma resposta. A guia **CURL** mostra as configurações necessárias para gerar uma resposta da ferramenta de linha de comando, [CURL](https://www.getpostman.com).

[![Publicar os resultados](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Para gerar uma resposta com CURL, conclua as seguintes etapas:

1. Copie o texto na guia CURL. 
1. Abra uma linha de comando ou um terminal e cole o texto.
1. Edite a pergunta para ser relevante para sua base de dados de conhecimento. Tenha cuidado para não remover o JSON que cerca a pergunta.
1. Insira o comando. 
1. A resposta inclui as informações relevantes sobre a resposta. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>Usar o ponto de extremidade de preparo com cURL

Se você quiser obter uma resposta do ponto de extremidade de preparo, use o `isTest` propriedade body.

```json
isTest:true
```

## <a name="next-steps"></a>Próximas etapas

A página de publicação também fornece informações para [gerar uma resposta](get-answer-from-kb-using-postman.md) com Postman. 

> [!div class="nextstepaction"]
> [Usar metadados ao gerar uma resposta](../How-to/metadata-generateanswer-usage.md)
