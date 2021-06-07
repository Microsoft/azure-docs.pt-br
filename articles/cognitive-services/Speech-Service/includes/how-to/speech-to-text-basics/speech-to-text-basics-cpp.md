---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: trbye
ms.openlocfilehash: 3d8558fbee05f663d72be64cb45ed4a2481513a3
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104867"
---
Um dos principais recursos do serviço de Fala é a capacidade de reconhecer e transcrever a fala humana (frequentemente denominada conversão de fala em texto). Neste guia de início rápido, você aprende a usar o SDK de Fala em seus aplicativos e produtos para executar uma conversão de fala em texto de alta qualidade.

## <a name="skip-to-samples-on-github"></a>Pular para os exemplos no GitHub

Se você quiser pular diretamente para o código de exemplo, confira os [exemplos do guia de início rápido do C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Antes de fazer qualquer coisa, você precisará instalar o SDK de Fala. Dependendo de sua plataforma, use as seguintes instruções:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Para chamar o serviço de Fala usando o SDK de Fala, você precisa criar um [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig). Essa classe inclui informações sobre sua assinatura, como sua chave e região, ponto de extremidade, host ou token de autorização associados. Crie [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) usando sua chave e região. Confira a página [Localizar chaves e região](../../../overview.md#find-keys-and-region) para localizar o par chave-região.

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;

auto config = SpeechConfig::FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Há algumas outras maneiras de inicializar um [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig):

* Com um ponto de extremidade: passe um ponto de extremidade do serviço de Fala. Uma chave ou um token de autorização é opcional.
* Com um host: passe um endereço de host. Uma chave ou um token de autorização é opcional.
* Com um token de autorização: passe um token de autorização e a região associada.

> [!NOTE]
> Independentemente se você estiver executando o reconhecimento de fala, a síntese de fala, tradução ou reconhecimento de intenção, você sempre criará uma configuração.

## <a name="recognize-from-microphone"></a>Reconhecer do microfone

Para reconhecer fala usando o microfone do dispositivo, crie um `AudioConfig` usando `FromDefaultMicrophoneInput()`. Em seguida, inicialize um [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer), passando `audioConfig` e `config`.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);

cout << "Speak into your microphone." << std::endl;
auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

Se você quiser usar um dispositivo de entrada de áudio *específico*, será necessário especificar a ID do dispositivo no `AudioConfig`. Saiba [como obter a identificação do dispositivo](../../../how-to-select-audio-input-devices.md) de entrada de áudio.

## <a name="recognize-from-file"></a>Reconhecer do arquivo

Se desejar reconhecer fala de um arquivo de áudio em vez de usar um microfone, você ainda precisará criar um `AudioConfig`. No entanto, ao criar o [`AudioConfig`](/cpp/cognitive-services/speech/audio-audioconfig), em vez de chamar `FromDefaultMicrophoneInput()`, você chamará `FromWavFileInput()` e passará o caminho do arquivo.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);

auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

## <a name="recognize-speech"></a>Reconhecer fala

A [classe Reconhecedor](/cpp/cognitive-services/speech/speechrecognizer) do SDK de Fala do C++ expõe alguns métodos que você pode usar para o reconhecimento de fala.

### <a name="single-shot-recognition"></a>Reconhecimento pontual

