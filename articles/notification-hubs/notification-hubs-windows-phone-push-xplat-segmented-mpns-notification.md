---
title: Enviar notificações por push para Windows Phones específicos usando Hubs de Notificação do Azure | Microsoft Docs
description: Neste tutorial, você aprende a usar os Hubs de Notificação do Azure para enviar notificações por push dispositivos Windows Phone 8 ou Windows Phone 8.1 específicos (não todos) registrados com o back-end do aplicativo.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 42726bf5-cc82-438d-9eaa-238da3322d80
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 67700ec643a27f8ae5c581fe9de8e3295a1925e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88998230"
---
# <a name="tutorial-send-push-notifications-to-specific-windows-phones-using-azure-notification-hubs"></a>Tutorial: Enviar notificações por push para Windows Phones específicos usando os Hubs de Notificação do Azure

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a dispositivos Windows Phone 8 ou Windows Phone 8.1 específicos. Se você estiver visando o Windows Phone 8.1 (sem Silverlight), confira a versão [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) deste tutorial.

Você habilita esse cenário por meio da inclusão de uma ou mais *marcas* durante a criação de um registro no hub de notificação. Quando as notificações são enviadas para uma marcação, todos os dispositivos que foram registrados para a marcação recebem a notificação. Para obter mais informações sobre marcas, consulte [Marcas em registros](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> O SDK do Windows Phone para Hubs de Notificação não oferecem suporte ao uso do WNS (Serviço de Notificação por Push do Windows) com aplicativos Silverlight para Windows Phone 8.1. Para usar o WNS (em vez de MPNS) com aplicativos Silverlight para Windows Phone 8.1, siga o [tutorial Hubs de Notificação - Silverlight para Windows Phone], que usa APIs REST.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar a seleção de categorias ao aplicativo móvel
> * Registrar-se para receber notificações com marcas
> * Enviar notificações marcadas
> * Testar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Concluir o [Tutorial: Enviar notificações para aplicativos do Windows Phone usando Hubs de Notificação do Azure](notification-hubs-windows-mobile-push-notifications-mpns.md). Neste tutorial, você atualiza o aplicativo móvel para poder se registrar nas categorias de últimas notícias que desejar e receber notificações por push apenas para essas categorias.

## <a name="add-category-selection-to-the-mobile-app"></a>Adicionar a seleção de categorias ao aplicativo móvel

A primeira etapa é adicionar os elementos da interface do usuário na página principal existente que permitem ao usuário selecionar categorias para o registro. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo é iniciado, o registro do dispositivo é criado no seu hub de notificação com as categorias selecionadas como rótulos.

1. Abra o arquivo `MainPage.xaml`, substitua os elementos `Grid` chamados `TitlePanel` e `ContentPanel` pelo seguinte código:

    ```xml
    <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
        <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
        <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
    </StackPanel>

    <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
        <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
        <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
        <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
        <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
        <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
2. Adicione uma classe denominada `Notifications` ao projeto. Adicione o modificador `public` à definição de classe. Em seguida, adicione as instruções `using` a seguir ao novo arquivo:

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    using System.IO.IsolatedStorage;
    using System.Windows;
    ```
3. Copie o código a seguir na nova classe `Notifications`:

    ```csharp
    private NotificationHub hub;

    // Registration task to complete registration in the ChannelUriUpdated event handler
    private TaskCompletionSource<Registration> registrationTask;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
        return categories != null ? categories.Split(',') : new string[0];
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        var categoriesAsString = string.Join(",", categories);
        var settings = IsolatedStorageSettings.ApplicationSettings;
        if (!settings.Contains("categories"))
        {
            settings.Add("categories", categoriesAsString);
        }
        else
        {
            settings["categories"] = categoriesAsString;
        }
        settings.Save();

        return await SubscribeToCategories();
    }

    public async Task<Registration> SubscribeToCategories()
    {
        registrationTask = new TaskCompletionSource<Registration>();

        var channel = HttpNotificationChannel.Find("MyPushChannel");

        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
            channel.ChannelUriUpdated += channel_ChannelUriUpdated;

            // This is optional, used to receive notifications while the app is running.
            channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
        }

        // If channel.ChannelUri is not null, complete the registrationTask here.  
        // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
        if (channel.ChannelUri != null)
        {
            await RegisterTemplate(channel.ChannelUri);
        }

        return await registrationTask.Task;
    }

    async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
    {
        await RegisterTemplate(e.ChannelUri);
    }

    async Task<Registration> RegisterTemplate(Uri channelUri)
    {
        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                            "<wp:Toast>" +
                                                "<wp:Text1>$(messageParam)</wp:Text1>" +
                                            "</wp:Toast>" +
                                        "</wp:Notification>";

        // The stored categories tags are passed with the template registration.

        registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(),
            templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

        return await registrationTask.Task;
    }

    // This is optional. It is used to receive notifications while the app is running.
    void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
    {
        StringBuilder message = new StringBuilder();
        string relativeUri = string.Empty;

        message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

        // Parse out the information that was part of the message.
        foreach (string key in e.Collection.Keys)
        {
            message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

            if (string.Compare(
                key,
                "wp:Param",
                System.Globalization.CultureInfo.InvariantCulture,
                System.Globalization.CompareOptions.IgnoreCase) == 0)
            {
                relativeUri = e.Collection[key];
            }
        }

        // Display a dialog of all the fields in the toast.
        System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
        {
            MessageBox.Show(message.ToString());
        });
    }
    ```

    Essa classe usa o armazenamento isolado para armazenar as categorias de notícias que este dispositivo deverá receber. Além disso, ela contém métodos para se registrar nessas categorias usando um registro de notificações de [modelo](notification-hubs-templates-cross-platform-push-messages.md) .
4. No arquivo de projeto `App.xaml.cs`, adicione a seguinte propriedade à classe `App`. Substitua os espaços reservados `<hub name>` e `<connection string with listen access>` pelo nome de seu hub de notificação e a cadeia de conexão para *DefaultListenSharedAccessSignature* obtidos anteriormente.

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

   > [!NOTE]
   > Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, você só deve distribuir a chave para acesso de escuta com o aplicativo cliente. O acesso de escuta permite que seu aplicativo se registre para receber notificações, mas os registros existentes não podem ser modificados e as notificações não podem ser enviadas. A chave de acesso completo é usada em um serviço back-end protegido para enviar notificações e alterar os registros existentes.

5. No `MainPage.xaml.cs`, adicione a seguinte linha:

    ```csharp
    using Windows.UI.Popups;
    ```
6. No arquivo de projeto MainPage.xaml.cs, adicione o seguinte método:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldCheckBox.IsChecked == true) categories.Add("World");
        if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
        if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
        if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
        if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
        if (SportsCheckBox.IsChecked == true) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
            result.RegistrationId);
    }
    ```

    Esse método cria uma lista de categorias e usa a classe `Notifications` para armazenar a lista no armazenamento local e registrar as marcas correspondentes com o hub de notificação. Quando as categorias são alteradas, o registro é recriado com as novas categorias.

Seu aplicativo agora é capaz de armazenar um conjunto de categorias no armazenamento local do dispositivo e registrar com o hub de notificação, sempre que o usuário alterar a seleção de categorias.

## <a name="register-for-notifications"></a>Registrar-se para receber notificações

Estas etapas registram com o hub de notificação na inicialização, usando as categorias que foram armazenadas no armazenamento local.

> [!NOTE]
> Como o URI do canal atribuído pelo MPNS (Serviço de Notificação por Push da Microsoft) pode mudar a qualquer momento, você deve se registrar para receber notificações com frequência para evitar falhas de notificação. Este exemplo registra notificações cada vez que o aplicativo é iniciado. Para os aplicativos que são executados com frequência, mais de uma vez por dia, é possível ignorar o registro para preservar a largura de banda se tiver passado menos de um dia desde o registro anterior.

1. Abra o arquivo App.xaml.cs e adicione o modificador `async` ao método `Application_Launching` e substitua o código do registro de Hubs de Notificação que você adicionou na [Introdução aos Hubs de Notificação] pelo seguinte código:

    ```csharp
    private async void Application_Launching(object sender, LaunchingEventArgs e)
    {
        var result = await notifications.SubscribeToCategories();

        if (result != null)
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
    }
    ```

    Esse código garante que o aplicativo recupere as categorias do armazenamento local e solicite um registro para essas categorias toda vez que ele for iniciado.
2. No arquivo de projeto MainPage.xaml.cs, adicione o seguinte código que implementa o método `OnNavigatedTo`:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
        if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
        if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
        if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
        if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
        if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
    }
    ```

    Esse código atualiza a página principal, com base no status das categorias salvas anteriormente.

