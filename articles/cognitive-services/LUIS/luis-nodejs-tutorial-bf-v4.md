---
title: 'Tutorial: Bot de Reconhecimento vocal Node.js v4'
description: Use este tutorial para criar um chatbot integrado ao LUIS (Reconhecimento vocal). Este chat bot usa o aplicativo de Recursos Humanos para implementar rapidamente uma solução de bot.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/30/2020
ms.custom: devx-track-js
ms.openlocfilehash: dd5f97f2b1c2ad51dda3446412cd936e45c5982e
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436888"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Tutorial: Use um Bot de aplicativo Web habilitado com Reconhecimento vocal em Node.js

Use o Node.js para compilar um chat bot integrado com reconhecimento vocal (LUIS). O bot é criado com o recurso [bot do aplicativo Web](/azure/bot-service/) e o [Bot Framework versão](https://github.com/Microsoft/botbuilder-dotnet) V4.

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Criar um bot de aplicativo Web. Esse processo cria um novo aplicativo LUIS para você.
> * Baixar o projeto de bot criado pelo serviço de bot da Web
> * Iniciar o bot e o emulador localmente no seu computador
> * Exibir resultados de enunciado no bot

## <a name="prerequisites"></a>Pré-requisitos

* [Bot Framework Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

## <a name="create-a-web-app-bot-resource"></a>Criar um recurso de bot do aplicativo Web

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar novo recurso**.

1. Na caixa de pesquisa, pesquise e selecione **Bot do aplicativo Web**. Selecione **Criar**.

1. Em **Serviço de bot**, forneça as informações necessárias:

    |Configuração|Finalidade|Configuração sugerida|
    |--|--|--|
    |Identificador de Bot|Nome do recurso|`luis-nodejs-bot-` + `<your-name>`, por exemplo, `luis-nodejs-bot-johnsmith`|
    |Subscription|Assinatura na qual criar o bot.|Sua assinatura primária.
    |Resource group|Grupo lógico de recursos do Azure|Crie um novo grupo para armazenar todos os recursos usados com este bot, dê um nome ao grupo `luis-nodejs-bot-resource-group`.|
    |Location|Região do Azure – não precisa ser a mesma que a região de publicação ou criação do LUIS.|`westus`|
    |Tipo de preço|Usado para os limites e a cobrança da solicitação de serviço.|`F0` é a camada gratuita.
    |Nome do aplicativo|O nome é usado como o subdomínio quando seu bot é implantado na nuvem (por exemplo, humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, por exemplo, `luis-nodejs-bot-johnsmith`|
    |Modelo de bot|Configurações do Bot framework – consulte a próxima tabela|
    |Local do aplicativo LUIS|Deve ser o mesmo que a região do recurso do LUIS|`westus`|
    |Local/plano de serviço de aplicativo|Não altere o valor padrão fornecido.|
    |Application Insights|Não altere o valor padrão fornecido.|
    |ID do Aplicativo e senha da Microsoft|Não altere o valor padrão fornecido.|

1. No **Modelo de bot**, selecione o seguinte e escolha o botão **Selecionar** nestas configurações:

    |Configuração|Finalidade|Seleção|
    |--|--|--|
    |Linguagem do SDK|Linguagem de programação do bot|**Node.js**|
    |Bot|Tipo de bot|**Bot básico**|

1. Selecione **Criar**. Isso cria e implanta o serviço de bot no Azure. Parte deste processo cria um aplicativo LUIS chamado `luis-nodejs-bot-XXXX`. Este nome é baseado no nome do aplicativo de Serviço de Bot do Azure.

    > [!div class="mx-imgBorder"]
    > [![Criar um bot de aplicativo Web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Aguarde o serviço de bot ser criado antes de continuar.

1. Selecione `Go to resource` na notificação para ir para a página do bot do aplicativo Web.

## <a name="the-bot-has-a-language-understanding-model"></a>O bot tem um modelo de Reconhecimento Vocal

O processo de criação do serviço de bot também cria um novo aplicativo LUIS com intenções e enunciados de exemplo. O bot fornece mapeamento de intenções para o novo aplicativo LUIS para as seguintes intenções:

|Intenções do LUIS do bot básico|exemplo de enunciado|
|--|--|
|Reservar voo|`Travel to Paris`|
|Cancelar|`bye`|
|GetWeather|`what's the weather like?`|
|Nenhum|Nada fora do domínio do aplicativo.|

## <a name="test-the-bot-in-web-chat"></a>Testar o bot no Webchat

1. Ainda no portal do Azure para o novo bot, selecione **Testar no Webchat**.
1. Na caixa de texto **Digitar sua mensagem**, digite o texto `Book a flight from Seattle to Berlin tomorrow`. O bot responde com a verificação de que você deseja reservar um voo.

    ![Captura de tela do portal do Azure, digite o texto "olá".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Você pode usar a funcionalidade de teste para testar rapidamente seu bot. Para realizar testes mais completos, o que inclui a depuração, baixe o código de bot e use o Visual Studio Code.

## <a name="download-the-web-app-bot-source-code"></a>Baixar o código-fonte do bot do aplicativo Web
Para desenvolver o código de bot do aplicativo Web, baixe o código e use em seu computador local.

1. No portal do Azure, selecione **Build** na seção **Gerenciamento de bot**.

1. Selecione **Baixar código-fonte do bot**.

    [![Baixar o código-fonte do bot de aplicativo Web para o bot básico](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Quando a caixa de diálogo pop-up perguntar **Deseja incluir as configurações do aplicativo no arquivo zip baixado?** , selecione **Sim**. Isso fornece as configurações do LUIS.

1. Quando o código-fonte for compactado, uma mensagem fornecerá um link para baixar o código. Selecione o link.

1. Salve o arquivo zip em seu computador local e extraia os arquivos. Abra a pasta do projeto com o Visual Studio Code.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Revisar o código para enviar o enunciado para o LUIS e obter resposta

1. Para enviar o enunciado do usuário para o ponto de extremidade de previsão do LUIS, abra o arquivo **diálogos -> flightBookingRecognizer.cs**. É aí que o enunciado do usuário inserido no bot é enviado para o LUIS. A resposta do LUIS é retornada do método **executeLuisQuery**.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                // Set the recognizer options depending on which endpoint version you want to use e.g v2 or v3.
                // More details can be found in https://docs.microsoft.com/en-gb/azure/cognitive-services/luis/luis-migration-api-v3
                const recognizerOptions = {
                    apiVersion: 'v3'
                };

                this.recognizer = new LuisRecognizer(config, recognizerOptions);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ```

1. **diálogos -> mainDialog** captura o enunciado e o envia para executeLuisQuery no método actStep.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { MessageFactory, InputHints } = require('botbuilder');
    const { LuisRecognizer } = require('botbuilder-ai');
    const { ComponentDialog, DialogSet, DialogTurnStatus, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');

    const MAIN_WATERFALL_DIALOG = 'mainWaterfallDialog';

    class MainDialog extends ComponentDialog {
        constructor(luisRecognizer, bookingDialog) {
            super('MainDialog');

            if (!luisRecognizer) throw new Error('[MainDialog]: Missing parameter \'luisRecognizer\' is required');
            this.luisRecognizer = luisRecognizer;

            if (!bookingDialog) throw new Error('[MainDialog]: Missing parameter \'bookingDialog\' is required');

            // Define the main dialog and its related components.
            // This is a sample "book a flight" dialog.
            this.addDialog(new TextPrompt('TextPrompt'))
                .addDialog(bookingDialog)
                .addDialog(new WaterfallDialog(MAIN_WATERFALL_DIALOG, [
                    this.introStep.bind(this),
                    this.actStep.bind(this),
                    this.finalStep.bind(this)
                ]));

            this.initialDialogId = MAIN_WATERFALL_DIALOG;
        }

        /**
         * The run method handles the incoming activity (in the form of a TurnContext) and passes it through the dialog system.
         * If no dialog is active, it will start the default dialog.
         * @param {*} turnContext
         * @param {*} accessor
         */
        async run(turnContext, accessor) {
            const dialogSet = new DialogSet(accessor);
            dialogSet.add(this);

            const dialogContext = await dialogSet.createContext(turnContext);
            const results = await dialogContext.continueDialog();
            if (results.status === DialogTurnStatus.empty) {
                await dialogContext.beginDialog(this.id);
            }
        }

        /**
         * First step in the waterfall dialog. Prompts the user for a command.
         * Currently, this expects a booking request, like "book me a flight from Paris to Berlin on march 22"
         * Note that the sample LUIS model will only recognize Paris, Berlin, New York and London as airport cities.
         */
        async introStep(stepContext) {
            if (!this.luisRecognizer.isConfigured) {
                const messageText = 'NOTE: LUIS is not configured. To enable all capabilities, add `LuisAppId`, `LuisAPIKey` and `LuisAPIHostName` to the .env file.';
                await stepContext.context.sendActivity(messageText, null, InputHints.IgnoringInput);
                return await stepContext.next();
            }

            const messageText = stepContext.options.restartMsg ? stepContext.options.restartMsg : 'What can I help you with today?\nSay something like "Book a flight from Paris to Berlin on March 22, 2020"';
            const promptMessage = MessageFactory.text(messageText, messageText, InputHints.ExpectingInput);
            return await stepContext.prompt('TextPrompt', { prompt: promptMessage });
        }

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {
            const bookingDetails = {};

            if (!this.luisRecognizer.isConfigured) {
                // LUIS is not configured, we just run the BookingDialog path.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt)
            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);
            switch (LuisRecognizer.topIntent(luisResult)) {
            case 'BookFlight': {
                // Extract the values for the composite entities from the LUIS result.
                const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                const toEntities = this.luisRecognizer.getToEntities(luisResult);

                // Show a warning for Origin and Destination if we can't resolve them.
                await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);

                // Initialize BookingDetails with any entities we may have found in the response.
                bookingDetails.destination = toEntities.airport;
                bookingDetails.origin = fromEntities.airport;
                bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));

                // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            case 'GetWeather': {
                // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                const getWeatherMessageText = 'TODO: get weather flow here';
                await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                break;
            }

            default: {
                // Catch all for unhandled intents
                const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
            }
            }

            return await stepContext.next();
        }

        /**
         * Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
         * In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
         * will be empty if those entity values can't be mapped to a canonical item in the Airport.
         */
        async showWarningForUnsupportedCities(context, fromEntities, toEntities) {
            const unsupportedCities = [];
            if (fromEntities.from && !fromEntities.airport) {
                unsupportedCities.push(fromEntities.from);
            }

            if (toEntities.to && !toEntities.airport) {
                unsupportedCities.push(toEntities.to);
            }

            if (unsupportedCities.length) {
                const messageText = `Sorry but the following airports are not supported: ${ unsupportedCities.join(', ') }`;
                await context.sendActivity(messageText, messageText, InputHints.IgnoringInput);
            }
        }

        /**
         * This is the final step in the main waterfall dialog.
         * It wraps up the sample "book a flight" interaction with a simple confirmation.
         */
        async finalStep(stepContext) {
            // If the child dialog ("bookingDialog") was cancelled or the user failed to confirm, the Result here will be null.
            if (stepContext.result) {
                const result = stepContext.result;
                // Now we have all the booking details.

                // This is where calls to the booking AOU service or database would go.

                // If the call to the booking service was successful tell the user.
                const timeProperty = new TimexProperty(result.travelDate);
                const travelDateMsg = timeProperty.toNaturalLanguage(new Date(Date.now()));
                const msg = `I have you booked to ${ result.destination } from ${ result.origin } on ${ travelDateMsg }.`;
                await stepContext.context.sendActivity(msg, msg, InputHints.IgnoringInput);
            }

            // Restart the main dialog with a different message the second time around
            return await stepContext.replaceDialog(this.initialDialogId, { restartMsg: 'What else can I do for you?' });
        }
    }

    module.exports.MainDialog = MainDialog;
    ```

<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="start-the-bot-code"></a>Iniciar o código do bot

1. Abra um Console do Windows ou um Terminal do Linux ou do macOS.

1. Altere para o diretório que contém o código do bot e insira o seguinte comando para instalar a dependência dotenv:

    ```console
    npm install dotenv --save
    ```

1. Insira o seguinte comando para iniciar o bot:

    ```console
    node index.js
    ```

Uma janela do navegador é aberta com o site do bot do aplicativo Web em `http://localhost:3978/`. Uma página inicial é exibida com informações sobre seu bot.

![Uma página inicial é exibida com informações sobre seu bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-framework-emulator-to-test-the-bot"></a>Use o Bot Framework Emulator para testar o bot

Fazer uma pergunta ao bot para a intenção de Reservar Voo.

1. Comece o Bot Framework Emulator e selecione **Abrir Bot**.
1. Na caixa de diálogo exibida **Abrir um bot**, digite sua URL, como `http://localhost:3978/api/messages`. A rota `/api/messages` é o endereço web do bot.
1. Insira a **ID do aplicativo Microsoft** e a **senha do Microsoft App**, localizada no arquivo **.env** na raiz do código do bot que você baixou.

1. No Bot Framework Emulator, digite `Book a flight from Seattle to Berlin tomorrow` e obtenha a mesma resposta para o bot básico que a recebida em **Testar no Webchat**.

    [![A captura de tela mostra o Bot Framework Emulator.](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Selecione **Sim** na barra superior. O bot responde com um resumo das ações dele.
1. No log do Bot Framework Emulator, selecione a linha que inclui `<- trace LuisV3 Trace`. Isso exibe a resposta JSON do LUIS para a intenção e as entidades do enunciado.

    [![A captura de tela mostra o Bot Framework Emulator com o painel à direita contendo o código JSON.](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Próximas etapas

Veja mais [exemplos](https://github.com/microsoft/botframework-solutions) com bots de conversação.

> [!div class="nextstepaction"]
> [Crie um aplicativo de Reconhecimento vocal com um domínio de assunto personalizado](./tutorial-intents-only.md)