---
title: 'Início Rápido: criar base de dados de conhecimento – REST, Go – QnA Maker'
description: Este início rápido baseado em REST de Go orienta você pela criação programática de um exemplo de base de dados de conhecimento do QnA Maker, que será exibida no Painel do Azure de sua conta da API dos Serviços Cognitivos.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: cff2d141e8108d9a3e2e12764174a0bf4978182f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352313"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Início Rápido: criar uma base de dados de conhecimento no QnA Maker usando o Go

Este início rápido ensina a criar uma base de dados de conhecimento do QnA Maker de maneira programática. O QnA Maker extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como perguntas frequentes, de [fontes de dados](../index.yml). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo da solicitação da API.

Este início rápido chama as APIs de QnA Maker:
* [Criar uma base de dados de conhecimento](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Obter detalhes da operação](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Documentação](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  de referência [Exemplo go](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **Início Rápido** para o recurso no portal do Azure.

## <a name="create-a-knowledge-base-go-file"></a>Criar um arquivo Go de base de dados de conhecimento

Crie um arquivo chamado `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

Na parte superior de `create-new-knowledge-base.go`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="dependencies":::

## <a name="add-the-kb-model-definition"></a>Adicionar a definição de modelo da base de dados de conhecimento
Após as constantes, adicione a seguinte definição de modelo da base de dados de conhecimento. O modelo é convertido em uma cadeia de caracteres após a definição.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="model":::

## <a name="add-supporting-structures-and-functions"></a>Adicionar funções e estruturas de suporte

Depois, adicione as seguintes funções de suporte.

1. Adicione a estrutura para uma resposta HTTP:

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="response":::

1. Adicione o seguinte método para tratar uma solicitação POST às APIs de QnA Maker. Para este início rápido, a solicitação POST é usada para enviar a definição da base de dados de conhecimento para o QnA Maker.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="post":::

1. Adicione o seguinte método para tratar uma solicitação GET às APIs de QnA Maker. Para este início rápido, a solicitação GET é usada para verificar o status da operação de criação.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get":::

## <a name="add-function-to-create-kb"></a>Adicionar funções para criar a base de dados de conhecimento

Adicione as seguintes funções para fazer uma solicitação HTTP POST para criar a base de dados de conhecimento. A _create_ **ID da operação** de criação é retornada no **local** do campo do cabeçalho de resposta post e, em seguida, usada como parte da rota na solicitação get. O `Ocp-Apim-Subscription-Key` é a chave de serviço do QnA Maker, usada para autenticação.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="create_kb":::

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

## <a name="add-function-to-get-status"></a>Adicionar função para obter o status

Adicione a seguinte função para fazer uma solicitação HTTP GET para verificar o status da operação. O `Ocp-Apim-Subscription-Key` é a chave de serviço do QnA Maker, usada para autenticação.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get_status":::

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
## <a name="add-main-function"></a>Adicionar função principal

A função a seguir é a função principal, que cria a base de dados de conhecimento e repete as verificações de status. Como a criação de KB pode levar algum tempo, você precisará repetir a chamadas para verificar o status até que o status indique êxito ou falha.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="main":::


## <a name="compile-the-program"></a>Compilar o programa
Insira o comando a seguir para compilar o arquivo. O prompt de comando não retorna nenhuma informação para um build bem-sucedido.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>Execute o programa

Insira o comando a seguir em uma linha de comando para executar o programa. Ele enviará a solicitação para a API de QnA Maker para criar a base de dados de conhecimento e, em seguida, sondará os resultados a cada 30 segundos. Todas as respostas são impressas na janela do console.

```bash
go run create-new-knowledge-base
```

Depois que sua base de dados de conhecimento tiver sido criada, você poderá exibi-la em seu Portal do QnA Maker, página [Minhas bases de dados de conhecimento](https://www.qnamaker.ai/Home/MyServices).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)