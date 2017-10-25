---
title: "Introdução ao Azure Mobile Engagement para Cordova/Phonegap"
description: "Aprenda a usar o Azure Mobile Engagement com Análises e Notificações por Push para Aplicativos Cordova/Phonegap."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d7a761310782faab1dda023785f93cf90742e2ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Introdução ao Azure Mobile Engagement para Cordova/Phonegap
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo e enviar notificações por push para usuários segmentados para um aplicativo móvel desenvolvido com Cordova.

Neste tutorial, criamos um aplicativo do Cordova em branco usando o Mac e integramos o SDK do Mobile Engagement. Ele coleta dados de análise básica e recebe notificações por push usando o sistema de notificações de Push da Apple (APNS) para iOS e Google Cloud Messaging (GCM) para o Android. Nós implantaremos em um dispositivo iOS ou Android para teste. 

> [!NOTE]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).
> 
> 

Este tutorial exige o seguinte:

* Xcode, que pode ser instalado em seu Mac App Store (para implantação no iOS)
* [SDK do Android e Emulador](http://developer.android.com/sdk/installing/index.html) (para a implantação no Android)
* Certificado de notificação por push (.p12) que pode ser obtido no seu centro de desenvolvimento da Apple para APNS
* Número do projeto do GCM que pode ser obtido seu Console de Desenvolvedor do Google para GCM
* [Plug-in do Mobile Engagement Cordova](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> Você pode encontrar o código-fonte e o arquivo ReadMe do plug-in Cordova no [GitHub](https://github.com/Azure/azure-mobile-engagement-cordova)
> 
> 

## <a id="setup-azme"></a>Configurar o Mobile Engagement para seu aplicativo Cordova
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Conectando seu aplicativo ao back-end do Mobile Engagement
Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push. 

Criaremos um aplicativo básico com Cordova para demonstrar a integração:

### <a name="create-a-new-cordova-project"></a>Crie um novo projeto com Cordova
1. Inicie a janela *Terminal* no seu computador Mac e digite o seguinte que criará um novo projeto Cordova por meio do modelo padrão. Certifique-se de que o perfil de publicação que você usará para implantar seu aplicativo iOS esteja usando “com.mycompany.myapp” como o ID do aplicativo 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. Execute o seguinte para configurar seu projeto para **iOS** e executá-lo no simulador do iOS:
   
        $ cordova platform add ios 
        $ cordova run ios
3. Execute o seguinte para configurar seu projeto para o **Android** e execute no emulador do Android. Certifique-se de que as configurações de emulador do Android SDK têm seu destino como APIs do Google (Google Inc.) com a CPU / ABI como APIs Google ARM.  
   
        $ cordova platform add android
        $ cordova run android
4. Adicione o plug-in do Console do Cordova. 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Conecte seu aplicativo ao back-end do Mobile Engagement
1. Instale o plug-in do Azure Mobile Engagement Cordova e forneça os valores de variáveis para configurar o plug-in:
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Ícone de Alcance do Android* : deve ser o nome do recurso sem qualquer extensão, nem o prefixo drawable (ex: mynotificationicon), e o arquivo de ícone deve ser copiado no seu projeto Android (plataforms/android/res/drawable)

*Ícone de Alcance do iOS*: deve ser o nome do recurso com a extensão (ex: meuiconenotificação.png) e o arquivo de ícone deve ser adicionado ao seu projeto do iOS com XCode (usando o Menu Adicionar Arquivos)

## <a id="monitor"></a>Habilitar monitoramento em tempo real
1. No projeto Cordova - edite **www/js/index.js** para adicionar a chamada para o Mobile Engagement para declarar uma nova atividade uma vez que o evento *deviceReady* é recebido.
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. Executar o aplicativo:
   
   * **Para iOS**
     
       Na janela `Terminal` , inicie seu aplicativo em uma nova instância do simulador, executando o seguinte:
     
           cordova run ios
   * **Para Android**
     
       Na janela `Terminal` , inicie seu aplicativo em uma nova instância do emulador, executando o seguinte:
     
           cordova run android
3. Você pode ver o seguinte nos logs do console:
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>Conectar o aplicativo com monitoramento em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Habilitar Notificações por Push e mensagens no aplicativo
O Mobile Engagement permite a você interagir com seus usuários com Notificações por Push e Mensagens no Aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement.
As seções a seguir irão configurar o aplicativo para recebê-los.

### <a name="configure-push-credentials-for-mobile-engagement"></a>Configure credenciais de Push para o Mobile Engagement
Para permitir que o Mobile Engagement envie notificações por Push em seu nome, você precisa conceder acesso ao certificado Apple iOS ou à chave da API do servidor de GCM. 

1. Navegue até o portal do Mobile Engagement. Verifique se você está no aplicativo que estamos usando para este projeto e, em seguida, clique no botão **Engajar** na parte inferior:
   
    ![][1]
2. Você será levado para a página de configurações no Portal do Engagement. Nele, clique na seção **Push Nativo** :
   
    ![][2]
3. Configure o certificado do iOS/a chave da API do servidor de GCM 
   
    **[iOS]**
   
    a. Selecione seu .p12, carregue-o e digite sua senha:
   
    ![][3]
   
    **[Android]**
   
    a. Clique no ícone para editar na frente da **Chave de API** na seção Configurações do GCM e no pop-up que aparece, cole a chave do Servidor GCM e clique em **OK**. 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>Habilitar notificações por push no aplicativo Cordova
Edite **www/js/index.js** para adicionar a chamada para o Mobile Engagement para solicitar notificações por push e declarar um manipulador:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>Execute o aplicativo
**[iOS]**

1. Nós usaremos o XCode para criar e implantar o aplicativo no dispositivo para testar as notificações por push já que o iOS permite apenas notificações por push para um dispositivo real. Vá para o local no qual o projeto Cordova é criado e navegue até **...\platforms\ios**. Abra o arquivo .xcodeproj nativo no XCode. 
2. Crie e implante o aplicativo Cordova para o dispositivo iOS usando a conta que tem o perfil de provisionamento que contém o certificado que você acabou de carregar para o portal do Mobile Engagement e a Id do aplicativo que corresponde à que você forneceu ao criar o aplicativo Cordova. Você pode conferir o *Identificador do pacote* no arquivo **Resources\*-info.plist** no XCode para ter uma correspondência. 
3. Você verá o popup iOS padrão em seu dispositivo dizendo que o aplicativo solicita de permissão para enviar notificações. Conceder permissão. 

**[Android]**

Você pode simplesmente usar o emulador para executar o aplicativo Android, já que as notificações GCM têm suporte no emulador do Android. 

    cordova run android

## <a id="send"></a>Envie uma notificação para seu aplicativo
Agora, vamos criar uma campanha de notificação por push simples que enviará um push para seu aplicativo executando o dispositivo:

1. Navegue até a guia **Reach** em seu portal do Mobile Engagement
2. Clique em **Novo Comunicado** para criar sua campanha de push.
   
    ![][6]
3. Forneça entradas para criar sua campanha do **[Android]**
   
   * Forneça um **Nome** para sua campanha. 
   * Selecione o **Tipo de Entrega** como *Sistema de notificação* *Simples*
   * Selecione o **horário de entrega** como *"A Qualquer Momento"*
   * Forneça um **Título** para a notificação que será a primeira linha no envio por push.
   * Forneça uma **Mensagem** para a notificação, que servirá como o corpo da mensagem. 
     
     ![][11]
4. Forneça entradas para criar sua campanha do **[iOS]**
   
   * Forneça um **Nome** para sua campanha. 
   * Selecione o **horário de entrega** como *"Somente fora do aplicativo"*
   * Forneça um **Título** para a notificação que será a primeira linha no envio por push.
   * Forneça uma **Mensagem** para a notificação, que servirá como o corpo da mensagem. 
     
     ![][12]
5. Role para baixo e, na seção Conteúdo, selecione **Somente notificação**
   
    ![][8]
6. [Opcional] Você também pode fornecer uma URL de ação. Verifique se ele usa um esquema de URL fornecido ao configurar a variável **AZME\_REDIRECT\_URL** do plug-in, por exemplo, *myapp://test*.  
7. Você concluiu a configuração da campanha mais básica possível. Agora role para baixo novamente e clique no botão **Criar** para salvar sua campanha.
8. Por fim, **Ative** sua campanha
   
    ![][10]
9. Agora você deve ver uma notificação por push em seu dispositivo ou emulador como parte desta campanha. 

## <a id="next-steps"></a>Próximas etapas
[Visão geral de todos os métodos disponíveis no SDK do Cordova Mobile Engagement](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

