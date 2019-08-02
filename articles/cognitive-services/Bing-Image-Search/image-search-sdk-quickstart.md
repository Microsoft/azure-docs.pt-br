---
title: 'Início Rápido: Pesquisar imagens – SDK de Pesquisa de Imagem do Bing para C#'
titleSuffix: Azure Cognitive Services
description: Use este início rápido para fazer sua primeira pesquisa de imagem usando o SDK de Pesquisa de Imagem do Bing, que é um wrapper para a API e contém os mesmos recursos. Esse aplicativo C# simples envia uma consulta de pesquisa de imagem, analisa a resposta JSON e exibe a URL da primeira imagem devolvida.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: b45fbf551d17de499b5019e22a4b7174653d9eff
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423866"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-c"></a>Início Rápido: Pesquisar imagens com o SDK de Pesquisa de Imagem do Bing para C#

Use este início rápido para fazer sua primeira pesquisa de imagem usando o SDK de Pesquisa de Imagem do Bing, que é um wrapper para a API e contém os mesmos recursos. Esse aplicativo C# simples envia uma consulta de pesquisa de imagem, analisa a resposta JSON e exibe a URL da primeira imagem devolvida.

O código-fonte para esse exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) com anotações e tratamentos de erro adicionais.

## <a name="prerequisites"></a>Pré-requisitos
* Qualquer edição do [Visual Studio 2017 ou posterior](https://visualstudio.microsoft.com/vs/whatsnew/).
* O [pacote NuGet de Pesquisa de Imagem Cognitiva](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch/1.2.0).

Para instalar o SDK de Pesquisa de Imagem do Bing no Visual Studio, use a opção **Gerenciar Pacotes do NuGet** do **Gerenciador de Soluções**.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Veja também [Cognitive Services Pricing - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

Primeiramente, crie um novo aplicativo de console C# no Visual Studio. Depois, adicione os seguintes pacotes ao seu projeto.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
```

No método principal do seu projeto, crie variáveis para sua chave de assinatura válida, os resultados da imagem que serão devolvidos pelo Bing e um termo de pesquisa. Em seguida, instancie o cliente de pesquisa de imagem usando a chave.

```csharp
//IMPORTANT: replace this variable with your Cognitive Services subscription key
string subscriptionKey = "ENTER YOUR KEY HERE";
//stores the image results returned by Bing
Images imageResults = null;
// the image search term to be used in the query
string searchTerm = "canadian rockies";

//initialize the client
//NOTE: If you're using version 1.2.0 or below for the Bing Image Search SDK, 
// use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.

var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
```

## <a name="send-a-search-query-using-the-client"></a>Envie uma consulta de pesquisa usando o cliente

Use o cliente para pesquisar com um texto de consulta:

```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Analisar e ver o primeiro resultado de imagem

Analise os resultados da imagem retornados na resposta.
Se a resposta contiver os resultados da pesquisa, armazene o primeiro resultado e imprima os detalhes, como URL da miniatura, a URL original, juntamente com o número total de imagens devolvidas.  

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única da Pesquisa de Imagem do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Consulte também

* [O que é a Pesquisa de Imagem do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Experimente uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obtenha uma chave de acesso de Serviços Cognitivos grátis](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Exemplos do .NET para o SDK de Serviços Cognitivos do Azure](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Documentação dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Referência da API de Pesquisa de Imagem do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
