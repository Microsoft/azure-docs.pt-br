---
title: Metadados com a API de GenerateAnswer – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker permite que você adicione metadados, na forma de pares chave/valor, aos seus pares de perguntas/respostas. Você pode filtrar os resultados para consultas de usuário e armazenar informações adicionais que podem ser usadas em conversas de acompanhamento.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 9d2100dbc2c5f24742a949778a1b7450bf303c5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232198"
---
# <a name="get-an-answer-with-the-generateanswer-api"></a>Obter uma resposta com a API do GenerateAnswer

Para obter a resposta prevista para a pergunta de um usuário, use a API GenerateAnswer. Ao publicar uma base de dados de conhecimento, você pode ver informações sobre como usar essa API na página **publicar** . Você também pode configurar a API para filtrar as respostas com base nas marcas de metadados e testar a base de dados de conhecimento do ponto de extremidade com o parâmetro de cadeia de caracteres de consulta de teste.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obter previsões de resposta com a API GenerateAnswer

Você usa a [API GenerateAnswer](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) em seu bot ou aplicativo para consultar sua base de dados de conhecimento com uma pergunta de usuário, para obter a melhor correspondência dos pares de perguntas e respostas.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicar para obter ponto de extremidade GenerateAnswer

Depois de publicar sua base de dados de conhecimento, seja no [portal de QnA Maker](https://www.qnamaker.ai)ou usando a [API](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), você pode obter os detalhes do seu ponto de extremidade GenerateAnswer.

Para obter os detalhes do ponto de extremidade:
1. Entre em [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. Em **minhas bases de dados de conhecimento**, selecione **Exibir código** para sua base de dados de conhecimento.
    ![Captura de tela de minhas bases de dados de conhecimento](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Obtenha os detalhes do ponto de extremidade do GenerateAnswer.

    # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

    ![Captura de tela dos detalhes do ponto de extremidade](../media/qnamaker-how-to-metadata-usage/view-code.png)

    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

    ![Captura de tela de detalhes de ponto de extremidade gerenciados](../media/qnamaker-how-to-metadata-usage/view-code-managed.png)

    ---

Também é possível obter os detalhes de ponto de extremidade na guia **Configurações** da base de dados de conhecimento.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuração de solicitação do GenerateAnswer

Você chama o GenerateAnswer com uma solicitação HTTP POST. Para código de exemplo que mostra como chamar o GenerateAnswer, consulte os [inícios rápidos](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

A solicitação POST usa:

* [Parâmetros de URI](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters) necessários
* Propriedade de cabeçalho necessária, `Authorization` , para segurança
* [Propriedades do corpo](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)necessárias.

A URL GenerateAnswer tem o seguinte formato:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Lembre-se de definir a propriedade do cabeçalho HTTP `Authorization` com um valor da cadeia de caracteres `EndpointKey` com um espaço à direita, depois a chave do ponto de extremidade encontrada na página **configurações** .

Um exemplo de corpo JSON é semelhante a:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Saiba mais sobre o [rankerType](../concepts/best-practices.md#choosing-ranker-type).

O JSON anterior solicitou apenas respostas com 30% ou acima da Pontuação de limite.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Propriedades de resposta do GenerateAnswer

A [resposta](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) é um objeto JSON, incluindo todas as informações de que você precisa para exibir a resposta e o próximo ativar a conversa, se disponível.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
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

O JSON anterior respondeu com uma resposta com uma pontuação de 38,5%.

## <a name="match-questions-only-by-text"></a>Somente correspondência de perguntas, por texto

Por padrão, QnA Maker pesquisa perguntas e respostas. Se você quiser pesquisar apenas as perguntas, para gerar uma resposta, use o `RankerType=QuestionOnly` no corpo da postagem da solicitação GenerateAnswer.

Você pode pesquisar os KB publicados, usando `isTest=false` ou na KB de teste usando `isTest=true` .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}

```
## <a name="use-qna-maker-with-a-bot-in-c"></a>Usar QnA Maker com um bot em C #

A estrutura de bot fornece acesso às propriedades do QnA Maker com a [API getrespondi](/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

O JSON anterior solicitou apenas respostas com 30% ou acima da Pontuação de limite.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Use QnA Maker com um bot no Node.js

A estrutura de bot fornece acesso às propriedades do QnA Maker com a [API getrespondi](/javascript/api/botbuilder-ai/qnamaker?preserve-view=true&view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

O JSON anterior solicitou apenas respostas com 30% ou acima da Pontuação de limite.

## <a name="get-precise-answers-with-generateanswer-api"></a>Obtenha respostas precisas com a API do GenerateAnswer

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Oferecemos um recurso de resposta preciso somente com a QnA Maker versão gerenciada.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

O usuário pode habilitar [respostas precisas](../reference-precise-answering.md) ao usar o QnA Maker recurso gerenciado. O parâmetro answerSpanRequest deve ser atualizado para o mesmo.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3,
    "answerSpanRequest": {
        "enable": true,
        "topAnswersWithSpan": 1
    }
}
```

Da mesma forma, os usuários podem optar por desabilitar respostas precisas não definindo o parâmetro answerSpanRequest.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3
}
```
### <a name="bot-settings"></a>Configurações de bot

Se você quiser definir configurações de resposta precisas para o serviço de bot, navegue até o recurso do serviço de aplicativo para o bot. Em seguida, você precisa atualizar as configurações adicionando a configuração a seguir.

- EnablePreciseAnswer
- DisplayPreciseAnswerOnly

|Configuração de vídeo|EnablePreciseAnswer|DisplayPreciseAnswerOnly|
|:--|--|--|
|Somente respostas precisas|true|true|
|Somente respostas longas|false|false|
|Respostas longas e precisas|true|false|

---

## <a name="common-http-errors"></a>Erros comuns de HTTP

|Código|Explicação|
|:--|--|
|2xx|Êxito|
|400|Os parâmetros da solicitação estão incorretos, o que significa que os parâmetros necessários estão ausentes, errados ou são muitos grandes|
|400|O corpo da solicitação está incorreto, o que significa que o JSON está ausente, errado ou é muito grande|
|401|Chave inválida|
|403|Proibido – você não tem as permissões corretas|
|404|O KB não existe|
|410|Essa API foi preterida e não está mais disponível|

## <a name="next-steps"></a>Próximas etapas

A página **publicar** também fornece informações para [gerar uma resposta com o](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) postmaster ou a ondulação.

> [!div class="nextstepaction"]
> [Obter análises na base de dados de conhecimento](../how-to/get-analytics-knowledge-base.md)
> [!div class="nextstepaction"]
> [Pontuação de confiança](../Concepts/confidence-score.md)
