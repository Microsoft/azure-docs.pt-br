---
title: Melhorar a base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: O aprendizado ativo permite melhorar a qualidade da sua base de dados de Conhecimento, sugerindo perguntas alternativas, com base em envios de usuário, para o par de pergunta e resposta. Você examina essas sugestões, adicionando-as às perguntas existentes ou rejeitando-as. Sua base de dados de conhecimento não é alterada automaticamente. Você deve aceitar as sugestões para qualquer alteração tenha efeito. Essas sugestões adicionam perguntas, mas não alteram nem removem perguntas existentes.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/19/2019
ms.author: diberry
ms.openlocfilehash: b73884e544ea1b8ee76c8a891048e6a8e17d6ab3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204089"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Use o aprendizado ativo para melhorar a sua base de dados de Conhecimento

O aprendizado ativo permite melhorar a qualidade da sua base de dados de Conhecimento, sugerindo perguntas alternativas, com base em envios de usuário, para o par de pergunta e resposta. Você examina essas sugestões, adicionando-as às perguntas existentes ou rejeitando-as. 

Sua base de dados de conhecimento não é alterada automaticamente. Em ordem para qualquer alteração entre em vigor, você deve aceitar as sugestões. Essas sugestões adicionam perguntas, mas não alteram nem removem perguntas existentes.

## <a name="what-is-active-learning"></a>O que é aprendizado ativo?

O QnA Maker aprende novas variações de pergunta com comentários implícitos e explícitos.
 
