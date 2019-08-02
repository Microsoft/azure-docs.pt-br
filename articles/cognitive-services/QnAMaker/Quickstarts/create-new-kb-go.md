---
title: 'Início Rápido: Criar base de dados de conhecimento – REST, Go – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este início rápido baseado em REST de Go orienta você pela criação programática de um exemplo de base de dados de conhecimento do QnA Maker, que será exibida no Painel do Azure de sua conta da API dos Serviços Cognitivos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: a6ee10c998af564630ae1aefd01ec2f4e360abf8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563059"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Início Rápido: Criar uma base de dados de conhecimento no QnA Maker usando Go

Este início rápido ensina a criar uma base de dados de conhecimento do QnA Maker de maneira programática. O QnA Maker extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como perguntas frequentes, de [fontes de dados](../Concepts/data-sources-supported.md). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo da solicitação da API. 

Este início rápido chama as APIs de QnA Maker:
* [Criar uma base de dados de conhecimento](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Obter detalhes da operação](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar sua chave, selecione **Teclas** em **Gerenciamento de Recursos** no seu painel. 

## <a name="create-a-knowledge-base-go-file"></a>Criar um arquivo Go de base de dados de conhecimento

Crie um arquivo chamado `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

Na parte superior de `create-new-knowledge-base.go`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias
Após as dependências necessárias mencionadas acima, adicione as constantes necessárias para acessar o QnA Maker. Substitua o valor da variável `subscriptionKey` pela sua chave do QnA Maker.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Adicionar a definição de modelo da base de dados de conhecimento
Após as constantes, adicione a seguinte definição de modelo da base de dados de conhecimento. O modelo é convertido em uma cadeia de caracteres após a definição.

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>Adicionar funções e estruturas de suporte

Depois, adicione as seguintes funções de suporte.

1. Adicione a estrutura para uma solicitação HTTP:

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. Adicione o seguinte método para tratar uma solicitação POST às APIs de QnA Maker. Para este início rápido, a solicitação POST é usada para enviar a definição da base de dados de conhecimento para o QnA Maker.

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. Adicione o seguinte método para tratar uma solicitação GET às APIs de QnA Maker. Para este início rápido, a solicitação GET é usada para verificar o status da operação de criação. 

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>Adicionar funções para criar a base de dados de conhecimento

Adicione as seguintes funções para fazer uma solicitação HTTP POST para criar a base de dados de conhecimento. A _ID da Operação_**de criação** é retornada no **Local** do campo de cabeçalho da resposta POST e, em seguida, usada como parte da rota na solicitação GET. O `Ocp-Apim-Subscription-Key` é a chave de serviço do QnA Maker, usada para autenticação. 

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

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

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

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

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


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
> [Referência da API REST do QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
