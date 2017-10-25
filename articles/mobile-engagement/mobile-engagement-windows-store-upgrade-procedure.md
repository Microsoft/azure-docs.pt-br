---
title: "Procedimentos de atualização de aplicativos do Windows Universal"
description: "Procedimentos de atualização do SDK do Windows Universal para o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 4c898175-2cd6-43db-b350-bb408332f24d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fe85a99a92fb39082cafe7422b356de1f20f14bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-sdk-upgrade-procedures"></a>Procedimentos de atualização de aplicativos do Windows Universal
Se você já tiver integrado uma versão anterior do Engagement no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Você precisará seguir vários procedimentos se perdeu várias versões do SDK. Por exemplo, se você migrar do 0.10.1 para 0.11.0 você tem que primeiro seguir o procedimento "de 0.9.0 a 0.10.1” e depois o procedimento "de 0.10.1 a 0.11.0".

## <a name="from-330-to-340"></a>De 3.3.0 a 3.4.0
### <a name="test-logs"></a>Logs de teste
Agora, os logs do console produzidos pelo SDK podem ser habilitados/desabilitados/filtrados. Para personalizar esse recurso, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um dos valores disponíveis na enumeração `EngagementTestLogLevel`, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Recursos
A sobreposição do Alcance foi aprimorada. Ela faz parte dos recursos do pacote NuGet do SDK.

Ao atualizar para a nova versão do SDK, você pode escolher se deseja ou não manter os arquivos existentes da pasta de sobreposição de seus recursos:

* Se a sobreposição anterior estiver funcionando para você ou se você estiver integrando os elementos `WebView` manualmente, você poderá optar por manter os arquivos existentes e ela ainda funcionará. 
* Se você deseja atualizar para a nova sobreposição, basta substituir toda a pasta `overlay` dos seus recursos pela nova pasta do pacote do SDK (aplicativos UWP: após a atualização, você pode obter a nova pasta de sobreposição de %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> Usar a nova sobreposição substituirá todas as personalizações feitas na versão anterior.
> 
> 

## <a name="from-320-to-330"></a>De 3.2.0 a 3.3.0
### <a name="resources"></a>Recursos
Esta etapa aborda apenas os recursos personalizados. Se você personalizou os recursos fornecidos pelo SDK (html, imagens, sobreposição), em seguida, você precisa fazer backup dos mesmos antes de atualizar e reaplicar sua personalização em recursos atualizados.

## <a name="from-310-to-320"></a>De 3.1.0 a 3.2.0
### <a name="resources"></a>Recursos
Esta etapa aborda apenas os recursos personalizados. Se você personalizou os recursos fornecidos pelo SDK (html, imagens, sobreposição), em seguida, você precisa fazer backup dos mesmos antes de atualizar e reaplicar sua personalização em recursos atualizados.

### <a name="webview-integration"></a>Integração do Modo de exibição da Web
Algumas melhorias para coincidir com fatores forma de dispositivo diferentes foram introduzidas nesta versão. Verifique se a integração da exibição da Web corresponde ao seguinte:

Na página XAML ():

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

E no arquivo .cs associado:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }

              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif

              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }

              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

## <a name="from-200-to-300"></a>De 2.0.0 a 3.0.0
### <a name="resources"></a>Recursos
Esta etapa aborda apenas os recursos personalizados. Se você personalizou os recursos fornecidos pelo SDK (html, imagens, sobreposição), em seguida, você precisa fazer backup dos mesmos antes de atualizar e reaplicar sua personalização em recursos atualizados.

## <a name="from-111-to-200"></a>De 1.1.1 a 2.0.0
O seguinte descreve como migrar uma integração do SDK do serviço Capptain oferecido pelo Capptain SAS em um aplicativo acionado pelo Azure Mobile Engagement. 

> [!IMPORTANT]
> O Capptain e o Mobile Engagement não são os mesmos serviços e o procedimento fornecido abaixo destaca apenas como migrar o aplicativo cliente. Migrar o SDK no aplicativo NÃO migrará os dados dos servidores Capptain para os servidores do Mobile Engagement
> 
> 

Se você estiver migrando de uma versão anterior, consulte o site do Capptain para migrar primeiro para a 1.1.1 e depois aplicar o procedimento a seguir

