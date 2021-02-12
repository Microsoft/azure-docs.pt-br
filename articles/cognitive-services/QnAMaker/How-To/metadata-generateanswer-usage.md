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
ms.openlocfilehash: 18b70d60ade7cd40f7ed51aa7c219c8c046abfc3
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584731"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Obtenha uma resposta com a API e os metadados do GenerateAnswer

Para obter a resposta prevista para a pergunta de um usuário, use a API GenerateAnswer. Ao publicar uma base de dados de conhecimento, você pode ver informações sobre como usar essa API na página **publicar** . Você também pode configurar a API para filtrar as respostas com base nas marcas de metadados e testar a base de dados de conhecimento do ponto de extremidade com o parâmetro de cadeia de caracteres de consulta de teste.

QnA Maker permite adicionar metadados, na forma de pares de chave e valor, aos seus pares de perguntas e respostas. Você pode usar essas informações para filtrar os resultados para consultas de usuário e armazenar informações adicionais que podem ser usadas em conversas de acompanhamento. Para mais informações, consulte a [Base de dados de conhecimento](../index.yml).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Armazenar perguntas e respostas com uma entidade QnA

É importante entender como QnA Maker armazena os dados de pergunta e resposta. A ilustração a seguir mostra uma entidade QnA:

![Ilustração de uma entidade QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidade QnA tem uma ID exclusiva e persistente. Você pode usar a ID para fazer atualizações em uma entidade QnA específica.

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

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Usar metadados para filtrar respostas por marcas de metadados personalizadas

A adição de metadados permite filtrar as respostas por essas marcas de metadados. Adicione a coluna de metadados do menu de **Opções de exibição** . Adicione metadados à sua base de dados de conhecimento selecionando o ícone de metadados **+** para adicionar um par de metadados. Esse par consiste em uma chave e um valor.

![Captura de tela da adição de metadados](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar resultados com strictFilters para marcas de metadados

Considere a pergunta do usuário "quando este hotel é fechado?", em que a intenção é implícita para o restaurante "Paradise".

Como os resultados são necessários apenas para o restaurante "Paradise", você pode definir um filtro na chamada GenerateAnswer nos metadados "nome do restaurante". O exemplo a seguir mostra isso:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>AND lógico por padrão

Para combinar vários filtros de metadados na consulta, adicione os filtros de metadados adicionais à matriz da `strictFilters` propriedade. Por padrão, os valores são logicamente combinados (e). Uma combinação lógica requer que todos os filtros correspondam aos pares de QnA para que o par seja retornado na resposta.

Isso é equivalente a usar a `strictFiltersCompoundOperationType` propriedade com o valor de `AND` .

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>OR lógico usando a propriedade strictFiltersCompoundOperationType

Ao combinar vários filtros de metadados, se você estiver preocupado apenas com um ou alguns dos filtros correspondentes, use a `strictFiltersCompoundOperationType` propriedade com o valor de `OR` .

Isso permite que sua base de dados de conhecimento retorne respostas quando qualquer filtro corresponder, mas não retornará respostas que não têm metadados.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Exemplos de metadados em guias de início rápido

Saiba mais sobre metadados no início rápido do portal QnA Maker para obter os metadados:
* [Criação – adicionar metadados ao par de pergunta e resposta](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Previsão de consulta – filtrar respostas por metadados](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Usar resultados de perguntas e respostas para manter o contexto da conversa

A resposta para o GenerateAnswer contém as informações de metadados correspondentes do par de perguntas e respostas correspondentes. Você pode usar essas informações em seu aplicativo cliente para armazenar o contexto da conversa anterior para uso em conversas posteriores.

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

## <a name="common-http-errors"></a>Erros comuns de HTTP

|Código|Explicação|
|:--|--|
|2xx|Sucesso|
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
