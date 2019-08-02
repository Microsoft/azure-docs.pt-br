---
title: 'Início Rápido: Criar base de dados de conhecimento – REST, Java – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este início rápido baseado em REST de Java orienta você pela criação programática de um exemplo de base de dados de conhecimento do QnA Maker, que será exibida no Painel do Azure de sua conta da API dos Serviços Cognitivos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 767866fd6768237c4d3a0d7ea422521254414fb3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559870"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Início Rápido: Criar uma base de dados de conhecimento no QnA Maker usando o Java

Este início rápido ensina a criar uma base de dados de conhecimento do QnA Maker de maneira programática. O QnA Maker extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como perguntas frequentes, de [fontes de dados](../Concepts/data-sources-supported.md). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo da solicitação da API.

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

## <a name="create-a-knowledge-base-file"></a>Criar um arquivo de base de dados de conhecimento

Criar um arquivo chamado `CreateKB.java`

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

Na parte superior de `CreateKB.java`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias
Após as dependências necessárias mencionadas acima, adicione as constantes necessárias à classe `CreateKB` para acessar o QnA Maker. Substitua o valor da variável `subscriptionKey` pela sua chave do QnA Maker. Você não precisa adicionar o colchete final para encerrar a classe; é o trecho de código final no final deste início rápido.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>Adicionar as classes de definição de modelo da base de dados de conhecimento
Após as constantes, adicione as seguintes classes e funções dentro da classe `CreateKB` para serializar o objeto de definição de modelo em JSON.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>Adicionar funções de suporte

Depois, adicione as seguintes funções de suporte dentro da classe `CreateKB`.

1. Adicione a seguinte função para imprimir JSON em um formato legível:

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. Adicione a seguinte classe para gerenciar a resposta HTTP:

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. Adicione o seguinte método para fazer uma solicitação POST às APIs de QnA Maker. O `Ocp-Apim-Subscription-Key` é a chave de serviço do QnA Maker, usada para autenticação.

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. Adicione o seguinte método para fazer uma solicitação GET às APIs de QnA Maker.

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>Adicione um método para criar a base de dados de conhecimento
Adicione o seguinte método para criar a base de dados de conhecimento chamando o método Post.

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

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

## <a name="add-a-method-to-get-status"></a>Adicionar um método para obter o status
Adicione o seguinte método para verificar o status da criação.

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

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

## <a name="add-a-main-method"></a>Adicionar um método principal
O método principal cria a base de dados de conhecimento, em seguida, sonda o status. A ID da operação de criação é retornada no **Local** do campo de cabeçalho da resposta POST e, em seguida, é usada como parte da rota na solicitação GET. O loop `while` repete o status se não for concluído.

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>Compilar e executar o programa

1. Verifique se a biblioteca gson está no diretório `./libs`. Na linha de comando, compile o arquivo `CreateKB.java`:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Insira o comando a seguir em uma linha de comando para executar o programa. Ele enviará a solicitação para a API de QnA Maker para criar a base de dados de conhecimento e, em seguida, sondará os resultados a cada 30 segundos. Todas as respostas são impressas na janela do console.

    ```base
    java -cp ",;libs/*" CreateKB
    ```

Depois que sua base de dados de conhecimento tiver sido criada, você poderá exibi-la em seu Portal do QnA Maker, página [Minhas bases de dados de conhecimento](https://www.qnamaker.ai/Home/MyServices).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
