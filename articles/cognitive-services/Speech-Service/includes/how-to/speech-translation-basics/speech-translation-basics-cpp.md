---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: eaf8d8f741c120297e6ae57e8ddb8b62f7c7f534
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105638"
---
Um dos principais recursos do serviço de fala é a capacidade de reconhecer a fala humana e traduzi-la para outras linguagens. Neste início rápido, você aprende a usar o SDK de fala em seus aplicativos e produtos para executar a tradução de fala de alta qualidade. Este guia de início rápido aborda os tópicos, incluindo:

* Convertendo a fala em texto
* Convertendo a fala em vários idiomas de destino
* Executando a conversão direta de fala em fala

## <a name="skip-to-samples-on-github"></a>Pular para os exemplos no GitHub

Se você quiser pular diretamente para o código de exemplo, confira os [exemplos do guia de início rápido do C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/translate-speech-to-text) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Antes de fazer qualquer coisa, você precisará instalar o SDK de Fala. Dependendo de sua plataforma, siga as instruções na seção <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">obter o SDK de fala</a> do artigo _sobre o SDK de fala_ .

## <a name="import-dependencies"></a>Importar dependências

Para executar os exemplos neste artigo, inclua as `#include` instruções e a seguir `using` na parte superior do arquivo de código C++.

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Variáveis de ambiente e dados confidenciais