O reconhecimento pontual reconhece de forma assíncrona um único enunciado. O fim de um único enunciado é determinado pela escuta de silêncio no fim ou até o máximo de 15 segundos de áudio processado. Veja um exemplo de reconhecimento pontual assíncrono com [`RecognizeOnceAsync`](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync):

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Será necessário escrever código para lidar com o resultado. Este exemplo avalia o [`result->Reason`](/cpp/cognitive-services/speech/recognitionresult#reason):

* Imprime o resultado de reconhecimento: `ResultReason::RecognizedSpeech`
* Se não houver correspondência com o reconhecimento, informe o usuário: `ResultReason::NoMatch`
* Se encontrar um erro, imprima a mensagem de erro: `ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>Reconhecimento contínuo

O reconhecimento contínuo é um pouco mais complexo que o pontual. É necessário assinar os eventos `Recognizing`, `Recognized` e `Canceled` para obter os resultados do reconhecimento. Para interromper o reconhecimento, você deve chamar [StopContinuousRecognitionAsync](/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Veja um exemplo de como o reconhecimento contínuo é realizado em um arquivo de entrada de áudio.

Comece definindo a entrada e inicializando um [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer):

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Em seguida, crie uma variável para gerenciar o estado do reconhecimento de fala. Para começar, declare um `promise<void>`, porque, no início do reconhecimento, podemos pressupor com segurança que ela ainda não está concluída.

```cpp
promise<void> recognitionEnd;
```

Assine os eventos enviados do [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer).

* [`Recognizing`](/cpp/cognitive-services/speech/asyncrecognizer#recognizing): sinal para os eventos que contêm resultados de reconhecimento intermediários.
* [`Recognized`](/cpp/cognitive-services/speech/asyncrecognizer#recognized): sinal para eventos que contêm resultados de reconhecimento finais (indicando uma tentativa de reconhecimento bem-sucedida).
* [`SessionStopped`](/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): sinal para eventos que indicam o fim de uma sessão de reconhecimento (operação).
* [`Canceled`](/cpp/cognitive-services/speech/asyncrecognizer#canceled): sinal para eventos que contêm resultados de reconhecimento cancelados (indicando uma tentativa de reconhecimento que foi cancelada em decorrência de uma solicitação de cancelamento direta ou uma falha de transporte ou protocolo).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

Com tudo configurado, chame [`StopContinuousRecognitionAsync`](/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync).

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Modo de Ditado

Ao usar o reconhecimento contínuo, você pode habilitar o processamento de ditado usando a função "habilitar ditado" correspondente. Esse modo fará a instância de configuração de fala interpretar as descrições das estruturas de frase, como pontuação. Por exemplo, o enunciado "Você mora na cidade ponto de interrogação" seria interpretado como o texto "Você mora na cidade?".

Para habilitar o modo de ditado, use o método [`EnableDictation`](/cpp/cognitive-services/speech/speechconfig#enabledictation) no [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig).

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Alterar idioma de origem

Uma tarefa comum no reconhecimento de fala é especificar o idioma da entrada (ou origem). Veja como você alteraria o idioma de entrada para alemão. No código, localize [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) e adicione esta linha logo abaixo.

```cpp
config->SetSpeechRecognitionLanguage("de-DE");
```

[`SetSpeechRecognitionLanguage`](/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) é um parâmetro que usa uma cadeia de caracteres como argumento. Você pode fornecer qualquer valor na lista de [localidades/idiomas](../../../language-support.md) compatíveis.

## <a name="improve-recognition-accuracy"></a>Aprimorar a precisão do reconhecimento

As Listas de Frases são usadas para identificar frases conhecidas nos dados do áudio, como o nome de uma pessoa ou um local específico. Ao fornecer uma lista de frases, você melhora a precisão do reconhecimento de fala.

Por exemplo, se você tiver um comando "Mover para" e um possível destino de "Ala" que pode ser falado, você poderá adicionar uma entrada "Mover para a Ala". A adição de uma frase aumentará a probabilidade de quando o áudio for reconhecido, que ele será reconhecido como "Mover para a Ala" em vez de "Ir para"

Palavras ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, uma entrada em uma lista de frases é usada para aumentar o reconhecimento das palavras e frases na lista, mesmo quando as entradas aparecem no meio do enunciado. 

> [!IMPORTANT]
> O recurso Lista de Frases está disponível nos seguintes idiomas: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN

Para usar uma lista de frases, primeiro crie um objeto [`PhraseListGrammar`](/cpp/cognitive-services/speech/phraselistgrammar). Em seguida, adicione palavras e frases específicas com [`AddPhrase`](/cpp/cognitive-services/speech/phraselistgrammar#addphrase).

Todas as alterações em [`PhraseListGrammar`](/cpp/cognitive-services/speech/phraselistgrammar) entrarão em vigor no próximo reconhecimento ou após uma reconexão com o serviço de Fala.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Se precisar limpar a lista de frases: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Outras opções para aprimorar a precisão do reconhecimento

As listas de frases são só uma opção para aprimorar a precisão do reconhecimento. Também é possível: 

* [Aprimorar a precisão com a Fala Personalizada](../../../custom-speech-overview.md)
* [Aprimorar a precisão com modelos de locatário](../../../tutorial-tenant-model.md)