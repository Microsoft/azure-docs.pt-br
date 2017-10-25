---
title: "Usar os Hubs de Notificação do Azure para enviar as últimas notícias (Plataforma Universal do Windows)"
description: "Use os Hubs de Notificação do Azure com marcações no registro para enviar as últimas notícias para um aplicativo da Plataforma Universal do Windows."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: d510e7e665adec9607aeee80802c466b363d5d5b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Usar hubs de notificação para enviar notícias recentes
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Visão geral
Este tópico mostra como usar os Hubs de Notificação do Azure para transmitir notificações de últimas notícias para um aplicativo da Windows Store ou do Windows Phone 8.1 (Sem Silverlight). Se estiver direcionando o Windows Phone 8.1 Silverlight, consulte a versão [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md). 

Depois de concluir esse processo, registre-se nas categorias de últimas notícias de seu interesse e você receberá notificações por push somente dessas categorias. Esse cenário é comum para muitos aplicativos (por exemplo, leitores de RSS ou aplicativos para fãs de música), em que as notificações devem ser enviadas para grupos de usuários que declararam um interesse nelas. 

Habilite cenários de difusão incluindo uma ou mais *marcações* durante a criação de um registro no hub de notificação. Quando as notificações são enviadas para uma marcação, todos os dispositivos que foram registrados para a marcação recebem a notificação. Como as marcações são simplesmente cadeias de caracteres, elas não precisam ser configuradas com antecedência. Para obter mais informações sobre marcações, consulte [Roteamento e expressões de marcação dos Hubs de Notificação](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Não há suporte para a versão de projetos da Windows Store e do Windows Phone 8.1 e anterior no Visual Studio 2017. Para saber mais, confira [Direcionamento e compatibilidade da plataforma Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs). 

## <a name="prerequisites"></a>Pré-requisitos
Este tópico se baseia no aplicativo criado em [Introdução aos Hubs de Notificação][get-started]. Antes de iniciar este tutorial, primeiro conclua a [Introdução aos Hubs de Notificação][get-started].

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categorias ao aplicativo
A primeira etapa é adicionar os elementos da interface do usuário à página principal existente, de modo que os usuários possam selecionar categorias nas quais se registrar. As categorias selecionadas são armazenadas no dispositivo. Quando o aplicativo é iniciado, o registro do dispositivo é criado no hub de notificação, com as categorias selecionadas como marcações.

1. Abra o arquivo de projeto MainPage.xaml e, em seguida, copie o seguinte código no elemento **Grid**:
   
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>

2. Clique com o botão direito do mouse no projeto **Compartilhado**, adicione uma nova classe chamada **Notificações**, adicione o modificador **public** à definição de classe e, depois, adicione as seguintes instruções **using** ao novo arquivo de código:
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. Copie o seguinte código para a nova classe **Notificações**:
   
        private NotificationHub hub;
   
        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }
   
        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }
   
        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }
   
        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            if (categories == null)
            {
                categories = RetrieveCategories();
            }
   
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.
   
            const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";
   
            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                    categories);
        }
   
    Essa classe usa o armazenamento local para armazenar as categorias de notícias que este dispositivo deve receber. Em vez de chamar o método *RegisterNativeAsync*, chame *RegisterTemplateAsync* para se registrar nas categorias usando um registro de modelo. 
   
    Já que talvez você deseje registrar mais de um modelo (por exemplo, um para as notificações do sistema e outro para blocos), também forneça um nome de modelo (por exemplo, “simpleWNSTemplateExample”). Nomeie os modelos para que você possa atualizá-los ou excluí-los.
   
    >[!NOTE]
    >Se um dispositivo registrar vários modelos com a mesma marcação, uma mensagem de entrada direcionada à marcação resultará na entrega de várias notificações ao dispositivo (uma para cada modelo). Esse comportamento é útil quando a mesma mensagem lógica deve resultar em várias notificações visuais (por exemplo, mostrar uma notificação e uma notificação do sistema em um aplicativo da Windows Store).
   
    Para saber mais, veja [Modelos](notification-hubs-templates-cross-platform-push-messages.md).

