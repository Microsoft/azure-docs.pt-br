---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 48a4c23fac5dd6b5d18de7f62bd38c4a78a5ce18
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104721"
---
Neste início rápido, você aprenderá os padrões de design básicos para Reconhecimento do Locutor usando o SDK de Fala, incluindo:

* Verificação dependente do texto e independente do texto
* Identificação do locutor para identificar uma amostra de voz em meio a um grupo de vozes
* Exclusão de perfis de voz

Para obter uma visão de alto nível sobre os conceitos do Reconhecimento de Fala, confira o artigo de [visão geral](../../../speaker-recognition-overview.md).

## <a name="skip-to-samples-on-github"></a>Pular para os exemplos no GitHub

Se você quiser pular diretamente para o código de exemplo, confira os [exemplos do guia de início rápido do C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Atualmente, o Reconhecimento do Locutor é compatível *somente* com os recursos de Fala do Azure criados na região `westus`.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Antes de fazer qualquer coisa, você precisará instalar o SDK de Fala. Dependendo de sua plataforma, use as seguintes instruções:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="import-dependencies"></a>Importar dependências

Para executar os exemplos neste artigo, adicione as instruções a seguir na parte superior do seu arquivo .cpp.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Para chamar o serviço de Fala usando o SDK de Fala, você precisa criar um [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig). Essa classe inclui informações sobre sua assinatura, como sua chave e região, ponto de extremidade, host ou token de autorização associados.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>Verificação dependente do texto

A Verificação do Locutor é o ato de confirmar que um locutor corresponde a uma voz conhecida ou **registrada**. A primeira etapa é **registrar** um perfil de voz, para que o serviço tenha algo com que comparar as futuras amostras de voz. Neste exemplo, você registra o perfil usando uma estratégia **dependente do texto**, que requer uma frase secreta específica a ser usada para o registro e a verificação. Confira os [documentos de referência](/rest/api/speakerrecognition/) para obter uma lista de frases secretas compatíveis.

### <a name="textdependentverification-function"></a>Função TextDependentVerification

Comece criando a função `TextDependentVerification`.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

Essa função cria um objeto [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) com o método [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync). Observe que há três [tipos](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype) de `VoiceProfile`:

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

Nesse caso, passe `VoiceProfileType::TextDependentVerification` para `CreateProfileAsync`.

Em seguida, chame duas funções auxiliares que você definirá a seguir, `AddEnrollmentsToTextDependentProfile` e `SpeakerVerify`. Por fim, chame [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) para limpar o perfil.

### <a name="addenrollmentstotextdependentprofile-function"></a>Função AddEnrollmentsToTextDependentProfile

Defina a função a seguir para registrar um perfil de voz.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

Nessa função, você registrará as amostras de áudio em um loop `while` que acompanha o número de amostras restantes e necessário para o registro. Em cada iteração, [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) solicitará que você fale a frase secreta no microfone e adicione a amostra ao perfil de voz.

### <a name="speakerverify-function"></a>Função SpeakerVerify

Defina `SpeakerVerify` da maneira a seguir.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

Nesta função, você criará um objeto [SpeakerVerificationModel](/cpp/cognitive-services/speech/speakerverificationmodel) com o método [SpeakerVerificationModel::FromProfile](/cpp/cognitive-services/speech/speakerverificationmodel#fromprofile), passando o objeto [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) criado anteriormente.

Em seguida, [SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) solicita que você fale a frase secreta novamente, mas desta vez ela a validará em relação ao seu perfil de voz e retornará uma pontuação de similaridade que varia de 0,0 a 1,0. O objeto [SpeakerRecognitionResult](/cpp/cognitive-services/speech/speakerrecognitionresult) também retornará `Accept` ou `Reject`, dependendo das correspondências ou não da frase secreta.

## <a name="text-independent-verification"></a>Verificação independente do texto

Em contraste com a verificação **dependente do texto**, a verificação **independente do texto**:

* Não exige a fala de uma frase secreta específica, mas pode-se falar qualquer coisa
* Não exige três amostras de áudio, mas, *sim*, 20 segundos de áudio total

### <a name="textindependentverification-function"></a>Função TextIndependentVerification

Comece criando a função `TextIndependentVerification`.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

Como a função `TextDependentVerification`, essa função cria um objeto [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) com o método [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync).

Nesse caso, passe `VoiceProfileType::TextIndependentVerification` para `CreateProfileAsync`.

Em seguida, chame duas funções auxiliares: `AddEnrollmentsToTextIndependentProfile`, que você definirá a seguir, e `SpeakerVerify`, que você já definiu. Por fim, chame [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) para limpar o perfil.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Defina a função a seguir para registrar um perfil de voz.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

Nessa função, você registrará as amostras de áudio em um loop `while` que acompanha o número de segundos de áudio restantes e necessários para o registro. Em cada iteração, [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) solicitará que você fale no microfone e adicione a amostra ao perfil de voz.

## <a name="speaker-identification"></a>Identificação do locutor

A Identificação do Locutor é usada para determinar **quem** está falando dentre um determinado grupo de vozes registradas. O processo é muito semelhante à **verificação independente do texto**, sendo que a principal diferença é a capacidade de verificação em relação a vários perfis de voz de uma só vez, em vez de verificar apenas um perfil de cada vez.

### <a name="textindependentidentification-function"></a>Função TextIndependentIdentification

Comece criando a função `TextIndependentIdentification`.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

Como as funções `TextDependentVerification` e `TextIndependentVerification`, essa função cria um objeto [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) com o método [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync).

Nesse caso, passe `VoiceProfileType::TextIndependentIdentification` para `CreateProfileAsync`.

Em seguida, chame duas funções auxiliares: `AddEnrollmentsToTextIndependentProfile`, que você já definiu, e `SpeakerIdentify`, que você definirá em seguida. Por fim, chame [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) para limpar o perfil.

### <a name="speakeridentify-function"></a>Função SpeakerIdentify

Defina a função `SpeakerIdentify` conforme descrito a seguir.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

Nessa função, você criará um objeto [SpeakerIdentificationModel](/cpp/cognitive-services/speech/speakeridentificationmodel) com o método [SpeakerIdentificationModel::FromProfiles](/cpp/cognitive-services/speech/speakeridentificationmodel#fromprofiles). `SpeakerIdentificationModel::FromProfiles` aceita uma lista de objetos [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile). Nesse caso, você apenas passará o objeto `VoiceProfile` criado anteriormente. No entanto, se desejar, você poderá passar vários objetos `VoiceProfile`, cada um registrado com amostras de áudio de uma voz diferente.

Em seguida, [SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) solicitará que você fale novamente. Desta vez, ele vai comparar sua voz com os perfis de voz registrados e retornar o perfil de voz mais semelhante.

## <a name="main-function"></a>Função principal

Por fim, defina a função `main` conforme descrito a seguir.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

Essa função simplesmente chama as funções que você definiu anteriormente. No entanto, ela primeiro cria um objeto [VoiceProfileClient](/cpp/cognitive-services/speech/voiceprofileclient) e um objeto [SpeakerRecognizer](/cpp/cognitive-services/speech/speakerrecognizer).

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

O `VoiceProfileClient` é usado para criar, registrar e excluir perfis de voz. O `SpeakerRecognizer` é usado para validar exemplos de fala em um ou mais perfis de voz registrados.

## <a name="changing-audio-input-type"></a>Alterando o tipo de entrada de áudio

Os exemplos deste artigo usam o microfone de dispositivo padrão como o meio de entrada das amostras de áudio. No entanto, em cenários em que você precisa usar arquivos de áudio de entrada por microfone, basta alterar a seguinte linha:

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

para:

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

Ou substitua qualquer uso de `audio_config` por [audio::AudioConfig::FromWavFileInput](/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput). Também é possível ter entradas mistas, usando o microfone para registro e arquivos para verificação, por exemplo.