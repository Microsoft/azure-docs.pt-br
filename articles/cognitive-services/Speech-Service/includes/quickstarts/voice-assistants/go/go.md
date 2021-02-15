---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 8f5a8aa2b949ddd07208cac054147596512f783f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99215434"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../overview.md#try-the-speech-service-for-free)
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md)
> * Criar um bot conectado ao [canal de Fala do Direct Line](/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Verificar se você tem acesso a um microfone para captura de áudio
>
  > [!NOTE]
  > Confira [a lista de regiões compatíveis com assistentes de voz](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) e implante seus recursos em uma dessas regiões.

## <a name="setup-your-environment"></a>Configurar seu ambiente

Atualize o arquivo go.mod com a versão mais recente do SDK adicionando esta linha
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.15.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Comece com código de texto clichê
Substitua o conteúdo do arquivo de origem (por exemplo, `quickstart.go`) pelo seguinte, que inclui:

- definição do pacote "principal"
- importar os módulos necessários do SDK de Fala
- variáveis para armazenar as informações de bot que serão substituídas posteriormente neste início rápido
- uma implementação simples usando o microfone para entrada de áudio
- manipuladores de eventos para vários eventos que ocorrem durante a interação de fala

```sh
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/dialog"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_BOT_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := dialog.NewBotFrameworkConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    connector, err := dialog.NewDialogServiceConnectorFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer connector.Close()
    activityReceivedHandler := func(event dialog.ActivityReceivedEventArgs) {
        defer event.Close()
        fmt.Println("Received an activity.")
    }
    connector.ActivityReceived(activityReceivedHandler)
    recognizedHandle := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognized ", event.Result.Text)
    }
    connector.Recognized(recognizedHandle)
    recognizingHandler := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognizing ", event.Result.Text)
    }
    connector.Recognizing(recognizingHandler)
    connector.ListenOnceAsync()
    <-time.After(10 * time.Second)
}
```

Substitua os valores `YOUR_SUBSCRIPTION_KEY` e `YOUR_BOT_REGION` pelos valores reais do recurso de Fala.

- Navegue até o portal do Azure e abra o recurso de Fala
- Em **Chaves e Ponto de Extremidade** à esquerda, há duas chaves de assinatura disponíveis
    - Use qualquer uma das duas como a substituição do valor `YOUR_SUBSCRIPTION_KEY`
- Na **Visão Geral** à esquerda, observe a região e mapeie-a para o identificador da região
    - Use o Identificador da região como a substituição do valor `YOUR_BOT_REGION`, por exemplo: `"westus"` para **Oeste dos EUA**

   > [!NOTE]
   > Confira [a lista de regiões compatíveis com assistentes de voz](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) e implante seus recursos em uma dessas regiões.

   > [!NOTE]
   > Para saber mais sobre como configurar, confira a documentação do Bot Framework para [o canal de Fala do Direct Line](/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="code-explanation"></a>Explicação de código
A região e a chave de assinatura da Fala são necessárias para criar um objeto de configuração de fala. O objeto de configuração é necessário para criar uma instância de um objeto de reconhecedor de fala.

A instância do reconhecedor expõe várias maneiras de reconhecer a fala. Neste exemplo, a fala é reconhecida continuamente. Essa funcionalidade informa ao serviço de Fala que você está enviando várias frases para reconhecimento e quando o programa é encerrado para interromper o reconhecimento de fala. Conforme os resultados são gerados, o código os grava no console.

## <a name="build-and-run"></a>Criar e executar
Agora está tudo pronto para você compilar o projeto e testar o assistente de voz personalizado usando o serviço de Fala.
1. Compile seu projeto, por exemplo, **"go build"**
2. Execute o módulo e fale uma frase ou uma sentença no microfone do dispositivo. Sua fala será transmitida para o canal de Fala do Direct Line e transcrita em texto, que aparecerá como a saída.


> [!NOTE]
> O SDK de Fala usará como padrão o reconhecimento do uso de en-us como o idioma; confira [Especificar o idioma de origem para conversão de fala em texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]