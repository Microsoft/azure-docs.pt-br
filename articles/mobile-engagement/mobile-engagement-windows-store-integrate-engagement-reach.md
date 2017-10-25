---
title: "Integração do SDK do Reach do Windows Universal"
description: Como integrar o Azure Mobile Engagement Reach com aplicativos do Windows Universal
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9311e998e67d8d0d56da68fc9460df32ce7ce5a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Integração do SDK do Reach do Windows Universal
Você deve seguir o procedimento de integração descrito na [Integração do SDK do Engagement do Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) , antes de seguir este guia.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Incorporação o SDK do Engagement Reach em seu projeto do Windows Universal
Você não tem nada a adicionar. `EngagementReach` já estão em seu projeto.

> [!TIP]
> Você pode personalizar imagens localizadas na pasta `Resources` do seu projeto, especialmente o ícone de marca (esse padrão para o ícone do Engagement). Em Aplicativos do Universal também é possível mover a pasta `Resources` em seu projeto compartilhado para compartilhar o seu conteúdo entre aplicativos, mas você precisa manter o arquivo `Resources\EngagementConfiguration.xml` em seu local padrão já que o mesmo é dependente da plataforma.
> 
> 

## <a name="enable-the-windows-notification-service"></a>Habilitar o Serviço de Notificação do Windows
### <a name="windows-8x-and-windows-phone-81-only"></a>Somente Windows 8.x e Windows Phone 8.1
Para usar o **Serviço de Notificação do Windows** (chamado de WNS) em seu arquivo `Package.appxmanifest` em `Application UI`, clique em `All Image Assets` na caixa bot à esquerda. À direita da caixa do `Notifications`, alterar `toast capable` de `(not set)` para `(Yes)`.

