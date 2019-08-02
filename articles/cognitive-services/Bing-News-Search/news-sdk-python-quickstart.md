---
title: 'Início Rápido: executar uma pesquisa de notícias usando o SDK de Pesquisa de Notícias do Bing para Python'
titleSuffix: Azure Cognitive Services
description: Use este Início Rápido para pesquisar notícias usando o SDK de Pesquisa de Notícias do Bing para Python e processe a resposta.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2f6185ca6c2ddd44be2c12a81cedf1adcc2e30cd
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67339018"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Início Rápido: executar uma pesquisa de notícias com o SDK de Pesquisa de Notícias do Bing para Python

Use este início rápido para começar a pesquisar notícias com o SDK de Pesquisa de Notícias do Bing para Python. Embora a Pesquisa de Notícias do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Pré-requisitos

* [Python](https://www.python.org/) 2.x ou 3.x

É recomendável usar um [ambiente virtual](https://docs.python.org/3/tutorial/venv.html) para o desenvolvimento com o Python. Instale e inicialize o ambiente virtual com o [módulo venv](https://pypi.python.org/pypi/virtualenv). É necessário instalar um virtualenv para o Python 2.7. Crie um ambiente virtual com:

```console
python -m venv mytestenv
```

Instale as dependências do SDK de Pesquisa de Notícias do Bing com este comando:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie um arquivo do Python em seu IDE ou editor favorito e importe as bibliotecas a seguir. Crie uma variável para a chave de assinatura e o termo de pesquisa.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Inicializar o cliente e enviar uma solicitação

1. Crie uma instância de `CognitiveServicesCredentials`. Instancie o cliente:
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. Envie uma consulta de pesquisa para a API de Pesquisa de Notícias e armazene a resposta.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Analisar a resposta

Se forem encontrados resultados da pesquisa, imprima o primeiro resultado da página da Web:

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(
        news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](tutorial-bing-news-search-single-page-app.md)
