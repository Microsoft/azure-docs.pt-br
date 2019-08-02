---
title: 'Tutorial: Recortar uma imagem no SDK da Pesquisa Visual do Bing'
description: Use o SDK da Pesquisa Visual do Bing para obter ideias de ares específicos em uma imagem.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/26/2019
ms.author: rosh
ms.openlocfilehash: a65994ffb2fc707c59e507cad71699211b6a64a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65896263"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Tutorial: Recortar uma imagem no SDK da Pesquisa Visual do Bing para C#

O SDK do Visual do Bing Search permite que você recortar uma imagem antes de encontrar imagens online semelhantes. Esse aplicativo recorta uma única pessoa a partir de uma imagem que contém várias pessoas e, em seguida, retorna resultados que contêm imagens semelhantes encontradas online.

O código-fonte completo desse aplicativo está disponível com anotações e tratamento de erro adicionais no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Esse tutorial ilustra como:

> [!div class="checklist"]
> * Enviar uma solicitação usando o SDK da Pesquisa Visual do Bing
> * Recortar uma área de imagem para pesquisar com a Pesquisa Visual do Bing
> * Receber e lidar com a resposta
> * Encontrar as URLs de itens de ação na resposta

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Se você estiver usando Linux/MacOS, este aplicativo poderá ser executado usando [Mono](https://www.mono-project.com/).
* O pacote [Pesquisa Personalizada do NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) instalado.
    - Gerenciador de soluções no Visual Studio, clique com botão direito no seu projeto e selecione **gerenciar pacotes NuGet** no menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. A instalação do pacote Pesquisa Personalizada do NuGet também instala os assemblies a seguir:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Especificar a área de imagem recortada

Este aplicativo corta uma área da imagem do Time de liderança sênior Microsoft. Essa área de corte é definida usando as coordenadas do canto superior esquerdo e inferior direito, representadas como uma porcentagem da imagem inteira:  

![Time de Liderança Sênior Microsoft](./media/MS_SrLeaders.jpg)

Essa imagem é cortada, criando um `ImageInfo`objeto da área de corte e carrega o `ImageInfo` objeto em um `VisualSearchRequest`. O `ImageInfo` objeto também inclui a URL da imagem:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Procure por imagens similares à área de corte

A variável `VisualSearchRequest` contém informações sobre a área de corte da imagem e sua URL. O `VisualSearchMethodAsync()` método obtém os resultados:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Obter os dados de URL do `ImageModuleAction`

Resultados de Pesquisa Visual do Bing são objetos `ImageTag`. Cada etiqueta contém uma lista de `ImageAction` objetos. Cada `ImageAction` contém um `Data` campo, que é uma lista de valores que dependem do tipo de ação.

Você consegue imprimir vários tipos com o seguinte código:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

A aplicação completa retorna:

|ActionType  |URL  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entidade -> WebSearchUrl     | [https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&amp;CID=03DCF882D7386A442137F49BD6596BEF&amp;rd=1&amp;h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&amp;v=1&amp;r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&amp;p=DevEx,5380.1](https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1)        |
|TopicResults -> WebSearchUrl    |  [https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&amp;CID=03DCF882D7386A442137F49BD6596BEF&amp;rd=1&amp;h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&amp;v=1&amp;r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&amp;p=DevEx,5382.1](https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1)        |
|ImageResults -> WebSearchUrl    |  [https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&amp;CID=03DCF882D7386A442137F49BD6596BEF&amp;rd=1&amp;h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&amp;v=1&amp;r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&amp;p=DevEx,5384.1](https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1)        |

Como mostrado acima, o ActionType `Entity` contém uma consulta de Pesquisa do Bing que retorna informação sobre uma pessoa reconhecida, lugar ou coisa. Os tipos `TopicResults` e `ImageResults` contêm consultas para imagens relacionadas. As URLs na lista vinculam os resultados de pesquisa do Bing.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Obter URLs para `PagesIncluding` `ActionType` imagens

Obter as URLs de imagem real requer que uma conversão que lê um `ActionType` como `ImageModuleAction`, que contém um `Data` elemento com uma lista de valores. Cada valor é a URL de uma imagem. As conversões a seguir a `PagesIncluding` tipo de ação para `ImageModuleAction` e lê os valores:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única da Pesquisa Visual](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Consulte também
> [O que é a API de pesquisa Visual do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
