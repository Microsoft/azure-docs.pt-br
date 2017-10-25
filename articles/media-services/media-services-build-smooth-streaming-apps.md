---
title: Tutorial do Aplicativo Windows Store do Smooth Streaming | Microsoft Docs
description: "Saiba como usar os Serviços de Mídia do Azure para criar um aplicativo C# da Windows Store com um controle XML MediaElement para reprodução de conteúdo de Smooth Streaming."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: juliako
ms.openlocfilehash: b4f8855fe6480bc58acfbbb53819f6eabe362bdb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Como compilar um aplicativo Smooth Streaming da Windows Store

O SDK do Smooth Streaming Client para Windows 8 permite que os desenvolvedores criem aplicativos da Windows Store que podem reproduzir conteúdo de Smooth Streaming sob demanda e ao vivo. Além da reprodução básica de conteúdo do Smooth Streaming, o SDK também fornece recursos avançados, como proteção do Microsoft PlayReady, restrição de nível de qualidade, Live DVR, alternância de fluxo de áudio, escuta de atualizações de status (como alterações no nível da qualidade), eventos de erros e assim por diante. Para obter mais informações sobre os recursos suportados, consulte as [notas da versão (a página pode estar em inglês)](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Para obter mais informações, consulte [Player Framework para Windows 8](http://playerframework.codeplex.com/). 

Este tutorial contém quatro lições:

1. Criar um aplicativo de armazenamento básico Smooth Streaming
2. Adicionar um barra deslizante para controlar o andamento da mídia
3. Selecionar fluxos do Smooth Streaming
4. Selecionar faixas do Smooth Streaming

## <a name="prerequisites"></a>Pré-requisitos
> [!NOTE]
> A versão de projetos 8.1 da Windows Store e versões anteriores não têm suporte no Visual Studio 2017.  Para saber mais, confira [Direcionamento e compatibilidade da plataforma Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

* Windows 8 32 bits ou 64 bits.
* Versões de 2012 até 2015 do Visual Studio.
* [SDK do Microsoft Smooth Streaming Client para Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home)

A solução completa para cada lição pode ser baixada das Amostras de Código de Desenvolvedor do MSDN (Galeria de Códigos): 

* [Lição 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - Um Media Player simples de Smooth Streaming do Windows 8, 
* [Lição 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - Um Media Player simples de Smooth Streaming do Windows 8 com um controle de barra deslizante, 
* [Lição 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - Um Media Player simples de Smooth Streaming do Windows 8 com seleção de fluxo,  
* [Lição 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) - um Media Player simples de Smooth Streaming do Windows 8 com seleção de faixa.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lição 1: Criar um aplicativo básico de Smooth Streaming para a Store

Nesta lição, você criará um aplicativo da Windows Store com um controle MediaElement para reproduzir conteúdo de Smooth Streaming.  O aplicativo em execução é semelhante a:

![Exemplo de aplicativo de Smooth Streaming da Windows Store][PlayerApplication]

Para obter mais informações sobre como desenvolver aplicativos da Windows Store, consulte [Desenvolver Ótimos Aplicativos para o Windows 8](http://msdn.microsoft.com/windows/apps/br229512.aspx). Esta lição contém os seguintes procedimentos:

1. Criar um novo projeto da Windows Store
2. Criar a interface do usuário (XAML)
3. Modificar o arquivo code-behind
4. Compilar e testar o aplicativo

**Para criar um novo projeto da Windows Store**

1. Execute o Visual Studio; há suporte para as versões de 2012 a 2015.
2. No menu **ARQUIVO**, clique em **Novo** e em **Projeto**.
3. Na caixa de diálogo Novo Projeto, digite ou selecione os seguintes valores:

| Nome | Valor |
| --- | --- |
| Grupo de modelos |Instalado/Modelos/Visual C#/Windows Store |
| Modelo |Aplicativo em branco (XAML) |
| Nome |SSPlayer |
| Local |C:\SSTutorials |
| Nome da solução |SSPlayer |
| Criar diretório para a solução |(selecionado) |

1. Clique em **OK**.

**Para adicionar uma referência ao SDK do Smooth Streaming Client**

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **SSPlayer** e, em seguida, clique em **Adicionar Referência**.
2. Digite ou selecione os valores a seguir:

| Nome | Valor |
| --- | --- |
| Grupo de referências |Windows/Extensions |
| Referência |Selecione SDK do Microsoft Smooth Streaming Client para Windows 8 e Pacote do Tempo de Execução do Microsoft Visual C++ |

1. Clique em **OK**. 

Depois de adicionar as referências, você deve selecionar a plataforma de destino (x64 ou x86). A adição de referências não funcionará para a configuração Qualquer plataforma de CPU.  No Gerenciador de Soluções, você verá a marca de aviso amarela para essas referências adicionadas.

**Para criar a interface do usuário do player**

1. No Gerenciador de Soluções, clique duas vezes em **MainPage.xaml** para abri-lo no modo de exibição de design.
2. Localize as marcas **&lt;Grid&gt;** e **&lt;/Grid&gt;** no arquivo XAML e cole o seguinte código entre as duas marcas:

         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
         </StackPanel>

         <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
         </StackPanel>

         <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
         </StackPanel>
   
   O controle MediaElement é usado para a reprodução de mídia. O controle deslizante denominado sliderProgress será usado na próxima lição para controlar o andamento da mídia.
3. Pressione **CTRL+S** para salvar o arquivo.

O controle MediaElement não oferece suporte a conteúdo de Smooth Streaming pronto para uso. Para habilitar o suporte do Smooth Streaming, você deve registrar o manipulador de fluxo de bytes do Smooth Streaming por extensão de nome de arquivo e tipo MIME.  Para se registrar, você deve usar o método MediaExtensionManager.RegisterByteStremHandler do namespace Windows.Media.

Nesse arquivo XAML, alguns manipuladores de eventos são associados aos controles.  Você deve definir esses manipuladores de eventos.

**Para modificar o arquivo code-behind**

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage.xaml** e clique em **Exibir Código**.
2. Na parte superior do arquivo, adicione a seguinte instrução using:
   
        using Windows.Media;
3. No início da classe **MainPage** adicione os membros de dados a seguir:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. No final do construtor **MainPage** adicione as duas linhas a seguir:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. No final da classe **MainPage** cole o código a seguir:
   
         # region UI Button Click Events
         private void btnPlay_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Play();
         txtStatus.Text = "MediaElement is playing ...";
         }
         private void btnPause_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Pause();
         txtStatus.Text = "MediaElement is paused";
         }
         private void btnSetSource_Click(object sender, RoutedEventArgs e)
         {

         sliderProgress.Value = 0;
         mediaElement.Source = new Uri(txtMediaSource.Text);

         if (chkAutoPlay.IsChecked == true)
         {
             txtStatus.Text = "MediaElement is playing ...";
         }
         else
         {
             txtStatus.Text = "Click the Play button to play the media source.";
         }
         }
         private void btnStop_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Stop();
         txtStatus.Text = "MediaElement is stopped";
         }
         private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
         {

         txtStatus.Text = "Seek to position " + sliderProgress.Value;
         mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
         }
         # endregion

O manipulador de eventos sliderProgress_PointerPressed é definido aqui.  Para fazer isso funcionar há mais trabalhos a fazer que serão abordados na próxima lição deste tutorial.
6. Pressione **CTRL+S** para salvar o arquivo.

O arquivo code-behind concluído deve ser semelhante ao seguinte:

![Exibição do código no Visual Studio do aplicativo de Smooth Streaming da Windows Store][CodeViewPic]

**Para compilar e testar o aplicativo**

1. No menu **COMPILAR** clique em **Gerenciador de Configurações**.
2. Altere **Plataforma da solução ativa** para que corresponda à sua plataforma de desenvolvimento.
3. Pressione **F6** para compilar o projeto. 
4. Pressione **F5** para executar o aplicativo.
5. Na parte superior do aplicativo, use a URL do Smooth Streaming padrão ou digite outra URL. 
6. Clique em **Definir Origem**. Como, por padrão, **Executar Automaticamente** está habilitado, a mídia deverá ser reproduzida automaticamente.  Você pode controlar a mídia usando os botões **Reproduzir**, **Pausar** e **Parar**.  É possível controlar o volume da mídia usando o controle deslizante vertical.  No entanto, a barra deslizante horizontal para controle do progresso da mídia ainda não está totalmente implementado. 

Você concluiu a Lição 1.  Nesta lição, você usa um controle MediaElement para reproduzir conteúdo de Smooth Streaming.  Na próxima lição, você irá adicionar um controle deslizante para controlar o andamento do conteúdo de Smooth Streaming.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lição 2: Adicionar um barra deslizante para controlar o andamento da mídia

Na Lição 1, você criou um aplicativo da Windows Store com um controle XAML MediaElement para reproduzir conteúdo de mídia de Smooth Streaming.  Ele vem com algumas funções básicas de mídia, como iniciar, parar e pausar.  Nesta lição, você irá adicionar um controle de barra deslizante ao aplicativo.

Neste tutorial, usaremos um timer para atualizar a posição do controle deslizante com base na posição atual do controle MediaElement.  A hora de início e de término do controle deslizante também precisam ser atualizadas no caso de conteúdo ao vivo.  Isso pode ser manipulado melhor no evento de atualização de origem adaptável.

As origens de mídia são objetos que geram dados de mídia.  O resolvedor de origem utiliza um fluxo de bytes ou uma URL e cria a origem de mídia adequada para esse conteúdo.  O resolvedor de origem é o modo padrão para os aplicativos criarem origens de mídia. 

Esta lição contém os seguintes procedimentos:

1. Registrar o manipulador do Smooth Streaming 
2. Adicionar manipuladores de eventos no nível do gerenciador de origem adaptável
3. Adicionar os manipuladores de eventos de origem adaptável
4. Adicionar manipuladores de eventos de MediaElement
5. Adicionar o código relacionado à barra de controle deslizante
6. Compilar e testar o aplicativo

**Para registrar o manipulador de fluxo de bytes de Smooth Streaming e aprovar o propertyset**

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage.xaml** e clique em **Exibir Código**.
2. No início do arquivo, adicione a seguinte instrução using:

        using Microsoft.Media.AdaptiveStreaming;
3. No início da classe MainPage adicione os membros de dados a seguir:

         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
4. No construtor **MainPage**, adicione o seguinte código após a linha **this.Initialize Components();** e as linhas de código do registro escritas na lição anterior:

        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
5. Dentro do construtor **MainPage** , modifique os dois métodos RegisterByteStreamHandler para adicionar os seguintes parâmetros:

         // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
         // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
         // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
         propertySet);
6. Pressione **CTRL+S** para salvar o arquivo.

**Para adicionar o manipulador de eventos no nível do gerenciador de origens adaptáveis**

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage.xaml** e clique em **Exibir Código**.
2. No início da classe **MainPage** adicione os membros de dados a seguir:
   
     private AdaptiveSource adaptiveSource = null;
3. No final da classe **MainPage** , adicione o manipulador de eventos a seguir:
   
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
4. No final do construtor **MainPage** , adicione a seguinte linha para inscrever-se para o evento de fonte aberta adaptável:
   
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
5. Pressione **CTRL+S** para salvar o arquivo.

**Para adicionar os manipuladores de eventos de origens adaptáveis**

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage.xaml** e clique em **Exibir Código**.
2. No início da classe **MainPage** adicione os membros de dados a seguir:
   
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate;   private Manifest manifestObject;
3. No final da classe **MainPage** , adicione os manipuladores de eventos a seguir:

         # region Adaptive Source Level Events
         private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
         }

         private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
         {

            adaptiveSourceStatusUpdate = args;
         }

         private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
         {

            txtStatus.Text = "Error: " + args.HttpResponse;
         }

         # endregion Adaptive Source Level Events
4. No final do método **mediaElement AdaptiveSourceOpened** , adicione o seguinte código para se inscrever nos eventos:
   
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
5. Pressione **CTRL+S** para salvar o arquivo.

Os mesmos eventos também estão disponíveis no nível do gerenciador de origens adaptáveis, que pode ser usado para manipular a funcionalidade comum a todos os elementos de mídia do aplicativo. Cada AdaptiveSource inclui seus próprios eventos e todos os eventos de AdaptiveSource serão colocados em cascata no AdaptiveSourceManager.

**Para adicionar manipuladores de eventos de elementos de mídia**

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage.xaml** e clique em **Exibir Código**.
2. No final da classe **MainPage** , adicione os manipuladores de eventos a seguir:

         # region Media Element Event Handlers
         private void MediaOpened(object sender, RoutedEventArgs e)
         {

            txtStatus.Text = "MediaElement opened";
         }

         private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
         {

            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
         }

         private void MediaEnded(object sender, RoutedEventArgs e)
         {

            txtStatus.Text ="MediaElement ended.";
         }

         # endregion Media Element Event Handlers
3. No final do construtor **MainPage** , adicione o seguinte código para se inscrever nos eventos:

         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
4. Pressione **CTRL+S** para salvar o arquivo.

**Para adicionar o código relacionado à barra de controle deslizante**

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage.xaml** e clique em **Exibir Código**.
2. No início do arquivo, adicione a seguinte instrução using:
      
        using Windows.UI.Core;
3. No início da classe **MainPage** adicione os membros de dados a seguir:
   
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
4. No final do construtor **MainPage** adicione o código a seguir:
   
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
5. No final da classe **MainPage** , adicione o código a seguir:

         # region sliderMediaPlayer
         private double SliderFrequency(TimeSpan timevalue)
         {

            long absvalue = 0;
            double stepfrequency = -1;

            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }

            TimeSpan totalDVRDuration = new TimeSpan(absvalue);

            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }

            return stepfrequency;
         }

         void updateSliderPositionoNTicks(object sender, object e)
         {

            sliderProgress.Value = mediaElement.Position.TotalSeconds;
         }

         public void setupTimer()
         {

            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
         }

         public void startTimer()
         {

            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderStartTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderEndTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
         }

         # endregion sliderMediaPlayer
      
