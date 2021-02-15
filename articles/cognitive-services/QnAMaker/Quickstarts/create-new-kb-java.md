---
title: 'Início Rápido: Criar base de dados de conhecimento - REST, Java - QnA Maker'
description: Este guia de início rápido baseado em REST Java orienta você pela criação de um exemplo QnA Maker base de dados de conhecimento, programaticamente, que aparecerá no seu painel do Azure de sua conta de API de serviços cognitivas.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 50433ee1e5a575ab671d562bfc3fe549b26fe00c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352296"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Início Rápido: criar uma base de dados de conhecimento no QnA Maker usando Java

Este início rápido ensina a criar uma base de dados de conhecimento do QnA Maker de maneira programática. O QnA Maker extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como perguntas frequentes, de [fontes de dados](../index.yml). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo da solicitação da API.

Este início rápido chama as APIs de QnA Maker:
* [Criar uma base de dados de conhecimento](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Obter detalhes da operação](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Documentação](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  de referência [Exemplo de Java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **Início Rápido** para o recurso no portal do Azure.

O [código de exemplo](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) está disponível no repositório GitHub para o QnA Maker com Java.

## <a name="create-a-knowledge-base-file"></a>Criar um arquivo de base de dados de conhecimento

Criar um arquivo chamado `CreateKB.java`

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

Na parte superior de `CreateKB.java`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias
Após as dependências necessárias mencionadas acima, adicione as constantes necessárias à classe `CreateKB` para acessar o QnA Maker.

Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o nome do recurso, selecione **Início Rápido** no portal do Azure para o recurso do QnA Maker.

Defina os seguintes valores:

* `<your-qna-maker-subscription-key>`– A **chave** é uma cadeia de 32 caracteres e está disponível no portal do Azure, no recurso QnA Maker, na página Início rápido. Essa chave não é igual à chave de ponto de extremidade de previsão.
* `<your-resource-name>` – O **nome do recurso** é usado para construir a URL de ponto de extremidade de criação para fins de criação, no formato de `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Esse nome de recurso não é o mesmo usado para consultar o ponto de extremidade de previsão.

Você não precisa adicionar o colchete final para encerrar a classe; é o snippet de código final no final deste início rápido.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="constants":::


## <a name="add-the-kb-model-definition-classes"></a>Adicionar as classes de definição de modelo da base de dados de conhecimento
Após as constantes, adicione as seguintes classes e funções dentro da classe `CreateKB` para serializar o objeto de definição de modelo em JSON.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="model":::

## <a name="add-supporting-functions"></a>Adicionar funções de suporte

Depois, adicione as seguintes funções de suporte dentro da classe `CreateKB`.

1. Adicione a seguinte função para imprimir JSON em um formato legível:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="pretty":::

2. Adicione a seguinte classe para gerenciar a resposta HTTP:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="response":::

3. Adicione o seguinte método para fazer uma solicitação POST às APIs de QnA Maker. O `Ocp-Apim-Subscription-Key` é a chave de serviço do QnA Maker, usada para autenticação.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="post":::

4. Adicione o seguinte método para fazer uma solicitação GET às APIs de QnA Maker.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get":::

## <a name="add-a-method-to-create-the-kb"></a>Adicione um método para criar a base de dados de conhecimento
Adicione o seguinte método para criar a base de dados de conhecimento chamando o método Post.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="create_kb":::

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

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get_status":::

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

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="main":::

## <a name="compile-and-run-the-program"></a>Compilar e executar o programa

1. Verifique se a biblioteca gson está no diretório `./libs`. Na linha de comando, compile o arquivo `CreateKB.java` :

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Insira o comando a seguir em uma linha de comando para executar o programa. Ele enviará a solicitação para a API de QnA Maker para criar a base de dados de conhecimento e, em seguida, sondará os resultados a cada 30 segundos. Todas as respostas são impressas na janela do console.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

Depois que sua base de dados de conhecimento tiver sido criada, você poderá exibi-la em seu Portal do QnA Maker, página [Minhas bases de dados de conhecimento](https://www.qnamaker.ai/Home/MyServices).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)