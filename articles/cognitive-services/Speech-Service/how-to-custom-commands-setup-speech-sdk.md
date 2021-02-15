---
title: Integrar com um aplicativo cliente usando o SDK de Fala
titleSuffix: Azure Cognitive Services
description: como fazer solicitações para um aplicativo de comandos personalizados publicados do SDK de fala em execução em um aplicativo UWP.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: fa3a6d16b79800043bdcd3f183dd86fa278dd1a9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026020"
---
# <a name="integrate-with-a-client-application-using-speech-sdk"></a>Integrar com um aplicativo cliente usando o SDK de fala

Neste artigo, você aprende a fazer solicitações para um aplicativo de comandos personalizados publicados do SDK de fala em execução em um aplicativo UWP. Para estabelecer uma conexão com o aplicativo de comandos personalizados, você precisa:

- Publicar um aplicativo de comandos personalizados e obter um identificador de aplicativo (ID do aplicativo)
- Criar um aplicativo cliente Plataforma Universal do Windows (UWP) usando o SDK de fala para permitir que você se comunique com seu aplicativo de comandos personalizados

## <a name="prerequisites"></a>Pré-requisitos

Um aplicativo de comandos personalizados é necessário para concluir este artigo. Se você não tiver criado um aplicativo de comandos personalizados, poderá fazer isso seguindo os guias de início rápido:
> [!div class = "checklist"]
> * [Criar um aplicativo de comandos personalizados](quickstart-custom-commands-application.md)

