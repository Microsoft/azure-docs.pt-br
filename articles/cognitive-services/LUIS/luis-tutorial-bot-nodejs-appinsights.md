---
title: Application Insights, Node.js
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
ms.openlocfilehash: cfed5477df75350f24e77786117e85b9c728c49a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657741"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Adicionar resultados do LUIS ao Application Insights de um Bot no Node.js
Este tutorial adiciona informações de bot e de Reconhecimento vocal ao armazenamento de dados telemétricos do [Application Insights](https://azure.microsoft.com/services/application-insights/). Quando tiver esses dados, você poderá consultá-los usando a linguagem Kusto ou o Power BI para analisar, agregar e relatar intenções e entidades do enunciado em tempo real. Esta análise ajuda a determinar se você deve adicionar ou editar as intenções e as entidades do seu aplicativo LUIS.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Capturar dados de bot e de Reconhecimento vocal no Application Insights
> * Consultar o Application Insights para obter dados de Reconhecimento vocal

## <a name="prerequisites"></a>Pré-requisitos

* Um bot do Serviço de bot do Azure, criado com o Application Insights habilitado.
* Código de bot baixado do **[tutorial](luis-nodejs-tutorial-bf-v4.md)** de bot anterior. 
* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Todo o código neste tutorial está disponível no [Repositório de Exemplos do Azure para Reconhecimento vocal no GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Adicionar o Application Insights a um projeto de bot do aplicativo Web
No momento, o serviço do Application Insights, usado nesse bot de aplicativo Web, coleta telemetria de estado geral para o bot. Ele não coleta informações do LUIS. 

Para capturar as informações do LUIS, o bot do aplicativo Web precisa ter o pacote NPM do **[Application Insights](https://www.npmjs.com/package/applicationinsights)** instalado e configurado.  

1. No terminal integrado do VSCode, na raiz do projeto de bot, adicione os seguintes pacotes NPM usando o comando mostrado: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    O pacote **sublinhado** é usado para nivelar a estrutura JSON do LUIS para que seja mais fácil vê-la e usá-la no Application Insights.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Capturar e enviar resultados da consulta LUIS para o Application Insights

1. No VSCode, crie um novo arquivo **appInsightsLog.js** e adicione o seguinte código:

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    Esse arquivo usa o contexto do bot e a resposta do LUIS, nivela os dois objetos e os insere em um evento de **rastreamento** no Application Insights. O nome do evento é **LUIS**. 

1. Abra a pasta **dialogs** e, em seguida, o arquivo **luisHelper.js**. Inclua o novo **appInsightsLog.js** como um arquivo obrigatório e capture o contexto do bot e a resposta do LUIS. O código completo para o arquivo é: 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

## <a name="add-application-insights-instrumentation-key"></a>Adicionar a chave de instrumentação do Application Insights 

Para adicionar dados ao Application Insights, você precisa da chave de instrumentação.

1. Em um navegador, no [portal do Azure](https://portal.azure.com), localize o recurso **Application Insights** de seu bot. Seu nome terá a maior parte do nome do bot e caracteres aleatórios no final do nome, como `luis-nodejs-bot-johnsmithxqowom`. 
1. No recurso do Application Insights, na página **Visão geral**, copie a **Chave de instrumentação**.
1. No VSCode, abra o arquivo **.env** na raiz do projeto do bot. O arquivo contém todas as suas variáveis de ambiente.  
1. Adicione uma nova variável, `MicrosoftApplicationInsightsInstrumentationKey`, com o valor de sua chave de instrumentação. Não coloque o valor entre aspas. 

## <a name="start-the-bot"></a>Iniciar o bot

1. No terminal integrado do VSCode, inicie o bot:
    
    ```console
    npm start
    ```

1. Inicie o emulador do bot e abra o bot. Esta [etapa](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) é descrita no tutorial anterior.

1. Faça uma pergunta ao bot. Esta [etapa](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) é descrita no tutorial anterior.

## <a name="view-luis-entries-in-application-insights"></a>Exibir entradas do LUIS no Application Insights

Abra o Application Insights para ver as entradas do LUIS. Poderão ser necessários alguns minutos para que os dados sejam exibidos no Application Insights.

1. No [portal do Azure](https://portal.azure.com), abra o recurso do Application Insights para o bot. 
1. Quando o recurso for aberto, selecione **Pesquisar** e pesquise todos os dados nos últimos **30 minutos** com o tipo de evento **Rastreamento**. Selecione o rastreamento denominado **LUIS**. 
1. As informações do bot e do LUIS estão disponíveis em **Propriedades personalizadas**. 

    ![Examine as propriedades personalizadas do LUIS armazenadas no Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Consulte o Application Insights para intenções, pontuações e enunciados
O Application Insights oferece a capacidade de consultar os dados com a linguagem [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use), bem como exportar para o [Power BI](https://powerbi.microsoft.com). 

1. Selecione **Log (Analytics)** . Uma nova janela é aberta com uma janela de consulta na parte superior e uma janela de tabela de dados abaixo. Se você tiver usado bancos de dados antes, essa organização será familiar. A consulta representa seus dados filtrados anteriores. A coluna **CustomDimensions** tem o bot e as informações do LUIS.
1. Para extrair as principais intenções, pontuações e enunciados, adicione o seguinte logo acima da última linha (a linha `|top...`) na janela de consulta:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Execute a consulta. As novas colunas de topIntent, pontuação e enunciado estão disponíveis. Selecione a coluna topIntent para classificar.

Saiba mais sobre a [Linguagem de consulta Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou sobre [exportar os dados para o Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Próximas etapas

Outras informações que você talvez queira adicionar aos dados do Application Insights incluem ID do aplicativo, ID de versão, data da última alteração do modelo, data do último treinamento, data da última publicação. Esses valores podem ser recuperados da URL do ponto de extremidade (ID do aplicativo e ID de versão) ou de uma chamada à API de criação e então definidos nas configurações do bot do aplicativo Web e capturados de lá.  

Se você estiver usando a mesma assinatura do ponto de extremidade para mais de um aplicativo LUIS, também deverá incluir a ID da assinatura e uma propriedade informando que ela é uma chave compartilhada. 

> [!div class="nextstepaction"]
> [Saiba mais sobre declarações de exemplo](luis-how-to-add-example-utterances.md)
