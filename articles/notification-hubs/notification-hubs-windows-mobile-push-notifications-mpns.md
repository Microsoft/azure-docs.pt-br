---
title: "Enviar notificações por push com os Hubs de Notificação do Azure no Windows Phone | Microsoft Docs"
description: "Neste tutorial, você aprende a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Silverlight do Windows Phone 8 ou Windows Phone 8.1."
services: notification-hubs
documentationcenter: windows
keywords: "notificação por push,notificação por push,envio por push do windows phone"
author: ysxu
manager: erikre
editor: erikre
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: f0bfe81f849813d146d644b32490af657b1071b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sending-push-notifications-with-azure-notification-hubs-on-windows-phone"></a>Enviar notificações por push com os Hubs de Notificação do Azure no Windows Phone
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Visão geral
> [!NOTE]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).
> 
> 

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Silverlight do Windows Phone 8 ou Windows Phone 8.1. Se você estiver visando o Windows Phone 8.1 (sem Silverlight), consulte a versão [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
Neste tutorial, você cria um aplicativo Windows Phone 8 em branco que recebe notificações por push usando o MPNS (Serviço de Notificação por Push da Microsoft). Ao finalizar, você poderá usar seu hub de notificação para transmitir notificações por push a todos os dispositivos que executam seu aplicativo.

> [!NOTE]
> O SDK do Windows Phone para Hubs de Notificação não oferecem suporte ao uso do WNS (Serviço de Notificação por Push do Windows) com aplicativos Silverlight para Windows Phone 8.1. Para usar o WNS (em vez de MPNS) com aplicativos Silverlight para Windows Phone 8.1, siga o [tutorial Hubs de Notificação - Silverlight para Windows Phone], que usa APIs REST.
> 
> 

O tutorial demonstra o cenário de transmissão simples usando Hubs de Notificação.

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial exige o seguinte:

* [Visual Studio 2012 Express para Windows Phone]ou uma versão posterior.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos Windows Phone 8.

## <a name="create-your-notification-hub"></a>Criar seu Hub de Notificação
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Clique na seção <b>Serviços de Notificação</b> (em <i>Configurações</i>), clique em <b>Windows Phone (MPNS)</b> e na caixa de seleção <b>Habilitar envio por push sem autenticação</b>.</p>
</li>
</ol>

&emsp;&emsp;![Portal do Azure - habilitar notificações por push não autenticadas](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

O hub foi criado e configurado para enviar a notificação não autenticada para o Windows Phone.

> [!NOTE]
> Este tutorial usa MPNS no modo não autenticado. O modo não autenticado MPNS é fornecido com restrições nas notificações que você pode enviar para cada canal. Os Hubs de Notificação dão suporte ao [modo autenticado do MPNS](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) , permitindo que você carregue seu certificado.
> 
> 

## <a name="connecting-your-app-to-the-notification-hub"></a>Conectando seu aplicativo ao Hub de Notificação
1. No Visual Studio, crie um novo aplicativo de console do Windows Phone 8.
   
       ![Visual Studio - New Project - Windows Phone App][13]
   
    No Visual Studio 2013 Atualização 2 ou posterior, você cria um aplicativo do Windows Phone Silverlight.
   
    ![Visual Studio - Novo Projeto - Aplicativo em branco - Windows Phone Silverlight][11]
2. No Visual Studio, clique com o botão direito do mouse na solução e clique em **Gerenciar Pacotes NuGet**.
   
    Isso mostra a caixa de diálogo **Gerenciar Pacotes NuGet** .
3. Procure `WindowsAzure.Messaging.Managed` , clique em **Instalar**e aceite os termos de uso.
   
    ![Visual Studio - Gerenciador de Pacotes do NuGet][20]
   
    Isso baixará, instalará, e adicionará uma referência à biblioteca de Mensagens do Azure para Windows usando o <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">pacote NuGet WindowsAzure.Messaging.Managed</a>.
4. Abra o arquivo App.xaml.cs e adicione as seguintes instruções de `using` :
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. Adicione o código a seguir à parte superior do método **Application_Launching** no App.xaml.cs:
   
        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }
   
        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());
   
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
   
   > [!NOTE]
   > O valor **MyPushChannel** é um índice que é usado para pesquisar um canal existente na coleção [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) . Se não houver uma, crie uma nova entrada com esse nome.
   > 
   > 
   
    Insira o nome de seu hub e a cadeia de conexão chamada **DefaultListenSharedAccessSignature** que você obteve na seção anterior.
    Esse código recupera no MPNS o URI do canal para o aplicativo e registra esse URI do canal no hub de notificação. Isso também garante que o URI do canal seja registrado em seu hub de notificação toda vez que o aplicativo é iniciado.
   
   > [!NOTE]
   > Este tutorial envia uma notificação do sistema ao dispositivo. Ao enviar uma notificação de bloco, você deve chamar o método **BindToShellTile** no canal. Para oferecer suporte às notificações em bloco e do sistema, chame ambos **BindToShellTile** e **BindToShellToast**.
   > 
   > 