### <a name="all-platforms"></a>Todas as plataformas
Você precisa sincronizar seu aplicativo com sua conta da Microsoft e com a plataforma de compromisso. Para isso, você precisa criar uma conta ou fazer logon no [centro de desenvolvimento do Windows](https://dev.windows.com). Depois que criar um novo aplicativo e localizar o SID e a chave secreta. No front-end do engagement vá para sua configuração de aplicativo em `native push` e cole suas credenciais. Depois disso, clique com botão direito no projeto, selecione `store` e `Associate App with the Store...`. Basta selecionar o aplicativo que você criou antes de sincronizá-la.

## <a name="initialize-the-engagement-reach-sdk"></a>Inicializar o SDK do Engagement Reach
Modifique o `App.xaml.cs`:

* Insira `EngagementReach.Instance.Init` logo após `EngagementAgent.Instance.Init` no método `InitEngagement`:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  O `EngagementReach.Instance.Init` é executado em um thread dedicado. Você não precisa fazê-lo.

> [!NOTE]
> Se estiver usando notificações por push em outro lugar em seu aplicativo, você precisará [compartilhar seu canal push](#push-channel-sharing) com o Engagement Reach.
> 
> 

## <a name="integration"></a>Integração
O Engagement oferece duas maneiras de adicionar banners no aplicativo e exibições intersticiais do Reach para anúncios e pesquisas em seu aplicativo: a integração de sobreposição e a integração manual de exibições da Web. Não combine as duas abordagens na mesma página.

A escolha entre as duas integrações pode ser resumida desta forma:

* Escolha a integração de sobreposição se suas páginas já herdam do Agente `EngagementPage`, é apenas uma questão de substituir `EngagementPage` por `EngagementPageOverlay` e `xmlns:engagement="using:Microsoft.Azure.Engagement"` por `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` nas páginas.
* Escolha a integração manual de exibições da Web se quiser colocar precisamente a interface do usuário do Reach dentro de suas páginas ou se não quiser adicionar outro nível de herança às páginas. 

### <a name="overlay-integration"></a>Integração de sobreposição
A sobreposição do Engagement adiciona dinamicamente os elementos de interface do usuário usados para exibir as campanhas do Reach em sua página. Se a sobreposição não for adequada para seu layout, considere a integração manual de exibições da Web.

Em seu arquivo .xaml, altere a referência `EngagementPage` para `EngagementPageOverlay`

* Adicione às suas declarações de namespaces:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* Substituir `engagement:EngagementPage` por `engagement:EngagementPageOverlay`:

**Com EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**Com EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

No seu arquivo .cs, marque a página em `EngagementPageOverlay` em vez de `EngagementPage` e importe `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

* Substituir `EngagementPage` por `EngagementPageOverlay`:

**Com EngagementPage:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**Com EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


A sobreposição do Engagement adiciona um elemento `Grid` sobre a página, composto de seu layout e dos dois elementos `WebView`, um para a faixa e outro para a exibição intersticial.

Você pode personalizar os elementos da sobreposição diretamente no arquivo `EngagementPageOverlay.cs`.

### <a name="web-views-manual-integration"></a>Integração manual de exibições da Web
O Reach pesquisará em suas páginas os dois elementos `WebView` responsáveis por exibir a faixa e a exibição intersticial. A única coisa que você precisa fazer é adicionar esses dois elementos `WebView` a algum lugar de suas páginas. Veja um exemplo:

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


Neste exemplo, os elementos `WebView` são alongados para se ajustarem a seus contêineres, o que automaticamente os dimensiona novamente em caso de alteração de tamanho de janela ou rotação de tela.

> [!WARNING]
> É importante manter a mesma nomenclatura `engagement_notification_content` e `engagement_announcement_content` para os elementos `WebView`. O Reach os identifica por seu nome. 
> 
> 

## <a name="handle-datapush-optional"></a>Manipular o push de dados (opcional)
Se desejar que o aplicativo seja capaz de receber push de dados do Reach, você precisa implementar dois eventos da classe EngagementReach:

Em App.xaml.cs no construtor App(), adicione:

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };

            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

Você pode ver que o retorno de chamada de cada método retorna um valor booleano. O Engagement envia um comentário ao seu back-end após distribuir o push de dados. Se o retorno de chamada retorna false, o comentário `exit` será enviado. Caso contrário, ele será `action`. Se nenhum retorno de chamada for definido para os eventos, o comentário `drop` retornará ao Engagement.

> [!WARNING]
> O Engagement não é capaz de receber múltiplos comentários para um push de dados. Se você pretende definir vários manipuladores em um evento, lembre-se de que os comentários corresponderá ao último enviado. Nesse caso, é recomendável sempre retornar o mesmo valor para evitar que os comentários sejam confusos no front-end.
> 
> 

## <a name="customize-ui-optional"></a>Personalizar a interface do usuário (opcional)
### <a name="first-step"></a>Primeira etapa
Permitir que você personalize a interface do usuário do reach.

Para fazer isso, você precisa criar uma subclasse da classe `EngagementReachHandler` .

**Exemplo de código :**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

Depois, defina o conteúdo do campo `EngagementReach.Instance.Handler` com seu objeto personalizado em sua classe `App.xaml.cs` dentro do método `App()`.

**Exemplo de código :**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [!NOTE]
> Por padrão, o Engagement usa a sua própria implementação de `EngagementReachHandler`.
> Você não precisa criar sua própria e se você fizer isso, você não precisa substituir cada método. O comportamento padrão é selecionar o objeto base do Engagement.
> 
> 

### <a name="web-view"></a>Modo de exibição da Web
Por padrão, o Reach usará os recursos incorporados da DLL para exibir as notificações e páginas.

Para fornecer possibilidades de personalização completa possibilidades usamos somente o modo de exibição da web. Se você desejar personalizar layouts substitua diretamente os arquivos de recursos `EngagementAnnouncement.html` e `EngagementNotification.html`. O Engagement precisa de todos os códigos em `<body></body>` para executar corretamente. Mas você pode adicionar a marca externa `engagement_webview_area`.

No entanto, você pode optar por usar seus próprios recursos.

Você pode substituir os métodos `EngagementReachHandler` em sua subclasse para informar o Engagement para usar seus layouts, mas tome cuidado com o mecanismo incorporado do engagement:

**Exemplo de código :**

            // In your subclass of EngagementReachHandler

            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


Por padrão, AnnouncementHTML é `ms-appx-web:///Resources/EngagementAnnouncement.html`. Representa o arquivo html que cria o conteúdo de uma mensagem de envio por push (anúncio de texto, anúncio da web e anúncio de pesquisa). AnnouncementName é `engagement_announcement_content`. É o nome do design do modo de exibição da web em sua página xaml.

NotfificationHTML é `ms-appx-web:///Resources/EngagementNotification.html`. Representa o arquivo html que cria a notificação de uma mensagem de envio por push. NotfificationName é `engagement_notification_content`. É o nome do design do modo de exibição da web em sua página xaml.

### <a name="customization"></a>Personalização
Você pode personalizar o modo de exibição da web de notificação e anúncio como desejar se você preservar o objeto Engagement. Atenção, pois o objeto de exibição da web é descrito três vezes - a primeira vez no xaml, a segunda vez no arquivo .cs no método "setwebview()" e a terceira vez no arquivo html.

* Em seu xaml você descreve o componente do modo de exibição da web do layout gráfico atual.
* Em seu arquivo .cs, você pode definir "setwebview()" no qual você define a dimensão dos dois modos de exibição da web (notificação, anúncio). É muito eficiente quando o aplicativo é redimensionado.
* No arquivo html do Engagement descrevemos o conteúdo do modo de exibição da web, o design e as posições dos elementos entre si.

### <a name="launch-message"></a>Iniciar mensagem
Quando um usuário clica em um sistema de notificação (um toast), o Engagement inicia o aplicativo, carrega o conteúdo das mensagens de envio por push e exibe a página da campanha correspondente.

Há um atraso entre a inicialização do aplicativo e a exibição da página (dependendo da velocidade da sua rede).

Para indicar ao usuário que algo está sendo carregado, você deve fornecer informações visuais, como uma barra de progresso ou um indicador de progresso. O Engagement não pode manipular isto por conta própria, mas oferece alguns manipuladores para você.

Para implementar o retorno de chamada, em App.xaml.cs em "Public App(){}" adicione:

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Você pode definir o retorno de chamada em seu método "Public App() {}" de seu arquivo `App.xaml.cs`, de preferência antes da chamada `EngagementReach.Instance.Init()`.

> [!TIP]
> Cada manipulador é chamado pelo Thread de interface do usuário. Você não precisa se preocupar ao usar uma MessageBox ou algo relacionado à interface do usuário.
> 
> 

## <a id="push-channel-sharing"></a> Enviar o compartilhamento de canal por push
Se estiver usando notificações por push para outra finalidade em seu aplicativo, você terá que usar o recurso de compartilhamento de canal de push do SDK do Engagement. Isso é para evitar notificações por push perdidas.

* Você pode oferecer seu próprio canal de push para inicializar o Engagement Reach. O SDK usará seu canal em vez de solicitar um novo.

Atualize a inicialização do Engagement Reach com o canal de push no método `InitEngagement` começando pelo arquivo `App.xaml.cs`:

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* Como alternativa, se só quiser consumir o canal de push após a inicialização do Reach, você poderá configurar um retorno de chamada no Engagement Reach para obter o canal de push após ele ser criado pelo SDK.

Configure o retorno de chamada em qualquer lugar **após** a inicialização do Reach:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Dica de esquema personalizado
Fornecemos o uso de esquema personalizado. Você pode enviar o tipo de URI diferente do front-end do Engagement para ser usado em seu aplicativo do engagement. Um esquema padrão como o `http, ftp, ...` é gerenciados pelo Windows, uma janela será solicitada se não houver aplicativo padrão instalado no dispositivo. Você também pode criar seu próprio esquema personalizado para o aplicativo.

A maneira simples de definir um esquema personalizado em seu aplicativo é abrir `Package.appxmanifest` no painel `Declarations`. Selecione `Protocol` na caixa de rolagem de Declarações Disponíveis e adicione-o. Edite o campo `Name` com o novo nome de protocolo desejado.

Agora, para usar esses protocolo edite seu `App.xaml.cs` método com o método `OnActivated` e não se esqueça de inicializar o engagement aqui também:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);

              //TODO design action to do when app is launch

              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;

                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion

