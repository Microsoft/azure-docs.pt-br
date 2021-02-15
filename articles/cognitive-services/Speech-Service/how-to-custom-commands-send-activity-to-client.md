---
title: Enviar atividade de comandos personalizados para o aplicativo cliente
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprende a enviar atividade de um aplicativo de comandos personalizados para um aplicativo cliente que executa o SDK de fala.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52a4dbc4ff01515af8cd7d2503877184a09f7e64
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566088"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Enviar atividade de comandos personalizados para o aplicativo cliente

Neste artigo, você aprende a enviar atividade de um aplicativo de comandos personalizados para um aplicativo cliente que executa o SDK de fala.

Você conclui as seguintes tarefas:

- Definir e enviar uma carga JSON personalizada de seu aplicativo de comandos personalizados
- Receber e visualizar o conteúdo da carga JSON personalizada de um aplicativo cliente do SDK de fala do C# UWP

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ou superior. Este guia usa o Visual Studio 2019
> * Uma chave de assinatura do Azure para o serviço de Fala: [Obtenha uma gratuitamente](overview.md#try-the-speech-service-for-free) ou crie-a no [portal do Azure](https://portal.azure.com)
> * Um [aplicativo de Comandos Personalizados criado](quickstart-custom-commands-application.md) anteriormente
> * Um aplicativo cliente habilitado para SDK de fala: [como integrar com um aplicativo cliente usando o SDK de fala](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Configurar atividade de envio para o cliente 
1. Abra o aplicativo de comandos personalizados que você criou anteriormente
1. Selecione o comando **TurnOnOff** , selecione **ConfirmationResponse** em regra de conclusão e, em seguida, selecione **Adicionar uma ação**
1. Em **nova ação-tipo** , selecione **Enviar atividade para o cliente**
1. Copie o JSON abaixo para o **conteúdo da atividade**
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
1. Clique em **salvar** para criar uma nova regra com uma ação enviar atividade, **treinar** e **publicar** a alteração

   > [!div class="mx-imgBorder"]
   > ![Regra de conclusão de atividade de envio](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integração ao aplicativo cliente

Em [como: configurar o aplicativo cliente com o SDK de fala (versão prévia)](./how-to-custom-commands-setup-speech-sdk.md), você criou um aplicativo cliente UWP com o SDK de fala que tratou comandos como `turn on the tv` , `turn off the fan` . Com alguns elementos visuais adicionados, você pode ver o resultado desses comandos.

Para adicionar caixas rotuladas com texto indicando **Ativar** ou **desativar** , adicione o seguinte bloco XML de StackPanel a `MainPage.xaml` .

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>Adicionar bibliotecas de referência

Como você criou uma carga JSON, precisará adicionar uma referência à biblioteca [JSON.net](https://www.newtonsoft.com/json) para lidar com a desserialização.

1. Cliente à sua solução.
1. Escolha **gerenciar pacotes NuGet para solução** , selecione **procurar** 
1. Se você já tiver instalado o **Newtonsoft.jsno** , verifique se sua versão é pelo menos 12.0.3. Caso contrário, vá para **gerenciar pacotes NuGet para solução-atualizações** , procure **Newtonsoft.jsem** para atualizá-lo. Este guia está usando a versão 12.0.3.

    > [!div class="mx-imgBorder"]
    > ![Enviar carga da atividade](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Além disso, verifique se o pacote NuGet **Microsoft. NetCore. UniversalWindowsPlatform** é pelo menos 6.2.10. Este guia está usando a versão 6.2.10.

Em ' MainPage. XAML. cs ', adicione

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>Manipular a carga recebida

No `InitializeDialogServiceConnector` , substitua o `ActivityReceived` manipulador de eventos pelo código a seguir. O `ActivityReceived` manipulador de eventos modificado extrairá a carga da atividade e alterará o estado visual da TV ou do ventilador, respectivamente.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.value?.state != null ? activity.value.state.ToString() : string.Empty;
        var device = activity?.value?.device != null ? activity.value.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Experimente

1. Iniciar o aplicativo
1. Selecione Habilitar microfone
1. Selecione o botão Falar
1. Diga `turn on the tv`
1. O estado visual da TV deve mudar para "on"
   > [!div class="mx-imgBorder"]
   > ![Captura de tela que mostra que o estado visual do T V está agora ativado.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: configurar pontos de extremidade da Web (visualização)](./how-to-custom-commands-setup-web-endpoints.md)
