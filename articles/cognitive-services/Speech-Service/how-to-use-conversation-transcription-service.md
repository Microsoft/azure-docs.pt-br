---
title: Transcrever conversas de vários participantes com o SDK de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a transcrição de conversa com o SDK de fala. Disponível para C++, C#e Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: jhakulin
ms.openlocfilehash: 8c4ecc017d058900297f2220173e064700e7051b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559460"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Transcrever conversas de vários participantes com o SDK de fala

A API **ConversationTranscriber** do SDK de fala permite transcrever reuniões/conversas com a capacidade de adicionar, remover e identificar participantes transmitindo áudio para os serviços de fala usando `PullStream` o ou `PushStream`o.

## <a name="limitations"></a>Limitações

* O transcrevedor de conversa tem suporte C++para C#o, o e o Java no Windows, Linux e Android.
* O ROOBO DevKit é o ambiente de hardware com suporte para a criação de transcrições de conversa, pois fornece uma matriz circular de vários microfones que pode ser utilizada com eficiência para a identificação do orador. [Para obter mais informações, consulte SDK de dispositivos de fala](speech-devices-sdk.md).
* O suporte do SDK de fala para transcrição de conversa é limitado ao uso de transmissão de áudio e fluxos de modo de push com oito canais de áudio PCM de 16 bits 16 kHz.
* A transcrição de conversa está disponível atualmente nos idiomas "en-US" e "zh-CN" nas seguintes regiões: centralus e eastasia.

## <a name="prerequisites"></a>Pré-requisitos

* [Saiba como usar a conversão de fala em texto com o SDK de fala.](quickstart-csharp-dotnet-windows.md)
* [Obtenha sua assinatura de avaliação de fala.](https://azure.microsoft.com/try/cognitive-services/)
* O Speech SDK versão 1.5.1 ou posterior é necessário.

## <a name="create-voice-signatures-for-participants"></a>Criar assinaturas de voz para participantes

A primeira etapa é criar assinaturas de voz para os participantes da conversa. A criação de assinaturas de voz é necessária para uma identificação de alto-falante eficiente.

### <a name="requirements-for-input-wave-file"></a>Requisitos para o arquivo wave de entrada

* O arquivo wave de entrada de áudio para criar assinaturas de voz deve estar em amostras de 16 bits, taxa de amostra de 16 kHz e um único formato de canal (mono).
* O comprimento recomendado para cada amostra de áudio é entre 30 e dois minutos.

O exemplo a seguir mostra duas maneiras diferentes de criar a assinatura de voz [usando a API REST](https://aka.ms/cts/signaturegenservice) de C#:

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>Transcrevendo conversas

Para `ConversationTranscriber` transcrever conversas com vários participantes, crie o objeto associado `AudioConfig` ao objeto criado para a sessão de conversa e transmita áudio usando `PullAudioInputStream` ou `PushAudioInputStream`.

Vamos supor que você tenha uma classe ConversationTranscriber chamada `MyConversationTranscriber`. Seu código pode ter a seguinte aparência:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
                {
                    Console.WriteLine($"CANCELED: Reason={e.Reason}");

                    if (e.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        stopTranscription.TrySetResult(0);
                    }
                };

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document.
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar nossos exemplos no GitHub](https://aka.ms/csspeech/samples)
