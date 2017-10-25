---
title: "Introdução ao Azure Mobile Engagement dos Aplicativos Windows Universal"
description: "Saiba como usar o Azure Mobile Engagement com análises e notificações por push para aplicativos universais do Windows."
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: 
ms.assetid: 48103867-7f64-4646-b019-42bd797d38e2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 40db7e4dd151ec391c754dc6d4145aeeb8058eca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-universal-apps"></a>Introdução ao Azure Mobile Engagement para aplicativos universais do Windows
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo e enviar notificações por push para usuários segmentados de um aplicativo do Windows Universal.
Esse tutorial demonstra um cenário de transmissão simples usando o Mobile Engagement. Você cria um Aplicativo Windows Universal em branco que coleta os dados básicos de uso do aplicativo e recebe notificações por push usando o Serviço de Notificação do Windows (WNS).

> [!NOTE]
> O serviço Azure Mobile Engagement será desativado em março de 2018 e, no momento, está disponível somente para os clientes existentes. Para saber mais, confira [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="set-up-mobile-engagement-for-your-windows-universal-app"></a>Configurar o Mobile Engagement para seu aplicativo Windows Universal
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement
Este tutorial apresenta uma "integração básica," que é o conjunto mínimo necessário para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [integração do SDK Universal do Windows para o Mobile Engagement](mobile-engagement-windows-store-sdk-overview.md).

Você cria um aplicativo básico com o Visual Studio para demonstrar a integração.

### <a name="create-a-windows-universal-app-project"></a>Criar um projeto de Aplicativo Windows Universal
As etapas a seguir pressupõem o uso do Visual Studio 2015, embora as etapas sejam semelhantes em versões anteriores do Visual Studio.

1. Inicie o Visual Studio e na tela **Início**, selecione **Novo Projeto**.
2. No menu pop-up, selecione **Windows** -> **Universal** -> **Aplicativo em Branco (Windows Universal)**. Preencha o **Nome** e o **Nome da solução** do aplicativo, em seguida, clique em **OK**.

    ![][1]

Agora, você criou um projeto Aplicativo Windows Universal no qual integrará o SDK do Azure Mobile Engagement.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Conecte seu aplicativo ao back-end do Mobile Engagement
1. Instale o pacote Nuget [MicrosoftAzure.MobileEngagement] em seu projeto. Se estiver visando as plataformas Windows e Windows Phone, precisará fazer isso em ambos os projetos. Para o Windows 8.x e o Windows Phone 8.1, o mesmo pacote Nuget coloca os binários corretos específicos da plataforma em cada projeto.
2. Abra **Package.appxmanifest** e verifique se a funcionalidade a seguir foi adicionada:

        Internet (Client)

    ![][2]
3. Agora, copie a cadeia de conexão que você copiou anteriormente para o seu Aplicativo Mobile Engagement e cole-o no arquivo `Resources\EngagementConfiguration.xml` entre as marcas `<connectionString>` e `</connectionString>`:

    ![][3]

    > [!TIP]
    > Se seu aplicativo for destinado para as plataformas Windows e Windows Phone, você ainda deverá criar dois Aplicativos do Mobile Engagement - um para cada plataforma com suporte. Ter dois aplicativos garante que você pode criar uma segmentação correta do público e pode enviar notificações de destino apropriadamente para cada plataforma.

    > [!IMPORTANT]
    > O NuGet ainda não copiará automaticamente os recursos do SDK em seu aplicativo UWP Windows 10. Você precisa fazê-lo manualmente seguindo as etapas que aparecem (readme.txt) quando o pacote Nuget é instalado.  

1. No arquivo `App.xaml.cs`:

    a. Adicione a instrução `using`:

            using Microsoft.Azure.Engagement;

    b. Adicione um método que inicializa o Engagement:

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    c. Inicialize o SDK no método **OnLaunched** :

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    c. Insira o seguinte no método **OnActivated** e adicione o método se ele não estiver presente:

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

## <a id="monitor"></a>Habilitar monitoramento em tempo real
Para iniciar o envio dos dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement.

1. Em **MainPage.xaml.cs**, adicione a seguinte instrução `using`:

    usando Microsoft.Azure.Engagement.Overlay;
2. Altere a classe base **MainPage** de **Page** para **EngagementPageOverlay**:

        class MainPage : EngagementPageOverlay
3. No arquivo `MainPage.xaml`:

    a. Adicione às suas declarações de namespaces:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    b. Substitua **Page** no nome da marcação XML por **engagement:EngagementPageOverlay**

> [!IMPORTANT]
> Se sua página substitui o método `OnNavigatedTo`, certifique-se de chamar `base.OnNavigatedTo(e)`. Caso contrário, a atividade não será registrada (`EngagementPage` chama `StartActivity` dentro de seu método `OnNavigatedTo`). Isso é especialmente importante em um projeto do Windows Phone, no qual o modelo padrão tem um método `OnNavigatedTo` .
>
> Para os **Aplicativos Universais do Windows 10**, use o método recomendado na seção "Método recomendado: sobrecarregar suas classes de Página" dos [Relatórios avançados com o SDK de Engajamento para Aplicativos Universais do Windows](mobile-engagement-windows-store-advanced-reporting.md), em vez do método mencionado acima.

## <a id="monitor"></a>Conectar o aplicativo com monitoramento em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Habilitar notificações por push e mensagens no aplicativo
O Mobile Engagement permite interagir e entrar em contato com usuários com notificações por push e mensagens no aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement.
As seções a seguir configuram seu aplicativo para recebê-las.

### <a name="enable-your-app-to-receive-wns-push-notifications"></a>Habilitar o aplicativo para receber Notificações por Push do WNS
1. No arquivo `Package.appxmanifest`, na guia **Aplicativo** em **Notificações**, defina **compatível com Toast:** para **Sim**

    ![][5]

### <a name="initialize-the-reach-sdk"></a>Inicializar o SDK do REACH
Em `App.xaml.cs`, chame **EngagementReach.Instance.Init(e);** na função **InitEngagement** logo após a inicialização do agente:

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

Você está pronto para enviar uma saudação. Em seguida, verificamos se você realizou corretamente essa integração básica.

### <a name="grant-access-to-mobile-engagement-to-send-notifications"></a>Conceder acesso ao Mobile Engagement para enviar notificações
1. Abra [Centro de Desenvolvimento da Windows Store] no navegador da Web, faça logon e crie uma conta, se necessário.
2. Clique em **Painel** no canto superior direito, em seguida, clique em **Criar um novo aplicativo** no menu do painel esquerdo.

    ![][9]
3. Crie seu aplicativo ao reservar o nome dele.

    ![][10]
4. Depois que o aplicativo tiver sido criado, navegue até **Serviços -> Notificações por push** no menu à esquerda.

    ![][11]
5. Na seção Notificações por push, clique no link **site de Serviços Dinâmicos** .

    ![][12]
6. Você navegará para a seção Credenciais de push. Verifique se você está na seção **Configurações do Aplicativo**, em seguida, copie o **SID do Pacote** e o **Segredo do cliente**

    ![][13]
7. Navegue até **Configurações** do portal do Mobile Engagement e clique na seção **Push Nativo** à esquerda. Em seguida, clique no botão **Editar** para inserir seu **identificador de segurança (SID) do pacote** e sua **Chave Secreta** mostrado:

    ![][6]
8. Por fim, certifique-se de que você tenha associado o aplicativo do Visual Studio a este aplicativo criado na Loja de aplicativos. Clique em **Associar Aplicativo à Loja** no Visual Studio.

    ![][7]

## <a id="send"></a>Envie uma notificação para seu aplicativo
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Se o aplicativo estiver em execução, você verá uma notificação no aplicativo. Caso contrário, se o aplicativo estiver fechado, verá uma notificação do toast.
Se você vir uma notificação no aplicativo, mas não uma notificação do toast, e estiver executando o aplicativo no modo de depuração no Visual Studio, então, experimente **ventos do ciclo de vida -> Suspender** na barra de ferramentas para garantir que o aplicativo está suspenso. Se você clicou no botão Início durante a depuração do aplicativo no Visual Studio, nem sempre ele será suspenso e embora você veja a notificação como no aplicativo, ela não aparecerá como uma notificação do sistema.  

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Centro de Desenvolvimento da Windows Store]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png
