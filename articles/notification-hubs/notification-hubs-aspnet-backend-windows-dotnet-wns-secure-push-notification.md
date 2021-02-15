---
title: Push seguro de hubs de notificação do Azure para Windows
description: Saiba como enviar notificações por push seguro no Azure. Amostras de código escrito em C# usando a API .NET.
author: sethmanheim
manager: femila
editor: thsomasu
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 98e587103e63cd5cc26eab5b00864d00e0b9007f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019406"
---
# <a name="send-secure-push-notifications-from-azure-notification-hubs"></a>Enviar notificações por push seguras dos hubs de notificação do Azure

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Visão geral

O suporte à notificação por push no Microsoft Azure permite que você acesse uma infraestrutura de envio por push fácil de usar, multiplataforma e expansível que simplifica em muito a implementação de notificações por push para aplicativos de consumidor e empresariais para plataformas móveis.

Devido a restrições regulatórias ou de segurança, às vezes, um aplicativo pode querer incluir algo na notificação que não pode ser transmitido por meio da infraestrutura de notificação por push padrão. Este tutorial descreve como obter a mesma experiência ao enviar informações confidenciais por meio de uma conexão segura e autenticada entre o dispositivo cliente e o back-end do aplicativo.

Em um nível superior, o fluxo é o seguinte:

1. O back-end do aplicativo:
   * Armazena uma carga segura no banco de dados de back-end.
   * Envia a ID dessa notificação ao dispositivo (nenhuma informação segura é enviada).
2. O dispositivo no aplicativo, ao receber a notificação:
   * O dispositivo entra em contato com o back-end solicitando a carga segura.
   * O aplicativo pode mostrar a carga como uma notificação no dispositivo.

É importante observar que no fluxo anterior (e neste tutorial), pressupomos que o dispositivo armazena um token de autenticação no armazenamento local depois que o usuário faz logon. Isso garante uma experiência perfeita  já que o dispositivo pode recuperar a carga de segurança da notificação usando este token. Se o seu aplicativo não armazenar tokens de autenticação no dispositivo, ou se esses tokens puderem expirar, o aplicativo do dispositivo, após receber a notificação, deve exibir uma notificação genérica solicitando que o usuário inicie o aplicativo. Dessa forma, o aplicativo autentica o usuário e mostra a carga de notificação.

