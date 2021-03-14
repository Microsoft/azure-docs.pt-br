---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: 502729e4c90a6d6ffa424ea7f379e2b426b3e3e0
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444910"
---
## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Para fazer qualquer coisa, instale o <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">SDK de Fala para JavaScript </a>. Dependendo de sua plataforma, use as seguintes instruções:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Navegador da Web </a>

## <a name="create-voice-signatures"></a>Criar assinaturas de voz

A primeira etapa é criar assinaturas de voz para os participantes da conversa para que eles possam ser identificados como alto-falantes exclusivos. O `.wav` arquivo de áudio de entrada para a criação de assinaturas de voz deve ser de 16 bits, 16 kHz de taxa de amostra e formato mono (canal único). O comprimento recomendado para cada amostra de áudio é entre trinta e dois minutos. O `.wav` arquivo deve ser uma amostra da voz de **uma pessoa** para que um perfil de voz exclusivo seja criado.

O exemplo a seguir mostra como criar uma assinatura de voz [usando a API REST](https://aka.ms/cts/signaturegenservice) em JavaScript. Observe que você precisa substituir informações reais para seu `subscriptionKey` , `region` e o caminho para um arquivo de exemplo `.wav` .

```javascript
const fs = require('fs');
const axios = require('axios');
const formData = require('form-data');
 
const subscriptionKey = 'your-subscription-key';
const region = 'your-region';
 
async function createProfile() {
    let form = new formData();
    form.append('file', fs.createReadStream('path-to-voice-sample.wav'));
    let headers = form.getHeaders();
    headers['Ocp-Apim-Subscription-Key'] = subscriptionKey;
 
    let url = `https://signature.${region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData`;
    let response = await axios.post(url, form, { headers: headers });
    
    // get signature from response, serialize to json string
    return JSON.stringify(response.data.Signature);
}
 
async function main() {
    // use this voiceSignature string with conversation transcription calls below
    let voiceSignatureString = await createProfile();
    console.log(voiceSignatureString);
}
main();
```

A execução desse script retorna uma cadeia de caracteres de assinatura de voz na variável `voiceSignatureString` . Execute a função duas vezes para que você tenha duas cadeias de caracteres para usar como entrada para as variáveis `voiceSignatureStringUser1` e `voiceSignatureStringUser2` abaixo.

> [!NOTE]
> As assinaturas de voz **só** podem ser criadas usando a API REST.

## <a name="transcribe-conversations"></a>Transcrever conversas

O código de exemplo a seguir demonstra como transcrever conversas em tempo real para dois alto-falantes. Ele pressupõe que você já criou cadeias de caracteres de assinatura de voz para cada palestrante, como mostrado acima. Substitua informações reais para `subscriptionKey` , `region` e o caminho `filepath` para o áudio que você deseja transcrever.

Este código de exemplo faz o seguinte:

* Cria um fluxo de envio por push para ser usado para transcrição e grava o arquivo de exemplo `.wav` nele.
* Cria um `Conversation` usando `createConversationAsync()` .
* Cria um `ConversationTranscriber` usando o construtor.
* Adiciona participantes à conversa. As cadeias de caracteres `voiceSignatureStringUser1` e `voiceSignatureStringUser2` devem vir como saída das etapas acima.
* Registra-se em eventos e inicia a transcrição.

```javascript
(function() {
    "use strict";
    var sdk = require("microsoft-cognitiveservices-speech-sdk");
    var fs = require("fs");
    
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav"; // 8-channel audio
    
    // create the push stream and write file to it
    var pushStream = sdk.AudioInputStream.createPushStream();
    fs.createReadStream(filepath).on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
    
    var speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription(subscriptionKey, region);
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    // create conversation and transcriber
    var conversation = sdk.Conversation.createConversationAsync(speechTranslationConfig, "myConversation");
    var transcriber = new sdk.ConversationTranscriber(audioConfig);
    
    // attach the transcriber to the conversation
    transcriber.joinConversationAsync(conversation,
    function () {
        // add first participant using voiceSignature created in enrollment step
        var user1 = sdk.Participant.From("user1@example.com", "en-us", voiceSignatureStringUser1);
        conversation.addParticipantAsync(user1,
        function () {
            // add second participant using voiceSignature created in enrollment step
            var user2 = sdk.Participant.From("user2@example.com", "en-us", voiceSignatureStringUser2);
            conversation.addParticipantAsync(user2,
            function () {
                transcriber.sessionStarted = function(s, e) {
                console.log("(sessionStarted)");
                };
                transcriber.sessionStopped = function(s, e) {
                console.log("(sessionStopped)");
                };
                transcriber.canceled = function(s, e) {
                console.log("(canceled)");
                };
                transcriber.transcribed = function(s, e) {
                console.log("(transcribed) text: " + e.result.text);
                console.log("(transcribed) speakerId: " + e.result.speakerId);
                };
    
                // begin conversation transcription
                transcriber.startTranscribingAsync(
                function () { },
                function (err) {
                    console.trace("err - starting transcription: " + err);
                });
        },
        function (err) {
            console.trace("err - adding user1: " + err);
        });
    },
    function (err) {
        console.trace("err - adding user2: " + err);
    });
    },
    function (err) {
    console.trace("err - " + err);
    });
}()); 
```