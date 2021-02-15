---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 058450a4f28fb03da317debc9557a9beb45b2e90
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88921386"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Criar projeto do .NET Core

Abra um novo prompt de comando (ou a sessão de terminal) e execute estes comandos:

```console
dotnet new console -o transliterate-sample
cd transliterate-sample
```

O primeiro comando faz duas coisas. Ele cria um novo aplicativo de console .NET e cria um diretório chamado `transliterate-sample`. O segundo comando é alterado para o diretório do seu projeto.

Em seguida, você precisará instalar o Json.Net. No diretório do projeto, execute:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Selecione a versão do idioma C#

Este início rápido requer C# 7.1 ou posterior. Há algumas maneiras de alterar a versão C# para o seu projeto. Neste guia, mostraremos como ajustar o `transliterate-sample.csproj` arquivo. Para todas as opções disponíveis, como alterar o idioma no Visual Studio, consulte [Selecionar a versão do idioma C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Abra seu projeto e, em seguida, abra `transliterate-sample.csproj`. Verifique se `LangVersion` está definido como 7.1 ou posterior. Se não houver um grupo de propriedades para a versão de idioma, adicione essas linhas:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Adicionar namespaces necessários ao seu projeto

O comando `dotnet new console` que você executou anteriormente criou um projeto, incluindo `Program.cs`. Esse arquivo é onde você colocará o código do aplicativo. Abra `Program.cs` e substitua o existente usando as instruções existentes. Essas instruções garantem que você tenha acesso a todos os tipos necessários para compilar e executar o aplicativo de exemplo.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Criar classes para a resposta JSON

Em seguida, criaremos uma classe que é usada ao desserializar a resposta JSON retornada pelo Tradutor.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator.
/// </summary>
public class TransliterationResult
{
    public string Text { get; set; }
    public string Script { get; set; }
}
```

## <a name="get-subscription-information-from-environment-variables"></a>Obter informações de assinatura das variáveis de ambiente

Adicione as linhas abaixo à classe `Program`. Essas linhas leem a chave da sua assinatura e o ponto de extremidade com base em variáveis de ambiente e gerarão um erro se você encontrar problemas.

```csharp
private const string key_var = "TRANSLATOR_TEXT_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
// The code in the next section goes here.
```

## <a name="create-a-function-to-transliterate-text"></a>Criar uma função para transliterar texto

Na classe `Program`, crie uma função assíncrona chamada `TransliterateTextRequest()`. Esta função usará quatro argumentos: `subscriptionKey`, `endpoint`, `route` e `inputText`.

```csharp
static public async Task TransliterateTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>Serializar a solicitação de tradução

Em seguida, precisaremos criar e serializar o objeto JSON que inclui o texto a ser traduzido. Lembre-se, você pode transmitir mais de um objeto em `body`.

```csharp
object[] body = new object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Instanciar o cliente e fazer uma solicitação

Essas linhas instanciam `HttpClient` e `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Construir a solicitação e imprimir a resposta

Dentro de `HttpRequestMessage`, você vai:

* Declarar o método HTTP
* Construir o URI de solicitação
* Inserir o corpo da solicitação (objeto JSON serializado)
* Adicionar os cabeçalhos necessários
* Fazer uma solicitação assíncrona
* Imprima a resposta

Adicione este código a `HttpRequestMessage`:

```csharp
// Build the request.
// Set the method to Post.
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(endpoint + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
TransliterationResult[] deserializedOutput = JsonConvert.DeserializeObject<TransliterationResult[]>(result);
// Iterate over the deserialized results.
foreach (TransliterationResult o in deserializedOutput)
{
    Console.WriteLine("Transliterated to {0} script: {1}", o.Script, o.Text);
}
```

Se estiver usando uma assinatura de vários serviço cognitivos, você também deve incluir o `Ocp-Apim-Subscription-Region` em seus parâmetros de solicitação. [Saiba mais sobre a autenticação com a assinatura de vários serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Colocar tudo isso junto

A última etapa é chamar `TransliterateTextRequest()` na função `Main`. Neste exemplo, estamos transliterando do japonês para o script latino. Localize `static void Main(string[] args)` e substitua por este código:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate
    string route = "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
    string textToTransliterate = @"こんにちは";
    await TransliterateTextRequest(subscriptionKey, endpoint, route, textToTransliterate);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```

É possível observar que em `Main`, você declara `subscriptionKey`, `endpoint`, `route` e o script para transcrever `textToTransliterate`.

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

E, pronto, você já pode executar seu aplicativo de exemplo. Na linha de comando (ou sessão de terminal), navegue até o diretório do projeto e execute:

```console
dotnet run
```

## <a name="sample-response"></a>Resposta de exemplo

Depois de executar o exemplo, você deverá ver o seguinte impresso no terminal:

```bash
Transliterated to latn script: Kon\'nichiwa
```

Esta mensagem é criada a partir do JSON bruto, que terá esta aparência:

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Limpar os recursos

Remova todas as informações confidenciais do código-fonte do seu aplicativo de exemplo, como as chaves de assinatura.

## <a name="next-steps"></a>Próximas etapas

Confira a referência da API para saber tudo o que você pode fazer com o Tradutor.

> [!div class="nextstepaction"]
> [Referência de API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
