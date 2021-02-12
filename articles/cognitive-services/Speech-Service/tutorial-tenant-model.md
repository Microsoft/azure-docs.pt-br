---
title: Criar um modelo de locatário (versão prévia) – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Gere automaticamente um modelo de locatário seguro e em conformidade (Fala Personalizada usando dados do Microsoft 365) que use seus dados do Microsoft 365 a fim de entregar um reconhecimento de fala ideal para termos específicos.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 48cde51ee9941f705aa848d121c419a8f0c9ad1a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013708"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Tutorial: Criar um modelo de locatário (versão prévia)

O Modelo de Locatário (Fala Personalizada usando dados do Microsoft 365) é um serviço de aceitação para clientes do Microsoft 365 Enterprise que gera automaticamente um modelo de reconhecimento de fala personalizada dos dados do Microsoft 365 da sua organização. O modelo é otimizado para termos técnicos, jargões e nomes de pessoas, tudo de maneira segura e em conformidade.

> [!IMPORTANT]
> Se a organização for registrada usando o serviço de Modelo de Locatário, o Serviço de Fala poderá acessar o modelo de linguagem da sua organização. O modelo é gerado com base em documentos e emails do grupo público do Microsoft 365, que podem ser vistos por qualquer pessoa em sua organização. O administrador da sua organização pode ligar ou desligar o uso do modelo de linguagem no âmbito de toda a organização por meio do portal de administração.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Registre-se no Modelo de Locatário usando o Centro de Administração do Microsoft 365
> * Obtenha uma chave de assinatura de Fala
> * Criar um modelo de locatário
> * Implantar um modelo de locatário
> * Use seu modelo de locatário com o SDK de Fala

## <a name="enroll-in-the-tenant-model-service"></a>Registrar-se no serviço de Modelo de Locatário

Antes de implantar seu modelo de locatário, você precisa estar registrado no serviço de Modelo de Locatário. O registro é concluído no centro de administração do Microsoft 365 e pode ser feito somente pelo administrador.

1. Entre no [Centro de Administração do Microsoft 365](https://admin.microsoft.com).

1. No painel esquerdo, selecione **Configurações**, depois, selecione **Configurações** no menu aninhado e, em seguida, selecione **Serviços de Fala do Azure** na janela principal.

   ![O painel "Serviços e suplementos"](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Marque a caixa de seleção **Permitir o modelo de linguagem em toda a organização** e, em seguida, selecione **Salvar alterações**.

   ![O painel Serviços de Fala do Azure](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Para desligar a instância do modelo de locatário:
1. Repita as etapas 1 e 2 anteriores.
1. Desmarque a caixa de seleção **Permitir o modelo de linguagem em toda a organização** e, em seguida, selecione **Salvar alterações**.

## <a name="get-a-speech-subscription-key"></a>Obtenha uma chave de assinatura de Fala

Para usar seu modelo de locatário com o SDK de Fala, será necessário um recurso de Fala e a chave de assinatura associada a ele.

1. Entre no [portal do Azure](https://aka.ms/azureportal).
1. Selecione **Criar um recurso**.
1. Na caixa **Pesquisa**, digite **Fala**.
1. Na lista de resultados, selecione **Fala** e, em seguida, selecione **Criar**.
1. Siga as instruções na tela para criar o recurso. Certifique-se de que:
   * **O local** é definido como **eastus** ou **westus**.
   * **O tipo de preço** é definido como **S0**.
1. Selecione **Criar**.

   O recurso será criado após alguns minutos. A chave de assinatura está disponível na seção **Visão Geral** para seu recurso.

## <a name="create-a-language-model"></a>Criar um modelo de linguagem

Depois que o administrador tiver habilitado o Modelo de Locatário para sua organização, será possível criar um modelo de linguagem com base em seus dados do Microsoft 365.

1. Entre no [Estúdio de Fala](https://speech.microsoft.com/).
1. No canto superior direito, selecione **Configurações** (o ícone de engrenagem) e, em seguida, selecione **Configurações do Modelo de Locatário**.

   ![O link "Configurações do Modelo de Locatário"](media/tenant-language-model/tenant-language-settings.png)

   O Estúdio de Fala exibe uma mensagem que informa se você está qualificado para criar um modelo de locatário.

   > [!NOTE]
   > Os clientes empresariais na América do Norte estão qualificados para criar um modelo de locatário (inglês). Se você for um cliente do Sistema de Proteção de Dados do Cliente, da Chave de Cliente ou do Office 365 Government, esse recurso não estará disponível. Para determinar se você é um cliente do Sistema de Proteção de Dados do Cliente ou da Chave do Cliente, confira:
   > * [Sistema de Proteção de Dados do Cliente](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Chave de Cliente](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365 Government](https://www.microsoft.com/microsoft-365/government)

1. Selecione **Aceitar**.

   Quando seu modelo de locatário estiver pronto, você receberá uma mensagem de email de confirmação com mais instruções.

## <a name="deploy-your-tenant-model"></a>Implantar seu modelo de locatário

Quando a instância do modelo de locatário estiver pronta, implante-a fazendo o seguinte:

1. Na mensagem de email de confirmação, selecione o botão **Exibir modelo**. Ou entre no [Estúdio de Fala](https://speech.microsoft.com/).
1. No canto superior direito, selecione **Configurações** (o ícone de engrenagem) e, em seguida, selecione **Configurações do Modelo de Locatário**.

   ![O link "Configurações do Modelo de Locatário"](media/tenant-language-model/tenant-language-settings.png)

1. Selecione **Implantar**.

   Quando o modelo for implantado, o status será alterado para *Implantado*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Use seu modelo de locatário com o SDK de Fala

Agora que você implantou o modelo, poderá usá-lo com o SDK de Fala. Nesta seção, você usará o código de exemplo para chamar o Serviço de Fala usando a autenticação do Azure AD (Azure Active Directory).

Vamos analisar o código que você usará para chamar o SDK de Fala no C#. Neste exemplo, você executará o reconhecimento de fala usando seu modelo de locatário. Este guia presume que sua plataforma já está configurada. Se precisar de ajuda para a instalação, confira o [Início rápido: Reconheça a fala, C# (.NET Core)](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore).

Copie este código em seu projeto:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // ServiceApplicationId is a fixed value. No need to change it.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Em seguida, será necessário recompilar e executar o projeto por meio da linha de comando. Antes de executar o comando, atualize alguns parâmetros fazendo o seguinte:

1. Substitua `<Username>` e `<Password>` pelos valores de um usuário de locatário válido.
1. Substitua `<Subscription-Key>` pela chave de assinatura de seu recurso de Fala. Esse valor está disponível na seção **Visão Geral** para seu recurso de Fala no [portal do Azure](https://aka.ms/azureportal).
1. Substitua o `<Endpoint-Uri>` pelo ponto de extremidade a seguir. Substitua `{your region}` pela região onde o recurso de Fala foi criado. Essas regiões têm suporte: `westus`, `westus2` e `eastus`. As informações de sua região estão disponíveis na seção **Visão Geral** do recurso de Fala no [portal do Azure](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Execute o comando a seguir:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

Neste tutorial, você aprendeu a usar os dados do Microsoft 365 para criar um modelo personalizado de reconhecimento de fala, implantá-lo e usá-lo com o SDK de Fala.

## <a name="next-steps"></a>Próximas etapas

* [Estúdio de Fala](https://speech.microsoft.com/)
* [SDK da fala](speech-sdk.md)