O aplicativo agora está completo e pode armazenar um conjunto de categorias no armazenamento local do dispositivo utilizado para registrá-las com o hub de notificação, sempre que o usuário alterar a seleção de categorias. Em seguida, defina um back-end que possa enviar notificações de categoria para esse aplicativo.

## <a name="send-tagged-notifications"></a>Enviar notificações marcadas

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Testar o aplicativo

1. No Visual Studio, pressione F5 para compilar e iniciar o aplicativo.

    ![Aplicativo móvel com categorias][1]

    A interface do usuário do aplicativo fornece um conjunto de alternâncias que permite escolher as categorias às quais assinar.
2. Habilite uma ou mais alternâncias de categorias e depois clique em **Assinar**.

    O aplicativo converte as categorias selecionadas em rótulos e solicita um novo registro do dispositivo para os rótulos selecionados do hub de notificação. As categorias registradas são retornadas e exibidas em uma caixa de diálogo.

    ![Mensagem assinada][2]
3. Depois de receber uma confirmação de que a assinatura de suas categorias está concluída, execute o aplicativo de console para enviar notificações para cada categoria. Confirme que você receba apenas uma notificação para as categorias nas quais você se inscreveu.

    ![Mensagem de notificação][3]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como enviar notificações por push para dispositivos específicos que têm tags associadas seus registros. Para saber como enviar notificações por push para usuários específicos que podem estar usando vários dispositivos, vá para o tutorial a seguir: 

> [!div class="nextstepaction"]
>[Enviar notificações por push para usuários específicos](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png

<!-- URLs.-->
[Introdução aos Hubs de Notificação]: notification-hubs-windows-mobile-push-notifications-mpns.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Phone]: ??
