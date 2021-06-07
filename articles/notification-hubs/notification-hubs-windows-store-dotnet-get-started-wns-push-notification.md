---
title: Enviar notificações para aplicativos da Plataforma Universal do Windows usando Hubs de Notificação do Azure | Microsoft Docs
description: Aprenda a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo da Plataforma Universal do Windows.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: 'mvc, ms.custom: devx-track-csharp'
ms.date: 12/05/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 4f55b6eafe230f722979d535111ce45aa35981f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93125030"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-using-azure-notification-hubs"></a>Tutorial: Enviar notificações para aplicativos da Plataforma Universal do Windows usando Hubs de Notificação do Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Neste tutorial, você cria um hub de notificação para enviar notificações por push para um aplicativo da Plataforma Universal do Windows (UWP). Você cria um aplicativo da Windows Store em branco que recebe notificações por push usando o serviço WNS (Notificação por Push do Windows). Em seguida, use seu hub de notificação para transmitir notificações por push a todos os dispositivos que estão executando seu aplicativo.

> [!NOTE]
> Você pode encontrar o código completo deste tutorial [no GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/UwpSample).

Siga estas etapas:

> [!div class="checklist"]
> * Criar um aplicativo na Windows Store
> * Criar um hub de notificação
> * Criar um aplicativo de exemplo do Windows
> * Enviar notificações de teste

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Microsoft Visual Studio 2017 ou posterior. Os exemplos neste tutorial usam o [Visual Studio 2019](https://www.visualstudio.com/products).
- [Ferramentas de desenvolvimento de aplicativos UWP instaladas](/windows/uwp/get-started/get-set-up)
- Uma conta ativa da Windows Store
- Confirme que a opção **Obter notificações de aplicativos e outros remetentes** está habilitada. 
    - Inicie a janela **Configurações** em seu computador.
    - Selecione o bloco **Sistema**.
    - Selecione **Notificações e ações** no menu à esquerda. 
    - Confirme que a opção **Obter notificações de aplicativos e outros remetentes** está habilitada. Se ela não estiver, habilite-a.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre os Hubs de Notificação para aplicativos do UWP.

## <a name="create-an-app-in-windows-store"></a>Criar um aplicativo na Windows Store

Para enviar notificações por push para aplicativos da UWP, associe seu aplicativo à Windows Store. Em seguida, configure seu Hub de Notificação para se integrar ao WNS.

1. Navegue até o [Centro de Desenvolvimento do Windows](https://partner.microsoft.com/dashboard/windows/first-run-experience), entre com sua conta da Microsoft e selecione **Criar um novo aplicativo**.

    ![Botão de novo aplicativo](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
2. Digite um nome para seu aplicativo e selecione **Reservar nome do produto**. Isso cria um novo registro da Windows Store para seu aplicativo.

    ![Armazene o nome do aplicativo](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
3. Expanda **Gerenciamento de produto**, selecione **WNS/MPNS** e, em seguida, **Site de serviços do Live**. Entre na sua conta da Microsoft. A página de registro de aplicativo é aberta em uma nova guia. Como alternativa, você pode navegar diretamente para a página [Meus aplicativos](https://apps.dev.microsoft.com) e selecionar o nome do aplicativo para chegar a esta página.

    ![Página de WNS MPNS](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
4. Tome nota da senha dos **Segredos de Aplicativo**, bem como do **SID (identificador de segurança do pacote)** e da **Identidade do Aplicativo** na seção Microsoft Store.

    >[!WARNING]
    >O segredo do aplicativo e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses valores com ninguém nem os distribua com seu aplicativo.

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-wns-settings-for-the-hub"></a>Definir as configurações WNS para o hub

1. Na categoria **CONFIGURAÇÕES DE NOTIFICAÇÃO**, selecione **Windows (WNS)** .
2. Insira valores para **SID do pacote** e **chave de segurança** que você anotou na seção anterior.
3. Clique em **Salvar** na barra de ferramentas.

    ![As caixas SID de Pacote e Chave de Segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

O hub de notificação agora está configurado para trabalhar com WNS. Você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações.

## <a name="create-a-sample-windows-app"></a>Criar um aplicativo de exemplo do Windows

1. No Visual Studio, abra o menu **Arquivo**, selecione **Novo** e, em seguida, **Projeto**.
2. Na caixa de diálogo **Criar um projeto**, conclua as seguintes etapas:

    1. Na caixa de pesquisa na parte superior, digite **Windows Universal**.
    2. Nos resultados da pesquisa, selecione **Aplicativo em Branco (Windows Universal)** e, em seguida, selecione **Avançar**.

       ![Caixa de diálogo Novo Projeto](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)

    3. Na caixa de diálogo **Configurar seu novo projeto**, insira um **Nome do projeto** e um **Local** para os arquivos do projeto.
    4. Selecione **Criar**.

3. Aceite os padrões para as versões **mínima** e de **destino** da plataforma e selecione **OK**.
4. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto do aplicativo da Microsoft Store, selecione **Publicar** e selecione **Associar aplicativo à Store**. O assistente **Associar seu aplicativo à Windows Store** é exibido.
5. No assistente, entre com sua conta da Microsoft.
6. Selecione o aplicativo que você registrou na etapa 2, selecione **Avançar** e selecione **Associar**. Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.
7. No Visual Studio, clique com o botão direito do mouse na solução e selecione **Gerenciar Pacotes NuGet**. A janela **Gerenciar Pacotes NuGet** é aberta.
8. Na caixa de pesquisa, digite **WindowsAzure.Messaging.Managed**, selecione **Instalar** e aceite os termos de uso.

    ![A janela Gerenciar Pacotes NuGet][20]

    Essa ação baixa, instala e adiciona uma referência à biblioteca dos Hubs de Notificação do Azure para Windows usando o [pacote NuGet Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).
9. Abra o arquivo de projeto `App.xaml.cs` e adicione as seguintes instruções:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.UI.Popups;
    ```

10. No arquivo `App.xaml.cs` do projeto, localize a classe `App` e adicione a seguinte definição de método `InitNotificationsAsync`. Substitua `<your hub name>` pelo nome do hub de notificação criado na portal do Azure e substitua `<Your DefaultListenSharedAccessSignature connection string>` pela cadeia de conexão `DefaultListenSharedAccessSignature` da página **Políticas de Acesso** do hub de notificação:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

    Esse código recupera no WNS o URI do canal para o aplicativo e registra esse URI do canal no hub de notificação.

    >[!NOTE]
    > Substitua o espaço reservado `hub name` pelo nome do hub de notificação que aparece no portal do Azure. Além disso, substitua o espaço reservado da cadeia de conexão pela cadeia de conexão `DefaultListenSharedAccessSignature` obtida na página **Políticas de Acesso** do seu hub de notificação em uma seção anterior.

11. Na parte superior do manipulador de eventos `OnLaunched` no `App.xaml.cs`, adicione a seguinte chamada ao novo método `InitNotificationsAsync`:

    ```csharp
    InitNotificationsAsync();
    ```

    Essa ação faz com que o URI do canal seja registrado em seu hub de notificação toda vez que o aplicativo for iniciado.

12. Clique com o botão direito do mouse em `Package.appxmanifest` e selecione Exibir Código (**F7**). Localize `<Identity .../>` e substitua o valor pela **Identidade do Aplicativo** do WNS criado [anteriormente](#create-an-app-in-windows-store).

13. Para executar o aplicativo, pressione a tecla **F5** do teclado. Uma caixa de diálogo que contém a chave de registro será exibida. Para fechar a caixa de diálogo, clique em **OK**.

    ![Registro bem-sucedido](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

Seu aplicativo agora está pronto para receber notificações do sistema.

## <a name="send-test-notifications"></a>Enviar notificações de teste

Você pode testar rapidamente o recebimento de notificações em seu aplicativo enviando notificações no [portal do Azure](https://portal.azure.com/).

1. No portal do Azure, alterne para a guia de visão geral e selecione **Envio de teste** na barra de ferramentas.

    ![Botão de envio de teste](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. Na janela **Envio de teste**, execute as seguintes ações:
    1. Para **Plataformas**, selecione **Windows**.
    2. Para **Tipo de notificação**, selecione **Notificação do sistema**.
    3. Selecione **Enviar**.

        ![O painel Envio de Teste](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Veja o resultado da operação de envio na lista **Resultados** na parte inferior da janela. Você também pode ver uma mensagem de alerta.

    ![Resultado da Operação de envio](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
4. Você verá a mensagem de notificação: **Mensagem de teste** na área de trabalho.

    ![Mensagem de notificação](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)

## <a name="next-steps"></a>Próximas etapas
Você enviou notificações para todos os seus dispositivos Windows usando o portal ou um aplicativo de console. Para saber como enviar notificações por push a dispositivos específicos, avance ao seguinte tutorial:

> [!div class="nextstepaction"]
>[Notificações por push para especificar dispositivos](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[toast catalog]: /previous-versions/windows/apps/hh761494(v=win.10)
[tile catalog]: /previous-versions/windows/apps/hh761491(v=win.10)
[badge overview]: /previous-versions/windows/apps/hh779719(v=win.10)
