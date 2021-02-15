---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: a18ce7b7c28b99967668bc33c5a94cbb58bfbc34
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377136"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de Fala do Azure](../../../../overview.md#try-the-speech-service-for-free)
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md)
> * Verificar se você tem acesso a um microfone para captura de áudio

## <a name="setup-your-environment"></a>Configurar seu ambiente

Atualize o arquivo go.mod com a versão mais recente do SDK adicionando esta linha
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.13.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Comece com código de texto clichê
1. Substitua o conteúdo do arquivo de origem (por exemplo, `sr-quickstart.go`) pelo seguinte, que inclui:

- definição do pacote "principal"
- importar os módulos necessários do SDK de Fala
- variáveis para armazenar as informações de assinatura que serão substituídas posteriormente neste início rápido
- uma implementação simples usando o microfone para entrada de áudio
- manipuladores de eventos para vários eventos que ocorrem durante o reconhecimento de fala

```sh
package recognizer

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

Substitua os valores `YOUR_SUBSCRIPTION_KEY` e `YOUR_SUBSCRIPTIONKEY_REGION` pelos valores reais do recurso de Fala.

- Navegue até o portal do Azure e abra o recurso de Fala
- Nas **Chaves** à esquerda, há duas chaves de assinatura disponíveis
    - Use qualquer uma das duas como a substituição do valor `YOUR_SUBSCRIPTION_KEY`
- Na **Visão Geral** à esquerda, observe a região e mapeie-a para o identificador da região
- Use o Identificador da região como a substituição do valor `YOUR_SUBSCRIPTIONKEY_REGION`, por exemplo: `"westus"` para **Oeste dos EUA**

## <a name="code-explanation"></a>Explicação de código
A região e a chave de assinatura da Fala são necessárias para criar um objeto de configuração de fala. O objeto de configuração é necessário para criar uma instância de um objeto de reconhecedor de fala.

A instância do reconhecedor expõe várias maneiras de reconhecer a fala. Neste exemplo, a fala é reconhecida continuamente. Essa funcionalidade informa ao serviço de Fala que você está enviando várias frases para reconhecimento e quando o programa é encerrado para interromper o reconhecimento de fala. Conforme os resultados são gerados, o código os grava no console.

## <a name="build-and-run"></a>Criar e executar
Agora, você está pronto para compilar o projeto e testar o reconhecimento de fala usando o serviço de Fala.
1. Compile seu projeto, por exemplo, **"go build"**
2. Execute o módulo e fale uma frase ou uma sentença no microfone do dispositivo. Sua fala será transmitida para o serviço de Fala e transcrita para texto, que será exibida na saída.


> [!NOTE]
> O SDK de Fala usará como padrão o reconhecimento do uso de en-us como o idioma; confira [Especificar o idioma de origem para conversão de fala em texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.


## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
