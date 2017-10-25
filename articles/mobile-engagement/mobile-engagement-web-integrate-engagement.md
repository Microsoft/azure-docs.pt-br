---
title: "Integração do SDK para Web do Azure Mobile Engagement | Microsoft Docs"
description: "As atualizações e procedimentos mais recentes do SDK para Web do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: b5daa2a2-942b-489d-aa1d-568c3b25e56f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 02/29/2016
ms.author: piyushjo
ms.openlocfilehash: 7d8eaa180e277741a583522ee62d68f5247b92bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Integrar o Azure Mobile Engagement em um aplicativo Web
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Os procedimentos neste artigo descrevem a maneira mais simples de ativar as funções de análise e monitoramento no Azure Mobile Engagement no aplicativo Web.

Siga as etapas para ativar os relatórios de log necessários para calcular todas as estatísticas sobre usuários, sessões, atividades, falhas e técnicas. Para estatísticas dependes do aplicativo, como eventos, erros e trabalhos, você deve ativar os relatórios de log manualmente usando a API do Azure Mobile Engagement. Para obter mais informações, aprenda [como usar a API de marcação avançada do Mobile Engagement em um aplicativo Web](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Introdução
[Baixe o SDK para Web do Azure Mobile Engagement](http://aka.ms/P7b453).
O SDK para Web do Mobile Engagement é fornecido como um único arquivo JavaScript, azure-engagement.js, que você precisa incluir em cada página do seu site ou aplicativo Web.

> [!IMPORTANT]
> Antes de executar esse script, você deve executar um script ou trecho de código escrito para configurar o Mobile Engagement para seu aplicativo.
> 
> 

## <a name="browser-compatibility"></a>Compatibilidade de navegador
O SDK para Web do Mobile Engagement usa a codificação e decodificação JSON nativa e solicitações AJAX entre domínios (que dependem da especificação W3C CORS). Ele é compatível com os seguintes navegadores:

* Microsoft Edge 12+
* Internet Explorer 10+
* Firefox 3.5 e superior
* Chrome 4+
* Safari 6+
* Opera 12 e superior

## <a name="configure-mobile-engagement"></a>Configurar o Mobile Engagement
Escreva um script que crie um objeto JavaScript `azureEngagement` global como no exemplo a seguir. Pelo fato de que seu site pode ter várias páginas, este exemplo supõe que este script esteja incluído em cada página. Neste exemplo, o objeto JavaScript é denominado `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

O valor `connectionString` do seu aplicativo é exibido no Portal do Azure.

> [!NOTE]
> `appVersionName` e `appVersionCode` são opcionais. No entanto, é recomendável configurá-los para que a análise possa processar informações de versão.
> 
> 

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Incluir scripts do Mobile Engagement em suas páginas
Adicione scripts do Mobile Engagement às suas páginas de uma das seguintes maneiras:

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

Ou assim:

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias
Depois que o script do SDK para Web do Mobile Engagement for carregado, ele criará o alias **engagement** para acessar as APIs do SDK. Você não pode usar esse alias para definir a configuração do SDK. Esse alias é usado como uma referência nesta documentação.

Observe que, se o alias padrão estiver em conflito com outra variável global da sua página, você poderá redefini-lo na configuração da seguinte maneira antes de carregar o SDK para Web do Mobile Engagement:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Relatórios básicos
Os relatórios básicos no Mobile Engagement abordam as estatísticas de nível de sessão, como estatísticas sobre usuários, sessões, atividades e falhas.

### <a name="session-tracking"></a>Rastreamento de sessão
Uma sessão do Mobile Engagement é dividida em uma sequência de atividades, cada uma delas identificada por um nome.

Em um site clássico, é recomendável declarar uma atividade diferente em cada página do site. Para um site ou aplicativo Web em que a página atual nunca muda, convém acompanhar as atividades em menor escala, como dentro da página.

De qualquer forma, para iniciar ou alterar a atividade do usuário atual, chame a função `engagement.agent.startActivity` . Por exemplo:

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

O servidor do Mobile Engagement encerra automaticamente uma sessão aberta em três minutos depois que a página do aplicativo é fechada.

Como alternativa, você pode encerrar uma sessão manualmente chamando `engagement.agent.endActivity`. Isso define a atividade do usuário atual como 'Ociosa'.  A sessão será encerrada 10 segundos depois, a menos que uma nova chamada para `engagement.agent.startActivity` retome a sessão.

Você pode configurar o atraso de 10 segundos no objeto de compromisso global da seguinte forma:

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [!NOTE]
> Não é possível usar `engagement.agent.endActivity` no retorno de chamada de `onunload`, porque você não pode fazer chamadas AJAX neste estágio.
> 
> 

## <a name="advanced-reporting"></a>Relatórios avançados
Opcionalmente, se você deseja relatar trabalhos, erros e eventos específicos do aplicativo, precisa usar a API do Mobile Engagement. Acesse a API do Mobile Engagement pelo objeto `engagement.agent` .

Você pode acessar todos os recursos avançados do Mobile Engagement na API do Mobile Engagement. A API é detalhada no artigo [Como usar a API de marcação avançada do Mobile Engagement em um aplicativo Web](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Personalizar as URLs usadas para chamadas AJAX
Você pode personalizar as URLs que o SDK para Web do Mobile Engagement usa. Por exemplo, para redefinir a URL de log (o ponto de extremidade do SDK para registro em log), você pode substituir a configuração desta forma:

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Se suas funções de URL retornarem uma cadeia de caracteres que começa com `/`, `//`, `http://` ou `https://`, o esquema padrão não será usado. Por padrão, o esquema `https://` é usado para essas URLs. Se desejar personalizar o esquema padrão, substitua a configuração desta forma:

    window.azureEngagement = {
      ...
      urls: {
        ...         
        scheme: '//'
      }
    };
