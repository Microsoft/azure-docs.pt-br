---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 3953e7182d90cb1737a2083e2315612b07f2eb84
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105385"
---
Neste início rápido, você aprenderá os padrões de design básicos para Reconhecimento do Locutor usando o SDK de Fala, incluindo:

* Verificação dependente do texto e independente do texto
* Identificação do locutor para identificar uma amostra de voz em meio a um grupo de vozes
* Exclusão de perfis de voz

Para obter uma visão de alto nível sobre os conceitos do Reconhecimento de Fala, confira o artigo de [visão geral](../../../speaker-recognition-overview.md).

## <a name="skip-to-samples-on-github"></a>Pular para os exemplos no GitHub

Se você quiser pular diretamente para o código de exemplo, confira os [exemplos do guia de início rápido do JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Atualmente, o Reconhecimento do Locutor é compatível *somente* com os recursos de Fala do Azure criados na região `westus`.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Para fazer qualquer coisa, instale o <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">SDK de Fala para JavaScript </a>. Dependendo de sua plataforma, use as seguintes instruções:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Navegador da Web </a>

Além disso, dependendo do ambiente de destino, use um dos seguintes:

# <a name="script"></a>[script](#tab/script)

Baixe e extraia o arquivo *microsoft.cognitiveservices.speech.sdk.bundle.js* do <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">SDK de Fala para JavaScript</a> e coloque-o em uma pasta acessível para o arquivo HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Se estiver direcionando a um navegador da Web e usando a tag `<script>`, o prefixo `sdk` não será necessário. O prefixo `sdk` é um alias usado para dar nome ao módulo `require`.

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Para obter mais informações sobre `import`, confira <a href="https://javascript.info/import-export" target="_blank">exportar e importar </a>.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Para obter mais informações sobre `require`, confira <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">o que é necessário?</a>.

---

## <a name="import-dependencies"></a>Importar dependências

Para executar os exemplos deste artigo, adicione as instruções a seguir na parte superior do seu arquivo .js.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

Essas instruções importam as bibliotecas necessárias e obtêm a região e a chave de assinatura do serviço de Fala de suas variáveis de ambiente. Elas também especificam os caminhos para os arquivos de áudio que você usará nas tarefas a seguir.

## <a name="create-helper-function"></a>Criar função auxiliar

Adicione a função auxiliar a seguir para ler arquivos de áudio em fluxos para uso pelo serviço de Fala.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

Nessa função, você usará os métodos [AudioInputStream.createPushStream](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream#createpushstream-audiostreamformat-) e [AudioConfig.fromStreamInput](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) para criar um objeto [AudioConfig](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig). Esse objeto `AudioConfig` representa um fluxo de áudio. Você usará vários objetos `AudioConfig` desses durante as tarefas a seguir.

## <a name="text-dependent-verification"></a>Verificação dependente do texto

A Verificação do Locutor é o ato de confirmar que um locutor corresponde a uma voz conhecida ou **registrada**. A primeira etapa é **registrar** um perfil de voz, para que o serviço tenha algo com que comparar as futuras amostras de voz. Neste exemplo, você registra o perfil usando uma estratégia **dependente do texto**, que requer uma frase secreta específica a ser usada para o registro e a verificação. Confira os [documentos de referência](/rest/api/speakerrecognition/) para obter uma lista de frases secretas compatíveis.

### <a name="textdependentverification-function"></a>Função TextDependentVerification

Comece criando a função `TextDependentVerification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

Essa função cria um objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) com o método [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-). Observe que há três [tipos](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype) de `VoiceProfile`:

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

Nesse caso, passe `VoiceProfileType.TextDependentVerification` para `VoiceProfileClient.createProfileAsync`.

Em seguida, chame duas funções auxiliares que você definirá a seguir, `AddEnrollmentsToTextDependentProfile` e `SpeakerVerify`. Por fim, chame [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) para remover o perfil.

### <a name="addenrollmentstotextdependentprofile-function"></a>Função AddEnrollmentsToTextDependentProfile

Defina a função a seguir para registrar um perfil de voz.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

Nessa função, você chamará a função `GetAudioConfigFromFile` definida anteriormente para criar objetos `AudioConfig` com base em amostras de áudio. Essas amostras de áudio contêm uma frase secreta como "Minha voz é meu passaporte, verifique-me". Em seguida, registre essas amostras de áudio usando o método [VoiceProfileClient.enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-).

### <a name="speakerverify-function"></a>Função SpeakerVerify

Defina `SpeakerVerify` da maneira a seguir.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

Nesta função, você criará um objeto [SpeakerVerificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel) com o método [SpeakerVerificationModel.FromProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel#fromprofile-voiceprofile-), passando o objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) criado anteriormente.

Em seguida, chame o método [SpeechRecognizer.recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) para validar uma amostra de áudio que contém a mesma frase secreta que as amostras de áudio que você registrou anteriormente. `SpeechRecognizer.recognizeOnceAsync` retorna um objeto [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult), cuja propriedade `score` contém uma pontuação de similaridade que varia de 0,0 a 1,0. O objeto `SpeakerRecognitionResult` também contém uma propriedade `reason` do tipo [ResultReason](/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason). Se a verificação tiver sido bem-sucedida, a propriedade `reason` deverá ter o valor `RecognizedSpeaker`.

## <a name="text-independent-verification"></a>Verificação independente do texto

Em contraste com a verificação **dependente do texto**, a verificação **independente do texto**:

* Não exige a fala de uma frase secreta específica, mas pode-se falar qualquer coisa
* Não exige três amostras de áudio, mas, *sim*, 20 segundos de áudio total

### <a name="textindependentverification-function"></a>Função TextIndependentVerification

Comece criando a função `TextIndependentVerification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

Como a função `TextDependentVerification`, essa função cria um objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) com o método [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-).

Nesse caso, passe `VoiceProfileType.TextIndependentVerification` para `createProfileAsync`.

Em seguida, chame duas funções auxiliares: `AddEnrollmentsToTextIndependentProfile`, que você definirá a seguir, e `SpeakerVerify`, que você já definiu. Por fim, chame [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) para remover o perfil.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Defina a função a seguir para registrar um perfil de voz.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

Nessa função, você chamará a função `GetAudioConfigFromFile` definida anteriormente para criar objetos `AudioConfig` com base em amostras de áudio. Em seguida, registre essas amostras de áudio usando o método [VoiceProfileClient.enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-).

## <a name="speaker-identification"></a>Identificação do locutor

A Identificação do Locutor é usada para determinar **quem** está falando dentre um determinado grupo de vozes registradas. O processo é semelhante à **verificação independente do texto**, sendo que a principal diferença é a capacidade de verificação em relação a vários perfis de voz de uma só vez, em vez de verificar apenas um perfil de cada vez.

### <a name="textindependentidentification-function"></a>Função TextIndependentIdentification

Comece criando a função `TextIndependentIdentification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

Como as funções `TextDependentVerification` e `TextIndependentVerification`, essa função cria um objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) com o método [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-).

Nesse caso, passe `VoiceProfileType.TextIndependentIdentification` para `VoiceProfileClient.createProfileAsync`.

Em seguida, chame duas funções auxiliares: `AddEnrollmentsToTextIndependentProfile`, que você já definiu, e `SpeakerIdentify`, que você definirá em seguida. Por fim, chame [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) para remover o perfil.

### <a name="speakeridentify-function"></a>Função SpeakerIdentify

Defina a função `SpeakerIdentify` conforme descrito a seguir.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

Nesta função, você criará um objeto [SpeakerIdentificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel) com o método [SpeakerIdentificationModel.fromProfiles](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel#fromprofiles-voiceprofile---), passando o objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) criado anteriormente.

Em seguida, chame o método [SpeechRecognizer.recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) e transmita um exemplo de áudio.
`SpeechRecognizer.recognizeOnceAsync` tenta identificar a voz desta amostra de áudio com base nos objetos `VoiceProfile` usados para criar o `SpeakerIdentificationModel`. Ele retorna um objeto [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult), cuja propriedade `profileId` identifica o `VoiceProfile` correspondente, se houver, enquanto a propriedade `score` contém uma pontuação de similaridade que varia de 0,0 a 1,0.

## <a name="main-function"></a>Função principal

Por fim, defina a função `main` conforme descrito a seguir.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

Essa função cria um objeto [VoiceProfileClient](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient), usado para criar, registrar e excluir perfis de voz. Ela chama as funções que você definiu anteriormente.