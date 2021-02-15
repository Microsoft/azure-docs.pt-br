---
title: Guia de início rápido da biblioteca de clientes Python da Pesquisa de Vídeo do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: 7a9fab8ba8bb9d21c9284cbf14bc67226d2ef9d3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "80289856"
---
Use este guia de início rápido para começar a pesquisar notícias com a biblioteca de clientes da Pesquisa de Vídeo do Bing para o Python. Embora a Pesquisa de Vídeo do Bing tenha uma API REST compatível com a maioria das linguagens de programação, a biblioteca de clientes é uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte desse exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) com anotações e recursos adicionais.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Python](https://www.python.org/) 2.x ou 3.x
- A biblioteca de clientes da Pesquisa de Vídeo do Bing para Python

É recomendável usar um [ambiente virtual](https://docs.python.org/3/tutorial/venv.html) do Python. Instale e inicialize o ambiente virtual com o [módulo venv](https://pypi.python.org/pypi/virtualenv). Instale o virtualenv para o Python 2.7 com:

```console
python -m venv mytestenv
```

Instalar a biblioteca de clientes da Pesquisa de Vídeo do Bing com:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie um novo arquivo Python em seu IDE ou o editor favorito e adicione as seguintes instruções de importação. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchClient
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Criar uma variável para a chave de assinatura. 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    ```

## <a name="create-the-search-client"></a>Crie o cliente de pesquisa

Crie uma instância do `CognitiveServicesCredentials` e instancie o cliente:

```python
client = VideoSearchAPI(endpoint, CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>Enviar uma solicitação de pesquisa e obter uma resposta

1. Use `client.videos.search()` com sua consulta de pesquisa para enviar uma solicitação para a API de Pesquisa de Vídeo do Bing e obter uma resposta.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Se a resposta contiver os resultados da pesquisa, obtenha o primeiro deles e imprima sua ID, seu nome e sua URL.

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Confira também 

- [O que é a API da Pesquisa de Vídeo do Bing?](../../overview.md)
- [Exemplos de SDK do .NET dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
