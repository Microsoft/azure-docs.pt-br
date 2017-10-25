---
title: "Adicionar notificações por push ao seu aplicativo da UWP (Plataforma Universal do Windows) | Microsoft Docs"
description: "Saiba como usar os Aplicativos Móveis do Serviço de Aplicativo do Azure e Hubs de Notificação do Azure para enviar notificações por push para seu aplicativo da UWP (Plataforma Universal do Windows)."
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.openlocfilehash: a14bb0320c1f6a563f766a6a0fad5cf556fe7b70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-windows-app"></a>Adicionar notificações por push ao seu aplicativo do Windows
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Visão geral
Neste tutorial, você adicionará notificações por push ao projeto de [Início rápido do Windows](app-service-mobile-windows-store-dotnet-get-started.md) de forma que sempre que um registro for inserido, uma notificação por push seja enviada.

Se você não usar o projeto baixado do início rápido do servidor, deve adicionar o pacote de extensão de notificação por push ao seu projeto. Confira [Trabalhar com o servidor back-end SDK do .NET para os Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

## <a name="configure-hub"></a>Configurar um novo Hub de Notificações
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registrar seu aplicativo para notificações por push
Envie seu aplicativo na Windows Store e, em seguida, configure seu projeto de servidor para integrar com os Serviços de Notificação do Windows (WNS) para enviar por push.

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo da UWP, clique em **Loja** > **associar Aplicativo à Loja...**.

    ![Associar aplicativo com a Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
2. No assistente, clique em **Avançar**, entre com sua conta da Microsoft, digite um nome para seu aplicativo em **Reservar um novo nome de aplicativo** e clique em **Reservar**.
3. Depois que o registro do aplicativo for criado com êxito, selecione o novo nome do aplicativo, clique em **Avançar** e em **Associar**. Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.  
4. Navegue até o [Centro de Desenvolvimento do Windows](https://dev.windows.com/en-us/overview), entre com sua conta da Microsoft, clique no registro do novo aplicativo em **Meus aplicativos** e expanda **Serviços** > **Notificações por push**.
5. Na página **Notificações por push**, clique em **Site dos Serviços ao Vivo** em **Serviços Móveis do Microsoft Azure**.
6. Na página de registro, anote o valor em **Segredos do aplicativo** e **SID do pacote**, que, em seguida, você usará para configurar o back-end do seu aplicativo móvel.

    ![Associar aplicativo com a Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > O segredo do cliente e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses valores com ninguém nem os distribua com seu aplicativo. A **ID do aplicativo** é usada com o segredo para configurar a autenticação da conta da Microsoft.
   >
   >

## <a name="configure-the-backend-to-send-push-notifications"></a>Configurar o back-end para enviar notificações por push
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Atualizar o servidor para enviar notificações por push
Use o procedimento abaixo, que corresponde ao seu tipo de projeto de back-end&mdash;, um [back-end .NET](#dotnet) ou [back-end Node.js](#nodejs).

### <a name="dotnet"></a>Projeto de back-end .NET
1. No Visual Studio, clique com o botão direito do mouse no projeto do servidor e clique em **Gerenciar Pacotes NuGet**, pesquise por Microsoft.Azure.NotificationHubs e então clique em **Instalar**. Isso instala a biblioteca de cliente de Hubs de notificação.
2. Expanda **Controladores**, abra TodoItemController.cs e adicione os elementos a seguir usando instruções:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. No método **PostTodoItem**, adicione o seguinte código após a chamada para **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create the notification hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Define a WNS payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                                + item.Text + @"</text></binding></visual></toast>";
        try
        {
            // Send the push notification.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Esse código informa o hub de notificação para enviar uma notificação por push após uma inserção de item nova.
4. Republicar o projeto de servidor.

### <a name="nodejs"></a>Projeto de back-end Node.js
1. Se você ainda não fez isso, [baixe o projeto de início rápido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou, caso contrário, use o [editor online no Portal do Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Substitua o código existente no arquivo todoitem.js pelo código a seguir:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the WNS payload that contains the new item Text.
        var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                    + context.item.text + "</text></binding></visual></toast>";

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a WNS native toast notification.
                    context.push.wns.sendToast(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;

    Isso envia uma notificação WNS que contém o item.text quando um novo item todo é inserido.
3. Ao editar o arquivo no seu computador local, republique o projeto do servidor.

## <a id="update-app"></a>Adicionar notificações de push para seu aplicativo
Em seguida, seu aplicativo deve se registrar para notificações por push na inicialização. Quando você já tiver habilitado a autenticação, certifique-se de que o usuário entre antes de tentar se registrar para receber notificações por push.

1. Abra o arquivo de projeto **App.xaml.cs** e adicione as instruções `using` a seguir:

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
2. No mesmo arquivo, adicione a seguinte definição de método **InitNotificationsAsync** à classe de **aplicativo**:

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Esse código recupera o ChannelURI do aplicativo de WNS e registra esse ChannelURI com seu Aplicativo Móvel do Serviço de Aplicativo.
3. Na parte superior do manipulador de eventos **OnLaunched** no **App.xaml.cs**, adicione o modificador **async** à definição do método e adicione a seguinte chamada ao novo método **InitNotificationsAsync**, como mostrado no seguinte exemplo:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    Isso garante que o ChannelURI de curta duração seja registrado sempre que o aplicativo for iniciado.
4. Recompile seu projeto de aplicativo da UWP. Seu aplicativo agora está pronto para receber notificações do sistema.

## <a id="test"></a>Testar notificações por push no seu aplicativo
[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Próximas etapas
Saiba mais sobre as notificações por push:

* [Como usar o cliente gerenciado para aplicativos móveis do Azure](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications)  
  modelos oferecem flexibilidade para enviar envios de plataforma cruzada e pushes localizados. Saiba como registrar modelos.
* [Diagnosticar problemas com notificações por push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  há vários motivos por que as notificações podem ser abandonadas ou não irem terminar nos dispositivos. Este tópico mostra como analisar e descobrir a causa de falhas de notificação por push.

Considere a possibilidade de prosseguir com um dos seguintes tutoriais:

* [Adicionar autenticação ao seu aplicativo](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.
* [Habilitar sincronização offline para seu aplicativo](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar suporte offline ao seu aplicativo usando um back-end de Aplicativo Móvel. Sincronização offline permite que os usuários finais interajam com um aplicativo móvel, &mdash;exibindo, adicionando ou modificando dados&mdash;, mesmo quando não há conexão de rede.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
