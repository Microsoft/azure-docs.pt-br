---
title: 'Início Rápido: pesquisar imagens usando a API REST de Pesquisa de Imagem do Bing e Python'
titleSuffix: Azure Cognitive Services
description: Use este Início Rápido para enviar solicitações de pesquisa de imagens para a API REST de Pesquisa de Imagem do Bing usando Python e receba respostas JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 0d0f3e3578f1fe40794ec5697291d35e3d277419
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341726"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Início Rápido: pesquisar imagens usando a API REST de Pesquisa de Imagem do Bing e Python

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Use este guia de início rápido para aprender a enviar solicitações de pesquisa para a API da Pesquisa de Imagem do Bing. Esse aplicativo Python envia uma consulta de pesquisa para a API e exibe a URL da primeira imagem nos resultados. Embora esse aplicativo seja escrito em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Para executar este exemplo como um notebook Jupyter em [MyBinder](https://mybinder.org), selecione a notificação **iniciar associador**:

[![Associador](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


O código-fonte para esse exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) com anotações e tratamentos de erro adicionais.


## <a name="prerequisites"></a>Pré-requisitos

* [Python 2.x ou 3.x](https://www.python.org/)
* A [PIL (Biblioteca de Imagens Python)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie um novo arquivo do Python em seu IDE ou editor favorito e importe os módulos a seguir. Crie uma variável para a chave de assinatura, um ponto de extremidade de pesquisa e um termo de pesquisa. Para `search_url`, você pode usar o ponto de extremidade global no código a seguir ou usar o ponto de extremidade do [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Adicione sua chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key` criando um dicionário e adicionando a chave como um valor. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Criar e enviar uma solicitação de pesquisa

1. Crie um dicionário para os parâmetros da solicitação de pesquisa. Adicione o termo de pesquisa ao parâmetro `q`. Defina o parâmetro `license` como `public` para pesquisar imagens no domínio público. Defina `imageType` como `photo` para pesquisar apenas fotos.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Use a biblioteca `requests` para chamar a API de Pesquisa de Imagem do Bing. Adicione o cabeçalho e os parâmetros à solicitação e retorne a resposta como um objeto JSON. Obtenha as URLs para várias imagens em miniatura do campo `thumbnailUrl` da resposta.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
    ```

## <a name="view-the-response"></a>Exibir a resposta

1. Crie uma figura com quatro colunas e quatro linhas usando a biblioteca matplotlib. 

2. Itere nas linhas e colunas da figura e use o método `Image.open()` da biblioteca PIL para adicionar uma miniatura de imagem a cada espaço. 

3. Use `plt.show()` para desenhar a figura e exibir as imagens.

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    plt.show()
    ```


## <a name="example-json-response"></a>Resposta JSON de exemplo

As respostas da API de Pesquisa de Imagem do Bing são retornadas como JSON. Este exemplo de resposta foi truncado para mostrar um único resultado.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única da Pesquisa de Imagem do Bing](../tutorial-bing-image-search-single-page-app.md)

* [O que é a API de Pesquisa de Imagem do Bing?](../overview.md)  
* [Detalhes de preço para as APIs de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Documentação dos Serviços Cognitivos do Azure](../../index.yml)
* [Referência da API de Pesquisa de Imagem do Bing](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)