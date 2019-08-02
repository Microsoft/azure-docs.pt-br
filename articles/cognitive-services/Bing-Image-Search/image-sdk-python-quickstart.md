---
title: 'Início Rápido: Pesquisar imagens – SDK de Pesquisa de Imagem do Bing para Python'
titleSuffix: Azure Cognitive Services
description: Use este início rápido para fazer sua primeira pesquisa de imagem usando o SDK de Pesquisa de Imagem do Bing, que é um wrapper para a API e contém os mesmos recursos. Esse aplicativo simples do Python envia uma consulta de pesquisa de imagem, analisa a resposta JSON e exibe a URL da primeira imagem devolvida.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 19ee6551edae2e27d7dd8aae9ba6a65e4e738cff
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341355"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-python"></a>Início Rápido: Pesquisar imagens com o SDK de Pesquisa de Imagem do Bing para Python

Use este início rápido para fazer sua primeira pesquisa de imagem usando o SDK de Pesquisa de Imagem do Bing, que é um wrapper para a API e contém os mesmos recursos. Esse aplicativo simples do Python envia uma consulta de pesquisa de imagem, analisa a resposta JSON e exibe a URL da primeira imagem devolvida.

O código-fonte para esse exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) com anotações e tratamentos de erro adicionais.

## <a name="prerequisites"></a>Pré-requisitos
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em **Pesquisar**.  Veja também [Cognitive Services Pricing - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

* [Python 2.7 ou 3.4](https://www.python.org/) e superior.

* O [SDK de Pesquisa de Imagem do Azure](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) para Python
    * Instale usando `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie um novo script Python em seu IDE ou editor favorito e as seguintes importações:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Crie variáveis para o termo de pesquisa e a chave de assinatura.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Crie o cliente de Pesquisa de Imagem

1. Crie uma instância do `CognitiveServicesCredentials` e use-a para instanciar o cliente:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
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
> [Tutorial de aplicativo de página única da Pesquisa de Imagem do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Consulte também

* [O que é a Pesquisa de Imagem do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Experimente uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obtenha uma chave de acesso de Serviços Cognitivos grátis](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Exemplos do Python para o SDK de Serviços Cognitivos do Azure](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Documentação dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Referência da API de Pesquisa de Imagem do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
