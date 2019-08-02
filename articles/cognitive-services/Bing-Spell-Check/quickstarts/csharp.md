---
title: 'Início Rápido: Verificar a ortografia com a API REST de Verificação Ortográfica do Bing e o C#'
titleSuffix: Azure Cognitive Services
description: Comece a usar a API REST de Verificação Ortográfica do Bing para verificar a ortografia e a gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: a2c121ed58882427022b716081b096c913d447f8
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423618"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Início Rápido: Verificar a ortografia com a API REST de Verificação Ortográfica do Bing e o C#

Use este Início Rápido para fazer sua primeira chamada à API REST de Verificação Ortográfica do Bing. Este aplicativo C# simples envia uma solicitação à API e retorna uma lista de correções sugeridas. Embora esse aplicativo seja escrito em C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código-fonte desse aplicativo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017 ou posterior](https://www.visualstudio.com/downloads/).
* Para instalar o `Newtonsoft.Json` como um pacote NuGet no Visual Studio:
    1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no arquivo de Solução.
    1. Selecione **Gerenciar Pacotes NuGet para a Solução**.
    1. Pesquise `Newtonsoft.Json` e instale o pacote.
* Se você estiver usando Linux/MacOS, este aplicativo poderá ser executado usando [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie uma solução de console chamada `SpellCheckSample` no Visual Studio. Em seguida, adicione os seguintes namespaces no arquivo de código principal.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Crie variáveis para o ponto de extremidade de API, a chave de assinatura e o texto que terá a ortografia verificada.

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "<ENTER-KEY-HERE>";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. Crie uma variável para os parâmetros de pesquisa. Acrescente o código de mercado após `mkt=`. O código de mercado é o país no qual você faz a solicitação. Além disso, acrescente o modo de verificação ortográfica após `&mode=`. O modo é `proof` (captura a maioria dos erros de ortografia/gramática) ou `spell` (captura a maioria dos erros de ortografia, mas não tantos erros de gramática).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Criar e enviar uma solicitação de verificação ortográfica

1. Crie uma função assíncrona chamada `SpellCheck()` para enviar uma solicitação à API. Crie um `HttpClient` e adicione a chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`. Em seguida, execute as etapas a seguir dentro da função.

    ```csharp
    async static void SpellCheck()
    {
        HttpClient client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = new HttpResponseMessage();
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Crie o URI para a sua solicitação acrescentando seu host, caminho e parâmetros.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Crie uma lista com um objeto `KeyValuePair` que contém o texto e use-o para criar um objeto `FormUrlEncodedContent`. Defina as informações de cabeçalho e use `PostAsync()` para enviar a solicitação.

    ```csharp
    List<KeyValuePair<string, string>> values = new List<KeyValuePair<string, string>>();
    values.Add(new KeyValuePair<string, string>("text", text));
    
    using (FormUrlEncodedContent content = new FormUrlEncodedContent(values))
    {
        content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
        response = await client.PostAsync(uri, content);
    }
    ```

## <a name="get-and-print-the-api-response"></a>Obter e imprimir a resposta da API

### <a name="get-the-client-id-header"></a>Obter o cabeçalho da ID do cliente

Se a resposta contém um cabeçalho `X-MSEdge-ClientID`, obtenha o valor e imprima-o.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Obter a resposta

Obtenha a resposta da API. Desserialize o objeto JSON e imprima-o no console.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>Chamar a função de verificação ortográfica

Na função Main do projeto, chame `SpellCheck()`.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="example-json-response"></a>Resposta JSON de exemplo

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorials/spellcheck.md)

- [O que é API de Verificação Ortográfica do Bing?](../overview.md)
- [Referência de API de Verificação Ortográfica do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
