---
title: Enviar notificações localizadas para aplicativos Windows usando Hubs de Notificação do Azure | Microsoft Docs
description: Saiba como usar os Hubs de Notificação do Azure para enviar notificações localizadas de últimas notícias.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: d91320d8f78942ca916084cbf29a88818d95f531
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92314631"
---
# <a name="tutorial-send-localized-push-notifications-to-windows-apps-using-azure-notification-hubs"></a>Tutorial: Enviar notificações por push localizadas para aplicativos Windows usando Hubs de Notificação do Azure

> [!div class="op_single_selector"]
> * [C# da Windows Store](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

## <a name="overview"></a>Visão geral

Este tutorial mostra como enviar notificações localizadas para dispositivos móveis registrados com o serviço de Hubs de Notificação. No tutorial, você atualiza aplicativos criados no [Tutorial: Enviar notificações para dispositivos específicos (Plataforma Universal do Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) para suportar os seguintes cenários:

- O aplicativo da Windows Store permite que dispositivos cliente especifiquem um idioma e se inscrevam em diferentes categorias de últimas notícias.
- O aplicativo de back-end transmite notificações usando os recursos **tag** e **template** dos Hubs de Notificação do Microsoft Azure.

Quando você concluir o tutorial, o aplicativo móvel permite registrar categorias que lhe interessam e também especificar um idioma no qual deseja receber as notificações. O aplicativo de back-end envia notificações que são localizadas por idioma e dispositivo.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Atualizar aplicativo do Windows para oferecer suporte a informações de localidade
> * Atualizar aplicativo de back-end para enviar notificações localizadas
> * Testar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Concluir o [Tutorial: Enviar notificações para dispositivos específicos (Plataforma Universal do Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

No [Tutorial: Enviar notificações para dispositivos específicos (Plataforma Universal do Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md), você criou um aplicativo usando **tags** para assinar notificações para diferentes **categorias** de notícias. Neste tutorial, você usa o recurso **modelo** de Hubs de Notificação para entregar facilmente notificações de últimas notícias **localizadas**.

Em um alto nível, os modelos são uma maneira de especificar o formato no qual um dispositivo específico deve receber uma notificação. O modelo especifica o formato exato da carga referindo-se às propriedades que fazem parte da mensagem enviada por seu aplicativo de back-end. Neste tutorial, o aplicativo de back-end envia uma mensagem que não reconhece a localidade que contém todos os idiomas suportados:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Os dispositivos se registram com um modelo que faz referência à propriedade correta. Por exemplo, um aplicativo da Windows Store que deseja receber uma mensagem de notificação do sistema em inglês se registrará no modelo a seguir, com quaisquer marcas correspondentes:

```xml
<toast>
    <visual>
    <binding template=\"ToastText01\">
        <text id=\"1\">$(News_English)</text>
    </binding>
    </visual>
</toast>
```

Para saber mais sobre modelos, confira [Push Templates](notification-hubs-templates-cross-platform-push-messages.md) (Modelos de push).

## <a name="update-windows-app-to-support-locale-information"></a>Atualizar aplicativo do Windows para oferecer suporte a informações de localidade

1. Abra a solução do Visual Studio que você criou para o [Tutorial: Enviar notificações para dispositivos específicos (Plataforma Universal do Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).
2. Atualize o arquivo `MainPage.xaml` da solução para incluir uma caixa de combinação de localidade:

    ```xml
    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
3. Na classe `Notifications`, adicione um parâmetro de localidade aos métodos `StoreCategoriesAndSubscribe` e `SubscribeToCategories`.

    ```csharp
    public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        ApplicationData.Current.LocalSettings.Values["locale"] = locale;
        return await SubscribeToCategories(locale, categories);
    }

    public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration. This makes supporting notifications across other platforms much easier.
        // Using the localized tags based on locale selected.
        string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
    }
    ```

    Em vez de chamar o método `RegisterNativeAsync`, chame `RegisterTemplateAsync`. Você deve registrar um formato de notificação específico no qual o modelo depende da localidade. Você também pode fornecer um nome para o modelo (“localizedWNSTemplateExample”), já que talvez você deseje registrar mais de um modelo (por exemplo, um para as notificações do sistema e outro para blocos). Você também precisa nomeá-los para atualizar ou excluí-los.

    Se um dispositivo registrar vários modelos com a mesma marca, uma mensagem de entrada direcionada para aquela marca resultará em várias notificações entregues ao dispositivo (um para cada modelo). Esse comportamento é útil quando a mesma mensagem lógica precisa resultar em várias notificações visuais, por exemplo, mostrando uma notificação e uma notificação do sistema em um aplicativo da Windows Store.
4. Adicione o seguinte método para recuperar a localidade armazenada:

    ```csharp
    public string RetrieveLocale()
    {
        var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
        return locale != null ? locale : "English";
    }
    ```

5. No arquivo `MainPage.xaml.cs`, atualize o manipulador de clique de botão para recuperar o valor atual da caixa de combinação Localidade e forneça-o à chamada para a classe `Notifications`:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var locale = (string)Locale.SelectedItem;

        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                categories);

        var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
            string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```
6. Por fim, no arquivo `App.xaml.cs`, atualize o método `InitNotificationsAsync` para recuperar a localidade e use-a ao inscrever-se:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```



## <a name="run-the-uwp-application"></a>Executar o aplicativo UWP

1. Execute o aplicativo da Plataforma Universal do Windows. Aguarde até ver a mensagem **registro bem-sucedido**.

    ![Registro e aplicativos móveis](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/registration-successful.png)
2. Selecione as **categorias** e a **localidade** e clique em **Assinar**. O aplicativo converte as categorias selecionadas em rótulos e solicita um novo registro do dispositivo para os rótulos selecionados do hub de notificação.

    ![Aplicativo móvel](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/mobile-app.png)
3. Você verá uma mensagem de **confirmação** sobre as **assinaturas**.

    ![Mensagem de assinatura](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/subscription-message.png)

## <a name="update-console-app-to-send-localized-notifications"></a>Atualizar o aplicativo de console para enviar notificações localizadas

Ao enviar notificações de modelos, você só precisa fornecer um conjunto de propriedades. Nesse tutorial, o aplicativo de back-end envia o conjunto de propriedades contendo a versão localizada das notícias atuais, por exemplo:

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

Nesta seção, você deve atualizar o projeto de aplicativo de console na solução. Modifique o método `SendTemplateNotificationAsync` no aplicativo de console que você criou anteriormente, com o código a seguir:

> [!IMPORTANT]
> Especifique o nome e a cadeia de caracteres da conexão com acesso completo para o hub de notificação no código.

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(
        "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications.
    // This includes APNS, FCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

Essa simples chamada entrega a notícia correta localizada a **todos** os dispositivos, independentemente da plataforma, enquanto o Hub de Notificação cria e entrega o conteúdo nativo correto a todos os dispositivos inscritos em uma marca específica.

## <a name="run-console-app-to-send-localized-notification"></a>Executar o aplicativo de console para enviar notificações localizadas
Execute o **aplicativo de console** para enviar notificações para cada categoria e em cada idioma com suporte. Verifique se você só receberá uma notificação para as categorias que você se inscreveu e a mensagem para a localidade que você selecionou.

![Mensagens de notificação](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/notifications.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como enviar notificações localizadas por push para dispositivos específicos que têm tags associadas seus registros. Para saber como enviar notificações por push para usuários específicos que podem estar usando mais de um dispositivo, vá para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Enviar notificações por push para usuários específicos](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)