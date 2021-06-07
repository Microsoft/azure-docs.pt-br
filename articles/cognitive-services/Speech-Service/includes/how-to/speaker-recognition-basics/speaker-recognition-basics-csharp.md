---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: ad7f6f600082a407a94833fe1575136fc360c9ae
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104876"
---
Neste início rápido, você aprenderá os padrões de design básicos para Reconhecimento do Locutor usando o SDK de Fala, incluindo:

* Verificação dependente do texto e independente do texto
* Identificação do locutor para identificar uma amostra de voz em meio a um grupo de vozes
* Exclusão de perfis de voz

Para obter uma visão de alto nível sobre os conceitos do Reconhecimento de Fala, confira o artigo de [visão geral](../../../speaker-recognition-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Atualmente, o Reconhecimento do Locutor é compatível *somente* com os recursos de Fala do Azure criados na região `westus`.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Antes de fazer qualquer coisa, você precisará instalar o SDK de Fala. Dependendo de sua plataforma, use as seguintes instruções:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnet" target="_blank">.NET Framework </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnetcore" target="_blank">.NET Core </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=unity" target="_blank">Unity </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=uwps" target="_blank">UWP </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=xaml" target="_blank">Xamarin </a>

## <a name="import-dependencies"></a>Importar dependências

Para executar os exemplos neste artigo, inclua as instruções `using` a seguir na parte superior do script.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Para chamar o serviço de Fala usando o SDK de Fala, você precisa criar um [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig). Neste exemplo, você cria um [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) usando uma chave de assinatura e uma região. Você também cria um código clichê básico a ser usado no restante do artigo e o modifica para personalizações diferentes.

Observe que a região é definida como `westus`, pois é a única região com suporte para o serviço.

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        string region = "westus";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>Verificação dependente do texto

A Verificação do Locutor é o ato de confirmar que um locutor corresponde a uma voz conhecida ou **registrada**. A primeira etapa é **registrar** um perfil de voz, para que o serviço tenha algo com que comparar as futuras amostras de voz. Neste exemplo, você registra o perfil usando uma estratégia **dependente do texto**, que requer uma frase secreta específica a ser usada para o registro e a verificação. Confira os [documentos de referência](/rest/api/speakerrecognition/) para obter uma lista de frases secretas compatíveis.

Comece criando a seguinte função em sua classe `Program` para registrar um perfil de voz.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine("Speak the passphrase, \"My voice is my passport, verify me.\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

Nessa função, `await client.CreateProfileAsync()` é o que realmente cria o perfil de voz. Após a criação, você deverá especificar como vai inserir amostras de áudio, usando `AudioConfig.FromDefaultMicrophoneInput()` neste exemplo para capturar o áudio do seu dispositivo de entrada padrão. Em seguida, você registra as amostras de áudio em um loop `while` que acompanha o número de amostras restantes e necessário para o registro. Em cada iteração, `client.EnrollProfileAsync(profile, audioInput)` solicitará que você fale a frase secreta no microfone e adicione a amostra ao perfil de voz.

Após a conclusão do registro, você chama `await SpeakerVerify(config, profile, profileMapping)` para verificar em relação ao perfil que você acabou de criar. Adicione outra função para definir `SpeakerVerify`.

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

Nessa função, você passa o objeto `VoiceProfile` que acabou de criar para inicializar um modelo em relação ao qual verificar. Em seguida, `await speakerRecognizer.RecognizeOnceAsync(model)` solicita que você fale a frase secreta novamente, mas desta vez ela a validará em relação ao seu perfil de voz e retornará uma pontuação de similaridade que varia de 0,0 a 1,0. O objeto `result` também retornará `Accept` ou `Reject`, dependendo da correspondência (ou não) da frase secreta.

Em seguida, modifique sua função `Main()` para chamar as novas funções que você criou. Além disso, observe que você cria uma `Dictionary<string, string>` para passar por referência por meio de suas chamadas de função. O motivo disso é que o serviço não permite o armazenamento de um nome legível com um `VoiceProfile`criado e armazena apenas um número de ID para fins de privacidade. Na função `VerificationEnroll`, você adiciona a esse dicionário uma entrada com a ID recém-criada, juntamente com um nome de texto. Em cenários de desenvolvimento de aplicativos que exigirem a exibição de um nome legível, **você deverá armazenar esse mapeamento em algum lugar (o serviço não poderá armazená-lo)** .

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

Execute o script e será solicitado que você fale a frase *Minha voz é meu passaporte, verifique-me!* três vezes para registro e mais uma vez para verificação. O resultado retornado é a pontuação de similaridade, que pode ser usada para criar seus próprios limites personalizados de verificação.

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>Verificação independente do texto

Em contraste com a verificação **dependente do texto**, a verificação **independente do texto**:

* Não exige a fala de uma frase secreta específica, mas pode-se falar qualquer coisa
* Não exige três amostras de áudio, mas *sim* 20 segundos de áudio total

Faça algumas alterações simples na função `VerificationEnroll` para mudar para a verificação **independente do texto**. Primeiro, altere o tipo de verificação para `VoiceProfileType.TextIndependentVerification`. Em seguida, altere o loop `while` para acompanhar `result.RemainingEnrollmentsSpeechLength`, que continuará solicitando que você fale até completar os 20 segundos de áudio capturados.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine("Continue speaking to add to the profile enrollment sample.");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

Execute o programa novamente e fale qualquer coisa durante a fase de verificação, já que não há necessidade de falar nenhuma frase secreta específica. Novamente, a pontuação de similaridade é retornada.

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:15.3200000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:09.8100008

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:05.1900000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00.8700000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>Identificação do locutor

A Identificação do Locutor é usada para determinar **quem** está falando dentre um determinado grupo de vozes registradas. O processo é muito semelhante à **verificação independente do texto**, sendo que a principal diferença é a capacidade de verificação em relação a vários perfis de voz de uma só vez, em vez de verificar apenas um perfil de cada vez.

Crie uma função `IdentificationEnroll` para registrar vários perfis de voz. O processo de registro para cada perfil é o mesmo que o processo de registro para a **verificação independente do texto**, exigindo 20 segundos de áudio para cada perfil. Essa função aceita uma lista de cadeias de caracteres `profileNames` e criará um perfil de voz para cada nome da lista. A função retorna uma lista de objetos `VoiceProfile`, que você usa na próxima função para identificar um locutor.

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Continue speaking to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

Crie a função `SpeakerIdentification` a seguir para enviar uma solicitação de identificação. A principal diferença dessa função em relação à uma solicitação de **verificação do locutor** é o uso de `SpeakerIdentificationModel.FromProfiles()`, que aceita uma lista de objetos `VoiceProfile`. 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similar voice profile is {profileMapping[result.ProfileId]} with similarity score {result.Score}");
}
```

Altere a função `Main()` conforme a seguir. Você cria uma lista de cadeias de caracteres `profileNames` e passa ela para a função `IdentificationEnroll()`. Então, será solicitado que você crie um perfil de voz para cada nome da lista, de modo que você possa adicionar mais nomes a fim de criar perfis adicionais para amigos ou colegas.

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

Execute o script e será solicitado que você fale para registrar as amostras de voz para o primeiro perfil. Depois que o registro for concluído, será solicitado que você repita esse processo para cada nome da lista `profileNames`. Após a conclusão de cada registro, será solicitado que você ponha **outra pessoa** para falar e o serviço tentará identificar essa pessoa entre os perfis de voz registrados por você.

Este exemplo retorna apenas a correspondência mais próxima e a respectiva pontuação de similaridade, mas você poderá obter a resposta completa que inclui as cinco principais pontuações de similaridade adicionando `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)` à sua função `SpeakerIdentification`.

## <a name="changing-audio-input-type"></a>Alterando o tipo de entrada de áudio

Os exemplos deste artigo usam o microfone de dispositivo padrão como o meio de entrada das amostras de áudio. No entanto, em cenários que exijam o uso de arquivos de áudio em vez da entrada por meio do microfone, basta alterar as instâncias de `AudioConfig.FromDefaultMicrophoneInput()` para `AudioConfig.FromWavFileInput(path/to/your/file.wav)` a fim de alternar para a entrada por meio de arquivo. Também é possível ter entradas mistas, usando o microfone para registro e arquivos para verificação, por exemplo.

## <a name="deleting-voice-profile-enrollments"></a>Excluindo registros de perfis de voz

Para excluir um perfil registrado, use a função `DeleteProfileAsync()` no objeto `VoiceProfileClient`. O exemplo de função a seguir mostra como excluir um perfil de voz de uma ID de perfil de voz conhecida.

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```