---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f0ff729084d194ff2e05e89eadc45782f775b1c5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172167"
---
Nesta seção, você envia as últimas notícias como notificações de modelo marcadas, de um aplicativo de console do .NET. 

1. No Visual Studio, crie um aplicativo de console em Visual C#: a. No menu, selecione **Arquivo** > **Novo** > **Projeto**.
    b. Expanda **Visual C#** e selecione **Área de Trabalho do Windows**. 
    c. Selecione **Aplicativo de Console (.NET Framework)** na lista de modelos. 
    d. Insira um **nome** para o aplicativo. 
    e. Selecione uma **pasta** para o aplicativo.
    f. Selecione **OK** para criar o projeto. 
2. No menu principal do Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** e, em seguida, no console da janela, insira a cadeia de caracteres a seguir:
   
    ```
    Install-Package Microsoft.Azure.NotificationHubs
    ```
   
3. Selecione **Inserir**.  
    Essa ação adiciona uma referência ao SDK dos Hubs de Notificação do Azure usando o [pacote NuGet Microsoft.Azure.Notification Hubs].

4. Abra o arquivo Program.cs e adicione a seguinte instrução `using`:
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. Na classe `Program` , adicione o seguinte método ou substitua-o, se ele já existir:
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```   
   
    Esse código envia uma notificação de modelo para cada uma das seis marcas na matriz de cadeia de caracteres. O uso de marcas garante que os dispositivos recebam notificações somente para as categorias de marcas.

5. No código anterior, substitua os espaços reservados `<hub name>` e `<connection string with full access>` pelo nome do hub de notificação e a cadeia de conexão por *DefaultFullSharedAccessSignature* do painel do hub de notificação.

6. No método **Main**, adicione as seguintes linhas:
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. Compile o aplicativo de console.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[pacote NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
