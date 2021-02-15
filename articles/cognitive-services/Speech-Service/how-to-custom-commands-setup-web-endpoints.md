---
title: Configurar pontos de extremidade da Web (versão prévia)
titleSuffix: Azure Cognitive Services
description: configurar pontos de extremidade da Web para comandos personalizados
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 6f2dfdbb5833b34441b4abba7359ad70c4717d1d
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602156"
---
# <a name="set-up-web-endpoints"></a>Configurar pontos de extremidade da Web

Neste artigo, você aprenderá a configurar pontos de extremidade da Web em um aplicativo de Comandos Personalizados que permitem fazer solicitações HTTP em um aplicativo cliente. Você realizará as seguintes tarefas:

- Configurar pontos de extremidade da Web no aplicativo de Comandos Personalizados
- Chamar pontos de extremidade da Web no aplicativo de Comandos Personalizados
- Receber a resposta dos pontos de extremidade da Web
- Integrar a resposta dos pontos de extremidade da Web a um conteúdo JSON personalizado, enviá-la e visualizá-la em um aplicativo cliente C# do SDK de Fala do UWP

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Uma chave de assinatura do Azure para o serviço de Fala: [Obtenha uma gratuitamente](overview.md#try-the-speech-service-for-free) ou crie-a no [portal do Azure](https://portal.azure.com)
> * Um [aplicativo de Comandos Personalizados criado](quickstart-custom-commands-application.md) anteriormente
> * Um aplicativo cliente habilitado para o SDK de Fala: [Como enviar uma atividade para o aplicativo cliente](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Configurar pontos de extremidade da Web

1. Abra o aplicativo de Comandos Personalizados criado anteriormente.
1. Acesse "Pontos de extremidade da Web" e clique em "Novo ponto de extremidade da Web".

   > [!div class="mx-imgBorder"]
   > ![Novo ponto de extremidade da Web](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Configuração | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Nome | UpdateDeviceState | Nome do ponto de extremidade da Web. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | A URL do ponto de extremidade com a qual você deseja que o aplicativo de Comandos Personalizados se comunique. |
   | Método | POST | As interações permitidas (como GET e POST) com o ponto de extremidade.|
   | Cabeçalhos | Chave: aplicativo; valor: use os oito primeiros dígitos da applicationId | Os parâmetros de cabeçalho a serem incluídos no cabeçalho da solicitação.|

    > [!NOTE]
    > - O exemplo de ponto de extremidade da Web criado por meio da [Função do Azure](../../azure-functions/index.yml), que se conecta ao banco de dados que salva o estado do dispositivo da TV e do ventilador
    > - O cabeçalho sugerido só é necessário para o exemplo de ponto de extremidade
    > - Para garantir que o valor do cabeçalho seja exclusivo no exemplo de ponto de extremidade, use os oito primeiros dígitos da applicationId
    > - No mundo real, o ponto de extremidade da Web pode ser o ponto de extremidade do [Hub IoT](../../iot-hub/about-iot-hub.md) que gerencia seus dispositivos

1. Clique em **Salvar**.

## <a name="call-web-endpoints"></a>Chamar pontos de extremidade da Web

1. Acesse o comando **TurnOnOff**, selecione **ConfirmationResponse** em regra de conclusão e escolha **Adicionar uma ação**.
1. Em **Novo Tipo de Ação**, selecione **Chamar ponto de extremidade da Web**
1. Em **Editar Ação – Pontos de Extremidade**, selecione **UpdateDeviceState**, que é o ponto de extremidade da Web que criamos.  
1. Em **Configuração**, coloque os seguintes valores:
   > [!div class="mx-imgBorder"]
   > ![Chamar parâmetros de ação dos pontos de extremidade da Web](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Configuração | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Pontos de extremidade | UpdateDeviceState | O ponto de extremidade da Web que você deseja chamar nessa ação. |
   | Parâmetros de consulta | item={SubjectDevice}&&value={OnOff} | Os parâmetros de consulta a serem acrescentados à URL do ponto de extremidade da Web.  |
   | Conteúdo do corpo | N/D | O conteúdo do corpo da solicitação. |

    > [!NOTE]
    > - Os parâmetros de consulta sugeridos só são necessários para o exemplo de ponto de extremidade

1. Em **Em Caso de Êxito – Ação a ser executada**, selecione **Enviar resposta de fala**.

    No **Editor simples**, insira `{SubjectDevice} is {OnOff}`.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela que mostra o em ação de sucesso a ser executada.](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Configuração | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Ação a ser executada | Enviar resposta de fala | Ação a ser executada se a solicitação ao ponto de extremidade da Web for bem-sucedida |

   > [!NOTE]
   > - Acesse também diretamente os campos na resposta HTTP usando `{YourWebEndpointName.FieldName}`. Por exemplo: `{UpdateDeviceState.TV}`

1. Em **Em Caso de Falha – Ação a ser executada**, selecione **Enviar resposta de fala**

    No **Editor simples**, insira `Sorry, {WebEndpointErrorMessage}`.

   > [!div class="mx-imgBorder"]
   > ![Chamar ação dos pontos de extremidade da Web em Caso de Falha](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Configuração | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Ação a ser executada | Enviar resposta de fala | Ação a ser executada se a solicitação ao ponto de extremidade da Web falhar |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` é opcional. Você terá a liberdade para removê-la se não quiser expor nenhuma mensagem de erro.
   > - No exemplo de ponto de extremidade, enviamos uma resposta HTTP novamente com mensagens de erro detalhadas para erros comuns, como parâmetros de cabeçalho ausentes.

### <a name="try-it-out-in-test-portal"></a>Experimente-o no portal de teste
- Resposta Em Caso de Êxito:
Salvar, treinar e testar
   > [!div class="mx-imgBorder"]
   > ![Captura de tela que mostra a resposta em caso de êxito.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Resposta Em Caso de Falha:
Remover um dos parâmetros de consulta, salvar, treinar novamente e testar
   > [!div class="mx-imgBorder"]
   > ![Chamar ação dos pontos de extremidade da Web em Caso de Êxito](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integração ao aplicativo cliente

Em [Como enviar uma atividade para o aplicativo cliente (versão prévia)](./how-to-custom-commands-send-activity-to-client.md), você adicionou uma ação **Enviar atividade para o cliente**. A atividade é enviada ao aplicativo cliente, independentemente de a ação **Chamar ponto de extremidade da Web** ser bem-sucedida ou não.
No entanto, na maioria dos casos, você só deseja enviar a atividade para o aplicativo cliente quando a chamada ao ponto de extremidade da Web é bem-sucedida. Neste exemplo, isso ocorre quando o estado do dispositivo é atualizado com êxito.

1. Exclua a ação **Enviar atividade para o cliente** adicionada anteriormente.
1. Editar o ponto de extremidade da Web de chamada:
    1. Em **Configuração**, verifique se a opção **Parâmetros de Consulta** é `item={SubjectDevice}&&value={OnOff}`
    1. Em **Em Caso de Êxito**, altere **Ação a ser executada** para **Enviar atividade para o cliente**
    1. Copie o JSON abaixo para **Conteúdo da Atividade**
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
Agora você só envia a atividade ao cliente quando a solicitação ao ponto de extremidade da Web é bem-sucedida.

### <a name="create-visuals-for-syncing-device-state"></a>Criar visuais para sincronizar o estado do dispositivo
Adicione o XML a seguir a `MainPage.xaml` acima do bloco `"EnableMicrophoneButton"`.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Sincronizar estado do dispositivo

Em `MainPage.xaml.cs`, adicione a referência `using Windows.Web.Http;`. Adicione o código a seguir à classe `MainPage` . Esse método enviará uma solicitação GET ao exemplo de ponto de extremidade e extrairá o estado atual do dispositivo para o aplicativo. Lembre-se de alterar `<your_app_name>` para o que você usou no **cabeçalho** no ponto de extremidade da Web de Comandos Personalizados

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request.
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Experimente

1. Iniciar o aplicativo
1. Clique em Sincronizar Estado do Dispositivo.
Se você testou o aplicativo com `turn on tv` na seção anterior, a TV é mostrada como "ligada".
    > [!div class="mx-imgBorder"]
    > ![Sincronizar estado do dispositivo](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Selecione Habilitar microfone
1. Selecione o botão Falar
1. Diga `turn on the fan`
1. O estado visual do ventilador deverá mudar para "ligado"
    > [!div class="mx-imgBorder"]
    > ![Ligar ventilador](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exportar o aplicativo de comandos personalizados como uma habilidade remota](./how-to-custom-commands-integrate-remote-skills.md)

