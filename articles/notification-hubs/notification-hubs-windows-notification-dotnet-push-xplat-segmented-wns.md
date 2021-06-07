---
title: Enviar notificações para dispositivos específicos (Plataforma Universal do Windows) | Microsoft Docs
description: Use os Hubs de Notificação do Azure com marcações no registro para enviar as últimas notícias para um aplicativo da Plataforma Universal do Windows.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 2c77eba69fd914e8ecc7d08a1b16f61ceefe101b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92320582"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>Tutorial: Enviar notificações para dispositivos específicos que executam aplicativos da Plataforma Universal do Windows

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Visão geral

Este tutorial mostra como usar os Hubs de Notificação do Azure para difundir notificações de últimas notícias. Este tutorial aborda aplicativos da Microsoft Store ou Windows Phone 8.1 (não Silverlight). Se você estiver direcionando o Windows Phone 8.1 Silverlight, confira [Enviar notificações por push a dispositivos Windows Phone específicos usando os Hubs de Notificação do Azure](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

Neste tutorial, você aprenderá a usar os Hubs de Notificação do Azure para enviar notificações por push a dispositivos específicos do Windows que estão executando um aplicativo da UWP (Plataforma Universal do Windows). Depois de concluir o tutorial, registre-se nas categorias de últimas notícias de seu interesse. Você receberá notificações por push apenas para essas categorias.

Para habilitar cenários de difusão, inclua uma ou mais *marcas* durante a criação de um registro no hub de notificação. Quando as notificações são enviadas para uma tag, todos os dispositivos que foram registrados na tag recebem a notificação. Para saber mais sobre tags, confira [Expressões de tag e de roteamento](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Não há suporte para a versão de projetos da Microsoft Store e do Windows Phone 8.1 e anterior no Visual Studio 2019. Para saber mais, confira [Direcionamento e Compatibilidade da Plataforma Visual Studio 2019](/visualstudio/releases/2019/compatibility).

Neste tutorial, você executará as seguintes tarefas:

> [!div class="checklist"]
> * Adicionar a seleção de categorias ao aplicativo móvel
> * Registrar-se para receber notificações
> * Enviar notificações marcadas
> * Executar o aplicativo e gerar notificações

## <a name="prerequisites"></a>Pré-requisitos

Concluir o [Tutorial: Enviar notificações a aplicativos de Plataforma Universal do Windows usando Hubs de Notificação do Azure][get-started] antes de iniciar este tutorial.  

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categorias ao aplicativo

A primeira etapa é adicionar os elementos da interface do usuário à página principal existente, de modo que os usuários possam selecionar categorias nas quais se registrar. As categorias selecionadas são armazenadas no dispositivo. Quando o aplicativo é iniciado, ele cria um registro de dispositivo no hub de notificação, com as categorias selecionadas como marcações.

1. Abra o arquivo de projeto *MainPage.xaml* e copie o seguinte código no elemento `Grid`:

    ```xml
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
    ```

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Classe**. Em **Adicionar Novo Item**, dê à classe o nome *Notificações* e selecione **Adicionar**. Se necessário, adicione o modificador `public` à definição de classe.

1. Adicione as instruções `using` a seguir ao novo arquivo:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. Copie o código a seguir para nova classe `Notifications`:

    ```csharp
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
    ```

    Essa classe usa o armazenamento local para armazenar as categorias de notícias que este dispositivo deve receber. Em vez de chamar o método `RegisterNativeAsync`, chame `RegisterTemplateAsync` para se registrar nas categorias usando um registro de modelo.

    Se desejar registrar mais de um modelo, forneça um nome de modelo, por exemplo, *simpleWNSTemplateExample*. Nomeie os modelos para que você possa atualizá-los ou excluí-los. Você pode registrar mais de um modelo para ter um para notificações do sistema e outro para blocos.

    >[!NOTE]
    > Com os Hubs de Notificação, um dispositivo pode registrar vários modelos usando a mesma tag. Nesse caso, uma mensagem de entrada direcionada à tag resulta na entrega de várias notificações ao dispositivo, uma para cada modelo. Esse processo permite exibir a mesma mensagem em várias notificações visuais, como uma notificação e como uma notificação do sistema em um aplicativo da Windows Store.

    Para saber mais, veja [Modelos](notification-hubs-templates-cross-platform-push-messages.md).

1. No arquivo de projeto *App.xaml.cs*, adicione a seguinte propriedade à classe `App`:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Use essa propriedade para criar e acessar uma instância `Notifications`.

    No código, substitua os espaços reservados `<hub name>` e `<connection string with listen access>` pelo nome do hub de notificação e a cadeia de conexão por **DefaultListenSharedAccessSignature** obtida anteriormente.

   > [!NOTE]
   > Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, distribua apenas a chave para acesso de *escuta* com o aplicativo cliente. Com o acesso de escuta, o aplicativo pode se registrar para receber notificações, mas os registros existentes não podem ser modificados e as notificações não podem ser enviadas. A chave de acesso completo é usada em um serviço back-end protegido para enviar notificações e alterar os registros existentes.

1. No arquivo *MainPage.xaml.cs*, adicione a seguinte linha:

    ```csharp
    using Windows.UI.Popups;
    ```

1. No arquivo *MainPage.xaml.cs*, adicione o seguinte método:

    ```csharp
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
    ```

    Esse método cria uma lista de categorias e usa a classe `Notifications` para armazenar a lista no armazenamento local. Ele também registra as marcações correspondentes no hub de notificação. Quando as categorias são alteradas, o registro é recriado com as novas categorias.

O aplicativo agora pode armazenar um conjunto de categorias no armazenamento local do dispositivo. O aplicativo é registrado no hub de notificação sempre que os usuários alteram a seleção de categorias.

## <a name="register-for-notifications"></a>Registrar-se para receber notificações

Nesta seção, você se registra no hub de notificação após a inicialização usando as categorias armazenadas no armazenamento local.

> [!NOTE]
> Como o URI do canal atribuído pelo WNS (Serviço de Notificação do Windows) pode ser alterado a qualquer momento, você deve se registrar para receber notificações com frequência para evitar falhas de notificação. Este exemplo registra a notificação a cada vez que o aplicativo é iniciado. Para os aplicativos executados com frequência, digamos, mais de uma vez por dia, você pode ignorar o registro para preservar a largura de banda, caso tenha decorrido menos de um dia desde o registro anterior.

1. Para usar a classe `notifications` para assinar com base em categorias, abra o arquivo *App.xaml.cs* e atualize o método `InitNotificationsAsync`.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Com esse processo, o aplicativo recuperará as categorias do armazenamento local quando for iniciado. Em seguida, ele solicitará o registro dessas categorias. Você criou o método `InitNotificationsAsync` como parte do tutorial [Enviar notificações para aplicativos da Plataforma Universal do Windows usando os Hubs de Notificação do Azure][get-started].
2. No arquivo de projeto *MainPage.xaml.cs*, adicione o seguinte código ao método `OnNavigatedTo`:

    ```csharp
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
    ```

    Esse código atualiza a página principal, com base no status das categorias salvas anteriormente.

O aplicativo agora está concluído. Ele pode armazenar um conjunto de categorias no armazenamento local do dispositivo. Quando os usuários alteram a seleção da categoria, as categorias salvas são usadas para se registrar no hub de notificações. Na próxima seção, você define um back-end que pode enviar notificações de categoria para esse aplicativo.

## <a name="run-the-uwp-app"></a>Executar o aplicativo UWP

1. No Visual Studio, selecione F5 para compilar e iniciar o aplicativo. A interface do usuário do aplicativo fornece um conjunto de alternâncias que permite escolher as categorias às quais assinar.

   ![Aplicativo Últimas Notícias](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Habilite uma ou mais alternâncias de categoria e, depois, selecione **Assinar**.

   O aplicativo converte as categorias selecionadas em rótulos e solicita um novo registro do dispositivo para os rótulos selecionados do hub de notificação. O aplicativo exibe as categorias registradas em uma caixa de diálogo.

    ![Alternâncias de categoria e botão Assinar](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Criar um aplicativo de console para enviar notificações marcadas

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>Executar o aplicativo de console para enviar notificações marcadas

Execute o aplicativo criado na seção anterior. As notificações para as categorias selecionadas são exibidas como notificações do sistema.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a difundir as últimas notícias por categoria. O aplicativo de back-end envia notificações marcadas por push a dispositivos que se registraram para receber notificações para essa tag. Para saber como enviar notificações por push para usuários específicos, independentemente de qual dispositivo eles usam, vá para o tutorial a seguir:

> [!div class="nextstepaction"]
> [Enviar notificações localizadas por push para aplicativos Windows usando Hubs de Notificação do Microsoft Azure](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)