>[!NOTE]
>O CoreDispatcher é usado para fazer alterações no thread da interface do usuário no thread que não é da interface do usuário. No caso de afunilamento no dispatcher de threads, o desenvolvedor pode optar por usar o dispatcher fornecido pelo elemento da interface do usuário que pretende atualizar.  Por exemplo:
   
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
6. Ao final do método **mediaElement_AdaptiveSourceStatusUpdated**, adicione este código:

         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
7. No final do método **MediaOpened** , adicione o seguinte código:

         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
8. Pressione **CTRL+S** para salvar o arquivo.

**Para compilar e testar o aplicativo**

1. Pressione **F6** para compilar o projeto. 
2. Pressione **F5** para executar o aplicativo.
3. Na parte superior do aplicativo, use a URL do Smooth Streaming padrão ou digite outra URL. 
4. Clique em **Definir Origem**. 
5. Testar a barra de controle deslizante.

Você concluiu a lição 2.  Nesta lição, você adicionou um controle deslizante ao aplicativo. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lição 3: Selecionar fluxos de Smooth Streaming
O Smooth Streaming é capaz de transmitir conteúdo com faixas de áudio de vários idiomas que podem ser selecionadas pelos visualizadores.  Nesta lição, você habilitará a seleção dos fluxos pelos visualizadores. Esta lição contém os seguintes procedimentos:

