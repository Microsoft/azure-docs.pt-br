---
title: 'Início Rápido: Chamar o ponto de extremidade da Pesquisa Personalizada do Bing usando o C# | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Use este início rápido para começar a solicitar os resultados da pesquisa na instância da Pesquisa Personalizada do Bing em C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: maheshb
ms.openlocfilehash: c1b118ec08e00705ad3eab9141d116fb9e6ef257
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405199"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Início Rápido: Chamar o ponto de extremidade da Pesquisa Personalizada do Bing usando o C# 

Use este início rápido para começar a solicitar os resultados da pesquisa na instância da Pesquisa Personalizada do Bing. Embora esse aplicativo seja escrito em C#, a API de Pesquisa Personalizada do Bing é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância da Pesquisa Personalizada do Bing. Confira [Início Rápido: Criar sua primeira instância da Pesquisa Personalizada do Bing](quick-start.md) para obter mais informações.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Qualquer edição do [Visual Studio 2019 ou posterior](https://www.visualstudio.com/downloads/)
- Se você estiver usando Linux/MacOS, este aplicativo poderá ser executado usando [Mono](https://www.mono-project.com/).
- O pacote NuGet [Pesquisa Personalizada do Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). 
    - No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no seu projeto e selecione **Gerenciar Pacotes NuGet** no menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. A instalação do pacote Pesquisa Personalizada do NuGet também instala os assemblies a seguir:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. No Visual Studio, crie um novo aplicativo de console C#. Depois, adicione os seguintes pacotes ao seu projeto.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Crie as seguintes classes para armazenar os resultados da pesquisa retornados pela API de Pesquisa Personalizada do Bing.

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. No método principal do seu projeto, crie variáveis para sua chave de assinatura de API de Pesquisa Personalizada do Bing, a ID de Configuração Personalizada da sua instância de pesquisa e um termo de pesquisa.

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Construa a URL de solicitação acrescentando o termo de pesquisa ao parâmetro de consulta `q=` e a ID de Configuração Personalizada da instância de pesquisa a `customconfig=`. separe os parâmetros com um caractere `&`. 

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber uma solicitação de pesquisa 

1. Crie um cliente de solicitação e adicione sua chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Execute a solicitação de pesquisa e obtenha a resposta como um objeto JSON.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
   ## <a name="process-and-view-the-results"></a>Processar e ver os resultados

3. Itere sobre o objeto de resposta para exibir informações sobre cada resultado da pesquisa, incluindo nome, URL e a data em que a página da Web foi rastreada pela última vez.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de Pesquisa Personalizada](./tutorials/custom-search-web-page.md)
