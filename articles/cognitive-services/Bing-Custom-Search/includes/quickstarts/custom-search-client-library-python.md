---
title: Início rápido da biblioteca de clientes Python da Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 3019881c42e0f7b64cc766b8b9e575eb60612432
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947571"
---
Introdução à biblioteca de clientes da Pesquisa Personalizada do Bing para Python. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. A API de Pesquisa Personalizada do Bing permite criar experiências de pesquisa personalizadas sem anúncios para tópicos importantes para você. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Use a biblioteca de clientes da Pesquisa Personalizada do Bing para Python com as seguintes finalidades:
* Localizar os resultados da pesquisa na Web, na sua instância da Pesquisa Personalizada do Bing.

[Documentação de referência](/python/api/azure-cognitiveservices-search-customsearch/) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) | [Pacote (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | [Amostras](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Pré-requisitos

- Uma instância da Pesquisa Personalizada do Bing. Confira [Início Rápido: Criar sua primeira instância da Pesquisa Personalizada do Bing](../../quick-start.md) para obter mais informações.
- Python [2.x ou 3.x](https://www.python.org/) 
- O [SDK da Pesquisa Personalizada do Bing para Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Instalar a biblioteca de clientes do Python

Instale a biblioteca de clientes da Pesquisa Personalizada do Bing com o comando a seguir.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Crie um novo aplicativo

Crie um novo arquivo Python em seu IDE ou editor favorito e adicione as importações a seguir.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Criar um cliente de pesquisa e enviar uma solicitação

1. Crie uma variável para a chave de assinatura e o ponto de extremidade.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Crie uma instância de `CustomSearchClient` usando um objeto `CognitiveServicesCredentials` com a chave da assinatura. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Envie uma solicitação de pesquisa com `client.custom_instance.search()`. Acrescente o termo de pesquisa ao parâmetro `query` e defina `custom_config` para a sua ID de configuração personalizada para usar a instância de pesquisa. Você pode obter sua ID do [portal da Pesquisa Personalizada do Bing](https://www.customsearch.ai/), clicando na guia **Produção**.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Exibir os resultados da pesquisa

Se resultados da pesquisa de páginas da Web foram encontrados, obtenha o primeiro deles e imprima seu nome, a URL e o total de páginas da Web encontradas.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de Pesquisa Personalizada](../../tutorials/custom-search-web-page.md)
