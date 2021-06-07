---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: c94070d74976172b5d10dcf52f63eee7139ff8f1
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104435"
---
Um dos principais recursos do serviço de Fala é a capacidade de reconhecer e transcrever a fala humana (frequentemente denominada conversão de fala em texto). Neste guia de início rápido, você aprende a usar o SDK de Fala em seus aplicativos e produtos para executar uma conversão de fala em texto de alta qualidade.

## <a name="skip-to-samples-on-github"></a>Pular para os exemplos no GitHub

Se você quiser pular diretamente para o código de exemplo, confira os [exemplos do guia de início rápido do Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Antes de fazer qualquer coisa, você precisará instalar o SDK de Fala. Dependendo de sua plataforma, use as seguintes instruções:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=jre" target="_blank">Java Runtime </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=android" target="_blank">Android </a>

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Para chamar o serviço de Fala usando o SDK de Fala, você precisa criar um [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig). Essa classe inclui informações sobre sua assinatura, como sua chave e região, ponto de extremidade, host ou token de autorização associados. Crie [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) usando sua chave e região. Confira a página [Localizar chaves e região](../../../overview.md#find-keys-and-region) para localizar o par chave-região.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

Há algumas outras maneiras de inicializar um [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig):

* Com um ponto de extremidade: passe um ponto de extremidade do serviço de Fala. Uma chave ou um token de autorização é opcional.
* Com um host: passe um endereço de host. Uma chave ou um token de autorização é opcional.
* Com um token de autorização: passe um token de autorização e a região associada.

> [!NOTE]
> Independentemente se você estiver executando o reconhecimento de fala, a síntese de fala, tradução ou reconhecimento de intenção, você sempre criará uma configuração.

## <a name="recognize-from-microphone"></a>Reconhecer do microfone

Para reconhecer fala usando o microfone do dispositivo, crie um `AudioConfig` usando `fromDefaultMicrophoneInput()`. Em seguida, inicialize um [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer), passando `audioConfig` e `config`.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromMic(speechConfig);
    }

    public static void fromMic(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        System.out.println("Speak into your microphone.");
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

Se você quiser usar um dispositivo de entrada de áudio *específico*, será necessário especificar a ID do dispositivo no `AudioConfig`. Saiba [como obter a identificação do dispositivo](../../../how-to-select-audio-input-devices.md) de entrada de áudio.

## <a name="recognize-from-file"></a>Reconhecer do arquivo

Se desejar reconhecer fala de um arquivo de áudio em vez de usar um microfone, você ainda precisará criar um `AudioConfig`. No entanto, ao criar o [`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig), em vez de chamar `fromDefaultMicrophoneInput()`, será preciso chamar `fromWavFileInput()` e passar o caminho do arquivo.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromFile(speechConfig);
    }

    public static void fromFile(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);
        
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

## <a name="error-handling"></a>Tratamento de erros

Os exemplos anteriores simplesmente obtêm o texto reconhecido de `result.getText()`, porém, para lidar com erros e outras respostas, você precisará escrever algum código a fim de lidar com o resultado. O seguinte exemplo avalia [`result.getReason()`](/java/api/com.microsoft.cognitiveservices.speech.recognitionresult.getreason) e:

* Imprime o resultado de reconhecimento: `ResultReason.RecognizedSpeech`
* Se não houver correspondência com o reconhecimento, informe o usuário: `ResultReason.NoMatch`
* Se encontrar um erro, imprima a mensagem de erro: `ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

## <a name="continuous-recognition"></a>Reconhecimento contínuo

Os exemplos anteriores usam o reconhecimento de captura única, que reconhece uma única expressão. O fim de um único enunciado é determinado pela escuta de silêncio no fim ou até o máximo de 15 segundos de áudio processado.

Por outro lado, o reconhecimento contínuo é usado quando você deseja **controlar** quando deve parar o reconhecimento. É necessário assinar os eventos `recognizing`, `recognized` e `canceled` para obter os resultados do reconhecimento. Para interromper o reconhecimento, chame [`stopContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync). Veja um exemplo de como o reconhecimento contínuo é realizado em um arquivo de entrada de áudio.

Comece definindo a entrada e inicializando um [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer):

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Em seguida, crie uma variável para gerenciar o estado do reconhecimento de fala. Para começar, declare um `Semaphore` no escopo da classe.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

Assine os eventos enviados do [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer).

* [`recognizing`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing): sinal para os eventos que contêm resultados de reconhecimento intermediários.
* [`recognized`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized): sinal para eventos que contêm resultados de reconhecimento finais (indicando uma tentativa de reconhecimento bem-sucedida).
* [`sessionStopped`](/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped): sinal para eventos que indicam o fim de uma sessão de reconhecimento (operação).
* [`canceled`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled): sinal para eventos que contêm resultados de reconhecimento cancelados (indicando uma tentativa de reconhecimento que foi cancelada em decorrência de uma solicitação de cancelamento direta ou uma falha de transporte ou protocolo).

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

Com tudo configurado, chame [`startContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.startcontinuousrecognitionasync).

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Modo de Ditado

Ao usar o reconhecimento contínuo, você pode habilitar o processamento de ditado usando a função "habilitar ditado" correspondente. Esse modo fará a instância de configuração de fala interpretar as descrições das estruturas de frase, como pontuação. Por exemplo, o enunciado "Você mora na cidade ponto de interrogação" seria interpretado como o texto "Você mora na cidade?".

Para habilitar o modo de ditado, use o método [`enableDictation`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation) no [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig).

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Alterar idioma de origem

Uma tarefa comum no reconhecimento de fala é especificar o idioma da entrada (ou origem). Veja como alterar o idioma de entrada para o francês. No código, localize [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) e adicione esta linha logo abaixo.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage) é um parâmetro que usa uma cadeia de caracteres como argumento. Você pode fornecer qualquer valor na lista de [localidades/idiomas](../../../language-support.md) compatíveis.

## <a name="improve-recognition-accuracy"></a>Aprimorar a precisão do reconhecimento

As Listas de Frases são usadas para identificar frases conhecidas nos dados do áudio, como o nome de uma pessoa ou um local específico. Ao fornecer uma lista de frases, você melhora a precisão do reconhecimento de fala.

Por exemplo, se você tiver um comando "Mover para" e um possível destino de "Ala" que pode ser falado, você poderá adicionar uma entrada "Mover para a Ala". A adição de uma frase aumentará a probabilidade de quando o áudio for reconhecido, que ele será reconhecido como "Mover para a Ala" em vez de "Ir para"

Palavras ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, uma entrada em uma lista de frases é usada para aumentar o reconhecimento das palavras e frases na lista, mesmo quando as entradas aparecem no meio do enunciado. 

> [!IMPORTANT]
> O recurso Lista de Frases está disponível nos seguintes idiomas: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN

Para usar uma lista de frases, primeiro crie um objeto [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar). Em seguida, adicione palavras e frases específicas com [`AddPhrase`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_).

Todas as alterações em [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar) entrarão em vigor no próximo reconhecimento ou após uma reconexão com o serviço de Fala.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Se precisar limpar a lista de frases: 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Outras opções para aprimorar a precisão do reconhecimento

As listas de frases são só uma opção para aprimorar a precisão do reconhecimento. Também é possível: 

* [Aprimorar a precisão com a Fala Personalizada](../../../custom-speech-overview.md)
* [Aprimorar a precisão com modelos de locatário](../../../tutorial-tenant-model.md)