1. Modificar o arquivo XAML
2. Modificar o arquivo code-behind
3. Compilar e testar o aplicativo

**Para modificar o arquivo XAML**

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage.xaml**, e, em seguida, clique em **Designer de Modos de Exibição**.
2. Localize &lt;Grid.RowDefinitions&gt; e modifique as RowDefinitions para que elas fiquem assim:
   
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
3. Nas marcas &lt;Grid&gt;&lt;/Grid&gt;, adicione o seguinte código para definir um controle de caixa de listagem, para que os usuários possam ver a lista de fluxos disponíveis e selecioná-los:

         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
         </Grid>
4. Pressione **CTRL+S** para salvar as alterações.

**Para modificar o arquivo code-behind**

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage.xaml** e clique em **Exibir Código**.
2. Dentro do namespace SSPlayer, adicione uma nova classe:
   
        #region class Stream
   
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
   
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
   
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
   
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream
3. No início da classe MainPage, adicione as seguintes definições de variáveis:
   
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
4. Dentro da classe MainPage, adicione a seguinte região:
   
        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
   
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
   
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
   
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
   
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
   
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
   
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
   
            // Select the frist video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
   
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
   
            // Select the frist audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
   
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
   
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection
5. Localize o método mediaElement_ManifestReady e acrescente o seguinte código no final da função:
   
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   
    Quando o manifesto MediaElement estiver pronto, o código obterá uma lista dos fluxos disponíveis e populará a caixa de listagem da interface do usuário com a lista.
