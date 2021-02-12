---
title: Início rápido da biblioteca de clientes C# da Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: e051eca990ae0aa0b5a79c208a594e1b2332bcb2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97612644"
---
Use este início rápido para fazer sua primeira pesquisa de imagem com a biblioteca de clientes da Pesquisa de Imagem do Bing. 

A biblioteca de pesquisa de clientes é um wrapper para a API REST e contém os mesmos recursos. 

Você criará um aplicativo C# simples que envia uma consulta de pesquisa de imagem, analisa a resposta JSON e exibe a URL da primeira imagem devolvida.

O código-fonte para esse exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) com anotações e tratamentos de erro adicionais.

## <a name="prerequisites"></a>Pré-requisitos

* Se você estiver usando o Windows, qualquer edição do [Visual Studio 2017 ou posterior](https://visualstudio.microsoft.com/vs/whatsnew/)
* Se você estiver usando o macOS ou Linux, [VS Code](https://code.visualstudio.com) com [.NET Core instalado](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
* [Uma assinatura gratuita do Azure](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

Veja também [Cognitive Services Pricing - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-a-console-project"></a>Criar um projeto de console

Primeiro, crie um aplicativo de console do C#.

# <a name="visual-studio"></a>[Visual Studio](#tab/visualstudio)

1. Crie uma solução de console chamada *BingImageSearch* no Visual Studio.
    
1. Adicione o [pacote NuGet de Pesquisa de Imagem Cognitiva](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch)
    1. Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções**.
    1. Selecione **Gerenciar Pacotes NuGet**.
    1. Pesquise e selecione *Microsoft.Azure.CognitiveServices.Search.ImageSearch*, depois instale o pacote.
    
# <a name="vs-code"></a>[Código do VS](#tab/vscode)

1. Abra a janela do terminal no VS Code.
1. Crie um projeto de console chamado *BingImageSearch* inserindo o seguinte código na janela do terminal:
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. Abra a pasta *BingImageSearch* no VS Code.
1. Adicione o [pacote NuGet de Pesquisa de Imagem Cognitiva](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) inserindo o seguinte código na janela do terminal:

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>Inicializar o aplicativo


1. Substitua todas as instruções `using` no *Program.cs* pelo seguinte código:

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. No método `Main` do seu projeto, crie variáveis para a chave de assinatura válida, os resultados da imagem que serão devolvidos pelo Bing e um termo de pesquisa. Em seguida, instancie o cliente de pesquisa de imagem usando a chave.

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>Envie uma consulta de pesquisa usando o cliente
    
Ainda no método `Main`, use o cliente para pesquisar com um texto de consulta:
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Analisar e ver o primeiro resultado de imagem

Analise os resultados da imagem retornados na resposta. 

Se a resposta contiver resultados da pesquisa, armazene o primeiro resultado e imprima alguns de seus detalhes.

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única da Pesquisa de Imagem do Bing](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Confira também

* [O que é a Pesquisa de Imagem do Bing?](../../overview.md)  
* [Experimente uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Exemplos do .NET para o SDK de Serviços Cognitivos do Azure](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Documentação dos Serviços Cognitivos do Azure](../../../index.yml)
* [Referência da API de Pesquisa de Imagem do Bing](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)