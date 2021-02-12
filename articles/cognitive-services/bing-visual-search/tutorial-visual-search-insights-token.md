---
title: Encontrar imagens semelhantes de pesquisas anteriores usando tokens de insights de imagem e a API da Pesquisa Visual do Bing
titleSuffix: Azure Cognitive Services
description: Use a biblioteca de clientes da Pesquisa Visual do Bing para obter URLs de imagens de pesquisas anteriores.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a9005082148803423ac20b4f18dd974bb5b43b80
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486854"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Tutorial: Localizar imagens semelhantes de pesquisas anteriores usando o token de insights de imagem

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisaram ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A biblioteca de clientes da Pesquisa Visual permite que você encontre imagens online de pesquisas anteriores que retornam um `ImageInsightsToken`. Este aplicativo obtém um `ImageInsightsToken` e usa o token em uma pesquisa subsequente. Em seguida, envia o `ImageInsightsToken` para o Bing e retorna os resultados que incluem as URLs de Pesquisa do Bing e as URLs de imagens semelhantes encontradas online.

O código-fonte completo desse tutorial pode ser encontrado com anotações e tratamento de erro adicionais no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Se você estiver usando Linux/MacOS, poderá executar usando o [Mono](https://www.mono-project.com/).
* O pacotes de Pesquisa Visual do NuGet e Pesquisa de Imagem.
    - No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no seu projeto e selecione **Gerenciar Pacotes do NuGet** no menu. Instalar o `Microsoft.Azure.CognitiveServices.Search.CustomSearch` pacote e o `Microsoft.Azure.CognitiveServices.Search.ImageSearch` pacote. Instalar os pacotes do NuGet também instala o seguinte:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-client-library"></a>Obter o ImageInsightsToken da biblioteca de clientes da Pesquisa de Imagem do Bing

Esse aplicativo usa um `ImageInsightsToken` obtido por meio da [biblioteca de clientes da Pesquisa de Imagem do Bing](../bing-image-search/quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp). Em um aplicativo de console C#, crie um cliente para chamar a API usando `ImageSearchClient()`. Em seguida, use `SearchAsync()` com sua consulta:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
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

Este `ImageInsightsToken` é enviado à Pesquisa Visual do Bing em uma solicitação.

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

Resultados de Pesquisa Visual são objetos `ImageTag`. Cada etiqueta contém uma lista de `ImageAction` objetos. Cada `ImageAction` contém um campo `Data` que é uma lista de valores que dependem do tipo de ação. Você pode iterar por meio de `ImageTag` objetos no `visualSearchResults.Tags`, por instância e obter a `ImageAction` marca dentro dele. O exemplo a seguir imprime os detalhes das ações `PagesIncluding`:

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

Obter as URLs de imagem real requer que uma conversão que lê um `ActionType` como `ImageModuleAction`, que contém um `Data` elemento com uma lista de valores. Cada valor é a URL de uma imagem.  O seguinte converte o tipo de ação `PagesIncluding` para `ImageModuleAction` e lê os valores:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Pra obter informações sobre esses tipos de dados, consulte [Imagens - Pesquisa Visual](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="returned-urls"></a>URLs retornadas

O aplicativo completo retorna as seguintes URLs:

|ActionType  |URL  |
|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2f www.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2f www.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Como mostrado acima, os tipos `TopicResults` e `ImageResults` contêm consultas de imagens relacionadas. As URLs na lista vinculam os resultados de pesquisa do Bing.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única da Pesquisa Visual](tutorial-bing-visual-search-single-page-app.md)