6. No Gerenciador de Soluções, expanda **Propriedades**, abra o arquivo `WMAppManifest.xml`, clique na guia **Recursos** e verifique se a funcionalidade **ID_CAP_PUSH_NOTIFICATION** está marcada.
   
       ![Visual Studio - Windows Phone App Capabilities][14]
   
       This ensures that your app can receive push notifications. Without it, any attempt to send a push notification to the app will fail.
7. Pressione a tecla `F5` para executar o aplicativo.
   
    Uma mensagem de registro é exibida no aplicativo.
8. Feche o aplicativo.  
   
   > [!NOTE]
   > Para receber uma notificação do sistema por push, o aplicativo não deve estar em execução em primeiro plano.
   > 
   > 

## <a name="send-push-notifications-from-your-backend"></a>Enviar notificações por push de seu back-end
Você pode enviar notificações por push usando os Hubs de Notificação de qualquer back-end por meio da <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a> pública. Neste tutorial, você envia notificações por push usando um aplicativo de console .NET. 

Para obter um exemplo de como enviar notificações por push de um back-end WebAPI ASP.NET que esteja integrado a Hubs de Notificação, confira [Hubs de Notificação do Azure notificam os usuários com back-end .NET](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md).  

Para obter um exemplo de como enviar notificações por push usando as [APIs REST](https://msdn.microsoft.com/library/azure/dn223264.aspx), confira [Como usar Hubs de Notificação do Java](notification-hubs-java-push-notification-tutorial.md) e [Como usar Hubs de Notificação do PHP](notification-hubs-php-push-notification-tutorial.md).

1. Clique com o botão direito do mouse na solução, selecione **Adicionar** e **Novo Projeto...** e, em **Visual C#**, clique em **Windows**, **Aplicativo do Console** e em **OK**.
   
       ![Visual Studio - New Project - Console Application][6]
   
    Isso adiciona um novo aplicativo de console do Visual C# à solução. Você também pode fazer isso em uma solução separada.
2. Clique em **Ferramentas**, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**.
   
    Isso exibe a Console do Gerenciador de Pacotes.
3. Na janela do **Console do Gerenciador de Pacotes**, defina o **Projeto padrão** como o seu novo projeto de aplicativo do console e execute o seguinte comando na janela do console:
   
       Install-Package Microsoft.Azure.NotificationHubs
   
   Isso adiciona uma referência ao SDK dos Hubs de Notificação do Azure usando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacote NuGet Microsoft.Azure.Notification Hubs</a>.
4. Abra o arquivo `Program.cs` e adicione a seguinte instrução `using`:
   
        using Microsoft.Azure.NotificationHubs;
5. Na classe `Program`, adicione o seguinte método:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }
   
    Substitua o espaço reservado `<hub name>` pelo nome do hub de notificação que aparece no portal. Além disso, substitua o espaço reservado da cadeia de conexão pela cadeia de conexão chamada **DefaultFullSharedAccessSignature** que você obteve na seção “Configurar seu hub de notificação”.
   
   > [!NOTE]
   > Verifique se você está usando a cadeia de conexão com acesso **Completo**, não com acesso para **Escutar**. A cadeia de acesso de escuta não tem permissões para enviar notificações por push.
   > 
   > 
6. Adicione a seguinte linha ao método `Main` :
   
         SendNotificationAsync();
         Console.ReadLine();
7. Com o emulador do Windows Phone em execução e o aplicativo fechado, defina o projeto do aplicativo de console como o projeto de inicialização padrão e pressione a tecla `F5` para executar o aplicativo.
   
    Você receberá uma notificação do sistema por push. Tocar na faixa de navegação carrega o aplicativo.

Você pode encontrar todas as cargas possíveis nos tópicos [catálogo de notificações do sistema] e [catálogo de blocos] no MSDN.

## <a name="next-steps"></a>Próximas etapas
Neste exemplo simples, você transmitiu notificações por push a todos os seus dispositivos Windows Phone 8. 

Para direcionar usuários específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários] . 

Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes]. 

Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação].

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Usar Hubs de Notificação para enviar notificações por push aos usuários]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Usar Hubs de Notificação para enviar notícias mais recentes]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[catálogo de notificações do sistema]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catálogo de blocos]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[tutorial Hubs de Notificação - Silverlight para Windows Phone]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

