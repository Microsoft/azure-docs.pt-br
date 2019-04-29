---
title: 'Início Rápido: converter texto em fala, .NET Core – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprenderá a converter texto em Fala com a API REST de Conversão de Texto em Fala. O texto de exemplo incluído neste guia é estruturado como Linguagem de marcação de síntese de Fala (SSML). Isso permite que você escolha a voz e o idioma da resposta de Fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: 5ae63b1738824095073ac6b9e1071f6b4a3e5ae1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460466"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>Início Rápido: converter o texto em fala usando o .NET Core

Neste início rápido, você aprenderá a converter texto em Fala usando .NET Core e a API REST de conversão de texto em Fala. O texto de amostra incluído neste guia é estruturado como [Linguagem de marcação de síntese de Fala (SSML)](speech-synthesis-markup.md), que permite escolher a voz e o idioma da resposta.

Este início rápido requer uma [conta de serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviços de fala. Se não tiver uma conta, você poderá usar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de assinatura.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [SDK do .NET Core](https://dotnet.microsoft.com/download)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou seu editor de texto favorito
* Uma chave de assinatura do Azure para o Serviço de Fala

## <a name="create-a-net-core-project"></a>Criar projeto do .NET Core

Abra um novo prompt de comando (ou a sessão de terminal) e execute estes comandos:

```console
dotnet new console -o tts-sample
cd tts-sample
```

O primeiro comando faz duas coisas. Ele cria um novo aplicativo de console .NET e cria um diretório chamado `tts-sample`. O segundo comando é alterado para o diretório do seu projeto.

## <a name="select-the-c-language-version"></a>Selecione a versão do idioma C#

Este início rápido requer C# 7.1 ou posterior. Há algumas maneiras de alterar a versão C# para o seu projeto. Neste guia, mostraremos como ajustar o `tts-sample.csproj` arquivo. Para todas as opções disponíveis, como alterar o idioma no Visual Studio, consulte [Selecionar a versão do idioma C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Abra seu projeto e, em seguida, abra `tts-sample.csproj`. Verifique se `LangVersion` está definido como 7.1 ou posterior. Se não houver um grupo de propriedades para a versão de idioma, adicione essas linhas:

```csharp
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

Certifique-se de salvar as alterações.

## <a name="add-required-namespaces-to-your-project"></a>Adicionar namespaces necessários ao seu projeto

O comando `dotnet new console` que você executou anteriormente criou um projeto, incluindo `Program.cs`. Esse arquivo é onde você colocará o código do aplicativo. Abra `Program.cs` e substitua o existente usando as instruções existentes. Essas instruções garantem que você tenha acesso a todos os tipos necessários para compilar e executar o aplicativo de exemplo.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>Criar uma classe para troca de token

A API REST de conversão de texto em fala requer um token de acesso para autenticação. Para obter um token de acesso, é necessária uma troca. Este exemplo troca sua chave de assinatura de serviços de fala para obter um token de acesso usando o `issueToken` ponto de extremidade.

Este exemplo pressupõe que sua assinatura dos serviços de fala é na região Oeste dos EUA. Se você estiver usando uma região diferente, atualize o valor para `FetchTokenUri`. Para uma lista completa, consulte [Regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> Para obter mais informações sobre autenticação, consulte [Autenticar com um token de acesso](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="get-an-access-token-and-set-the-host-url"></a>Obter um token de acesso e definir a URL do host

Localize `static void Main(string[] args)` e substitua por `static async Task Main(string[] args)`.

Em seguida, copie esse código para o seu método principal. Ele faz algumas coisas, mas o mais importante, leva o texto como uma entrada e chama a função `Authentication` para trocar a sua chave de assinatura por um token de acesso. Se algo der errado, o erro é impresso no console.

Certifique-se de adicionar sua chave de assinatura antes de executar o aplicativo.

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

Em seguida, defina o host e a rota de conversão do texto em fala:

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>Criar a solicitação SSML

O texto é enviado como o corpo de uma solicitação `POST`. Com o SSML, você pode especificar a voz e o idioma. Neste início rápido, vamos usar SSML com o idioma definido como `en-US` e a voz definida como `ZiraRUS`. Vamos construir o SSML para a sua solicitação:

```csharp
string body = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> Este exemplo usa a fonte de voz `ZiraRUS`. Para obter uma lista completa das vozes / idiomas fornecidos pela Microsoft, consulte [Suporte ao idioma](language-support.md). Se você estiver interessado em criar uma voz exclusiva e reconhecível para sua marca, consulte [Criando fontes de voz personalizadas](how-to-customize-voice-font.md).

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>Criar uma instância do cliente, fazer uma solicitação e salvar áudio sintetizado em um arquivo

Há muitas coisas acontecendo no código de exemplo. Vamos analisar rapidamente o que está acontecendo:

* O cliente e solicitação são instanciados.
* O método HTTP é definido como `POST`.
* Cabeçalhos necessários são adicionados à solicitação.
* A solicitação é enviada e o código de status é verificado.
* A resposta é ler de forma assíncrona e gravada em um arquivo chamado sample.wav.

Copie este código em seu projeto. Certifique-se de substituir o valor do `User-Agent` cabeçalho com o nome do seu recurso do portal do Azure.

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

É isso, você está pronto para executar seu aplicativo de texto para fala. Na linha de comando (ou sessão de terminal), navegue até o diretório do projeto e execute:

```console
dotnet run
```

Se bem sucedido, o arquivo de fala é salvo na pasta do seu projeto. Reproduzi-lo usando o player de mídia favorito.

## <a name="clean-up-resources"></a>Limpar recursos

Se você embutiu sua chave de assinatura no programa, remova-a quando tiver terminado este início rápido.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de .NET no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NETCore)

## <a name="see-also"></a>Consulte também

* [Referência de API de texto em fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criar fontes de voz personalizada](how-to-customize-voice-font.md)
* [Grave amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
