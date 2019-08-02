---
title: 'Início Rápido: Criar base de dados de conhecimento – REST, Python – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este início rápido baseado em REST do Python orienta você pela criação programática de um exemplo de base de dados de conhecimento do QnA Maker, que será exibida no Painel do Azure de sua conta da API dos Serviços Cognitivos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 43ae6e7100efd923c81a0f9b94867a9101549d9b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559850"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>Início Rápido: Criar uma base de dados de conhecimento no QnA Maker usando Python

Este início rápido ensina a criar e a publicar uma base de dados de conhecimento do QnA Maker de maneira programática. O QnA Maker extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como perguntas frequentes, de [fontes de dados](../Concepts/data-sources-supported.md). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo da solicitação da API. 

Este início rápido chama as APIs de QnA Maker:
* [Criar uma base de dados de conhecimento](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Obter detalhes da operação](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.7](https://www.python.org/downloads/)
* Você precisa ter um serviço QnA Maker. Para recuperar sua chave, selecione Chaves em Gerenciamento de Recursos no painel.

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="create-a-knowledge-base-python-file"></a>Criar um arquivo Python da base de dados de conhecimento

Crie um arquivo chamado `create-new-knowledge-base-3x.py`.

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

Na parte superior de `create-new-knowledge-base-3x.py`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias
Após as dependências necessárias mencionadas acima, adicione as constantes necessárias para acessar o QnA Maker. Substitua o valor da variável `subscriptionKey` pela sua chave do QnA Maker.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=5-13 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Adicionar a definição de modelo da base de dados de conhecimento

Após as constantes, adicione a seguinte definição de modelo da base de dados de conhecimento. O modelo é convertido em uma cadeia de caracteres após a definição.

[!code-python[Add the KB model definition](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=15-41 "Add the KB model definition")]

## <a name="add-supporting-function"></a>Adicionar funções de suporte

Adicione a seguinte função para imprimir JSON em um formato legível:

[!code-python[Add supporting function](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=43-45 "Add supporting function")]

## <a name="add-function-to-create-kb"></a>Adicionar funções para criar a base de dados de conhecimento

Adicione a seguinte função para fazer uma solicitação HTTP POST para criar a base de dados de conhecimento. Essa chamada à API retorna uma resposta JSON que inclui a ID da operação no campo **Local** do cabeçalho. Use a ID da operação para determinar se a base de dados de conhecimento foi criada com êxito. O `Ocp-Apim-Subscription-Key` é a chave de serviço do QnA Maker, usada para autenticação. 

[!code-python[Add function to create KB](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=48-59 "Add function to create KB")]

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

[!code-python[Add function to check creation status](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=61-67 "Add function to check creation status")]

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

[!code-python[Add main code block](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=70-96 "Add main code block")]

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Insira o comando a seguir em uma linha de comando para executar o programa. Ele enviará a solicitação para a API de QnA Maker para criar a base de dados de conhecimento e, em seguida, sondará os resultados a cada 30 segundos. Todas as respostas são impressas na janela do console.

```bash
python create-new-knowledge-base-3x.py
```

Depois que sua base de dados de conhecimento tiver sido criada, você poderá exibi-la em seu Portal do QnA Maker, página [Minhas bases de dados de conhecimento](https://www.qnamaker.ai/Home/MyServices). Selecione o nome da base de dados de conhecimento, por exemplo, Perguntas frequentes do QnA Maker, a exibir.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