4. No arquivo de projeto App.xaml.cs, adicione a propriedade a seguir na classe **Aplicativo** :
   
        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
   
    Use essa propriedade para criar e acessar uma instância **Notificações**.
   
    No código, substitua os espaços reservados `<hub name>` e `<connection string with listen access>` pelo nome do hub de notificação e a cadeia de conexão por *DefaultListenSharedAccessSignature* obtida anteriormente.
   
   > [!NOTE]
   > Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, distribua apenas a chave para acesso de *escuta* com o aplicativo cliente. Com o acesso de escuta, o aplicativo pode se registrar para receber notificações, mas os registros existentes não podem ser modificados e as notificações não podem ser enviadas. A chave de acesso completo é usada em um serviço back-end protegido para enviar notificações e alterar os registros existentes.
   > 
   > 
5. No arquivo de projeto MainPage.xaml.cs, adicione a seguinte linha:
   
        using Windows.UI.Popups;

6. No arquivo de projeto MainPage.xaml.cs, adicione o seguinte método:
   
        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");
   
            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
   
            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
   
    Esse método cria uma lista de categorias e usa a classe **Notificações** para armazenar a lista no armazenamento local. Ele também registra as marcações correspondentes no hub de notificação. Quando as categorias são alteradas, o registro é recriado com as novas categorias.

O aplicativo agora pode armazenar um conjunto de categorias no armazenamento local do dispositivo. O aplicativo é registrado no hub de notificação sempre que os usuários alteram a seleção de categorias.

## <a name="register-for-notifications"></a>Registrar-se para receber notificações
Nesta seção, você se registra no hub de notificação após a inicialização usando as categorias armazenadas no armazenamento local.

> [!NOTE]
> Como o URI do canal atribuído pelo WNS (Serviço de Notificação do Windows) pode ser alterado a qualquer momento, você deve se registrar para receber notificações com frequência para evitar falhas de notificação. Este exemplo registra a notificação a cada vez que o aplicativo é iniciado. Para os aplicativos executados com frequência (mais de uma vez por dia), é possível ignorar o registro para preservar a largura de banda, caso tenha decorrido menos de um dia desde o registro anterior.
> 
> 

1. Para usar a classe `notifications` para assinar com base nas categorias, abra o arquivo App.xaml.cs e, em seguida, atualize o método **InitNotificationsAsync**.
   
        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
   
        var result = await notifications.SubscribeToCategories();
   
    Esse processo garante que quando o aplicativo for iniciado, ele recupere as categorias do armazenamento local e solicite o registro dessas categorias. Você criou o método **InitNotificationsAsync** como parte do tutorial [Introdução aos Hubs de Notificação][get-started].

2. No arquivo de projeto MainPage.xaml.cs, adicione o seguinte código ao método *OnNavigatedTo* :
   
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();
   
            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
        }
   
    Esse código atualiza a página principal, com base no status das categorias salvas anteriormente.

O aplicativo agora está concluído. Ele pode armazenar um conjunto de categorias no armazenamento local do dispositivo que é usado para se registrar no hub de notificação quando os usuários alteram a seleção de categorias. Na próxima seção, você define um back-end que pode enviar notificações de categoria para esse aplicativo.

## <a name="send-tagged-notifications"></a>Enviar notificações marcadas
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Executar o aplicativo e gerar notificações
1. No Visual Studio, selecione **F5** para compilar e iniciar o aplicativo.  
    A interface do usuário do aplicativo fornece um conjunto de alternâncias que permite escolher as categorias às quais assinar. 
   
    ![Aplicativo Últimas Notícias][1]

2. Habilite uma ou mais alternâncias de categoria e, depois, clique em **Assinar**.
   
    O aplicativo converte as categorias selecionadas em rótulos e solicita um novo registro do dispositivo para os rótulos selecionados do hub de notificação. As categorias registradas são retornadas e exibidas em uma caixa de diálogo.
   
    ![Alternâncias de categoria e botão Assinar][19]

3. Envie uma nova notificação do back-end usando uma das seguintes maneiras:

   * **Aplicativo de console:** inicie o aplicativo de console.
   * **Java/PHP:** execute o aplicativo/script.
     
     As notificações para as categorias selecionadas são exibidas como notificações do sistema.
     
     ![Notificações do sistema][14]

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a difundir as últimas notícias por categoria. Considere a possibilidade de concluir o tutorial a seguir, que ressalta outro cenário avançado dos Hubs de Notificação:

* [Usar os Hubs de Notificação para difundir as últimas notícias localizadas] Este tutorial aborda como expandir o aplicativo de últimas notícias para habilitar o envio de notificações localizadas.

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification
[Usar os Hubs de Notificação para difundir as últimas notícias localizadas]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
