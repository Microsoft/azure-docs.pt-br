---
title: LUIS e QnA Maker – integração do bot
titleSuffix: Azure Cognitive Services
description: À medida que sua base de dados de conhecimento do QnA Maker aumentar, ficará difícil mantê-la como um único conjunto monolítico definido e haverá a necessidade de dividir a base de dados de conhecimento em partes lógicas menores.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: fa79f519c8f3eb8baeaab04870f22a1cfefa59ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61431257"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Usar um bot com o QnA Maker e o LUIS para distribuir uma base de dados de conhecimento
À medida que sua base de dados de conhecimento do QnA Maker aumentar, ficará difícil mantê-la como um único conjunto monolítico definido e haverá a necessidade de dividir a base de dados de conhecimento em partes lógicas menores.

Embora seja simples criar várias bases de dados de conhecimento no QnA Maker, você precisará de alguma lógica para rotear a pergunta de entrada para a base de dados de conhecimento apropriada. Você pode fazer isso usando o LUIS.

Este artigo usa a estrutura do Bot v3 SDK. Consulte este [artigo do Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp), se você estiver interessado na versão SDK do Bot Framework v4 dessas informações.

## <a name="architecture"></a>Arquitetura

![Arquitetura luis do QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

No cenário anterior, primeiro o QnA Maker obtém a intenção do ponto de entrada de um modelo LUIS e então usa isso para roteá-lo para a base de dados de conhecimento QnA Maker correta.

## <a name="create-a-luis-app"></a>Criar um aplicativo LUIS

1. Entre no portal do [LUIS](https://www.luis.ai/).
1. [Criar um aplicativo](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Adicione uma intenção](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) para cada base de dados de conhecimento do QnA Maker. As declarações de exemplo devem corresponder às perguntas nas bases de dados de conhecimento do QnA Maker.
1. [Treine o aplicativo LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) e [publique o aplicativo LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) seu aplicativo LUIS.
1. Na seção **Gerenciar**, anote a ID do aplicativo LUIS, a chave do ponto de extremidade do LUIS e a região do host. Você precisará desses valores mais tarde. 

## <a name="create-qna-maker-knowledge-bases"></a>Criar bases de dados de conhecimento do QnA Maker

1. Entre no [QnA Maker](https://qnamaker.ai).
1. [Crie](https://www.qnamaker.ai/Create) uma base de dados de conhecimento para cada intenção no aplicativo LUIS.
1. Teste e publique as bases de dados de conhecimento. Ao publicar cada KB, anote a ID da base de conhecimento, o host (subdomínio antes de _.azurewebsites.net/qnamaker_) e a chave do ponto de extremidade de autorização. Você precisará desses valores mais tarde. 

    Este artigo pressupõe que os KBs foram criados na mesma assinatura do Azure QnA Maker.

    ![Solicitação HTTP do QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Bot do aplicativo Web

1. [Criar um bot do aplicativo Web](https://docs.microsoft.com/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample) com o modelo do LUIS. Selecione o SDK 3.x e a linguagem de programação C#.

1. Após a criação do bot do aplicativo Web, selecione-o no portal do Azure.
1. Selecione **Configurações do Aplicativo** na navegação do bot do aplicativo Web, depois role para baixo até a seção **Configurações do aplicativo** nas configurações disponíveis.
1. Altere a **LuisAppId** para o valor do aplicativo LUIS criado na seção anterior, depois selecione **Salvar**.


## <a name="change-code-in-basicluisdialogcs"></a>Modifique o código em BasicLuisDialog.cs
1. Na seção **Gerenciamento de Bot** da navegação do bot do aplicativo Web no portal do Azure, selecione **Compilar**.
2. Selecione **Abrir editor de código online**. Uma nova guia do navegador é aberta com o ambiente de edição online. 
3. Na seção **WWWROOT**, selecione o diretório **Caixas de Diálogo** e abra **BasicLuisDialog.cs**.
4. Adicione dependências na parte superior do arquivo **BasicLuisDialog.cs**:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. Adicione as classes abaixo para desserializar a resposta do QnA Maker:

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. Adicione a seguinte classe para fazer uma solicitação HTTP ao serviço do QnA Maker. O valor do cabeçalho **Autorização** inclui a palavra `EndpointKey` com um espaço depois dela. O resultado do JSON é desserializado em classes anteriores, e a primeira resposta é retornada.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceHostName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string hostName, string kbId, string endpointkey)
        {
            qnaServiceHostName = hostName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. Modifique a classe BasicLuisDialog. Cada intenção do LUIS deve ter um método decorado com **LuisIntent**. O parâmetro para a decoração é o nome real da intenção do LUIS. O nome do método que é decorado _deve_ ser o nome da intenção do LUIS para fins de legibilidade e manutenção, mas não precisa ser o mesmo no design ou tempo de execução.  

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all KBs are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_endpointDomain = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>Criar o bot
1. No editor de código, clique duas vezes em `build.cmd` e selecione **Executar no Console**.

    ![executar no console](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. O modo de exibição de código é substituído por uma janela de terminal mostrando o andamento e os resultados da compilação.

    ![compilação de console](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testar o bot
No portal do Azure, selecione **Testar no Webchat** para testar o bot. Digite mensagens com intenções diferentes para obter a resposta da base de dados de conhecimento correspondente.

![teste de chat na Web](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar um plano de continuidade de negócios para o QnA Maker](../How-To/business-continuity-plan.md)
