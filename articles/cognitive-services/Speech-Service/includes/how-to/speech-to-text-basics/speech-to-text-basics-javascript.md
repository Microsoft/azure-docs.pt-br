---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2021
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 5a8065daca11e5b79f02510f82ab622c8fb1af2d
ms.sourcegitcommit: 91361cbe8fff7c866ddc4835251dcbbe2621c055
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105729781"
---
Um dos principais recursos do serviço de Fala é a capacidade de reconhecer e transcrever a fala humana (frequentemente denominada conversão de fala em texto). Neste guia de início rápido, você aprende a usar o SDK de Fala em seus aplicativos e produtos para executar uma conversão de fala em texto de alta qualidade.

## <a name="skip-to-samples-on-github"></a>Pular para os exemplos no GitHub

Se você quiser pular diretamente para o código de exemplo, confira os [exemplos do guia de início rápido do JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) no GitHub.

Como alternativa, confira o [exemplo do React](https://github.com/Azure-Samples/AzureSpeechReactSample) para saber como usar o SDK de Fala em um ambiente baseado em navegador.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Antes de fazer qualquer coisa, instale o SDK de Fala para Node.js. Se você quiser apenas o nome do pacote a ser instalado, execute `npm install microsoft-cognitiveservices-speech-sdk`. Para obter instruções de instalação guiada, confira o artigo de [introdução](../../../quickstarts/setup-platform.md?pivots=programming-language-javascript&tabs=dotnet%2clinux%2cjre%2cnodejs).

Use a instrução `require` a seguir para importar o SDK.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Para obter mais informações sobre o `require`, confira a [documentação necessária](https://nodejs.org/en/knowledge/getting-started/what-is-require/).

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Para chamar o serviço de Fala usando o SDK de Fala, você precisa criar um [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig). Essa classe inclui informações sobre sua assinatura, como sua chave e região, ponto de extremidade, host ou token de autorização associados. Crie um [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) usando sua chave e região. Confira a página [Localizar chaves e região](../../../overview.md#find-keys-and-region) para localizar o par chave-região.

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Há algumas outras maneiras de inicializar um [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig):

* Com um ponto de extremidade: passe um ponto de extremidade do serviço de Fala. Uma chave ou um token de autorização é opcional.
* Com um host: passe um endereço de host. Uma chave ou um token de autorização é opcional.
* Com um token de autorização: passe um token de autorização e a região associada.

> [!NOTE]
> Independentemente se você estiver executando o reconhecimento de fala, a síntese de fala, tradução ou reconhecimento de intenção, você sempre criará uma configuração.

## <a name="recognize-from-microphone-browser-only"></a>Reconhecimento por meio do microfone (somente navegador)

O reconhecimento de fala de um microfone **não é compatível com o Node.js**, esse recurso só é compatível com um ambiente JavaScript baseado em navegador. Confira o [exemplo do React](https://github.com/Azure-Samples/AzureSpeechReactSample) no GitHub para ver a [implementação de conversão de fala em texto por meio do microfone](https://github.com/Azure-Samples/AzureSpeechReactSample/blob/main/src/App.js#L29).

> [!NOTE]
> Se você quiser usar um dispositivo de entrada de áudio *específico*, será necessário especificar a ID do dispositivo no `AudioConfig`. Saiba [como obter a identificação do dispositivo](../../../how-to-select-audio-input-devices.md) de entrada de áudio.

## <a name="recognize-from-file"></a>Reconhecer do arquivo 

Crie um `AudioConfig` usando `fromWavFileInput()`, que aceitará um objeto `Buffer` para executar o reconhecimento de fala de um arquivo de áudio. Em seguida, inicialize um [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer), passando `audioConfig` e `speechConfig`.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    let audioConfig = sdk.AudioConfig.fromWavFileInput(fs.readFileSync("YourAudioFile.wav"));
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);

    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

## <a name="recognize-from-in-memory-stream"></a>Reconhecer do fluxo na memória

Para vários casos de uso, os dados de áudio talvez sejam provenientes do armazenamento de blobs. Caso contrário, eles já estarão na memória como um [`ArrayBuffer`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) ou uma estrutura de dados brutos semelhante. O seguinte código:

* Cria um fluxo de envio por push usando `createPushStream()`.
* Lê um arquivo `.wav` usando `fs.createReadStream` para fins de demonstração. No entanto, caso já tenha dados de áudio em um `ArrayBuffer`, será possível pular diretamente para a gravação do conteúdo no fluxo de entrada.
* Cria uma configuração de áudio usando o fluxo de envio por push.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromStream() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromStream();
```

Usar um fluxo por push como entrada pressupõe que os dados de áudio são um PCM bruto, por exemplo, ignorando todos os cabeçalhos.
A API ainda funcionará em determinados casos se o cabeçalho não foi ignorado, mas para obter os melhores resultados, considere implementar a lógica para ler os cabeçalhos de forma que o `fs` comece no *início dos dados de áudio*.

## <a name="error-handling"></a>Tratamento de erros

Os exemplos anteriores simplesmente obtêm o texto reconhecido de `result.text`, mas para lidar com erros e outras respostas, você precisará escrever algum código para lidar com o resultado. O código a seguir avalia a propriedade [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult#reason) e:

* Imprime o resultado de reconhecimento: `ResultReason.RecognizedSpeech`
* Se não houver correspondência com o reconhecimento, informe o usuário: `ResultReason.NoMatch`
* Se encontrar um erro, imprima a mensagem de erro: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>Reconhecimento contínuo

Os exemplos anteriores usam o reconhecimento de captura única, que reconhece uma única expressão. O fim de um único enunciado é determinado pela escuta de silêncio no fim ou até o máximo de 15 segundos de áudio processado.

Por outro lado, o reconhecimento contínuo é usado quando você deseja **controlar** quando deve parar o reconhecimento. É necessário assinar os eventos `Recognizing`, `Recognized` e `Canceled` para obter os resultados do reconhecimento. Para interromper o reconhecimento, chame [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#stopcontinuousrecognitionasync). Veja um exemplo de como o reconhecimento contínuo é realizado em um arquivo de entrada de áudio.

Inicie definindo a entrada e inicializando um [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer):

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
```

Em seguida, assine os eventos enviados do [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer).

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizing): sinal para os eventos que contêm resultados de reconhecimento intermediários.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognized): sinal para eventos que contêm resultados de reconhecimento finais (indicando uma tentativa de reconhecimento bem-sucedida).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#sessionstopped): sinal para eventos que indicam o fim de uma sessão de reconhecimento (operação).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#canceled): sinal para eventos que contêm resultados de reconhecimento cancelados (indicando uma tentativa de reconhecimento que foi cancelada em decorrência de uma solicitação de cancelamento direta ou uma falha de transporte ou protocolo).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Com tudo configurado, chame [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) para iniciar o reconhecimento.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Modo de Ditado

Ao usar o reconhecimento contínuo, você pode habilitar o processamento de ditado usando a função "habilitar ditado" correspondente. Esse modo fará a instância de configuração de fala interpretar as descrições das estruturas de frase, como pontuação. Por exemplo, o enunciado "Você mora na cidade ponto de interrogação" seria interpretado como o texto "Você mora na cidade?".

Para habilitar o modo de ditado, use o método [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#enabledictation--) no [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig).

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Alterar idioma de origem

Uma tarefa comum no reconhecimento de fala é especificar o idioma da entrada (ou origem). Veja como você alteraria o idioma de entrada para italiano. No código, localize [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) e adicione esta linha logo abaixo.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

A propriedade [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#speechrecognitionlanguage) espera uma cadeia de caracteres em formato idioma-localidade. Defina qualquer valor na coluna **Localidade** na lista de [localidades/idiomas](../../../language-support.md) com suporte.

## <a name="improve-recognition-accuracy"></a>Aprimorar a precisão do reconhecimento

As Listas de Frases são usadas para identificar frases conhecidas nos dados do áudio, como o nome de uma pessoa ou um local específico. Ao fornecer uma lista de frases, você melhora a precisão do reconhecimento de fala.

Por exemplo, se você tiver um comando "Mover para" e um possível destino de "Ala" que pode ser falado, você poderá adicionar uma entrada "Mover para a Ala". A adição de uma frase aumentará a probabilidade de quando o áudio for reconhecido, que ele será reconhecido como "Mover para a Ala" em vez de "Ir para"

Palavras ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, uma entrada em uma lista de frases é usada para aumentar o reconhecimento das palavras e frases na lista, mesmo quando as entradas aparecem no meio do enunciado. 

> [!IMPORTANT]
> O recurso Lista de Frases está disponível nos seguintes idiomas: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN

Para usar uma lista de frases, primeiro crie um objeto [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar). Em seguida, adicione palavras e frases específicas com [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar#addphrase-string-).

Todas as alterações em [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) entrarão em vigor no próximo reconhecimento ou após uma reconexão com o serviço de Fala.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Se precisar limpar a lista de frases:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Outras opções para aprimorar a precisão do reconhecimento

As listas de frases são só uma opção para aprimorar a precisão do reconhecimento. Também é possível: 

* [Aprimorar a precisão com a Fala Personalizada](../../../custom-speech-overview.md)
* [Aprimorar a precisão com modelos de locatário](../../../tutorial-tenant-model.md)