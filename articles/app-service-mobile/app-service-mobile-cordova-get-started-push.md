---
title: Adicionar notificações por push a um aplicativo Apache Cordova com o recurso Aplicativos Móveis do Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como usar Aplicativos Móveis para enviar notificações por push para seu aplicativo Apache Cordova.
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: ''
author: elamalani
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: e6755c3fb1fca342d94fdaa96c0dce614d762172
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443563"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Adicionar notificações por push ao seu aplicativo Apache Cordova

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-get-started-push) hoje mesmo.
>

## <a name="overview"></a>Visão geral

Neste tutorial, você adicionará notificações por push para o [guia de início rápido do Apache Cordova][5] de projeto para que uma notificação por push é enviada para o dispositivo sempre que um registro for inserido.

Se você não usar o projeto baixado do início rápido do servidor, você precisará do pacote de extensão de notificação por push. Para obter mais informações, consulte [trabalhar com o servidor de back-end do .NET SDK para aplicativos móveis][1].

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial assume que você possui um aplicativo Apache Cordova que foi desenvolvido com o Visual Studio 2015. Esse dispositivo deve ser executado no Emulador do Google Android, um dispositivo Android, um dispositivo Windows ou um dispositivo iOS.

Para concluir este tutorial, você precisará:

* Um PC com [Visual Studio Community 2015][2] ou posterior
* [Ferramentas do Visual Studio para Apache Cordova][4]
* Um [conta ativa do Azure][3]
* Um concluídos [guia de início rápido do Apache Cordova][5] projeto
* (Android) Um [conta do Google][6] com um endereço de email verificado
* (iOS) Uma [programa de desenvolvedores da Apple][7] e um dispositivo iOS (iOS Simulator não suporta enviar notificações por push)
* (Windows) Um [conta de desenvolvedor do Microsoft Store][8] e um dispositivo Windows 10

## <a name="configure-hub"></a>Configurar um Hub de Notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Assista a um vídeo mostrando as etapas nesta seção][9].

## <a name="update-the-server-project"></a>Atualizar o projeto de servidor

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Modificar seu aplicativo Cordova

Para garantir que seu projeto de aplicativo do Apache Cordova esteja pronto para lidar com notificações por push, instale o plug-in de push do Cordova e todos os serviços de push específicos da plataforma.

#### <a name="update-the-cordova-version-in-your-project"></a>Atualize a versão do Cordova em seu projeto.

Se o seu projeto usa uma versão do Apache Cordova anterior à versão 6.1.1, atualize o projeto do cliente. Para atualizar o projeto, execute as etapas a seguir:

* Para abrir o designer de configuração, clique com o botão direito do mouse em `config.xml`.
* Selecione a guia **Plataformas**.
* Na caixa de texto **Cordova CLI**, selecione **6.1.1**. 
* Para atualizar o projeto, selecione **Compilar** e, em seguida, **Compilar Solução**.

#### <a name="install-the-push-plugin"></a>Instalar o plug-in de push

Os aplicativos Apache Cordova não manipulam recursos de rede ou do dispositivo de forma nativa.  Essas funcionalidades são fornecidas por plug-ins publicados no [npm][10] ou no GitHub. O plug-in `phonegap-plugin-push` manipula as notificações por push de rede.

É possível instalar o plug-in de push com uma das seguintes maneiras:

**No prompt de comando:**

Execute o comando a seguir:

    cordova plugin add phonegap-plugin-push

**No Visual Studio:**

1. No Gerenciador de Soluções, abra o arquivo `config.xml`. Em seguida, selecione **Plug-ins** > **Personalizar**. Em seguida, selecione **Git** como a origem da instalação.

2. Insira `https://github.com/phonegap/phonegap-plugin-push` como a origem.

    ![Abra o arquivo config.xml file no Gerenciador de Soluções][img1]

3. Selecione a seta próxima à origem de instalação.