6. Na classe MainPage, localize a região de eventos de cliques de botões da interface do usuário e adicione a seguinte definição de função:
   
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Para compilar e testar o aplicativo**

1. Pressione **F6** para compilar o projeto. 
2. Pressione **F5** para executar o aplicativo.
3. Na parte superior do aplicativo, use a URL do Smooth Streaming padrão ou digite outra URL. 
4. Clique em **Definir Origem**. 
5. O idioma padrão é audio_eng. Tente alternar entre audio_eng e audio_es. Toda vez que selecionar um novo fluxo, você deverá clicar no botão Enviar.

Você concluiu a lição 3.  Nesta lição, você adicionará a funcionalidade de escolher fluxos.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lição 4: Selecionar faixas de Smooth Streaming
Um apresentação de Smooth Streaming pode conter vários arquivos de vídeo codificados com diferentes níveis de qualidade (taxas de bits) e resoluções. Nesta lição, você habilitará a seleção de faixas pelos usuários. Esta lição contém os seguintes procedimentos:

1. Modificar o arquivo XAML
2. Modificar o arquivo code-behind
3. Compilar e testar o aplicativo

**Para modificar o arquivo XAML**

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage.xaml**, e, em seguida, clique em **Designer de Modos de Exibição**.
2. Localize a marca &lt;Grid&gt; com o nome **gridStreamAndBitrateSelection** e acrescente o seguinte código ao final da marca:
   
         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
         </StackPanel>
