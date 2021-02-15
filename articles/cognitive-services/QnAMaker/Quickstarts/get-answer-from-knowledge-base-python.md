---
title: 'Início Rápido: Obter respostas da base de dados de conhecimento – REST, Python – QnA Maker'
description: Este início rápido baseado em Python REST orienta você para obter uma resposta de uma base de dados de conhecimento programaticamente.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: d74993d35accea451c5b003ba10df3819d65c43c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345875"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Início rápido: Obtenha respostas para uma pergunta de uma base de dados de conhecimento com Python

Este início rápido o orienta a obter programaticamente uma resposta de uma base de dados de conhecimento publicada do QnA Maker. A base de conhecimento contém perguntas e respostas de [fontes de dados](../index.yml), como perguntas frequentes. A [pergunta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada para o serviço QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta prevista na parte superior.

[Documentação de referência](/rest/api/cognitiveservices/qnamaker4.0/Runtime) | [Amostra](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/get-answer/get-answer-3x.py)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.6 ou superior](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar sua chave, selecione **Chaves** em **Gerenciamento de Recursos** no painel do Azure para seu recurso do QnA Maker.
* Configurações da página **Publicar**. Caso não tenha uma base de dados de conhecimento publicada, crie uma base de dados de conhecimento vazia, importe-a na página **Configurações** e depois publique-a. Você pode baixar e usar [essa base de dados de conhecimento básico](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv).

    As configurações da página de publicação incluem o valor da rota, o valor do Host e o valor do EndpointKey.

    ![Configurações de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-python-file"></a>Criar um arquivo Python

Abra o VSCode e crie um novo arquivo denominado `get-answer-3x.py`.

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

Na parte superior do arquivo `get-answer-3x.py`, adicione as dependências necessárias ao projeto:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="dependencies":::

<!--TBD - reword this following paragraph -->

O host e a rota são diferentes de como eles aparecem na página **Publicar**. Isso ocorre porque a biblioteca do python não permite nenhum roteamento no host. O roteamento que aparece na página **Publicar** como parte do host foi movido para a rota.

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Adicione as constantes necessárias para acessar o QnA Maker. Esses valores ficam na página **Publicar** após a publicação da base de dados de conhecimento.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="constants":::

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Adicionar uma solicitação POST para enviar perguntas e obter uma resposta

O código a seguir faz uma solicitação HTTPS à API de QnA Maker para enviar a pergunta à base de dados de conhecimento e recebe a resposta:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="main":::

O valor do cabeçalho de `Authorization` inclui a cadeia de caracteres `EndpointKey`.

## <a name="run-the-program"></a>Execute o programa

Execute o programa na linha de comando. Ela enviará automaticamente a solicitação para a API de QnA Maker, depois a resposta será impressa na janela do console.

Execute o arquivo:

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Saiba mais sobre a [solicitação](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e a [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)