Este tutorial mostra como enviar uma notificação por push com segurança. O tutorial se baseia no tutorial [notificar usuários](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , portanto, você deve concluir as etapas nesse tutorial primeiro.

> [!NOTE]
> Este tutorial pressupõe que você criou e configurou seu hub de notificação conforme descrito em [enviar notificações para plataforma universal do Windows aplicativos](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Além disso, observe que o Windows Phone 8.1 requer credenciais do Windows (não Windows Phone) e tarefas em segundo plano não funcionam no Windows Phone 8.0 ou Silverlight 8.1. Para aplicativos da Windows Store, você pode receber notificações por meio de uma tarefa de segundo plano somente se o aplicativo estiver com o bloqueio de tela habilitado (clique na caixa de seleção em Appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modificar o projeto de Windows Phone

1. No projeto **NotifyUserWindowsPhone** , adicione o código a seguir em App.xaml.cs para registrar a tarefa de segundo plano de push. Adicione a seguinte linha de código ao final do método `OnLaunched()` :

    ```csharp
    RegisterBackgroundTask();
    ```

2. Ainda no App.xaml.cs, adicione o código a abaixo imediatamente após o método `OnLaunched()` :

    ```csharp
    private async void RegisterBackgroundTask()
    {
        if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
        {
            var result = await BackgroundExecutionManager.RequestAccessAsync();
            var builder = new BackgroundTaskBuilder();

            builder.Name = "PushBackgroundTask";
            builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
            builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
            BackgroundTaskRegistration task = builder.Register();
        }
    }
    ```

3. Adicione as seguintes instruções `using` na parte superior do arquivo App.xaml.cs:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```

4. No menu **Arquivo** no Visual Studio, clique em **Salvar Tudo**.

## <a name="create-the-push-background-component"></a>Criar o componente de plano de fundo de push

A próxima etapa é criar o componente de segundo plano de push.

1. No Gerenciador de Soluções, clique com o botão direito do mouse no nó do nível superior da solução (**Solução SecurePush** nesse caso) e clique em **Adicionar** e em **Novo Projeto**.
2. Expanda **Aplicativos da Loja** e clique em **Aplicativos do Windows Phone** e em **componente do Windows Runtime (Windows Phone)**. Nomeie o projeto **PushBackgroundComponent** e clique em **OK** para criar o projeto.

    ![Captura de tela da caixa de diálogo Adicionar novo projeto com a opção Visual C# do componente de Windows Runtime (Windows Phone) realçada.][12]
3. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **PushBackgroundComponent (Windows Phone 8.1)** e clique em **Adicionar** e em **Classe**. Nomeie a nova classe `PushBackgroundTask.cs`. Clique em **Adicionar** para gerar a classe.
4. Substitua todo o conteúdo da definição do namespace `PushBackgroundComponent` pelo código a seguir, substituindo o espaço reservado `{back-end endpoint}` pelo ponto de extremidade do back-end obtido ao implantar o back-end:

    ```csharp
    public sealed class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public sealed class PushBackgroundTask : IBackgroundTask
        {
            private string GET_URL = "{back-end endpoint}/api/notifications/";

            async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
            {
                // Store the content received from the notification so it can be retrieved from the UI.
                RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                var notificationId = raw.Content;

                // retrieve content
                BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                var httpClient = new HttpClient();
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                ShowToast(notification);

                deferral.Complete();
            }

            private void ShowToast(Notification notification)
            {
                ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                ToastNotification toast = new ToastNotification(toastXml);
                ToastNotificationManager.CreateToastNotifier().Show(toast);
            }
        }
    ```

5. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **PushBackgroundComponent (Windows Phone 8.1)** e em **Gerenciar Pacotes NuGet**.
6. No lado esquerdo, clique em **Online**.
7. Na caixa **Pesquisar**, digite **Http Client**.
8. Na lista de resultados, clique em **Bibliotecas de Cliente HTTP da Microsoft** e em **Instalar**. Conclua a instalação.
9. De volta à caixa **Pesquisar** do NuGet, digite **Json.net**. Instale o pacote **Json.NET** e feche a janela do Gerenciador de Pacotes NuGet.
10. Adicione as seguintes instruções `using` na parte superior do arquivo `PushBackgroundTask.cs`:

    ```csharp
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```

11. Em Gerenciador de Soluções, no projeto **NotifyUserWindowsPhone (Windows Phone 8,1)** , clique com o botão direito do mouse em **referências** e clique em **Adicionar referência..**.. No diálogo Gerenciador de referências, marque a caixa ao lado de **PushBackgroundComponent** e clique em **OK**.
12. No Gerenciador de Soluções, clique duas vezes em **Package.appxmanifest** no projeto **NotifyUserWindowsPhone (Windows Phone 8.1)**. Em **Notificações**, defina **Compatível com Toast** como **Sim**.

    ![Captura de tela da janela Gerenciador de Soluções com foco no Package. appxmanifest com a opção compatível com notificação do sistema definida como Sim contornada em vermelho.][3]
13. Ainda em **Package.appxmanifest**, clique no menu **Declarações** próximo à parte superior. Na lista suspensa **Declarações Disponíveis**, clique em **Tarefas de Segundo Plano** e em **Adicionar**.
14. Em **Package.appxmanifest**, em **Propriedades**, marque **Notificação por push**.
15. Em **Package.appxmanifest**, em **Configurações do Aplicativo**, digite **PushBackgroundComponent.PushBackgroundTask** no campo **Ponto de Entrada**.

    ![Captura de tela da janela Gerenciador de Soluções com foco no Package. appxmanifest com as declarações disponíveis, declarações com suporte, notificações por push e opções de ponto de entrada descritos em vermelho.][13]
16. No menu **arquivo** , clique em **salvar tudo**.

## <a name="run-the-application"></a>Executar o aplicativo

Para executar o aplicativo, faça o seguinte:

1. No Visual Studio, execute o aplicativo da API Web **AppBackend** . Uma página da Web do ASP.NET é exibida.
2. No Visual Studio, execute o aplicativo do Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)** . O emulador do Windows Phone executa e carrega o aplicativo automaticamente.
3. Na interface do usuário do aplicativo **NotifyUserWindowsPhone** , insira um nome de usuário e senha. Pode ser qualquer cadeia de caracteres, mas elas devem ter o mesmo valor.
4. Na interface do usuário do aplicativo **NotifyUserWindowsPhone**, clique em **Fazer logon e registrar-se**. Em seguida, clique em **Enviar push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
