---
title: Início rápido da biblioteca de clientes Python da Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 5d5aaf84482dae6786ac7fd9f9ee837efca71b34
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105910"
---
A biblioteca de clientes da Pesquisa na Web do Bing facilita a integração da Pesquisa na Web do Bing ao seu aplicativo Python. Neste início rápido, você aprenderá a enviar uma solicitação, receber uma resposta JSON e filtrar e analisar os resultados.

Deseja ver o código agora mesmo? As amostras para as [bibliotecas de clientes da Pesquisa do Bing para Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/search) estão disponíveis no GitHub.


## <a name="prerequisites"></a>Pré-requisitos
O SDK da Pesquisa na Web do Bing é compatível com o Python 2.7 ou 3.6+. É recomendável usar um ambiente virtual para este início rápido.

* Python 2.7 ou 3.6+
* [virtualenv](https://docs.python.org/3/tutorial/venv.html) para Python 2.7
* [venv](https://pypi.python.org/pypi/virtualenv) para Python 3.x

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]

## <a name="create-and-configure-your-virtual-environment"></a>Criar e configurar seu ambiente virtual

As instruções para instalar e configurar um ambiente virtual são diferentes para o Python 2.x e o Python 3.x. Siga as etapas abaixo para criar e inicializar seu ambiente virtual.

### <a name="python-2x"></a>Python 2.x

Criar um ambiente virtual com `virtualenv` para Python 2.7:

```console
virtualenv mytestenv
```

Atualize seu ambiente:

```console
cd mytestenv
source bin/activate
```

Instale dependências de SDK de Pesquisa na Web do Bing:

```console
python -m pip install azure-cognitiveservices-search-websearch
```

### <a name="python-3x"></a>Python 3.x

Criar um ambiente virtual com `venv` para Python 3.x:

```console
python -m venv mytestenv
```

Atualize seu ambiente:

```console
mytestenv\Scripts\activate.bat
```

Instale dependências de SDK de Pesquisa na Web do Bing:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```

## <a name="create-a-client-and-print-your-first-results"></a>Criar um cliente e imprima seus primeiros resultados

Agora que você configurou o ambiente virtual e instalou as dependências, vamos criar um cliente. O cliente manipulará solicitações e respostas da API de Pesquisa na Web do Bing.

Se a resposta contiver páginas da Web, imagens, notícias ou vídeos, o primeiro resultado para cada um será impresso.

1. Crie um novo projeto Python usando seu IDE ou editor favorito.

1. Copie este exemplo de código em seu projeto. `endpoint` pode ser o ponto de extremidade global abaixo ou o ponto de extremidade do [subdomínio personalizado](~/articles/cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.  

    ```python
    # Import required modules.
    from azure.cognitiveservices.search.websearch import WebSearchClient
    from azure.cognitiveservices.search.websearch.models import SafeSearch
    from msrest.authentication import CognitiveServicesCredentials

    # Replace with your subscription key.
    subscription_key = "YOUR_SUBSCRIPTION_KEY"

    # Instantiate the client and replace with your endpoint.
    client = WebSearchClient(endpoint="YOUR_ENDPOINT", credentials=CognitiveServicesCredentials(subscription_key))

    # Make a request. Replace Yosemite if you'd like.
    web_data = client.web.search(query="Yosemite")
    print("\r\nSearched for Query# \" Yosemite \"")

    '''
    Web pages
    If the search response contains web pages, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.web_pages, 'value'):

        print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

        first_web_page = web_data.web_pages.value[0]
        print("First web page name: {} ".format(first_web_page.name))
        print("First web page URL: {} ".format(first_web_page.url))

    else:
        print("Didn't find any web pages...")

    '''
    Images
    If the search response contains images, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.images, 'value'):

        print("\r\nImage Results#{}".format(len(web_data.images.value)))

        first_image = web_data.images.value[0]
        print("First Image name: {} ".format(first_image.name))
        print("First Image URL: {} ".format(first_image.url))

    else:
        print("Didn't find any images...")

    '''
    News
    If the search response contains news, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.news, 'value'):

        print("\r\nNews Results#{}".format(len(web_data.news.value)))

        first_news = web_data.news.value[0]
        print("First News name: {} ".format(first_news.name))
        print("First News URL: {} ".format(first_news.url))

    else:
        print("Didn't find any news...")

    '''
    If the search response contains videos, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.videos, 'value'):

        print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

        first_video = web_data.videos.value[0]
        print("First Videos name: {} ".format(first_video.name))
        print("First Videos URL: {} ".format(first_video.url))

    else:
        print("Didn't find any videos...")
    ```

1. Substitua `SUBSCRIPTION_KEY` por uma chave de assinatura válida.

1. Substitua `YOUR_ENDPOINT` pela URL do ponto de extremidade no portal e remova a seção "bing/v7.0" do ponto de extremidade.

1. Execute o programa. Por exemplo: `python your_program.py`.

## <a name="define-functions-and-filter-results"></a>Definir funções e filtrar resultados

Agora que você fez sua primeira chamada à API da Pesquisa na Web do Bing, vamos examinar algumas funções. As seções a seguir destacam a funcionalidade do SDK para refinar consultas e filtrar resultados. Cada função pode ser adicionada ao programa Python criado na seção anterior.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Limitar o número de resultados retornados pelo Bing

Este exemplo usa os parâmetros `count` e `offset` para limitar o número de resultados retornados usando o [método `search`](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations) do SDK. São impressos o `name` e a `url` para o primeiro resultado.

1. Adicione este código ao seu projeto Python:

   ```python
    # Declare the function.
    def web_results_with_count_and_offset(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, offset, and count using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
            print("\r\nSearching for \"Best restaurants in Seattle\"")

            if web_data.web_pages.value:
                '''
                If web pages are available, print the # of responses, and the first and second
                web pages returned.
                '''
                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't find any web pages...")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. Execute o programa.

### <a name="filter-for-news-and-freshness"></a>Filtro para notícias e atualização

Este exemplo usa os parâmetros `response_filter` e `freshness` para filtrar os resultados da pesquisa usando o método [`search` do SDK](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations). Os resultados da pesquisa retornados são limitados a artigos de notícias e as páginas que Bing descobriu nas últimas 24 horas. São impressos o `name` e a `url` para o primeiro resultado.

1. Adicione este código ao seu projeto Python:

    ```python
    # Declare the function.
    def web_search_with_response_filter(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
        try:
            '''
            Set the query, response_filter, and freshness using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="xbox",
                response_filter=["News"],
                freshness="Day")
            print("\r\nSearching for \"xbox\" with the response filter set to \"News\" and freshness filter set to \"Day\".")

            '''
            If news articles are available, print the # of responses, and the first and second
            articles returned.
            '''
            if web_data.news.value:

                print("# of news results: {}".format(len(web_data.news.value)))

                first_web_page = web_data.news.value[0]
                print("First article name: {} ".format(first_web_page.name))
                print("First article URL: {} ".format(first_web_page.url))

                print("")

                second_web_page = web_data.news.value[1]
                print("\nSecond article name: {} ".format(second_web_page.name))
                print("Second article URL: {} ".format(second_web_page.url))

            else:
                print("Didn't find any news articles...")

        except Exception as err:
            print("Encountered exception. {}".format(err))

    # Call the function.
    web_search_with_response_filter(subscription_key)
    ```

1. Execute o programa.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Use a pesquisa segura, a contagem de respostas e o filtro de promoção

Este exemplo usa os parâmetros `answer_count`, `promote` e `safe_search` para filtrar os resultados da pesquisa usando o [método `search`](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations) do SDK. São exibidos o `name` e a `url` para o primeiro resultado.

1. Adicione este código ao seu projeto Python:

    ```python
    # Declare the function.
    def web_search_with_answer_count_promote_and_safe_search(subscription_key):

        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, answer_count, promote, and safe_search parameters using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(
                query="Niagara Falls",
                answer_count=2,
                promote=["videos"],
                safe_search=SafeSearch.strict  # or directly "Strict"
            )
            print("\r\nSearching for \"Niagara Falls\"")

            '''
            If results are available, print the # of responses, and the first result returned.
            '''
            if web_data.web_pages.value:

                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't see any Web data..")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. Execute o programa.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você terminar este projeto, remova sua chave de assinatura do código do programa e desative o seu ambiente virtual.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exemplos do SDK do Python dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="see-also"></a>Confira também

* [Referência de SDK do Python do Azure](/python/api/azure-cognitiveservices-search-websearch/)
