---
title: 'Tutorial: Extrair detalhes da imagem com a API REST e o C# – Pesquisa de Imagem do Bing'
titleSuffix: Azure Cognitive Services
description: Use este tutorial para criar um aplicativo em C# para extrair detalhes da imagem usando a API de Pesquisa de Imagem do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 12/06/2019
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: cef39b940f0d61fc60ea10156acfd781289157de
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591964"
---
# <a name="tutorial-extract-image-details-using-the-bing-image-search-api-and-c"></a>Tutorial: extrair detalhes da imagem usando a API de Pesquisa de Imagem do Bing e C#

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisaram ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Existem vários [pontos de extremidade](./image-search-endpoint.md) disponíveis na API de Pesquisa de Imagem do Bing. O ponto de extremidade `/details` aceita uma solicitação POST com uma imagem e pode retornar uma variedade de detalhes sobre a imagem. Este aplicativo em C# envia uma imagem usando essa API e exibe os detalhes retornados pelo Bing, que são objetos JSON, como os seguintes:

![[Resultados JSON]](media/cognitive-services-bing-images-api/jsonResult.jpg)

Este tutorial explica como:

> [!div class="checklist"]
> * Usar o ponto de extremidade `/details` Pesquisa de Imagem em uma solicitação `POST`
> * Especificar os cabeçalhos da solicitação
> * Usar parâmetros de URL para especificar os resultados
> * Carregar os dados da imagem e enviar a solicitação `POST`
> * Imprimir os resultados JSON para o console

O código-fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingGetSimilarImages.cs).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017 ou posterior](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="construct-an-image-details-search-request"></a>Construir uma solicitação de pesquisa de detalhes da imagem

A seguir está o ponto de extremidade `/details`, que aceita solicitações POST com os dados de imagem no corpo da solicitação. Você pode usar o ponto de extremidade global abaixo ou o ponto de extremidade de [subdomínio personalizado](../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```

Ao construir a URL de solicitação de pesquisa, o parâmetro `modules` segue o ponto de extremidade acima e especifica os tipos dos detalhes que os resultados conterão:

* `modules=All`
* `modules=RecognizedEntities` (pessoas ou locais visíveis na imagem)

Especifique `modules=All` na solicitação POST para obter o texto JSON que inclui o seguinte:

* `bestRepresentativeQuery` - uma consulta do Bing que retorna imagens semelhantes à imagem carregada
* `detectedObjects` – objetos encontrados na imagem
* `image` – metadados da imagem
* `imageInsightsToken` – um token para solicitações GET posteriores que extraem `RecognizedEntities` (pessoas ou lugares visíveis na imagem) da imagem.
* `imageTags` – tags da imagem
* `pagesIncluding` - Páginas da Web que incluem a imagem
* `relatedSearches` – pesquisas baseadas nos detalhes da imagem.
* `visuallySimilarImages` – imagens semelhantes na Web.

Na solicitação POST, especifique que `modules=RecognizedEntities` só extrairá `imageInsightsToken`, que pode ser usado em uma solicitação GET posterior para identificar pessoas ou lugares na imagem.

## <a name="create-a-webclient-object-and-set-headers-for-the-api-request"></a>Criar um objeto WebClient e definir os cabeçalhos para a solicitação de API

Crie um objeto `WebClient` e defina os cabeçalhos. Todas as solicitações para a API de Pesquisa do Bing requerem um `Ocp-Apim-Subscription-Key`. Uma solicitação `POST` para carregar uma imagem também deve especificar `ContentType: multipart/form-data`.

```javascript
WebClient client = new WebClient();
client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
client.Headers["ContentType"] = "multipart/form-data";
```

## <a name="upload-the-image-and-display-the-results"></a>Carregar a imagem e exibir os resultados

O método `UpLoadFile()` da classe `WebClient` formata os dados da solicitação `POST`, incluindo a formatação de `RequestStream` e a chamada para `HttpWebRequest`.

Chame `WebClient.UpLoadFile()` com o ponto de extremidade `/details` e o arquivo de imagem a ser carregado. Use a resposta JSON para inicializar uma instância da estrutura `SearchResult` e armazenar a resposta.

```javascript        
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";
// The image to upload. Replace with your file and path.
const string imageFile = "your-image.jpg";
byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
var json = System.Text.Encoding.Default.GetString(resp);
// Create result object for return
var searchResult = new SearchResult()
{
    jsonResult = json,
    relevantHeaders = new Dictionary<String, String>()
};
```
Depois, essa resposta JSON pode ser impressa no console.

## <a name="use-an-image-insights-token-in-a-request"></a>Usar um token de insights de imagem em uma solicitação

Para usar o `ImageInsightsToken` retornado com os resultados de um `POST`, é necessário adicioná-lo a uma solicitação `GET`. Por exemplo:

```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```

Se houver pessoas ou locais identificáveis na imagem, essa solicitação retornará informações sobre eles.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exibir imagens e opções de pesquisa em um aplicativo Web de página única ](tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Confira também

* [Referência da API de Pesquisa de Imagem do Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)