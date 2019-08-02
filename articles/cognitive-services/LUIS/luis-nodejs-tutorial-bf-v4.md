---
title: Bot de Reconhecimento vocal Node.js v4
titleSuffix: Azure Cognitive Services
description: Usando Node.js, crie um chat bot integrado com reconhecimento vocal (LUIS). Este chat bot usa o aplicativo de Recursos Humanos para implementar rapidamente uma solução de bot. O bot é criado com o Bot Framework versão 4 e o bot de aplicativo Web do Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: a06bd5a1a061de82230e93b867ea88e333b3cc93
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442547"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Tutorial: Use um Bot de aplicativo Web habilitado com Reconhecimento vocal em Node.js 

Use o Node.js para compilar um chat bot integrado com reconhecimento vocal (LUIS). O bot é criado com o recurso [bot do aplicativo Web](https://docs.microsoft.com/azure/bot-service/) e o [Bot Framework versão](https://github.com/Microsoft/botbuilder-dotnet) V4.

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Criar um bot de aplicativo Web. Esse processo cria um novo aplicativo LUIS para você.
> * Baixar o projeto de bot criado pelo serviço de bot da Web
> * Iniciar o bot e o emulador localmente no seu computador
> * Exibir resultados de enunciado no bot

## <a name="prerequisites"></a>Pré-requisitos

* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Criar um recurso de bot do aplicativo Web

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar novo recurso**.

1. Na caixa de pesquisa, pesquise e selecione **Bot do aplicativo Web**. Selecione **Criar**.

1. Em **Serviço de bot**, forneça as informações necessárias:

    |Configuração|Finalidade|Configuração sugerida|
    |--|--|--|
    |Nome do bot|Nome do recurso|`luis-nodejs-bot-` + `<your-name>`, por exemplo, `luis-nodejs-bot-johnsmith`|
    |Assinatura|Assinatura na qual criar o bot.|Sua assinatura primária.
    |Grupo de recursos|Grupo lógico de recursos do Azure|Crie um novo grupo para armazenar todos os recursos usados com este bot, dê um nome ao grupo `luis-nodejs-bot-resource-group`.|
    |Local padrão|Região do Azure – não precisa ser a mesma que a região de publicação ou criação do LUIS.|`westus`|
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
    |Versão do SDK|Versão do Bot framework|**SDK v4**|
    |Linguagem do SDK|Linguagem de programação do bot|**Node.js**|
    |Bot|Tipo de bot|**Bot básico**|
    
1. Selecione **Criar**. Isso cria e implanta o serviço de bot no Azure. Parte deste processo cria um aplicativo LUIS chamado `luis-nodejs-bot-XXXX`. Este nome é baseado no nome do aplicativo de Serviço de Bot do Azure.

    [![Criar um bot de aplicativo Web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Aguarde o serviço de bot ser criado antes de continuar.

## <a name="the-bot-has-a-language-understanding-model"></a>O bot tem um modelo de Reconhecimento Vocal

O processo de criação do serviço de bot também cria um novo aplicativo LUIS com intenções e enunciados de exemplo. O bot fornece mapeamento de intenções para o novo aplicativo LUIS para as seguintes intenções: 

|Intenções do LUIS do bot básico|exemplo de enunciado|
|--|--|
|Reservar voo|`Travel to Paris`|
|Cancelar|`bye`|
|Nenhum|Nada fora do domínio do aplicativo.|

## <a name="test-the-bot-in-web-chat"></a>Testar o bot no Webchat

1. Ainda no portal do Azure para o novo bot, selecione **Testar no Webchat**. 
1. Na caixa de texto **Digitar sua mensagem**, digite o texto `hello`. O bot responde com informações sobre a estrutura do bot, bem como com consultas de exemplo para o modelo LUIS específico, como a reserva de um voo para Paris. 

    ![Captura de tela do portal do Azure, digite o texto "olá".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Você pode usar a funcionalidade de teste para testar rapidamente o seu bot. Para realizar testes mais completos, o que inclui a depuração, baixe o código de bot e use o Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Baixar o código-fonte do bot do aplicativo Web
Para desenvolver o código de bot do aplicativo Web, baixe o código e use em seu computador local. 

1. No portal do Azure, selecione **Build** na seção **Gerenciamento de bot**. 

1. Selecione **Baixar código-fonte do bot**. 

    [![Baixar o código-fonte do bot de aplicativo Web para o bot básico](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Quando a caixa de diálogo pop-up perguntar **Deseja incluir as configurações do aplicativo no arquivo zip baixado?** , selecione **Sim**.

1. Quando o código-fonte for compactado, uma mensagem fornecerá um link para baixar o código. Selecione o link. 

1. Salve o arquivo zip em seu computador local e extraia os arquivos. Abra o projeto com o Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Revisar o código para enviar o enunciado para o LUIS e obter resposta

1. Abra as **caixas de diálogo -> arquivo luisHelper.js**. É aí que o enunciado do usuário inserido no bot é enviado para o LUIS. A resposta do LUIS é retornada do método como um objeto JSON **bookDetails**. Ao criar seu próprio bot, você também deve criar seu próprio objeto para retornar os detalhes do LUIS. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    
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

1. Abra **Caixas de diálogo -> bookingDialog.js** para entender como o objeto BookingDetails é usado para gerenciar o fluxo de conversas. Os detalhes de viagem são solicitados em etapas e toda a reserva é confirmada e, por fim, repetida para o usuário. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { ConfirmPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
    const { CancelAndHelpDialog } = require('./cancelAndHelpDialog');
    const { DateResolverDialog } = require('./dateResolverDialog');
    
    const CONFIRM_PROMPT = 'confirmPrompt';
    const DATE_RESOLVER_DIALOG = 'dateResolverDialog';
    const TEXT_PROMPT = 'textPrompt';
    const WATERFALL_DIALOG = 'waterfallDialog';
    
    class BookingDialog extends CancelAndHelpDialog {
        constructor(id) {
            super(id || 'bookingDialog');
    
            this.addDialog(new TextPrompt(TEXT_PROMPT))
                .addDialog(new ConfirmPrompt(CONFIRM_PROMPT))
                .addDialog(new DateResolverDialog(DATE_RESOLVER_DIALOG))
                .addDialog(new WaterfallDialog(WATERFALL_DIALOG, [
                    this.destinationStep.bind(this),
                    this.originStep.bind(this),
                    this.travelDateStep.bind(this),
                    this.confirmStep.bind(this),
                    this.finalStep.bind(this)
                ]));
    
            this.initialDialogId = WATERFALL_DIALOG;
        }
    
        /**
         * If a destination city has not been provided, prompt for one.
         */
        async destinationStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            if (!bookingDetails.destination) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'To what city would you like to travel?' });
            } else {
                return await stepContext.next(bookingDetails.destination);
            }
        }
    
        /**
         * If an origin city has not been provided, prompt for one.
         */
        async originStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the response to the previous step's prompt
            bookingDetails.destination = stepContext.result;
            if (!bookingDetails.origin) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'From what city will you be travelling?' });
            } else {
                return await stepContext.next(bookingDetails.origin);
            }
        }
    
        /**
         * If a travel date has not been provided, prompt for one.
         * This will use the DATE_RESOLVER_DIALOG.
         */
        async travelDateStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.origin = stepContext.result;
            if (!bookingDetails.travelDate || this.isAmbiguous(bookingDetails.travelDate)) {
                return await stepContext.beginDialog(DATE_RESOLVER_DIALOG, { date: bookingDetails.travelDate });
            } else {
                return await stepContext.next(bookingDetails.travelDate);
            }
        }
    
        /**
         * Confirm the information the user has provided.
         */
        async confirmStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.travelDate = stepContext.result;
            const msg = `Please confirm, I have you traveling to: ${ bookingDetails.destination } from: ${ bookingDetails.origin } on: ${ bookingDetails.travelDate }.`;
    
            // Offer a YES/NO prompt.
            return await stepContext.prompt(CONFIRM_PROMPT, { prompt: msg });
        }
    
        /**
         * Complete the interaction and end the dialog.
         */
        async finalStep(stepContext) {
            if (stepContext.result === true) {
                const bookingDetails = stepContext.options;
    
                return await stepContext.endDialog(bookingDetails);
            } else {
                return await stepContext.endDialog();
            }
        }
    
        isAmbiguous(timex) {
            const timexPropery = new TimexProperty(timex);
            return !timexPropery.types.has('definite');
        }
    }
    
    module.exports.BookingDialog = BookingDialog;
    ```


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Instale as dependências e inicie o código do bot no Visual Studio

1. No VSCode, no terminal integrado, instale as dependências com o comando `npm install`.
1. Também no terminal integrado, inicie o bot com o comando `npm start`. 


## <a name="use-the-bot-emulator-to-test-the-bot"></a>Usar o emulador de bot para testar o bot

1. Inicie o Emulador do Bot e selecione **Abrir Bot**.
1. Na caixa de diálogo exibida **Abrir um bot**, digite sua URL, como `http://localhost:3978/api/messages`. A rota `/api/messages` é o endereço web do bot.
1. Insira a **ID do aplicativo Microsoft** e a **senha do Microsoft App**, localizada no arquivo **.env** na raiz do código do bot que você baixou.

    Opcionalmente, você pode criar uma nova configuração de bot e copiar `MicrosoftAppId` e `MicrosoftAppPassword` do arquivo **.env** no projeto do Visual Studio para o bot. O nome do arquivo de configuração do bot deve ser o mesmo do nome do bot. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from .env>",
                "appPassword": "<appPassword from .env>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. No emulador do bot, digite `Hello` e obtenha a mesma resposta para o bot básico como você recebeu em **Testa no Webchat**.

    [![Resposta do bot básico no emulador](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Fazer uma pergunta ao bot para a intenção de Reservar Voo

1. No emulador de bot, reserve um voo inserindo o seguinte enunciado: 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    O emulador do bot solicita a confirmação. 

1. Selecione **Sim**. O bot responde com um resumo das ações dele. 
1. No log do emulador de bot, selecione a linha que inclui `Luis Trace`. Isso exibe a resposta JSON do LUIS para a intenção e as entidades do enunciado.

    [![Resposta do bot básico no emulador](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Próximas etapas

Veja mais [exemplos](https://github.com/microsoft/botframework-solutions) com bots de conversação. 

> [!div class="nextstepaction"]
> [Crie um aplicativo de Reconhecimento vocal com um domínio de assunto personalizado](luis-quickstart-intents-only.md)