O código-fonte de exemplo neste artigo depende de variáveis de ambiente para armazenar dados confidenciais, como a chave de assinatura do recurso de fala e a região. O arquivo de código C++ contém dois valores de cadeia de caracteres que são atribuídos das variáveis de ambiente de máquinas host, ou seja, `SPEECH__SUBSCRIPTION__KEY` e `SPEECH__SERVICE__REGION` . Esses dois campos estão no escopo de classe, tornando-os acessíveis dentro dos corpos de método da classe. Para obter mais informações sobre variáveis de ambiente, consulte [variáveis de ambiente e configuração de aplicativo](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
```

## <a name="create-a-speech-translation-configuration"></a>Criar uma configuração de tradução de fala

Para chamar o serviço de Fala usando o SDK de Fala, você precisa criar um [`SpeechTranslationConfig`][config]. Essa classe inclui informações sobre sua assinatura, como sua chave e região, ponto de extremidade, host ou token de autorização associados.

> [!TIP]
> Independentemente se você estiver executando o reconhecimento de fala, a síntese de fala, tradução ou reconhecimento de intenção, você sempre criará uma configuração.

Há algumas maneiras de inicializar um [`SpeechTranslationConfig`][config]:

* Com uma assinatura: passe uma chave e a região associada.
* Com um ponto de extremidade: passe um ponto de extremidade do serviço de Fala. Uma chave ou um token de autorização é opcional.
* Com um host: passe um endereço de host. Uma chave ou um token de autorização é opcional.
* Com um token de autorização: passe um token de autorização e a região associada.

Veja como criar um [`SpeechTranslationConfig`][config] com a chave e a região. Obtenha essas credenciais seguindo as etapas em [Experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>Alterar idioma de origem

Uma tarefa comum de tradução de fala é especificar o idioma de entrada (ou origem). Veja como você alteraria o idioma de entrada para italiano. Em seu código, interaja com a [`SpeechTranslationConfig`][config] instância, chamando o `SetSpeechRecognitionLanguage` método.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

A propriedade [`SpeechRecognitionLanguage`][recognitionlang] espera uma cadeia de caracteres em formato idioma-localidade. Defina qualquer valor na coluna **Localidade** na lista de [localidades/idiomas](../../../language-support.md) com suporte.

## <a name="add-translation-language"></a>Adicionar idioma de tradução

Outra tarefa comum de tradução de fala é especificar os idiomas de tradução de destino, pelo menos um é necessário, mas há suporte para múltiplos. O trecho de código a seguir define o francês e o alemão como destinos da linguagem de tradução.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

Com cada chamada para [`AddTargetLanguage`][addlang] , um novo idioma de tradução de destino é especificado. Em outras palavras, quando a fala é reconhecida a partir do idioma de origem, cada tradução de destino está disponível como parte da operação de tradução resultante.

## <a name="initialize-a-translation-recognizer"></a>Inicializar um reconhecedor de tradução

Depois de criar um [`SpeechTranslationConfig`][config], a próxima etapa é inicializar um [`TranslationRecognizer`][recognizer]. Ao inicializar um [`TranslationRecognizer`][recognizer], você precisará passar `translationConfig` para ele. O objeto de configuração fornece as credenciais que o serviço de fala requer para validar sua solicitação.

Se você estiver reconhecendo fala por meio do microfone padrão do dispositivo, veja qual deve ser a aparência de [`TranslationRecognizer`][recognizer]:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

Se você quiser especificar o dispositivo de entrada de áudio, precisará criar um [`AudioConfig`][audioconfig] e fornecer o parâmetro `audioConfig` ao inicializar o [`TranslationRecognizer`][recognizer].

> [!TIP]
> [Saiba como obter a identificação do dispositivo de entrada de áudio](../../../how-to-select-audio-input-devices.md).

Primeiro, você fará referência ao `AudioConfig` objeto da seguinte maneira:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

Se desejar fornecer um arquivo de áudio em vez de usar um microfone, você ainda precisará fornecer um `audioConfig`. No entanto, ao criar um [`AudioConfig`][audioconfig], em vez de chamar `FromDefaultMicrophoneInput`, você chamará `FromWavFileInput` e passará o parâmetro `filename`.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Traduzir fala

Para traduzir a fala, o SDK de fala depende de um microfone ou de uma entrada de arquivo de áudio. O reconhecimento de fala ocorre antes da tradução de fala. Depois que todos os objetos tiverem sido inicializados, chame a função Recognize-Once e obtenha o resultado.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

Para obter mais informações sobre conversão de fala em texto, consulte [noções básicas do reconhecimento de fala](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Sintetizar traduções

Após um reconhecimento de fala e uma tradução bem-sucedidos, o resultado contém todas as traduções em um dicionário. A [`Translations`][translations] chave de dicionário é o idioma de tradução de destino e o valor é o texto traduzido. A fala reconhecida pode ser traduzida e, em seguida, sintetizada em um idioma diferente (fala a fala).

### <a name="event-based-synthesis"></a>Síntese baseada em evento

O `TranslationRecognizer` objeto expõe um `Synthesizing` evento. O evento é acionado várias vezes e fornece um mecanismo para recuperar o áudio sintetizado do resultado do reconhecimento de tradução. Se você estiver traduzindo para vários idiomas, consulte [síntese manual](#manual-synthesis). Especifique a voz de síntese atribuindo um [`SetVoiceName`][voicename] e fornecendo um manipulador de eventos para o `Synthesizing` evento, obtenha o áudio. O exemplo a seguir salva o áudio traduzido como um arquivo *. wav* .

> [!IMPORTANT]
> A síntese baseada em evento funciona apenas com uma única tradução, não adicione vários **idiomas de tradução** de destino. Além disso, o [`SetVoiceName`][voicename] deve ser o mesmo idioma que o idioma de tradução de destino, por exemplo; `"de"` pode mapear para `"de-DE-Hedda"` .

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>Síntese manual

O [`Translations`][translations] dicionário pode ser usado para sintetizar o áudio do texto de tradução. Iterar em cada tradução e sintetizar a tradução. Ao criar uma `SpeechSynthesizer` instância, o `SpeechConfig` objeto precisa ter sua [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] propriedade definida como a voz desejada. O exemplo a seguir é convertido em cinco idiomas, e cada tradução é sintetizada em um arquivo de áudio no idioma neural correspondente.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

Para obter mais informações sobre a síntese de fala, consulte [noções básicas sobre a síntese de fala](../../../get-started-text-to-speech.md).

[config]: /cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: /cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: /cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: /cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: /cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: /cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: /cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: /cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename