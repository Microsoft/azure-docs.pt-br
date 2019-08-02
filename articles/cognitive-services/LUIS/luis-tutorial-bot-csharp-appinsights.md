---
title: Application Insights, C#
titleSuffix: Azure Cognitive Services
description: Este tutorial adiciona informações de bot e de Reconhecimento vocal ao armazenamento de dados telemétricos do Application Insights.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: 720352403fd5f5937669f9838f3974cb0d3f8797
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657797"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-c"></a>Adicionar resultados do LUIS ao Application Insights de um Bot em C#

Este tutorial adiciona informações de bot e de Reconhecimento vocal ao armazenamento de dados telemétricos do [Application Insights](https://azure.microsoft.com/services/application-insights/). Quando tiver esses dados, você poderá consultá-los usando a linguagem Kusto ou o Power BI para analisar, agregar e relatar intenções e entidades do enunciado em tempo real. Esta análise ajuda a determinar se você deve adicionar ou editar as intenções e as entidades do seu aplicativo LUIS.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Capturar dados de bot e de Reconhecimento vocal no Application Insights
> * Consultar o Application Insights para obter dados de Reconhecimento vocal

## <a name="prerequisites"></a>Pré-requisitos

* Um bot do Serviço de bot do Azure, criado com o Application Insights habilitado.
* Código de bot baixado do **[tutorial](luis-csharp-tutorial-bf-v4.md)** de bot anterior. 
* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Todo o código neste tutorial está disponível no [Repositório de Exemplos do Azure para Reconhecimento vocal no GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Adicionar o Application Insights a um projeto de bot do aplicativo Web

No momento, o serviço do Application Insights, usado nesse bot de aplicativo Web, coleta telemetria de estado geral para o bot. Ele não coleta informações do LUIS. 

Para capturar as informações do LUIS, o bot do aplicativo Web precisa ter o pacote NuGet **[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** instalado e configurado.  

1. No Visual Studio, adicione a dependência à solução. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Gerenciar Pacotes NuGet...** . O gerenciador de pacotes do NuGet mostra uma lista de pacotes instalados. 
1. Selecione **Procurar** e, em seguida, pesquise **Microsoft.ApplicationInsights**.
1. Instale o pacote. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Capturar e enviar resultados da consulta LUIS para o Application Insights

1. Abra o arquivo `LuisHelper.cs` e substitua o conteúdo pelo código a seguir. O método **LogToApplicationInsights** captura os dados do bot e do LUIS e os envia ao Application Insights como um evento de rastreamento denominado `LUIS`.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.ApplicationInsights;
    using System.Collections.Generic;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    LuisHelper.LogToApplicationInsights(configuration, turnContext, recognizerResult);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
            public static void LogToApplicationInsights(IConfiguration configuration, ITurnContext turnContext, RecognizerResult result)
            {
                // Create Application Insights object
                TelemetryClient telemetry = new TelemetryClient();
    
                // Set Application Insights Instrumentation Key from App Settings
                telemetry.InstrumentationKey = configuration["BotDevAppInsightsKey"];
    
                // Collect information to send to Application Insights
                Dictionary<string, string> logProperties = new Dictionary<string, string>();
    
                logProperties.Add("BotConversation", turnContext.Activity.Conversation.Name);
                logProperties.Add("Bot_userId", turnContext.Activity.Conversation.Id);
    
                logProperties.Add("LUIS_query", result.Text);
                logProperties.Add("LUIS_topScoringIntent_Name", result.GetTopScoringIntent().intent);
                logProperties.Add("LUIS_topScoringIntentScore", result.GetTopScoringIntent().score.ToString());
    
    
                // Add entities to collected information
                int i = 1;
                if (result.Entities.Count > 0)
                {
                    foreach (var item in result.Entities)
                    {
                        logProperties.Add("LUIS_entities_" + i++ + "_" + item.Key, item.Value.ToString());
                    }
                }
    
                // Send to Application Insights
                telemetry.TrackTrace("LUIS", ApplicationInsights.DataContracts.SeverityLevel.Information, logProperties);
    
            }
        }
    }
    ```

## <a name="add-application-insights-instrumentation-key"></a>Adicionar a chave de instrumentação do Application Insights 

Para adicionar dados ao Application Insights, você precisa da chave de instrumentação.

1. Em um navegador, no [portal do Azure](https://portal.azure.com), localize o recurso **Application Insights** de seu bot. Seu nome terá a maior parte do nome do bot e caracteres aleatórios no final do nome, como `luis-csharp-bot-johnsmithxqowom`. 
1. No recurso do Application Insights, na página **Visão geral**, copie a **Chave de instrumentação**.
1. No Visual Studio, abra o arquivo **appsettings.json** na raiz do projeto do bot. O arquivo contém todas as suas variáveis de ambiente.
1. Adicione uma nova variável, `BotDevAppInsightsKey`, com o valor de sua chave de instrumentação. O valor deve estar entre aspas. 

## <a name="build-and-start-the-bot"></a>Criar e iniciar o bot

1. No Visual Studio, compile e execute o bot. 
1. Inicie o emulador do bot e abra o bot. Esta [etapa](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) é descrita no tutorial anterior.

1. Faça uma pergunta ao bot. Esta [etapa](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) é descrita no tutorial anterior.

## <a name="view-luis-entries-in-application-insights"></a>Exibir entradas do LUIS no Application Insights

Abra o Application Insights para ver as entradas do LUIS. Poderão ser necessários alguns minutos para que os dados sejam exibidos no Application Insights.

1. No [portal do Azure](https://portal.azure.com), abra o recurso do Application Insights para o bot. 
1. Quando o recurso for aberto, selecione **Pesquisar** e pesquise todos os dados nos últimos **30 minutos** com o tipo de evento **Rastreamento**. Selecione o rastreamento denominado **LUIS**. 
1. As informações do bot e do LUIS estão disponíveis em **Propriedades personalizadas**. 

    ![Examine as propriedades personalizadas do LUIS armazenadas no Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Consulte o Application Insights para intenções, pontuações e enunciados
O Application Insights oferece a capacidade de consultar os dados com a linguagem [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use), bem como exportar para o [Power BI](https://powerbi.microsoft.com). 

1. Selecione **Log (Analytics)** . Uma nova janela é aberta com uma janela de consulta na parte superior e uma janela de tabela de dados abaixo. Se você tiver usado bancos de dados antes, essa organização será familiar. A consulta representa seus dados filtrados anteriores. A coluna **CustomDimensions** tem o bot e as informações do LUIS.
1. Para extrair as principais intenções, pontuações e enunciados, adicione o seguinte logo acima da última linha (a linha `|top...`) na janela de consulta:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Execute a consulta. As novas colunas de topIntent, pontuação e enunciado estão disponíveis. Selecione a coluna topIntent para classificar.

Saiba mais sobre a [Linguagem de consulta Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou sobre [exportar os dados para o Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Saiba mais sobre o Bot Framework

Saiba mais sobre o [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Próximas etapas

Outras informações que você talvez queira adicionar aos dados do Application Insights incluem ID do aplicativo, ID de versão, data da última alteração do modelo, data do último treinamento, data da última publicação. Esses valores podem ser recuperados da URL do ponto de extremidade (ID do aplicativo e ID de versão) ou de uma chamada à API de criação e então definidos nas configurações do bot do aplicativo Web e capturados de lá.  

Se você estiver usando a mesma assinatura do ponto de extremidade para mais de um aplicativo LUIS, também deverá incluir a ID da assinatura e uma propriedade informando que ela é uma chave compartilhada.

> [!div class="nextstepaction"]
> [Saiba mais sobre declarações de exemplo](luis-how-to-add-example-utterances.md)
