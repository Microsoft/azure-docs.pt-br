---
title: Localizar imagens semelhantes de pesquisas anteriores usando ImageInsightsToken - Pesquisa Visual do Bing
titleSuffix: Azure Cognitive Services
description: Usar o SDK de pesquisa Visual do Bing para obter URLs das imagens especificadas pelo ImageInsightsToken.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/05/2019
ms.author: rosh
ms.openlocfilehash: 39a95e877c766eb8f491c166edeb9d96f21db7dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829507"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Localizar imagens semelhantes de pesquisas anteriores usando ImageInsightsToken

O SDK de Pesquisa Visual possibilita que você encontre imagens online de pesquisas anteriores que retornam um `ImageInsightsToken`. Este aplicativo obtém um `ImageInsightsToken` e usa o token em uma pesquisa subsequente. Em seguida, ele envia o `ImageInsightsToken` ao Bing e retornar resultados que incluem URLs de pesquisa do Bing e URLs de imagens similares encontradas online.

O código-fonte completo para este tutorial pode ser encontrado com anotações e tratamento de erro adicionais no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInisghtsTokens.cs).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Se você estiver usando Linux/MacOS, você pode executar esse aplicativo usando [Mono](https://www.mono-project.com/).
* O pacotes de Pesquisa Visual do NuGet e Pesquisa de Imagem.
    - Gerenciador de soluções no Visual Studio, clique com botão direito no seu projeto e selecione **gerenciar pacotes NuGet** no menu. Instalar o `Microsoft.Azure.CognitiveServices.Search.CustomSearch` pacote e o `Microsoft.Azure.CognitiveServices.Search.ImageSearch` pacote. Instalar os pacotes do NuGet também instala o seguinte:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Obter o ImageInsightsToken do SDK da Pesquisa de Imagem do Bing

Esse aplicativo usa um `ImageInsightsToken` obtido por meio do [SDK de Pesquisa de Imagem do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). Em um aplicativo de console C#, crie um cliente para chamar a API usando `ImageSearchAPI()`. Em seguida, usar `SearchAsync()` com sua consulta:

```csharp
var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Armazene o primeiro resultado da pesquisa usando `imageResults.Value.First()`e, em seguida, armazene o insight de imagem `ImageInsightsToken`.

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Isso `ImageInsightsToken` é enviada para pesquisa Visual do Bing em uma solicitação.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Adicionar o ImageInsightsToken a uma solicitação de Pesquisa Visual

Especifique `ImageInsightsToken` para uma solicitação de Pesquisa Visual, criando um `ImageInfo` objeto de `ImageInsightsToken` contido nas respostas da Pesquisa Visual do Bing.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Use a Pesquisa Visual do Bing para encontrar imagens de um ImageInsightsToken

O `VisualSearchRequest` objeto contém informações sobre a imagem no `ImageInfo` a ser pesquisada. O método `VisualSearchMethodAsync()` consegue os resultados. Você não precisa fornecer um binário de imagem, uma vez que a imagem é representada pelo token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iterar pelos resultados da Pesquisa Visual

Resultados de Pesquisa Visual são objetos `ImageTag`. Cada etiqueta contém uma lista de `ImageAction` objetos. Cada `ImageAction` contém um `Data` campo, que é uma lista de valores que dependem do tipo de ação. Você pode iterar por meio de `ImageTag` objetos no `visualSearchResults.Tags`, por instância e obter a `ImageAction` marca dentro dele. O exemplo a seguir imprime os detalhes de `PagesIncluding` ações:

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

Obter as URLs de imagem real requer que uma conversão que lê um `ActionType` como `ImageModuleAction`, que contém um `Data` elemento com uma lista de valores. Cada valor é a URL de uma imagem.  As conversões a seguir a `PagesIncluding` tipo de ação para `ImageModuleAction` e lê os valores:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Pra obter informações sobre esses tipos de dados, consulte [Imagens - Pesquisa Visual](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="returned-urls"></a>URLs retornadas

O aplicativo completo retorna as seguintes URLs:

|ActionType  |URL  | |
|---------|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Como mostrado acima, os tipos `TopicResults` e `ImageResults` contêm consultas de imagens relacionadas. As URLs na lista vinculam os resultados de pesquisa do Bing.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única da Pesquisa Visual](tutorial-bing-visual-search-single-page-app.md)
