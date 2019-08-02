---
title: Metadados com a API de GenerateAnswer – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker permite adicionar metadados na forma de pares chave-valor aos conjuntos de perguntas/respostas. Você pode filtrar os resultados para consultas de usuário e armazenar informações adicionais que podem ser usadas nas conversas de acompanhamento.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: 6bfcb531d0e4e8073a5553f7bc84a25e4f8a92a9
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785690"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Obter uma resposta com a API GenerateAnswer e metadados

Para obter a resposta prevista à pergunta de um usuário, use a API GenerateAnswer. Quando você publica uma base de Conhecimento, você pode ver informações sobre como usar essa API na **publicar** página. Você também pode configurar a API para filtrar as respostas com base em marcas de metadados e testar a base de conhecimento do ponto de extremidade com o parâmetro de cadeia de caracteres de consulta de teste.

O QnA Maker permite adicionar metadados, na forma de pares chave-valor, em seus conjuntos de perguntas e respostas. Em seguida, você pode usar essas informações para filtrar resultados para consultas de usuário e para armazenar informações adicionais que podem ser usadas nas conversas de acompanhamento. Para mais informações, consulte a [Base de dados de conhecimento](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Perguntas e respostas com uma entidade de QnA de Store

É importante entender como o QnA Maker armazena os dados de pergunta e resposta. A ilustração a seguir mostra uma entidade QnA:

![Ilustração de uma entidade do QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidade QnA tem uma ID exclusiva e persistente. Você pode usar a ID para fazer atualizações em uma determinada entidade QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obter previsões de resposta com a API GenerateAnswer

Você usa o [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) no bot ou aplicativo para consultar sua base de dados de conhecimento com uma pergunta do usuário obter a melhor correspondência da pergunta e resposta define.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicar para obter o ponto de extremidade GenerateAnswer 

Depois de publicar sua base de dados de Conhecimento, a partir de [portal QnA Maker](https://www.qnamaker.ai), ou usando o [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), você pode obter os detalhes do ponto de extremidade GenerateAnswer.

Para obter os detalhes do ponto de extremidade:
1. Entre em [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. Na **meu bases de Conhecimento**, selecione **Exibir código** para sua base de Conhecimento.
    ![Captura de tela de Meus bases de Conhecimento](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Obtenha os detalhes do ponto de extremidade do GenerateAnswer.

    ![Captura de tela de detalhes do ponto de extremidade](../media/qnamaker-how-to-metadata-usage/view-code.png)

Também é possível obter os detalhes de ponto de extremidade na guia **Configurações** da base de dados de conhecimento.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuração de solicitação GenerateAnswer

Você chama o GenerateAnswer com uma solicitação HTTP POST. Para código de exemplo que mostra como chamar o GenerateAnswer, consulte os [inícios rápidos](../quickstarts/csharp.md). 

Usa a solicitação POST:

* Necessário [parâmetros de URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Exigido [propriedade header](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer), `Authorization`, para segurança
* Exigido [propriedades do corpo](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto). 

A URL GenerateAnswer tem o seguinte formato: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Lembre-se de definir a propriedade de cabeçalho HTTP do `Authorization` com um valor da cadeia de caracteres `EndpointKey` com à direita de espaço, em seguida, a chave de ponto de extremidade encontrada na **configurações** página.

Um exemplo de corpo JSON é semelhante a:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Propriedades da resposta GenerateAnswer

O [resposta](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) é um objeto JSON incluindo todas as informações necessárias exibir a resposta e a próxima ativar na conversa, se disponível.

```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
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

## <a name="use-qna-maker-with-a-bot-in-c"></a>Use o QnA Maker com um bot emC#

A estrutura de bot fornece acesso às propriedades do QnA Maker:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

O bot de suporte tenha [um exemplo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) com esse código.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Use o QnA Maker com um bot no Node. js

A estrutura de bot fornece acesso às propriedades do QnA Maker:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.03,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

O bot de suporte tenha [um exemplo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) com esse código.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Usar metadados para respostas de filtro por marcas de metadados personalizados

Adicionar metadados permite que você filtre as respostas por essas marcas de metadados. Adicionar a coluna de metadados do **opções de exibição** menu. Adicionar metadados a sua base de dados de Conhecimento, selecionando os metadados **+** ícone para adicionar um par de metadados. Esse par consiste em uma chave e um valor.

![Captura de tela da adição de metadados](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar resultados com strictFilters para marcas de metadados

Considere a pergunta do usuário "Quando faz fechar este hotel?", em que a intenção é implícita para o restaurante "Paraíso".

Como os resultados são necessários somente para o restaurante "Paraíso", você pode definir um filtro na chamada GenerateAnswer nos metadados de "Nome do restaurante". O exemplo a seguir mostra isso:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Usar resultados de pergunta e resposta para manter o contexto de conversa

A resposta para o GenerateAnswer contém as informações de metadados correspondente do conjunto de perguntas e respostas correspondente. Você pode usar essas informações em seu aplicativo cliente para armazenar o contexto da conversa anterior para uso em conversas posteriores. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>Corresponder a apenas, perguntas por texto

Por padrão, o QnA Maker pesquisa por meio de perguntas e respostas. Se você quiser pesquisar apenas perguntas, para gerar uma resposta, use o `RankerType=QuestionOnly` no corpo POST da solicitação GenerateAnswer.

Você pode pesquisar por meio de Conhecimento publicados, usando `isTest=false`, ou no teste kb usando `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>Próximas etapas

O **Publish** página também fornece informações para gerar uma resposta com [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) e [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](./create-knowledge-base.md)