4. Em **SENDER_ID**, se você já tiver uma ID numérica do projeto para o projeto de Console de Desenvolvedor do Google, poderá adicioná-la aqui. Caso contrário, insira um valor de espaço reservado, como 777777. Se você estiver destinando ao Android, será possível atualizar esse valor no arquivo config.xml posteriormente.

    >[!NOTE]
    >A partir da versão 2.0.0, o google-services.json precisa ser instalado no diretório raiz do seu projeto para configurar a ID do remetente. Para obter mais informações, consulte a [documentação de instalação.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Selecione **Adicionar**.

O plug-in de push agora está instalado.

#### <a name="install-the-device-plugin"></a>Instalar o plug-in do dispositivo

Siga o mesmo procedimento usado para instalar o plug-in de push. Adicione o plug-in do Dispositivo da lista de plug-ins do Núcleo. (Para localizá-lo, selecione **Plug-ins** > **Núcleo**.) Você precisa desse plug-in para obter o nome da plataforma.

#### <a name="register-your-device-when-the-application-starts"></a>Registrar o dispositivo quando o aplicativo iniciar 

Inicialmente, incluiremos alguns códigos mínimos para o Android. Posteriormente, você poderá modificar o aplicativo para executar em iOS ou Windows 10.

1. Adicione uma chamada para **registerForPushNotifications** durante o retorno de chamada para o processo de entrada. Como alternativa, é possível adicioná-la na parte inferior do método **onDeviceReady**:

    ```javascript
    // Log in to the service.
    client.login('google')
        .then(function () {
            // Create a table reference.
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems.
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item.
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

                // Added to register for push notifications.
            registerForPushNotifications();

        }, handleError);
    ```

    Este exemplo mostra a chamada **registerForPushNotifications** após a autenticação bem-sucedida. Você pode chamar `registerForPushNotifications()` sempre que precisar.

2. Adicione o novo método **registerForPushNotifications** da seguinte maneira:

    ```javascript
    // Register for push notifications. Requires that phonegap-plugin-push be installed.
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
    ```
3. (Android) No código anterior, substitua `Your_Project_ID` com o valor numérico ID do projeto de seu aplicativo a partir de [Console do desenvolvedor do Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Opcional) Configurar e executar o aplicativo no Android

Conclua esta seção para habilitar notificações por push para o Android.

#### <a name="enable-gcm"></a>Habilitar mensagens de nuvem Firebase

Como você está destinando à plataforma Google Android inicialmente, será necessário habilitar o Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Configurar o back-end de Aplicativo Móvel para enviar solicitações por push usando FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Configurar seu aplicativo Cordova para Android

No seu aplicativo Cordova, abra **config.xml**. Em seguida, substitua `Your_Project_ID` com o valor numérico ID do projeto de seu aplicativo na [Console do desenvolvedor do Google][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Abra **index.js**. Em seguida, atualize o código para usar a ID do projeto numérico.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Configurar seu dispositivo Android para depuração USB

Antes de implantar o aplicativo em seu Dispositivo Android, será necessário habilitar depuração USB. Execute as seguintes etapas no seu telefone Android:

1. Vá para **Configurações** > **Sobre o telefone** . Em seguida, toque o **Número de build** até que o modo de desenvolvedor seja ativado (aproximadamente de sete vezes).
2. Retorne para **Configurações** > **Opções do Desenvolvedor**, habilite **depuração USB**. Em seguida, conecte seu telefone Android ao seu computador de desenvolvimento com um cabo USB.

Testamos isso usando um dispositivo Google Nexus 5X que executa o Android 6.0 (Marshmallow). No entanto, as técnicas são comuns em qualquer versão moderna do Android.

#### <a name="install-google-play-services"></a>Instalar os Serviços do Google Play

O plug-in de push depende dos Serviços do Google Play no Android para enviar notificações por push.

1. No Visual Studio, selecione **Ferramentas** > **Android** > **Gerenciador do SDK do Android**. Em seguida, expanda a pasta **Extras**. Verifique as caixas apropriadas para garantir que cada um dos seguintes SDKs esteja instalado:

   * Android 2.3 ou superior
   * Google Repository revisão 27 ou superior
   * Serviços do Google Play 9.0.2 ou superior

2. Selecione **Instalar Pacotes**. Em seguida, aguarde a conclusão da instalação.

As bibliotecas exigidas atualmente estão listadas na [documentação de instalação do phonegap-plugin-push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testar notificações por push no aplicativo em Android

Agora é possível testar notificações por push executando o aplicativo e inserindo itens na tabela TodoItem. Você pode testar a partir do mesmo dispositivo ou de um segundo dispositivo, desde que esteja usando o mesmo back-end. Teste seu aplicativo Cordova na plataforma Android usando uma das seguintes maneiras:

* *Em um dispositivo físico:*  anexe seu dispositivo Android ao computador de desenvolvimento com um cabo USB.  Em vez de **Emulador do Google Android**, selecione **Dispositivo**. O Visual Studio implanta o aplicativo no dispositivo e executa o aplicativo. Em seguida, você poderá interagir com o aplicativo no dispositivo.

  Aplicativos de compartilhamento de tela, como [Mobizen][20] pode ajudá-lo a desenvolver aplicativos Android. O Mobizen projeta sua tela Android para um navegador da Web em seu computador.

* *Em um emulador do Android:* são necessárias etapas de configuração adicionais quando você estiver usando um emulador.

    Verifique se você está implantando em um dispositivo virtual com as APIs do Google definidas como destino, conforme mostrado no Gerenciador de AVD (dispositivo virtual Android).

    ![Gerenciador de Dispositivo Virtual do Android](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Se você quiser usar um x86 mais rápido emulador [instalar o driver HAXM][11]e, em seguida, configure o emulador para usá-lo.

    Adicione uma conta do Google ao dispositivo Android, selecionando **Aplicativos** > **Configurações** > **Adicionar conta**. Em seguida, siga os prompts.

    ![Adicionar uma conta do Google no dispositivo Android](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Execute o aplicativo todolist como feito anteriormente s e insira um novo item de tarefa pendente. Dessa vez, um ícone de notificação é exibido na área de notificação. Você pode abrir a gaveta de notificações para exibir o texto completo da notificação.

    ![Exibir notificação](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Opcional) Configurar e executar no iOS

Esta seção é para executar o projeto Cordova em dispositivos iOS. Se você não estiver trabalhando com dispositivos iOS, poderá ignorar esta seção.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Instalar e executar o agente de build remoto do iOS em um Mac ou serviço de nuvem

Antes de executar um aplicativo Cordova no iOS usando o Visual Studio, percorra as etapas a [guia de configuração do iOS][12] para instalar e executar o agente de build remoto.

Verifique se que você pode criar o aplicativo para iOS. As etapas no guia de instalação são necessárias para compilar o aplicativo para iOS do Visual Studio. Se você não tiver um Mac, poderá criar para iOS usando o agente de build remoto em um serviço como o MacInCloud. Para obter mais informações, consulte [executar seu aplicativo iOS na nuvem][21].

> [!NOTE]
> O Xcode 7 ou superior é necessário para usar o plug-in de push no iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Localizar a ID a ser usada como a ID do aplicativo

Antes de registrar seu aplicativo para notificações por push, abra o config.xml em seu aplicativo Cordova, localize o valor do atributo `id` no elemento de widget e, em seguida, copie-o para uso posterior. No XML a seguir, a ID é `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Depois, use esse identificador ao criar uma ID de aplicativo no portal do desenvolvedor da Apple. Se você criar uma ID do Aplicativo diferente no portal do desenvolvedor, deverá realizar algumas etapas adicionais posteriormente neste tutorial. A ID no elemento de widget deve corresponder à ID do Aplicativo no portal do desenvolvedor.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrar o aplicativo nas notificações por push no portal do desenvolvedor da Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Assista a um vídeo que mostra etapas semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações por push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Verifique se a ID do aplicativo corresponde ao aplicativo Cordova

Se a ID do Aplicativo que você criou na sua conta de Desenvolvedor Apple já corresponder à ID do elemento do widget no arquivo config.xml, você poderá ignorar esta etapa. No entanto, se as IDs não correspondem, siga estas etapas:

1. Exclua a pasta plataformas do seu projeto.
2. Exclua a pasta plugins do seu projeto.
3. Exclua a pasta node_modules do seu projeto.
4. Atualize o atributo ID do elemento widget no arquivo config.xml para usar a ID do aplicativo que você criou na conta de desenvolvedor da Apple.
5. Recompile o projeto.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testar notificações por push em seu aplicativo iOS

1. No Visual Studio, certifique-se de que o **iOS** está selecionado como destino de implantação. Em seguida, selecione **Dispositivo** para executar as notificações por push no dispositivo iOS conectado.

    É possível executar as notificações por push em um dispositivo iOS conectado ao seu computador com o iTunes. O simulador do iOS não dá suporte a notificações por push.

2. Selecione o botão **Executar** ou **F5** no Visual Studio para compilar o projeto e iniciar o aplicativo em um dispositivo iOS. Em seguida, selecione **OK** para aceitar as notificações por push.

   > [!NOTE]
   > O aplicativo solicita confirmação para notificações por push durante a primeira execução.

3. No aplicativo, digite uma tarefa e, em seguida, selecione o ícone de adição **(+)** .
4. Verifique se uma notificação foi recebida. Em seguida, selecione **OK** para ignorar a notificação.

## <a name="optional-configure-and-run-on-windows"></a>(Opcional) Configurar e executar no Windows

Esta seção descreve como executar o projeto do aplicativo Apache Cordova em dispositivos Windows 10 (o plug-in de push do PhoneGap tem suporte no Windows 10). Se não estiver trabalhando com dispositivos Windows, você poderá ignorar esta seção.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrar seu aplicativo Windows para notificações por push com WNS

Para usar as opções Loja no Visual Studio, selecione um destino do Windows na lista Plataformas da Solução, como, por exemplo, **Windows x64** ou **Windows x86**. (Evite o **Windows AnyCPU** para notificações por push.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Assista a um vídeo que mostra etapas semelhantes][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurar o hub de notificação para WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Configurar seu aplicativo Cordova para dar suporte a notificações por push do Windows

Abra o designer de configuração, clicando com o botão direito do mouse em **config.xml**. Em seguida, selecione **Designer de Exibição**. Em seguida, selecione a guia **Windows** e selecione **Windows 10** na **Versão de Destino do Windows**.

Para dar suporte a notificações por push em seus builds padrão (depuração), abra o arquivo **build.json**. Em seguida, copie a configuração de "versão" na configuração de depuração.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Após a atualização, o arquivo **build.json** deverá conter o código a seguir:

```json
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
```

Compile o aplicativo e verifique se não há erros. Agora, você deve registrar o aplicativo cliente para as notificações do back-end de Aplicativos Móveis. Repita esta seção para cada projeto do Windows em sua solução.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testar as notificações por push em seu aplicativo para Windows

No Visual Studio, verifique se uma plataforma Windows está selecionada como o destino de implantação, como **Windows x64** ou **Windows x86**. Para executar o aplicativo em um computador com Windows 10 hospedando o Visual Studio, escolha **Computador Local**.

1. Selecione o botão **Executar** para compilar o projeto e iniciar o aplicativo.

2. No aplicativo, digite um nome para um novo todoitem e, em seguida, selecione o ícone de adição **(+)** para adicioná-lo.

Verifique se uma notificação é recebida quando o item é adicionado.

## <a name="next-steps"></a>Próximas etapas

* Leia sobre os [Hubs de Notificação][17] para saber mais sobre notificações por push.
* Se você ainda não fez isso, continue o tutorial [adicionando autenticação][14] ao seu aplicativo Apache Cordova.

Saiba como usar os SDKs a seguir:

* [SDK do Apache Cordova][15]
* [SDK do Servidor ASP.NET][1]
* [SDK do Servidor Node.js][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: https://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: https://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: https://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: https://taco.visualstudio.com/en-us/docs/build_ios_cloud/
