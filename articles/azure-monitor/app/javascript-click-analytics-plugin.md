---
title: Clique em plug-in de coleta automática de análise para Application Insights SDK do JavaScript
description: Como instalar e usar o plug-in de coleta automática do clique do Analytics para Application Insights SDK do JavaScript.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: lagayhar
ms.openlocfilehash: e48d669321ad8c58681e8a92e68f2089962bdc17
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429843"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>Clique em plug-in de coleta automática de análise para Application Insights SDK do JavaScript

Esse plug-in rastreia automaticamente eventos de clique em páginas da Web e usa atributos data-* em elementos HTML para popular a telemetria de eventos.

## <a name="getting-started"></a>Introdução

Os usuários podem configurar o plug-in do clique de coleção automática do Click via NPM.

### <a name="npm-setup"></a>configuração do NPM

Instale o pacote NPM:

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="how-to-effectively-use-the-plugin"></a>Como usar efetivamente o plug-in

1. Os dados de telemetria gerados com base nos eventos de clique são armazenados como `customEvents` na seção Application insights do portal do Azure.
2. O `name` de customEvent é populado com base nas seguintes regras:
    1.  O `id` fornecido no `data-*-id` será usado como o nome do customEvent. Por exemplo, se o elemento HTML clicado tiver o atributo "data-Sample-ID" = "Button1", então, "Button1" será o nome do customEvent.
    2. Se nenhum atributo desse tipo existir e se `useDefaultContentNameOrId` for definido como `true` na configuração, o atributo HTML do elemento clicado `id` ou o nome do conteúdo do elemento será usado como o nome do customEvent.
    3. Se `useDefaultContentNameOrId` for false, o nome do customEvent será "not_specified".

    > [!TIP]
    > Nossas recomendações são definir `useDefaultContentNameOrId` como true para gerar dados significativos.  
3. `parentDataTag` faz duas coisas:
    1. Se essa marca estiver presente, o plug-in irá buscar os `data-*` atributos e valores de todos os elementos HTML pai do elemento clicado.
    2. Para melhorar a eficiência, o plug-in usa essa marca como um sinalizador, quando ela for encontrada, interromperá ainda mais o processamento do DOM (Modelo de Objeto do Documento) para cima.
    
    > [!CAUTION]
    > Uma vez `parentDataTag` usado, o SDK começará a procurar marcas pai em todo o seu aplicativo e não apenas no elemento HTML onde você o utilizou.
