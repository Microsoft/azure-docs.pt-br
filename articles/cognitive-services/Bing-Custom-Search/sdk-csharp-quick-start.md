---
title: 'Início Rápido: Chamar o ponto de extremidade da Pesquisa Personalizada do Bing usando o SDK do C# | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Use o SDK da Pesquisa Personalizada do Bing para C# para pesquisar sua instância de pesquisa personalizada.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: scottwhi
ms.openlocfilehash: 0381df439d0c0904e8741bb1f31b179566c72ec5
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206171"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>Início Rápido: Chamar o ponto de extremidade da Pesquisa Personalizada do Bing usando o SDK do C# 

Use este início rápido para começar a solicitar os resultados da pesquisa na instância da Pesquisa Personalizada do Bing, usando o SDK do C#. Embora a Pesquisa Personalizada do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK da Pesquisa Personalizada do Bing fornece uma maneira fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância da Pesquisa Personalizada do Bing. Confira [Início Rápido: Criar sua primeira instância da Pesquisa Personalizada do Bing](quick-start.md) para obter mais informações.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Qualquer edição do [Visual Studio 2017 ou posterior](https://www.visualstudio.com/downloads/)
- Se você estiver usando Linux/MacOS, este aplicativo poderá ser executado usando [Mono](https://www.mono-project.com/).
- O pacote NuGet [Pesquisa Personalizada do Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). 
    - No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no seu projeto e selecione **Gerenciar Pacotes NuGet** no menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. A instalação do pacote Pesquisa Personalizada do NuGet também instala os assemblies a seguir:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. No Visual Studio, crie um novo aplicativo de console C#. Depois, adicione os seguintes pacotes ao seu projeto.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. No método principal do aplicativo, crie uma instância do cliente de pesquisa com sua chave de API.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Enviar a solicitação de pesquisa e receber uma resposta
    
1. Envie uma consulta de pesquisa usando o método `SearchAsync()` do cliente e salve a resposta. Substitua a `YOUR-CUSTOM-CONFIG-ID` pela ID de configuração da instância (encontre a ID no [portal da Pesquisa Personalizada do Bing](https://www.customsearch.ai/)). Este exemplo pesquisa "Xbox".

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. O método `SearchAsync()` retorna um objeto `WebData`. Use o objeto para iterar em todas as `WebPages` encontradas. Esse código encontra o primeiro resultado da página da Web e imprime `Name` e `URL` da página da Web.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)
