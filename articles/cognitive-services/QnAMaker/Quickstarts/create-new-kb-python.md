---
title: 'Início Rápido: Criar base de dados de conhecimento – REST, Python – QnA Maker'
description: Este início rápido baseado em REST do Python orienta você pela criação programática de um exemplo de base de dados de conhecimento do QnA Maker, que será exibida no Painel do Azure de sua conta da API dos Serviços Cognitivos.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: 9f3f433742ec25a1ee1abb2ede32a38e6b611f14
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352279"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>Início Rápido: Criar uma base de dados de conhecimento no QnA Maker usando Python

Este início rápido ensina a criar e a publicar uma base de dados de conhecimento do QnA Maker de maneira programática. O QnA Maker extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como perguntas frequentes, de [fontes de dados](../index.yml). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo da solicitação da API.

Este início rápido chama as APIs de QnA Maker:
* [Criar uma base de dados de conhecimento](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Obter detalhes da operação](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Documentação](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  de referência [Exemplo de Python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3,7](https://www.python.org/downloads/)
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **Início Rápido** para o recurso no portal do Azure.

## <a name="create-a-knowledge-base-python-file"></a>Criar um arquivo Python da base de dados de conhecimento

Crie um arquivo chamado `create-new-knowledge-base-3x.py`.

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

Na parte superior de `create-new-knowledge-base-3x.py`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="dependencies":::

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias
Após as dependências necessárias mencionadas acima, adicione as constantes necessárias para acessar o QnA Maker. Substitua o valor do `<your-qna-maker-subscription-key>` e do `<your-resource-name>` por sua própria chave e seu próprio nome do recurso do QnA Maker.

Na parte superior da classe Program, adicione as constantes necessárias para acessar o QnA Maker.

Defina os seguintes valores:

* `<your-qna-maker-subscription-key>`– A **chave** é uma cadeia de 32 caracteres e está disponível no portal do Azure, no recurso QnA Maker, na página Início rápido. Isso não é o mesmo que a chave de ponto de extremidade de previsão.
* `<your-resource-name>` – O **nome do recurso** é usado para construir a URL de ponto de extremidade de criação para fins de criação, no formato de `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Essa não é a mesma URL usada para consultar o ponto de extremidade de previsão.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="constants":::

## <a name="add-the-kb-model-definition"></a>Adicionar a definição de modelo da base de dados de conhecimento

Após as constantes, adicione a seguinte definição de modelo da base de dados de conhecimento. O modelo é convertido em uma cadeia de caracteres após a definição.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="model":::

## <a name="add-supporting-function"></a>Adicionar funções de suporte

Adicione a seguinte função para imprimir JSON em um formato legível:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="pretty":::

## <a name="add-function-to-create-kb"></a>Adicionar funções para criar a base de dados de conhecimento

Adicione a seguinte função para fazer uma solicitação HTTP POST para criar a base de dados de conhecimento.
Essa chamada à API retorna uma resposta JSON que inclui a ID da operação no campo **Local** do cabeçalho. Use a ID da operação para determinar se a base de dados de conhecimento foi criada com êxito. O `Ocp-Apim-Subscription-Key` é a chave de serviço do QnA Maker, usada para autenticação.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="create_kb":::

Essa chamada à API retorna uma resposta JSON que inclui a ID da operação. Use a ID da operação para determinar se a base de dados de conhecimento foi criada com êxito.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-function-to-check-creation-status"></a>Adicionar a função para verificar o status de criação

A função a seguir verifica o status de criação ao enviar a ID de operação no final da rota de URL. A chamada para `check_status` está dentro do loop _while_ principal.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="get_status":::

Essa chamada à API retorna uma resposta JSON que inclui o status da operação:

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Repita a chamada até ter um resultado com êxito ou falha:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-main-code-block"></a>Adicionar bloco de código principal
O seguinte loop sonda o status da operação de criação periodicamente até que a operação seja concluída.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="main":::

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Insira o comando a seguir em uma linha de comando para executar o programa. Ele enviará a solicitação para a API de QnA Maker para criar a base de dados de conhecimento e, em seguida, sondará os resultados a cada 30 segundos. Todas as respostas são impressas na janela do console.

```bash
python create-new-knowledge-base-3x.py
```

Depois que sua base de dados de conhecimento tiver sido criada, você poderá exibi-la em seu Portal do QnA Maker, página [Minhas bases de dados de conhecimento](https://www.qnamaker.ai/Home/MyServices). Selecione o nome da base de dados de conhecimento, por exemplo, Perguntas frequentes do QnA Maker, a exibir.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)