4. `customDataPrefix` fornecido pelo usuário sempre deve começar com `data-` , por exemplo `data-sample-` . Em HTML, os `data-*` atributos globais formam uma classe de atributos chamada atributos de dados personalizados, que permitem que informações proprietárias sejam trocadas entre o HTML e sua representação dom por scripts. Os navegadores mais antigos (Internet Explorer, Safari) descartarão os atributos que ele não entende, a menos que comecem com `data-` .

    O `*` no `data-*`  pode ser substituído por qualquer nome após a [regra de produção de nomes XML](https://www.w3.org/TR/REC-xml/#NT-Name) com as seguintes restrições:
    - O nome não deve começar com "XML", seja qual for o caso usado para essas letras.
    - O nome não deve conter ponto e vírgula (U + 003A).
    - O nome não deve conter letras maiúsculas.

## <a name="configuration"></a>Configuração

| Nome                  | Type                               | Padrão | Descrição                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| Captura de           | booleano                            | true    | Configuração de captura automática.                                                                                                         |
| retorno de chamada              | [IValueCallback](#ivaluecallback)  | nulo    | Configuração de retornos de chamada.                                                                                                                 |
| pageTags              | string                             | nulo    | Marcas de página.                                                                                                                               |
| Marcas de datatags              | [ICustomDataTags](#icustomdatatags)| nulo    | Marcas de dados personalizadas fornecidas para substituir as marcas padrão usadas para capturar dados de clique.                                                           |
| urlCollectHash        | booleano                            | false   | Habilita o log de valores após um caractere "#" da URL.                                                                          |
| urlCollectQuery       | booleano                            | false   | Habilita o log da cadeia de caracteres de consulta da URL.                                                                                      |
| behaviorValidator     | Função                           | nulo  | Função de retorno de chamada a ser usada para a `data-*-bhvr` validação do valor. Para obter mais informações, acesse a [seção behaviorValidator](#behaviorvalidator).|
| defaultRightClickBhvr | Cadeia de caracteres (ou) número                 | ''      | Valor de comportamento padrão quando o evento de clique com o botão direito do mouse ocorreu. Esse valor será substituído se o elemento tiver o `data-*-bhvr` atributo. |
| dropInvalidEvents     | booleano                            | false   | Sinalizador para descartar eventos que não têm dados de clique úteis.                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| Nome               | Type     | Padrão | Descrição                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | Função | nulo    | Função para substituir o comportamento de captura PageName padrão.                           |
| pageActionPageTags | Função | nulo    | Uma função de retorno de chamada para aumentar o pageTags padrão coletado durante o evento PageAction.  |
| contentName        | Função | nulo    | Uma função de retorno de chamada para popular contentname personalizado.                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| Nome                      | Type    | Padrão   | Marca padrão a ser usada no HTML |   Descrição                                                                                |
|---------------------------|---------|-----------|-------------|----------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | booleano | false     | N/D         |Coleta o atributo HTML padrão para contentname quando um determinado elemento não está marcado com customDataPrefix padrão ou quando customDataPrefix não é fornecido pelo usuário. |
| customDataPrefix          | string  | `data-`   | `data-*`| Nome e valor de conteúdo de captura automática de elementos que são marcados com o prefixo fornecido. Por exemplo, `data-*-id` , `data-<yourcustomattribute>` pode ser usado nas marcas HTML.   |
| aiBlobAttributeTag        | string  | `ai-blob` |  `data-ai-blob`| O plug-in dá suporte a um atributo de blob JSON em vez de `data-*` atributos individuais. |
| metaDataPrefix            | string  | nulo      | N/D  | Nome do metaelemento e conteúdo do cabeçalho HTML da captura automática com o prefixo fornecido durante a captura. Por exemplo, `custom-` pode ser usado na marca meta de HTML. |
| captureAllMetaDataContent | booleano | false     | N/D   | Capturar automaticamente todos os nomes de elementos e conteúdo do cabeçalho HTML. O padrão é false. Se habilitado, isso substituirá o metaDataPrefix fornecido. |
| parentDataTag             | string  | nulo      |  N/D  | Interrompe a passagem do DOM para capturar o nome do conteúdo e o valor dos elementos quando encontrados com essa marca. Por exemplo, `data-<yourparentDataTag>` pode ser usado nas marcas HTML.|
| dntDataTag                | string  | `ai-dnt`  |  `data-ai-dnt`| Elementos HTML com este atributo serão ignorados pelo plug-in para capturar dados de telemetria.|

### <a name="behaviorvalidator"></a>behaviorValidator

As funções behaviorValidator verificam automaticamente se os comportamentos marcados no código estão em conformidade com uma lista predefinida. Isso garante que os comportamentos marcados sejam consistentes com a taxonomia estabelecida da sua empresa. Não é necessário ou esperado que a maioria dos Azure Monitor clientes usará isso, mas está disponível para cenários avançados. Há três funções diferentes de retorno de chamada behaviorValidator expostas como parte dessa extensão. No entanto, os usuários podem usar suas próprias funções de retorno de chamada se as funções expostas não resolverem seu requisito. A intenção é trazer sua própria estrutura de dados de comportamentos, o plug-in usa essa função de validador ao extrair os comportamentos das marcas de dados.

| Name                   | Descrição                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | Use essa função de retorno de chamada se sua estrutura de dados de comportamentos for uma matriz de cadeias de caracteres.|
| BehaviorMapValidator   | Use essa função de retorno de chamada se sua estrutura de dados de comportamentos for um dicionário.       |
| BehaviorEnumValidator  | Use essa função de retorno de chamada se sua estrutura de dados de comportamentos for uma enumeração.            |

#### <a name="sample-usage-with-behaviorvalidator"></a>Exemplo de uso com behaviorValidator

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>Aplicativo de exemplo

[Aplicativo Web simples com plug-in de coleta automática do clique de análise habilitado](https://go.microsoft.com/fwlink/?linkid=2152871).

## <a name="next-steps"></a>Próximas etapas

- Confira o [repositório do GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js) e o [pacote NPM](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js) para o plug-in do clique de coleção de análise automática.
- Use a [análise de eventos na experiência de uso](usage-segmentation.md) para analisar os principais cliques e a fatia por dimensões disponíveis.
- Localize clique em dados no campo conteúdo no atributo customDimensions na tabela CustomEvents em [log Analytics](../logs/log-analytics-tutorial.md#write-a-query). Consulte [aplicativo de exemplo](https://go.microsoft.com/fwlink/?linkid=2152871) para obter diretrizes adicionais.
- Crie uma [pasta de trabalho](../visualize/workbooks-overview.md) ou [exporte para Power bi](../logs/log-powerbi.md#integrating-queries) para criar visualizações personalizadas de clicar em dados.