Você também precisará de:
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ou superior. Este guia se baseia no Visual Studio 2019.
> * Uma chave de assinatura do Azure para os Serviços de Fala. [Obtenha um gratuitamente](overview.md#try-the-speech-service-for-free) ou crie-o no [portal do Azure](https://portal.azure.com)
> * [Habilitar o dispositivo para desenvolvimento](/windows/uwp/get-started/enable-your-device-for-development)

## <a name="step-1-publish-custom-commands-application"></a>Etapa 1: publicar o aplicativo de comandos personalizados

1. Abra o aplicativo de comandos personalizados criado anteriormente
1. Vá para **configurações**, selecione **recurso Luis**
1. Se o **recurso de previsão** não for atribuído, selecione uma chave de previsão de consulta ou crie uma nova

    A chave de previsão de consulta sempre é necessária antes de publicar um aplicativo. Para obter mais informações sobre os recursos do LUIS, consulte [Create Luis Resource](../luis/luis-how-to-azure-subscription.md)

1. Volte para edição de comandos, selecione **publicar**

   > [!div class="mx-imgBorder"]
   > ![aplicativo Publicar](media/custom-commands/setup-speech-sdk-publish-application.png)

1. Copiar a ID do aplicativo da notificação de publicação para uso posterior
1. Copiar a chave de recurso de fala para uso posterior

## <a name="step-2-create-a-visual-studio-project"></a>Etapa 2: criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>Etapa 3: adicionar código de exemplo

Nesta etapa, adicionamos o código XAML que define a interface do usuário do aplicativo e adiciono a implementação code-behind do C#.

### <a name="xaml-code"></a>Código XAML

Crie a interface do usuário do aplicativo adicionando o código XAML.

1. Em **Gerenciador de soluções**, abra `MainPage.xaml`

1. Na exibição XAML do designer, substitua todo o conteúdo pelo seguinte snippet de código:

   ```xml
   <Page
       x:Class="helloworld.MainPage"
       xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       xmlns:local="using:helloworld"
       xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
       xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
       mc:Ignorable="d"
       Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

       <Grid>
           <StackPanel Orientation="Vertical" HorizontalAlignment="Center"
                       Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
               <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"
                       Margin="0,10,10,0" Click="EnableMicrophone_ButtonClicked"
                       Height="35"/>
               <Button x:Name="ListenButton" Content="Talk"
                       Margin="0,10,10,0" Click="ListenButton_ButtonClicked"
                       Height="35"/>
               <StackPanel x:Name="StatusPanel" Orientation="Vertical"
                           RelativePanel.AlignBottomWithPanel="True"
                           RelativePanel.AlignRightWithPanel="True"
                           RelativePanel.AlignLeftWithPanel="True">
                   <TextBlock x:Name="StatusLabel" Margin="0,10,10,0"
                              TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                   <Border x:Name="StatusBorder" Margin="0,0,0,0">
                       <ScrollViewer VerticalScrollMode="Auto"
                                     VerticalScrollBarVisibility="Auto" MaxHeight="200">
                           <!-- Use LiveSetting to enable screen readers to announce
                                the status update. -->
                           <TextBlock
                               x:Name="StatusBlock" FontWeight="Bold"
                               AutomationProperties.LiveSetting="Assertive"
                               MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}"
                               Margin="10,10,10,20" TextWrapping="Wrap"  />
                       </ScrollViewer>
                   </Border>
               </StackPanel>
           </StackPanel>
           <MediaElement x:Name="mediaElement"/>
       </Grid>
   </Page>
   ```

O modo de exibição de Design é atualizado para mostrar a interface do usuário do aplicativo.

### <a name="c-code-behind-source"></a>Origem code-behind em C#

Adicione a fonte code-behind para que o aplicativo funcione conforme o esperado. A origem code-behind inclui:

- Instruções obrigatórias `using` para `Speech` os `Speech.Dialog` namespaces e
- Uma implementação simples para garantir o acesso ao microfone, conectado a um manipulador de botão
- Auxiliares básicos de interface do usuário para apresentar erros e mensagens no aplicativo
- Um ponto inicial para o caminho do código de inicialização que será populado posteriormente
- Um auxiliar para reproduzir a conversão de texto em fala (sem suporte para streaming)
- Um manipulador de botão vazio para iniciar a escuta que será populado mais tarde

Adicione a fonte code-behind da seguinte maneira:

1. Em **Gerenciador de soluções**, abra o arquivo de origem code-behind `MainPage.xaml.cs` (agrupado em `MainPage.xaml` )

1. Substitua o conteúdo do arquivo pelo seguinte código: 

   ```csharp
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Audio;
   using Microsoft.CognitiveServices.Speech.Dialog;
   using System;
   using System.IO;
   using System.Text;
   using Windows.UI.Xaml;
   using Windows.UI.Xaml.Controls;
   using Windows.UI.Xaml.Media;

   namespace helloworld
   {
       public sealed partial class MainPage : Page
       {
           private DialogServiceConnector connector;

           private enum NotifyType
           {
               StatusMessage,
               ErrorMessage
           };

           public MainPage()
           {
               this.InitializeComponent();
           }

           private async void EnableMicrophone_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               bool isMicAvailable = true;
               try
               {
                   var mediaCapture = new Windows.Media.Capture.MediaCapture();
                   var settings =
                       new Windows.Media.Capture.MediaCaptureInitializationSettings();
                   settings.StreamingCaptureMode =
                       Windows.Media.Capture.StreamingCaptureMode.Audio;
                   await mediaCapture.InitializeAsync(settings);
               }
               catch (Exception)
               {
                   isMicAvailable = false;
               }
               if (!isMicAvailable)
               {
                   await Windows.System.Launcher.LaunchUriAsync(
                       new Uri("ms-settings:privacy-microphone"));
               }
               else
               {
                   NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
               }
           }

           private void NotifyUser(
               string strMessage, NotifyType type = NotifyType.StatusMessage)
           {
               // If called from the UI thread, then update immediately.
               // Otherwise, schedule a task on the UI thread to perform the update.
               if (Dispatcher.HasThreadAccess)
               {
                   UpdateStatus(strMessage, type);
               }
               else
               {
                   var task = Dispatcher.RunAsync(
                       Windows.UI.Core.CoreDispatcherPriority.Normal,
                       () => UpdateStatus(strMessage, type));
               }
           }

           private void UpdateStatus(string strMessage, NotifyType type)
           {
               switch (type)
               {
                   case NotifyType.StatusMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Green);
                       break;
                   case NotifyType.ErrorMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Red);
                       break;
               }
               StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text)
                   ? strMessage : "\n" + strMessage;

               if (!string.IsNullOrEmpty(StatusBlock.Text))
               {
                   StatusBorder.Visibility = Visibility.Visible;
                   StatusPanel.Visibility = Visibility.Visible;
               }
               else
               {
                   StatusBorder.Visibility = Visibility.Collapsed;
                   StatusPanel.Visibility = Visibility.Collapsed;
               }
               // Raise an event if necessary to enable a screen reader
               // to announce the status update.
               var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
               if (peer != null)
               {
                   peer.RaiseAutomationEvent(
                       Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
               }
           }

           // Waits for and accumulates all audio associated with a given
           // PullAudioOutputStream and then plays it to the MediaElement. Long spoken
           // audio will create extra latency and a streaming playback solution
           // (that plays audio while it continues to be received) should be used --
           // see the samples for examples of this.
           private void SynchronouslyPlayActivityAudio(
               PullAudioOutputStream activityAudio)
           {
               var playbackStreamWithHeader = new MemoryStream();
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
               playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
               playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

               byte[] pullBuffer = new byte[2056];

               uint lastRead = 0;
               do
               {
                   lastRead = activityAudio.Read(pullBuffer);
                   playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
               }
               while (lastRead == pullBuffer.Length);

               var task = Dispatcher.RunAsync(
                   Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                   mediaElement.SetSource(
                       playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                   mediaElement.Play();
               });
           }

           private void InitializeDialogServiceConnector()
           {
               // New code will go here
           }

           private async void ListenButton_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               // New code will go here
           }
       }
   }
   ```
    > [!NOTE]
    > Se você vir o erro: "o tipo ' Object ' é definido em um assembly que não é referenciado"
    > 1. Cliente à sua solução.
    > 1. Escolha **gerenciar pacotes NuGet para a solução**, selecione **atualizações** 
    > 1. Se você vir **Microsoft. NetCore. UniversalWindowsPlatform** na lista de atualizações, atualize **Microsoft. NetCore. UniversalWindowsPlatform** para a versão mais recente

1. Adicione o código a seguir ao corpo do método de `InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. Substitua as cadeias de caracteres `YourApplicationId` , `YourSpeechSubscriptionKey` e `YourServiceRegion` pelos seus próprios valores para seu aplicativo, a assinatura de fala e a [região](regions.md)

1. Acrescente o seguinte trecho de código ao final do corpo do método de `InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += (sender, activityReceivedEventArgs) =>
   {
       NotifyUser(
           $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

       if (activityReceivedEventArgs.HasAudio)
       {
           SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
       }
   };

   // Canceled will be signaled when a turn is aborted or experiences an error condition
   connector.Canceled += (sender, canceledEventArgs) =>
   {
       NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
       if (canceledEventArgs.Reason == CancellationReason.Error)
       {
           NotifyUser(
               $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
       }
   };

   // Recognizing (not 'Recognized') will provide the intermediate recognized text
   // while an audio stream is being processed
   connector.Recognizing += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
   };

   // Recognized (not 'Recognizing') will provide the final recognized text
   // once audio capture is completed
   connector.Recognized += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
   };

   // SessionStarted will notify when audio begins flowing to the service for a turn
   connector.SessionStarted += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
   };

   // SessionStopped will notify when a turn is complete and
   // it's safe to begin listening again
   connector.SessionStopped += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
   };
   ```

1. Adicione o seguinte trecho de código ao corpo do `ListenButton_ButtonClicked` método na `MainPage` classe

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. Na barra de menus, escolha **arquivo**  >  **salvar tudo** para salvar suas alterações

## <a name="try-it-out"></a>Experimente

1. Na barra de menus, escolha **Compilar** > **Compilar Solução** para compilar o aplicativo. O código deve compilar sem erros.

1. Escolha **Depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo. A janela **helloworld** é exibida.

   ![Exemplo de aplicativo de assistente virtual UWP em C# – Início rápido](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Selecione **Habilitar Microfone**. Se a solicitação de permissão de acesso for exibida, selecione **Sim**.

   ![Solicitação de permissão de acesso ao microfone](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecione **falar** e fale uma frase ou sentença em inglês no microfone do seu dispositivo. Sua fala será transmitida para o canal de Fala do Direct Line e transcrita em texto, que será exibida na janela.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: enviar atividade para o aplicativo cliente (visualização)](./how-to-custom-commands-send-activity-to-client.md)