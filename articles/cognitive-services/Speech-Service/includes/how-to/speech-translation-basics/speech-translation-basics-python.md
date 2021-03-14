---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 679104a1941e677dd78190fb80964a5d39813e61
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445733"
---
Um dos principais recursos do serviço de fala é a capacidade de reconhecer a fala humana e traduzi-la para outras linguagens. Neste início rápido, você aprende a usar o SDK de fala em seus aplicativos e produtos para executar a tradução de fala de alta qualidade. Este guia de início rápido aborda os tópicos, incluindo:

* Convertendo a fala em texto
* Convertendo a fala em vários idiomas de destino
* Executando a conversão direta de fala em fala

## <a name="skip-to-samples-on-github"></a>Pular para os exemplos no GitHub

Se você quiser pular diretamente para o código de exemplo, confira os [exemplos do guia de início rápido do Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Antes de fazer qualquer coisa, você precisará instalar o SDK de Fala. Dependendo de sua plataforma, siga as instruções na seção <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">obter o SDK de fala</a> do artigo _sobre o SDK de fala_ .

## <a name="import-dependencies"></a>Importar dependências

Para executar os exemplos neste artigo, inclua as instruções a seguir `import` na parte superior do arquivo de código do Python.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Variáveis de ambiente e dados confidenciais

O código-fonte de exemplo neste artigo depende de variáveis de ambiente para armazenar dados confidenciais, como a chave de assinatura do recurso de fala e a região. O arquivo de código Python contém dois valores que são atribuídos das variáveis de ambiente de máquinas de host, ou seja, `SPEECH__SUBSCRIPTION__KEY` e `SPEECH__SERVICE__REGION` . Ambas as variáveis estão no escopo global, tornando-as acessíveis na definição de função do arquivo de código. Para obter mais informações sobre variáveis de ambiente, consulte [variáveis de ambiente e configuração de aplicativo](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
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

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Alterar idioma de origem

Uma tarefa comum de tradução de fala é especificar o idioma de entrada (ou origem). Veja como você alteraria o idioma de entrada para italiano. Em seu código, interaja com a [`SpeechTranslationConfig`][config] instância, atribuindo à `speech_recognition_language` propriedade.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

A propriedade [`speech_recognition_language`][recognitionlang] espera uma cadeia de caracteres em formato idioma-localidade. Defina qualquer valor na coluna **Localidade** na lista de [localidades/idiomas](../../../language-support.md) com suporte.

## <a name="add-translation-language"></a>Adicionar idioma de tradução

Outra tarefa comum de tradução de fala é especificar os idiomas de tradução de destino, pelo menos um é necessário, mas há suporte para múltiplos. O trecho de código a seguir define o francês e o alemão como destinos da linguagem de tradução.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Com cada chamada para [`add_target_language`][addlang] , um novo idioma de tradução de destino é especificado. Em outras palavras, quando a fala é reconhecida a partir do idioma de origem, cada tradução de destino está disponível como parte da operação de tradução resultante.

## <a name="initialize-a-translation-recognizer"></a>Inicializar um reconhecedor de tradução

Depois de criar um [`SpeechTranslationConfig`][config], a próxima etapa é inicializar um [`TranslationRecognizer`][recognizer]. Ao inicializar um [`TranslationRecognizer`][recognizer], você precisará passar `translation_config` para ele. O objeto de configuração fornece as credenciais que o serviço de fala requer para validar sua solicitação.

Se você estiver reconhecendo fala por meio do microfone padrão do dispositivo, veja qual deve ser a aparência de [`TranslationRecognizer`][recognizer]:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Se você quiser especificar o dispositivo de entrada de áudio, precisará criar um [`AudioConfig`][audioconfig] e fornecer o parâmetro `audio_config` ao inicializar o [`TranslationRecognizer`][recognizer].

> [!TIP]
> [Saiba como obter a identificação do dispositivo de entrada de áudio](../../../how-to-select-audio-input-devices.md).

Primeiro, você fará referência ao `AudioConfig` objeto da seguinte maneira:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

Se desejar fornecer um arquivo de áudio em vez de usar um microfone, você ainda precisará fornecer um `audioConfig`. No entanto, quando você cria um [`AudioConfig`][audioconfig] , em vez de chamar com `use_default_microphone=True` , você chamará `filename="path-to-file.wav"` e fornecerá o `filename` parâmetro.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>Traduzir fala

Para traduzir a fala, o SDK de fala depende de um microfone ou de uma entrada de arquivo de áudio. O reconhecimento de fala ocorre antes da tradução de fala. Depois que todos os objetos tiverem sido inicializados, chame a função Recognize-Once e obtenha o resultado.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

Para obter mais informações sobre conversão de fala em texto, consulte [noções básicas do reconhecimento de fala](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Sintetizar traduções

Após um reconhecimento de fala e uma tradução bem-sucedidos, o resultado contém todas as traduções em um dicionário. A [`translations`][translations] chave de dicionário é o idioma de tradução de destino e o valor é o texto traduzido. A fala reconhecida pode ser traduzida e, em seguida, sintetizada em um idioma diferente (fala a fala).

### <a name="event-based-synthesis"></a>Síntese baseada em evento

O `TranslationRecognizer` objeto expõe um `Synthesizing` evento. O evento é acionado várias vezes e fornece um mecanismo para recuperar o áudio sintetizado do resultado do reconhecimento de tradução. Se você estiver traduzindo para vários idiomas, consulte [síntese manual](#manual-synthesis). Especifique a voz de síntese atribuindo um [`voice_name`][voicename] e fornecendo um manipulador de eventos para o `Synthesizing` evento, obtenha o áudio. O exemplo a seguir salva o áudio traduzido como um arquivo *. wav* .

> [!IMPORTANT]
> A síntese baseada em evento funciona apenas com uma única tradução, não adicione vários **idiomas de tradução** de destino. Além disso, o [`voice_name`][voicename] deve ser o mesmo idioma que o idioma de tradução de destino, por exemplo; `"de"` pode mapear para `"de-DE-Hedda"` .

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>Síntese manual

O [`translations`][translations] dicionário pode ser usado para sintetizar o áudio do texto de tradução. Iterar em cada tradução e sintetizar a tradução. Ao criar uma `SpeechSynthesizer` instância, o `SpeechConfig` objeto precisa ter sua [`speech_synthesis_voice_name`][speechsynthesisvoicename] propriedade definida como a voz desejada. O exemplo a seguir é convertido em cinco idiomas, e cada tradução é sintetizada em um arquivo de áudio no idioma neural correspondente.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

Para obter mais informações sobre a síntese de fala, consulte [noções básicas sobre a síntese de fala](../../../get-started-text-to-speech.md).

[config]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig
[audioconfig]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig
[recognizer]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig
[addlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#add-target-language-language--str-
[translations]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult#translations
[voicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#voice-name
[speechsynthesisvoicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#speech-synthesis-voice-name