3. Pressione **CTRL+S** para salvar as alterações

**Para modificar o arquivo code-behind**

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage.xaml** e clique em **Exibir Código**.
2. Dentro do namespace SSPlayer, adicione uma nova classe:
   
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
   
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
   
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
   
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track
3. No início da classe MainPage, adicione as seguintes definições de variáveis:
   
        private List<Track> availableTracks;
4. Dentro da classe MainPage, adicione a seguinte região:
   
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>
   
        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();
   
            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;
   
            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;
   
                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }
   
        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }
   
        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }
   
        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }
   
        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection
5. Localize o método mediaElement_ManifestReady e acrescente o seguinte código no final da função:
   
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
6. Na classe MainPage, localize a região de eventos de cliques de botões da interface do usuário e adicione a seguinte definição de função:
   
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }

**Para compilar e testar o aplicativo**

1. Pressione **F6** para compilar o projeto. 
2. Pressione **F5** para executar o aplicativo.
3. Na parte superior do aplicativo, use a URL do Smooth Streaming padrão ou digite outra URL. 
4. Clique em **Definir Origem**. 
5. Por padrão, todas as faixas do fluxo de vídeo são selecionadas. Para testar as alterações de taxa de bits, é possível selecionar a taxa de bits mais baixa disponível e, em seguida, selecionar a taxa de bits mais alta disponível. Você deve clicar em Enviar após cada alteração.  Você pode ver as alterações na qualidade do vídeo.

Você concluiu a lição 4.  Nesta lição, você adicionará a funcionalidade de escolher faixas.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Outros recursos:
* [Como criar um aplicativo JavaScript de Smooth Streaming do Windows 8 com recursos avançados](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Visão Geral Técnica de Smooth Streaming](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