### <a name="nuget-package"></a>Pacote NuGet
Substitua **Capptain.WindowsPhone** pelo pacote Nuget **MicrosoftAzure.MobileEngagement**.

### <a name="applying-mobile-engagement"></a>Aplicando o Mobile Engagement
O SDK usa o termo `Engagement`. Você precisa atualizar seu projeto para corresponder a esta alteração.

Você precisa desinstalar o pacote nuget do Capptain atual. Considere que todas as alterações na pasta de recursos Capptain serão removidas. Se você quiser manter esses arquivos, então faça uma cópia deles.

Depois disso, instale o novo pacote nuget do Engagement do Microsoft Azure em seu projeto. Você pode encontrá-lo diretamente no [site do nuget]. ou aqui no índice. Essa ação substitui todos os arquivos de recursos usados pelo Engagement e adiciona a nova DLL do Engagement às suas referências do projeto.

Você precisa limpar as referências do projeto, excluindo as referências de Capptain DLL. Se você não fizer isso, a versão do Capptain entrará em conflito e ocorrerão erros.

Se você personalizou os recursos do Capptain, copie o conteúdo de arquivos antigos e cole-os em novos arquivos do Engagement. Observe que os arquivos xaml e cs devem ser atualizados.

Quando essas etapas forem concluídas, você só precisará substituir as referências antigas do Capptain por novas referências do Engagement.

1. Todos os namespaces Capptain precisam ser atualizados.
   
    Antes da migração:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    Após a migração:
   
        using Microsoft.Azure.Engagement;
2. Todas as classes Capptain que contêm "Capptain" devem conter “Engagement".
   
    Antes da migração:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    Após a migração:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Para os arquivos xaml o namespace e atributos Capptain também se alteram.
   
    Antes da migração:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    Após a migração:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Alterações na página de sobreposição
   
   > [!IMPORTANT]
   > A sobreposição também será alterada. O novo namespace é `Microsoft.Azure.Engagement.Overlay`. Ele deve ser usado nos arquivos xaml e cs. Além disso, `CapptainGrid` será nomeado `EngagementGrid`, `capptain_notification_content` e `capptain_announcement_content` são nomeados `engagement_notification_content` e `engagement_announcement_content`.
   > 
   > 
   
    Para sobreposição:
   
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
   
    Torna-se:
   
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>
5. Para outros recursos como as imagens do Capptain e arquivos HTML, observe que eles também foram renomeados para usar “Engagement".

### <a name="project-declaration"></a>Declaração do projeto
Em Package.appxmanifest `File Type Associations` foi atualizado a partir de:

* capptain\_reach\_content para engagement\_reach\_content
* capptain\_log\_file para engagement\_log\_file

### <a name="application-id--sdk-key"></a>ID do aplicativo / chave do SDK
O Engagement usa uma cadeia de conexão. Você não precisa especificar uma ID de aplicativo e uma chave do SDK com o Mobile Engagement, você só precisa especificar uma cadeia de conexão. Você pode configurá-la em seu arquivo EngagementConfiguration.

A configuração do Engagement pode ser definida no arquivo `Resources\EngagementConfiguration.xml` do seu projeto.

Edite esse arquivo para especificar:

* A cadeia de conexão do aplicativo entre as marcas `<connectionString>` and `<\connectionString>`.

Se você quiser especificá-lo em tempo de execução, você pode chamar o método a seguir antes da inicialização do agente do Engagement:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

A cadeia de conexão do seu aplicativo é exibida no Portal Clássico do Azure.

### <a name="items-name-change"></a>Alteração do nome de itens
Todos os itens chamados *capptain* foram nomeados como *engagement*. Da mesma forma de *Capptain* para *Engagement*.

Exemplos de itens do Capptain usados normalmente :

* CapptainConfiguration agora denominado EngagementConfiguration
* CapptainAgent agora denominado EngagementAgent
* CapptainReach agora denominado EngagementReach
* CapptainHttpConfig agora denominado EngagementHttpConfig
* GetCapptainPageName agora denominado GetEngagementPageName

Observe que renomear também afeta métodos substituídos.

