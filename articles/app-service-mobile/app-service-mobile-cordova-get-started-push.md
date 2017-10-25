---
title: "Adicionar notificações por push ao aplicativo Apache Cordova com os Aplicativos Móveis do Azure | Microsoft Docs"
description: "Saiba como usar Aplicativos Móveis do Azure para enviar notificações por push para seu aplicativo Apache Cordova."
services: app-service\mobile
documentationcenter: javascript
manager: syntaxc4
editor: 
author: ggailey777
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: dc3cab0a6a8b4a56ab0fba1a02e5bba9d0ed1b1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Adicionar notificações por push ao seu aplicativo Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Visão geral
Neste tutorial, você adicionará notificações por push ao projeto de [Início rápido do Apache Cordova] para que, sempre que um registro for inserido, uma notificação por push seja enviada.

Se você não usar o projeto baixado do início rápido do servidor, deverá adicionar o pacote de extensão de notificação por push ao seu projeto. Para saber mais, veja [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure][1].

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial cobre um aplicativo Apache Cordova desenvolvido no Visual Studio 2015 e executado no Emulador do Google Android, em um dispositivo Android, em um dispositivo Windows e em um dispositivo iOS.

Para concluir este tutorial, você precisará:

* Um computador com o [Visual Studio Community 2015][2] ou versão posterior.
* [Ferramentas do Visual Studio para Apache Cordova][4].
* Uma [conta ativa do Azure][3].
* Um projeto de [Início rápido do Apache Cordova][5] concluído.
* (Android) Uma [Conta do Google][6] com um endereço de email confirmado.
* (iOS) Uma [associação ao Programa de Desenvolvedores da Apple][7] e um dispositivo iOS (o simulador iOS não dá suporte a envio por push).
* (Windows) Uma [conta de desenvolvedor da Windows Store][8] e um dispositivo com Windows 10.

## <a name="configure-hub"></a>Configurar um Hub de Notificação
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Assista a um vídeo mostrando as etapas nesta seção][9]

## <a name="update-the-server-project"></a>Atualizar o projeto de servidor
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Modificar seu aplicativo Cordova
Verifique se seu projeto de aplicativo do Apache Cordova está pronto para lidar com notificações por push instalando o plug-in de envio por push do Cordova e outros serviços de envio por push específicos da plataforma.

#### <a name="update-the-cordova-version-in-your-project"></a>Atualize a versão do Cordova em seu projeto.
Se seu projeto usar uma versão ao Apache Cordova anterior à v6.1.1, atualize o projeto do cliente. Para atualizar o projeto:

* Clique com o botão direito do mouse em `config.xml` para abrir o designer de configuração.
* Selecione a guia Plataformas.
* Escolha 6.1.1 na caixa de texto **CLI do Cordova**.
* Escolha **Criar** e **Compilar Solução** para atualizar o projeto.

#### <a name="install-the-push-plugin"></a>Instalar o plug-in de push
Os aplicativos Apache Cordova não manipulam recursos de rede ou do dispositivo de forma nativa.  Essas funcionalidades são fornecidas por plug-ins publicados no [npm][10] ou no GitHub.  O plug-in `phonegap-plugin-push` é usado para manipular notificações por push de rede.

É possível instalar o plug-in de push com uma das seguintes maneiras:

**No prompt de comando:**

Execute o seguinte comando:

    cordova plugin add phonegap-plugin-push

**No Visual Studio:**

1. No Gerenciador de Soluções, abra o arquivo `config.xml`, clique em **Plug-ins** > **Personalizado**, selecione **Git** como a fonte de instalação e, depois, insira `https://github.com/phonegap/phonegap-plugin-push` como a fonte.

   ![][img1]

2. Clique na seta ao lado da fonte de instalação.
3. Em **SENDER_ID**, se você já tiver uma ID numérica do projeto para o projeto de Console de Desenvolvedor do Google, poderá adicioná-la aqui. Caso contrário, insira um valor de espaço reservado, como 777777.  Se estiver direcionando o projeto para o Android, atualize esse valor no arquivo config.xml mais tarde.
4. Clique em **Adicionar**.

O plug-in de push agora está instalado.

#### <a name="install-the-device-plugin"></a>Instalar o plug-in do dispositivo
Siga o mesmo procedimento usado para instalar o plug-in de push.  Adicionar o plug-in do Dispositivo na lista de plug-ins do Núcleo (clique em **Núcleo** > **de Plug-ins** para encontrá-lo). Você precisa desse plug-in para obter o nome da plataforma.

#### <a name="register-your-device-on-application-start-up"></a>Registrar seu dispositivo na inicialização do aplicativo
Inicialmente, incluiremos alguns códigos mínimos para o Android. Posteriormente, modifique o aplicativo para ser executado no iOS ou Windows 10.

1. Adicione uma chamada para **registerForPushNotifications** durante o retorno de chamada para o processo de logon ou na parte inferior do método **onDeviceReady**:

        // Login to the service.
        client.login('google')
            .then(function () {
                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                    // Added to register for push notifications.
                registerForPushNotifications();

            }, handleError);

    Este exemplo mostra a chamada **registerForPushNotifications** após a autenticação bem-sucedida.  Você pode chamar `registerForPushNotifications()` sempre que precisar.

2. Adicione o novo método **registerForPushNotifications** da seguinte maneira:

        // Register for Push Notifications. Requires that phonegap-plugin-push be installed.
        var pushRegistration = null;
        function registerForPushNotifications() {
          pushRegistration = PushNotification.init({
              android: { senderID: 'Your_Project_ID' },
              ios: { alert: 'true', badge: 'true', sound: 'true' },
              wns: {}
          });

        // Handle the registration event.
        pushRegistration.on('registration', function (data) {
          // Get the native platform of the device.
          var platform = device.platform;
          // Get the handle returned during registration.
          var handle = data.registrationId;
          // Set the device-specific message template.
          if (platform == 'android' || platform == 'Android') {
              // Register for GCM notifications.
              client.push.register('gcm', handle, {
                  mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'iOS') {
              // Register for notifications.
              client.push.register('apns', handle, {
                  mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'windows') {
              // Register for WNS notifications.
              client.push.register('wns', handle, {
                  myTemplate: {
                      body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                      headers: { 'X-WNS-Type': 'wns/toast' } }
              });
          }
        });

        pushRegistration.on('notification', function (data, d2) {
          alert('Push Received: ' + data.message);
        });

        pushRegistration.on('error', handleError);
        }
3. (Android) No código anterior, substitua `Your_Project_ID` pela ID numérica do projeto do aplicativo no [Console do Desenvolvedor do Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Opcional) Configurar e executar o aplicativo no Android
Conclua esta seção para habilitar notificações por push para o Android.

#### <a name="enable-gcm"></a>Habilitar mensagens de nuvem Firebase
Como nosso alvo é a plataforma Google Android, primeiro você deve habilitar as mensagens de nuvem Firebase.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Configurar seu back-end de Aplicativo Móvel para enviar solicitações por push usando FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Configurar seu aplicativo Cordova para Android
Em seu aplicativo Cordova, abra o arquivo config.xml e substitua `Your_Project_ID` pelo valor numérico da ID de projeto para seu aplicativo no [Console de Desenvolvedor do Google][18].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Abra index.js e atualize o código para usar o valor numérico da ID do projeto.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

#### <a name="configure-device"></a>Configurar seu dispositivo Android para depuração USB
Antes de implantar seu aplicativo em seu dispositivo Android, você precisa habilitar a Depuração USB.  Execute as etapas a seguir em seu telefone com Android:

1. Acesse **Configurações** > **Sobre o telefone** e, depois, toque no **Número de build** até que o modo de desenvolvedor seja habilitado (cerca de sete vezes).
2. Novamente em **Configurações** > **Opções do Desenvolvedor**, habilite a **Depuração por USB** e, depois, conecte seu telefone Android ao computador de desenvolvimento com um Cabo USB.

Testamos isso usando um dispositivo Google Nexus 5X que executa o Android 6.0 (Marshmallow).  No entanto, as técnicas são comuns em qualquer versão moderna do Android.

#### <a name="install-google-play-services"></a>Instalar os Serviços do Google Play
O plug-in de push depende dos Serviços do Google Play no Android para enviar notificações por push.

1. No Visual Studio, clique em **Ferramentas** > **Android** > **Gerenciador de SDK do Android**, expanda a pasta **Extras** e marque a caixa para garantir que cada um dos SDKs a seguir é instalado.

   * Android 2.3 ou superior
   * Google Repository revisão 27 ou superior
   * Serviços do Google Play 9.0.2 ou superior

2. Clique em **Instalar Pacotes** e aguarde a conclusão da instalação.

As bibliotecas exigidas atualmente estão listadas na [documentação de instalação do phonegap-plugin-push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testar notificações por push no aplicativo em Android
Agora é possível testar notificações por push executando o aplicativo e inserindo itens na tabela TodoItem. Você pode realizar o testo no mesmo dispositivo ou em um segundo dispositivo, desde que esteja usando o mesmo back-end. Teste seu aplicativo Cordova na plataforma Android usando uma das seguintes maneiras:

* **Em um dispositivo físico:** anexe seu dispositivo Android ao computador de desenvolvimento com um cabo USB.  Em vez de **Emulador do Google Android**, selecione **Dispositivo**. O Visual Studio implanta o aplicativo no dispositivo e o executa.  Em seguida, você poderá interagir com o aplicativo no dispositivo.

  Melhore a experiência de desenvolvimento.  O compartilhamento de tela de aplicativos como o [Mobizen][20] pode ajudar a desenvolver um aplicativo Android.  O Mobizen projeta sua tela Android para um navegador da Web em seu computador.

* **Em um emulador do Android**: há etapas de configuração adicionais exigidas durante a execução em um emulador.

    Verifique se você está implantando em um dispositivo virtual com as APIs do Google definidas como destino, conforme mostrado no Gerenciador de AVD (dispositivo virtual Android).

    ![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Se você quiser usar um emulador x86 mais rápido, [instale o driver HAXM][11] e configure o emulador para usá-lo.

    Adicione uma conta do Google para o dispositivo Android clicando em **Aplicativos** > **Configurações** > **Adicionar conta** e então siga os prompts.

    ![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Execute o aplicativo todolist como feito anteriormente s e insira um novo item de tarefa pendente. Dessa vez, um ícone de notificação é exibido na área de notificação. Você pode abrir a gaveta de notificações para exibir o texto completo da notificação.

    ![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Opcional) Configurar e executar no iOS
Esta seção é para executar o projeto Cordova em dispositivos iOS. Se não estiver trabalhando com dispositivos iOS, você poderá ignorar esta seção.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Instalar e executar o agente de build remoto do iOS em um Mac ou serviço de nuvem
Antes de executar um aplicativo Cordova no iOS usando o Visual Studio, percorra as etapas no [Guia de configuração do iOS][12] para instalar e executar o agente de build remoto.

Verifique se que você pode criar o aplicativo para iOS. As etapas no guia de instalação são necessárias para criar para iOS no Visual Studio. Se você não tiver um Mac, poderá criar para iOS usando o agente de build remoto em um serviço como o MacInCloud. Para saber mais, confira [Executar seu aplicativo iOS na nuvem][21].

> [!NOTE]
> O XCode 7 ou superior é necessário para usar o plug-in de push no iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Localizar a ID a ser usada como a ID do aplicativo
Antes de registrar seu aplicativo para notificações por push, abra o config.xml em seu aplicativo Cordova, localize o valor de atributo `id` no elemento de widget e copie-o para uso posterior. No XML a seguir, a ID é `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
          version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Depois, use esse identificador ao criar uma ID de aplicativo no portal do desenvolvedor da Apple. Se você criar uma ID do Aplicativo diferente no portal do desenvolvedor, deverá realizar algumas etapas adicionais posteriormente neste tutorial. A ID no elemento de widget deve corresponder à ID do Aplicativo no portal do desenvolvedor.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrar o aplicativo nas notificações por push no portal do desenvolvedor da Apple
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Assista a um vídeo que mostra etapas semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações por push
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Verifique se a ID do aplicativo corresponde ao aplicativo Cordova
Se a ID do aplicativo que criou na sua conta de desenvolvedor da Apple já corresponde à ID do elemento widget em config.xml, você pode ignorar esta etapa. No entanto, se as IDs não correspondem, siga estas etapas:

1. Exclua a pasta plataformas do seu projeto.
2. Exclua a pasta plugins do seu projeto.
3. Exclua a pasta node_modules do seu projeto.
4. Atualize a ID de atributo do elemento widget em config.xml para usar a ID do Aplicativo que você criou na sua Conta de Desenvolvedor da Apple.
5. Recompile o projeto.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testar notificações por push em seu aplicativo iOS
1. No Visual Studio, verifique se **iOS** está selecionado como o destino da implantação e escolha **Dispositivo** para executar no dispositivo iOS conectado.

    Você pode executar em um dispositivo iOS conectado ao seu PC usando o iTunes. O simulador do iOS não dá suporte a notificações por push.

2. Pressione o botão **Executar** ou **F5** no Visual Studio para compilar o projeto e iniciar o aplicativo em um dispositivo iOS. Em seguida, clique em **OK** para aceitar as notificações por push.

   > [!NOTE]
   > O aplicativo solicita confirmação para notificações por push durante a primeira execução.

3. No aplicativo, digite uma tarefa e, em seguida, clique no ícone do sinal de adição (+).
4. Verifique se uma notificação é recebida e clique em OK para ignorar a notificação.

## <a name="optional-configure-and-run-on-windows"></a>(Opcional) Configurar e executar no Windows
Esta seção é para executar o projeto de aplicativo do Apache Cordova em dispositivos com Windows 10 (o plug-in de notificação por push PhoneGap tem suporte no Windows 10). Se não estiver trabalhando com dispositivos Windows, você poderá ignorar esta seção.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrar seu aplicativo Windows para notificações por push com WNS
Para usar as opções de Loja no Visual Studio, selecione um destino do Windows na lista Plataformas da Solução, como **Windows x64** ou **Windows x86** (evite **Windows AnyCPU** para notificações por push).

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Assista a um vídeo que mostra etapas semelhantes][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurar o hub de notificação para WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Configurar seu aplicativo Cordova para dar suporte a notificações por push do Windows
Abra o designer de configuração (clique com o botão direito do mouse no arquivo config.xml e selecione **Designer de Exibição**), selecione a guia **Windows** e escolha **Windows 10** em **Versão de Destino do Windows**.

Para dar suporte a envio de notificações por push nas versões padrão, abra o arquivo build.json. Copie a configuração de "versão" em sua configuração de depuração.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

Após a atualização, o build.json deve conter o código a seguir:

    "windows": {
        "release": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            },
        "debug": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            }
        }

Compile o aplicativo e verifique se não há erros. Agora, o aplicativo cliente deve se registrar para notificações no back-end do Aplicativo Móvel. Repita esta seção para cada projeto do Windows em sua solução.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testar as notificações por push em seu aplicativo para Windows
No Visual Studio, verifique se uma plataforma Windows está selecionada como o destino de implantação, como **Windows x64** ou **Windows x86**. Para executar o aplicativo em um PC com Windows 10 hospedando o Visual Studio, escolha **Computador Local**.

Pressione o botão Executar para compilar o projeto e iniciar o aplicativo.

No aplicativo, digite um nome para um novo todoitem e clique no ícone de adição (+) para realizar esta ação.

Verifique se uma notificação é recebida quando o item é adicionado.

## <a name="next-steps"></a>Próximas etapas
* Leia sobre os [Hubs de Notificação][17] para saber mais sobre notificações por push.
* Se você ainda não fez isso, continue o tutorial [Adicionando Autenticação][14] para o seu aplicativo Apache Cordova.

Saiba como usar os SDKs.

* [SDK do Apache Cordova][15]
* [SDK do Servidor ASP.NET][1]
* [SDK do Servidor Node.js][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: http://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: http://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: http://taco.visualstudio.com/en-us/docs/build_ios_cloud/
