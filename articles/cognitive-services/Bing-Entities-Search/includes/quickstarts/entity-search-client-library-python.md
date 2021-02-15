---
title: Início rápido da biblioteca de clientes Python da Pesquisa de Entidade do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 0c0a124773eab8166806312dc47ded24e1cd841f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "79136596"
---
Use este início rápido para começar a pesquisar entidades com a biblioteca de clientes da Pesquisa de Entidade do Bing para o Python. Embora a Pesquisa de Entidade do Bing tenha uma API REST compatível com a maioria das linguagens de programação, a biblioteca de clientes é uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Pré-requisitos

* Python [2.x ou 3.x](https://www.python.org/)

* O [SDK da Pesquisa de Entidade do Bing para o Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

É recomendável usar um ambiente virtual do Python. Instale e inicialize o ambiente virtual com o módulo venv. Você pode instalar o virtualenv com:

```Console
python -m venv mytestenv
```

Instalar a biblioteca de clientes da Pesquisa de Entidade do Bing com:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie um novo arquivo Python em seu IDE ou o editor favorito e adicione as seguintes instruções de importação. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Crie uma variável para a chave de assinatura e o ponto de extremidade. Crie uma instância do cliente criando um objeto `CognitiveServicesCredentials` com a chave.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Enviar uma solicitação de pesquisa e receber uma resposta

1. Envie uma solicitação de pesquisa para a Pesquisa de Entidade do Bing com `client.entities.search()` e uma consulta de pesquisa. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Se alguma entidade for retornada, converta `entity_data.entities.value` em uma lista e imprima o primeiro resultado.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidade do Bing?](../../overview.md )