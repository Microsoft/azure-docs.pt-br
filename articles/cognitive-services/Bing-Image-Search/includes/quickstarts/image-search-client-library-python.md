---
title: Início rápido da biblioteca de clientes Python da Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 338e4f0c1c47a7786e21ad98bb3e56a1505f9bb2
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105644"
---
Use este início rápido para fazer sua primeira pesquisa de imagem usando a biblioteca de clientes da Pesquisa de Imagem do Bing, que é um wrapper para a API e contém os mesmos recursos. Esse aplicativo simples do Python envia uma consulta de pesquisa de imagem, analisa a resposta JSON e exibe a URL da primeira imagem devolvida.

O código-fonte para esse exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) com anotações e tratamentos de erro adicionais.

## <a name="prerequisites"></a>Pré-requisitos

* [Python 2.7 ou 3.6 e posterior](https://www.python.org/).

* A [Biblioteca de clientes da Pesquisa de Imagem do Azure](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) para Python
    * Instale usando `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie um novo script Python em seu IDE ou editor favorito e as seguintes importações:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Crie variáveis para o termo de pesquisa e a chave de assinatura.

    ```python
    subscription_key = "Enter your key here"
    subscription_endpoint = "Enter your endpoint here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Crie o cliente de Pesquisa de Imagem

1. Crie uma instância do `CognitiveServicesCredentials` e use-a para instanciar o cliente:

    ```python
    client = ImageSearchClient(endpoint=subscription_endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```
1. Enviar a consulta de pesquisa para a API de Pesquisa de Imagem do Bing:
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>Processar e ver os resultados

Analise os resultados da imagem retornados na resposta.


Se a resposta contiver os resultados da pesquisa, armazene o primeiro resultado e imprima os detalhes, como URL da miniatura, a URL original, juntamente com o número total de imagens devolvidas.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única da Pesquisa de Imagem do Bing](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Confira também

* [O que é a Pesquisa de Imagem do Bing?](../../overview.md)  
* [Experimente uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Exemplos do Python para o SDK de Serviços Cognitivos do Azure](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Documentação dos Serviços Cognitivos do Azure](../../../index.yml)
* [Referência da API de Pesquisa de Imagem do Bing](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)