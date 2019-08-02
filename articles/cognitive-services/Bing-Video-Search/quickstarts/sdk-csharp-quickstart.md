---
title: 'Início Rápido: Pesquisar vídeos usando o SDK de Pesquisa de Vídeo do Bing paraC#'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para enviar solicitações de pesquisa de vídeo usando o SDK de Pesquisa de Vídeo do Bing para C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 3673f18ff38b2ae98180f470b9f76f1fc57ee8b6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442528"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Início Rápido: Executar uma pesquisa de vídeo com o SDK de Pesquisa de Vídeo do Bing para C#

Use este início rápido para começar a pesquisar notícias com o SDK de Pesquisa de Vídeo do Bing para C#. Embora a Pesquisa de Vídeo do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço aos seus aplicativos. O código-fonte desse exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) com anotações e recursos adicionais.

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017 ou posterior](https://visualstudio.microsoft.com/downloads/).
* A estrutura Json.NET, disponível [como um pacote do NuGet](https://www.nuget.org/packages/Newtonsoft.Json/).

Para adicionar o SDK de Pesquisa de Vídeo do Bing ao seu projeto, selecione **Gerenciar Pacotes do NuGet** no **Gerenciador de Soluções** do Visual Studio. Adicione o pacote `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

Instalar o pacote [[SDK de Pesquisa de Vídeo do NuGet]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) também instala as seguintes dependências:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie uma solução de console C# no Visual Studio. Em seguida, adicione os itens a seguir ao arquivo de código principal.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Instanciar um cliente criando um novo objeto `ApiKeyServiceClientCredentials` com sua chave de assinatura e chamando o construtor.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Enviar uma solicitação de pesquisa e processar os resultados

1. Use o cliente para enviar uma solicitação de pesquisa. Use "SwiftKey" para a consulta de pesquisa.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Se algum resultado for retornado, obtenha o primeiro com `videoResults.Value[0]`. Em seguida, imprima a ID, o título e a URL do vídeo.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Consulte também 

* [O que é a API da Pesquisa de Vídeo do Bing?](../overview.md)
* [Exemplos de SDK do .NET dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