* [Comentários implícito](#how-qna-makers-implicit-feedback-works) – o classificador compreende quando uma pergunta do usuário tem várias respostas com pontuação que é muito parecidas e considera isso como comentários. Você não precisa fazer nada para que isso aconteça.
* [Comentários explícito](#how-you-give-explicit-feedback-with-the-train-api) – quando várias respostas com pouca variação em pontuações são retornadas da base de Conhecimento, o aplicativo cliente pede ao usuário qual pergunta é a pergunta correta. Comentários de explícita do usuário é enviado para o QnA Maker com os [Train API](#train-api). 

Os dois métodos fornecem o classificador com consultas semelhantes que estão clusterizadas.

## <a name="how-active-learning-works"></a>Como o aprendizado ativo funciona

Aprendizado ativo é disparado com base nas pontuações das respostas alguns superior retornadas por QnA Maker. Se as diferenças de pontuação residem dentro de um pequeno intervalo, a consulta é considerada uma sugestão possíveis (como uma pergunta alternativa) para cada um dos pares QnA possíveis. Depois de aceitar a pergunta sugerida para um par de QnA específico, ele é rejeitado para os outros pares. Você precisa se lembrar de salvar e treinar, depois de aceitar sugestões.

O aprendizado ativo fornece as melhores sugestões possíveis em casos em que os pontos de extremidade estão obtendo uma quantidade e variedade razoáveis de consultas de uso. Quando as consultas de 5 ou mais semelhantes estão agrupadas, a cada 30 minutos, QnA Maker sugere as perguntas baseadas no usuário para o designer de dados de conhecimento para aceitar ou rejeitar. Todas as sugestões são clusterizadas por similaridade e as principais sugestões de perguntas alternativas são exibidas com base na frequência das consultas específicas por usuários finais.

Depois que as perguntas são sugeridas no portal do QnA Maker, você precisa examinar e aceitar ou rejeitar essas sugestões. Não há uma API para gerenciar sugestões.

## <a name="how-qna-makers-implicit-feedback-works"></a>Como funciona a comentários de implícita do QnA Maker

Comentários de implícita do QnA Maker usa um algoritmo para determinar a proximidade de pontuação e em seguida, fazer sugestões de aprendizado ativo. O algoritmo para determinar a proximidade não é um cálculo simples. Os intervalos no exemplo a seguir não devem ser corrigidas, mas devem ser usados como um guia para entender o impacto de apenas o algoritmo.

Quando a pontuação de uma pergunta tem um alto grau de confiabilidade, tal como 80%, o intervalo de pontuações que são consideradas para aprendizado ativo é amplo, dentro de aproximadamente 10%. Conforme a pontuação de confiança diminui, por exemplo, para 40%, o intervalo de pontuações diminui também, para dentro de aproximadamente 4%. 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Como você enviar comentários explícita com a API de treinamento

É importante que o QnA Maker obtém explícito comentários que sobre o qual as respostas foi a melhor resposta. Como a melhor resposta é determinada cabe a você e pode incluir:

* Comentários do usuário, selecionando uma das respostas.
* Lógica de negócios, como determinar um aceitável pontuar intervalo.  
* Uma combinação de ambos os lógica de negócios e comentários do usuário.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Atualizar sua versão de tempo de execução para usar o aprendizado ativo

O aprendizado ativo é compatível com a versão de tempo de execução 4.4.0 e superior. Se sua base de dados de conhecimento foi criada em uma versão anterior, [atualize seu tempo de execução](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) para usar esse recurso. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>Ativar o aprendizado ativo para ver sugestões

O aprendizado ativo está desativado por padrão. Ative-o para ver sugestões de perguntas. Depois que você ativa o aprendizado ativo, você precisa enviar informações do aplicativo cliente para o QnA Maker. Para obter mais informações, consulte [fluxo de arquitetura para usando GenerateAnswer e APIs de treinamento de um bot](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Selecione **publicar** para publicar a base de dados de Conhecimento. Consultas de aprendizado ativo são coletadas do ponto de extremidade de previsão de API GenerateAnswer apenas. As consultas para o painel de teste no portal do QnA Maker não afetam o aprendizado ativo.

1. Para desativar o Active Directory de aprendizado no portal do QnA Maker, vá para o canto superior direito, selecione suas **nome**, acesse [ **configurações de serviço**](https://www.qnamaker.ai/UserSettings).  

    ![Ative o aprendizado ativo sugerido alternativas de pergunta da página de configurações de serviço. Selecione seu nome de usuário no menu superior direito e selecione configurações de serviço.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Localize o serviço QnA Maker e, em seguida, ative o **aprendizado ativo**. 

    [![Na página de configurações de serviço, ativar/desativar o recurso de aprendizado ativo. Se você não conseguir ativar/desativar o recurso, você precisa atualizar seu serviço.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Uma vez **aprendizado ativo** é habilitado, a base de Conhecimento sugere novas perguntas em intervalos regulares, com base em perguntas enviadas por usuário. Você pode desabilitar o **aprendizado ativo** desativando a configuração novamente.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Aceitar uma sugestão de aprendizado ativo na base de Conhecimento

1. Para ver as sugestões de perguntas sobre o **editar** página de base de Conhecimento, selecione **opções de exibição**, em seguida, selecione **Mostrar sugestões de aprendizado ativo**. 

    [![Na seção de edição do portal, selecione Mostrar sugestões para ver novas alternativas de pergunta do aprendizado ativo.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrar a base de dados de conhecimento com pares de pergunta e resposta para mostrar apenas sugestões, selecionando **filtrar por sugestões**.

    [![Use o filtro por alternância de sugestões para exibir apenas as alternativas do aprendizado ativo pergunta sugeridas.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Cada par de QnA sugere as alternativas de pergunta nova com uma marca de seleção `✔` , aceite a pergunta ou um `x` para rejeitar as sugestões. Selecione a marca de seleção para adicionar a pergunta. 

    [![Selecione ou rejeitar alternativas de pergunta sugerida do aprendizado ativo, selecionando a marca de seleção verde ou marca de exclusão vermelho.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Você pode adicionar ou excluir _todas as sugestões_ selecionando **adicionar todos** ou **rejeitar tudo** na barra de ferramentas contextual.

1. Selecione **Salvar e Treinar** para salvar as alterações à base de dados de conhecimento.

1. Selecione **Publish** para permitir que as alterações para estar disponível a partir de [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Quando as consultas de 5 ou mais semelhantes estão agrupadas, a cada 30 minutos, QnA Maker sugere perguntas de alternativas para aceitar ou rejeitar.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Fluxo de arquitetura para usando GenerateAnswer e APIs de treinamento de um bot

Um bot ou outro aplicativo cliente deve usar o seguinte fluxo de arquitetura para usar o aprendizado ativo:

* Bot [obtém a resposta da base de Conhecimento](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) com a API GenerateAnswer, usando o `top` propriedade para obter um número de respostas.
* Bot determina comentários explícito:
    * Usando seu próprio [lógica de negócios personalizada](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), filtrar as pontuações baixas.
    * No bot ou aplicativo cliente, exibir a lista de possíveis respostas para o usuário e obter a resposta selecionada do usuário.
* Bot [envia a resposta selecionada para o QnA Maker](#bot-framework-sample-code) com o [Train API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Use a propriedade superior na solicitação GenerateAnswer obter várias respostas de correspondência

Ao enviar uma pergunta para o QnA Maker para obter uma resposta, o `top` propriedade do corpo JSON define o número de respostas para retornar. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Use a propriedade de pontuação, juntamente com a lógica de negócios para obter a lista de respostas para mostrar o usuário

Quando o aplicativo cliente (por exemplo, um chat bot) recebe a resposta, as principais 3 perguntas são retornadas. Use o `score` propriedade para analisar a proximidade entre as pontuações. Esse intervalo de proximidade é determinado pela sua própria lógica de negócios. 

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Acompanhamento do aplicativo cliente quando perguntas têm pontuações semelhantes

Seu aplicativo cliente exibe as perguntas com uma opção para o usuário selecione _única pergunta_ que mais representa suas intenções. 

Depois que o usuário seleciona uma das perguntas existentes, o aplicativo cliente envia a escolha do usuário como comentários usando a API de treinamento do QnA Maker. Esses comentários conclui a ativa loop de comentários de aprendizado. 

## <a name="train-api"></a>API de treinamento

Comentários de aprendizado ativo é enviado para o QnA Maker com a solicitação de POSTAGEM da API de treinamento. A assinatura de API é:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Propriedade de solicitação HTTP|NOME|Type|Finalidade|
|--|--|--|--|
|Parâmetro de rota de URL|ID da base de dados de Conhecimento|cadeia de caracteres|o GUID da base de dados de conhecimento.|
|Host subdomain|Nome do recurso QnAMaker|cadeia de caracteres|O nome do host para o QnA Maker em sua assinatura do Azure. Isso está disponível na página de configurações depois de publicar a base de Conhecimento. |
|Cabeçalho|Tipo de conteúdo|cadeia de caracteres|o tipo de mídia do corpo enviado para a API. Valor padrão é: `application/json`|
|Cabeçalho|Autorização|cadeia de caracteres|sua chave de ponto de extremidade (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corpo do POST|Objeto JSON|JSON|Os comentários de treinamento|

O corpo JSON tem várias configurações:

|Propriedade de corpo JSON|Type|Finalidade|
|--|--|--|--|
|`feedbackRecords`|matriz|Lista de comentários.|
|`userId`|cadeia de caracteres|A ID de usuário da pessoa que está aceitando as sugestões de perguntas. O formato de ID de usuário cabe a você. Por exemplo, um endereço de email pode ser uma ID de usuário válido em sua arquitetura. Opcional.|
|`userQuestion`|cadeia de caracteres|Texto exato da consulta do usuário. Obrigatório.|
|`qnaID`|número|ID da pergunta, encontrada na [GenerateAnswer resposta](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Um exemplo de corpo JSON é semelhante a:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Uma resposta bem-sucedida retorna um status de 204 e nenhum corpo de resposta JSON. 

### <a name="batch-many-feedback-records-into-a-single-call"></a>Número de registros de comentários do lote em uma única chamada

No aplicativo cliente, como um bot, você pode armazenar os dados e enviar muitos registros em um corpo JSON único no `feedbackRecords` matriz. 

Um exemplo de corpo JSON é semelhante a:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Código de exemplo do bot framework

Seu código do bot framework precisa chamar a API de treinamento, se a consulta do usuário deve ser usada para aprendizado ativo. Há duas partes de código para escrever:

* Determinar se a consulta deve ser usada para o aprendizado ativo
* Enviar consulta de volta à API de treinamento do QnA Maker para aprendizado ativo

No [exemplo de Bot do Azure](https://aka.ms/activelearningsamplebot), das duas atividades tenham sido programadas. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Exemplo C# código para a API de treinamento com o Bot Framework 4. x

O código a seguir ilustra como enviar informações de volta para o QnA Maker com a API de treinamento. Isso [exemplo de código completo](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) está disponível no GitHub.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="host">Endpoint host of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string host, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = host + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Exemplo de código Node. js para API de treinamento com o Bot Framework 4. x 

O código a seguir ilustra como enviar informações de volta para o QnA Maker com a API de treinamento. Isso [exemplo de código completo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) está disponível no GitHub.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);
            
            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aprendizado ativo é salvo na base de Conhecimento exportado

Quando seu aplicativo tenha habilitado de aprendizado ativo, e você exporta o aplicativo, o `SuggestedQuestions` coluna no arquivo tsv retém os dados de aprendizado ativo. 

O `SuggestedQuestions` coluna é um objeto JSON de informações de implícita, `autosuggested`e explícito, `usersuggested` comentários. Um exemplo desse objeto JSON para uma única pergunta enviado pelo usuário de `help` é:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Quando você reimportar esse aplicativo, o aprendizado ativo continua a coletar informações e recomendável sugestões para sua base de dados de Conhecimento. 

## <a name="best-practices"></a>Práticas recomendadas

Para as práticas recomendadas ao usar o aprendizado ativo, veja [Práticas recomendadas](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Próximas etapas
 
> [!div class="nextstepaction"]
> [Usar metadados com a API de GenerateAnswer](metadata-generateanswer-usage.md)
