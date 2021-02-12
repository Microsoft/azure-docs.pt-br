---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-js
ms.openlocfilehash: 25a3613f2aa45d31f47a8a4f288abbb1e9ee78fa
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948059"
---
Um dos principais recursos do serviço de fala é a capacidade de reconhecer a fala humana e traduzi-la para outras linguagens. Neste início rápido, você aprende a usar o SDK de fala em seus aplicativos e produtos para executar a tradução de fala de alta qualidade. Este guia de início rápido aborda os tópicos, incluindo:

* Convertendo a fala em texto
* Convertendo a fala em vários idiomas de destino
* Executando a conversão direta de fala em fala

## <a name="skip-to-samples-on-github"></a>Pular para os exemplos no GitHub

Se você quiser pular diretamente para o código de exemplo, confira os [exemplos do guia de início rápido do JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Para fazer qualquer coisa, instale o <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">SDK de Fala para JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a>. Dependendo de sua plataforma, use as seguintes instruções:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Navegador da Web <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Além disso, dependendo do ambiente de destino, use um dos seguintes:

# <a name="script"></a>[script](#tab/script)

Baixe e extraia o arquivo *microsoft.cognitiveservices.speech.sdk.bundle.js* do <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">SDK de Fala para JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a> e coloque-o em uma pasta acessível para o arquivo HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Se estiver direcionando a um navegador da Web e usando a tag `<script>`, o prefixo `sdk` não será necessário. O prefixo `sdk` é um alias usado para dar nome ao módulo `require`.

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Para obter mais informações sobre `import`, confira <a href="https://javascript.info/import-export" target="_blank">exportar e importar <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Para obter mais informações sobre `require`, confira <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">o que é necessário?<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="create-a-translation-configuration"></a>Criar uma configuração de tradução

Para chamar o serviço de tradução usando o SDK de fala, você precisa criar um [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) . Essa classe inclui informações sobre sua assinatura, como sua chave e região, ponto de extremidade, host ou token de autorização associados.

> [!NOTE]
> Independentemente se você estiver executando o reconhecimento de fala, a síntese de fala, tradução ou reconhecimento de intenção, você sempre criará uma configuração.
Há algumas maneiras de inicializar um [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig):

* Com uma assinatura: passe uma chave e a região associada.
* Com um ponto de extremidade: passe um ponto de extremidade do serviço de Fala. Uma chave ou um token de autorização é opcional.
* Com um host: passe um endereço de host. Uma chave ou um token de autorização é opcional.
* Com um token de autorização: passe um token de autorização e a região associada.

Veja como criar um [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) com a chave e a região. Obtenha essas credenciais seguindo as etapas em [Experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>Inicializar um tradutor

Depois de criar um [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig), a próxima etapa é inicializar um [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer). Ao inicializar um [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer), você precisará passar `speechTranslationConfig` para ele. Isso fornece as credenciais que o serviço de tradução requer para validar sua solicitação.

Se você estiver traduzindo a fala fornecida por meio do microfone padrão do seu dispositivo, aqui está o que [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) deve ser o seguinte:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Se você quiser especificar o dispositivo de entrada de áudio, precisará criar um [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) e fornecer o parâmetro `audioConfig` ao inicializar o [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer).

> [!TIP]
> [Saiba como obter a identificação do dispositivo de entrada de áudio](../../../how-to-select-audio-input-devices.md).
Referencie o objeto `AudioConfig` da seguinte maneira:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

Se desejar fornecer um arquivo de áudio em vez de usar um microfone, você ainda precisará fornecer um `audioConfig`. No entanto, isso só pode ser feito ao direcionar ao **Node.js** e quando você cria um [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig), em vez de chamar `fromDefaultMicrophoneInput`, você chamará `fromWavFileOutput` e passará o parâmetro `filename`.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>Traduzir fala

A [classe TranslationRecognizer](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) para o SDK de fala para JavaScript expõe alguns métodos que você pode usar para a tradução de fala.

* Conversão de captura única (Async) – executa a conversão em um modo não bloqueado (assíncrono). Isso irá traduzir um único expressão. O fim de um único enunciado é determinado pela escuta de silêncio no fim ou até o máximo de 15 segundos de áudio processado.
* Conversão contínua (Async) – inicia de forma assíncrona a operação de conversão contínua. O usuário registra em eventos e manipula vários Estados do aplicativo. Para interromper a tradução contínua assíncrona, chame [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) .

> [!NOTE]
> Saiba mais sobre como [escolher um modo de reconhecimento de fala](../../../get-started-speech-to-text.md).
### <a name="specify-a-target-language"></a>Especificar um idioma de destino

Para traduzir, você deve especificar um idioma de origem e pelo menos um idioma de destino.
Você pode escolher um idioma de origem usando uma localidade listada na [tabela de tradução de fala](../../../language-support.md#speech-translation). Encontre suas opções de idioma traduzido no mesmo link. As opções para idiomas de destino são diferentes quando você deseja exibir texto ou deseja ouvir a fala traduzida sintetizada. Para traduzir de Inglês para alemão, modifique o objeto de configuração de Tradução:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>Reconhecimento pontual

Aqui está um exemplo de conversão de um único instantâneo assíncrono usando [`recognizeOnceAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizeonceasync) :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Será necessário escrever código para lidar com o resultado. Este exemplo avalia o [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult) para uma tradução para o alemão:

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

Seu código também pode manipular atualizações fornecidas durante o processamento da conversão.
Você pode usar essas atualizações para fornecer comentários visuais sobre o progresso da tradução.
Consulte [Este exemplo de JavaScript Node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js) para ver o código de exemplo que mostra as atualizações fornecidas durante o processo de tradução. O código a seguir também exibe detalhes produzidos durante o processo de tradução.

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>Tradução contínua

A tradução contínua é um pouco mais envolvida do que o reconhecimento de captura única. É necessário assinar os eventos `recognizing`, `recognized` e `canceled` para obter os resultados do reconhecimento. Para interromper a tradução, você deve chamar [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) . Aqui está um exemplo de como a tradução contínua é executada em um arquivo de entrada de áudio.

Comece definindo a entrada e inicializando um [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer):

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Assine os eventos enviados do [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer).

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizing): Sinal para eventos que contêm resultados intermediários de tradução.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognized): Sinal para eventos que contêm resultados de tradução final (indicando uma tentativa de tradução bem-sucedida).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#sessionstopped): Sinal para eventos que indicam o fim de uma sessão de tradução (operação).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#canceled): Sinal para eventos que contêm resultados de tradução cancelados (indicando uma tentativa de conversão que foi cancelada como resultado ou uma solicitação de cancelamento direta ou, como alternativa, uma falha de protocolo ou transporte).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
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

Com tudo configurado, chame [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync).

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>Escolher um idioma de origem

Uma tarefa comum para a tradução de fala é especificar o idioma de entrada (ou origem). Veja como você alteraria o idioma de entrada para italiano. Em seu código, localize seu [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) e adicione a seguinte linha diretamente abaixo dele.

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

A propriedade [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig#speechrecognitionlanguage) espera uma cadeia de caracteres em formato idioma-localidade. Defina qualquer valor na coluna **Localidade** na lista de [localidades/idiomas](../../../language-support.md) com suporte.

## <a name="choose-one-or-more-target-languages"></a>Escolher um ou mais idiomas de destino

O SDK de fala pode ser convertido em vários idiomas de destino em paralelo. Os idiomas de destino disponíveis são um pouco diferentes da lista de idiomas de origem e você especifica os idiomas de destino usando um código de idioma, em vez de uma localidade.
Consulte uma lista de códigos de idioma para destinos de texto na [tabela de tradução de fala na página de suporte do idioma](../../../language-support.md#speech-translation). Você também pode encontrar detalhes sobre a tradução em idiomas sintetizados.

O código a seguir adiciona o alemão como um idioma de destino:

```javascript
translationConfig.addTargetLanguage("de");
```

Como várias traduções de idioma de destino são possíveis, seu código deve especificar o idioma de destino ao examinar o resultado. O código a seguir obtém os resultados da tradução para o alemão.

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```
