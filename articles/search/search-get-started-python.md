---
title: 'Início rápido do Python: Criar, carregar e consultar índices usando Azure Search APIs REST-Azure Search'
description: Explica como criar um índice, carregar dados e executar consultas usando Python, notebooks Jupyter e a API REST do Azure Search.
ms.date: 07/11/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 1c570549514ff5a5e7e598aa54d8e2ac4b5a5341
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849792"
---
# <a name="quickstart-create-an-azure-search-index-in-python-using-jupyter-notebooks"></a>Início Rápido: Criar um índice de Azure Search no Python usando notebooks Jupyter
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postmaster (REST)](search-get-started-postman.md)
> * [Portal](search-create-index-portal.md)
> 

Crie um bloco de anotações Jupyter que cria, carrega e consulta um índice de Azure Search usando Python e as [APIs REST de Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Este artigo explica como criar um bloco de anotações passo a passo. Como alternativa, você pode [baixar e executar um notebook Jupyter Python concluído](https://github.com/Azure-Samples/azure-search-python-samples).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços e ferramentas são necessários para este guia de início rápido. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), que fornece o Python 3.x e os Notebooks Jupyter.

+ [Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar a camada gratuita para este guia de início rápido. 

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas REST exigem a URL do serviço e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Search à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="connect-to-azure-search"></a>Conectar-se ao Azure Search

Nesta tarefa, inicie um notebook Jupyter e verifique se você pode se conectar ao Azure Search. Você fará isso solicitando uma lista de índices do seu serviço. No Windows com Anaconda3, você pode usar o Anaconda Navigator para iniciar um bloco de anotações.

1. Crie um novo notebook Python3.

1. Na primeira célula, carregue as bibliotecas usadas para trabalhar com solicitações HTTP JSON e formular.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Na segunda célula, insira os elementos de solicitação que serão constantes em cada solicitação. Substitua o nome do serviço de pesquisa (NOME-DO-SERVIÇO-DE-PESQUISA) e a chave de API de administração (CHAVE-DE-API-DE-ADMINISTRAÇÃO) por valores válidos. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. Na terceira célula, Formule a solicitação. Essa solicitação GET visa a coleção de índices do serviço de pesquisa e seleciona a propriedade nome dos índices existentes.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Execute cada etapa. Se houver índices, a resposta conterá uma lista de nomes de índice. Na captura de tela abaixo, o serviço já tem um índice azureblob e realestate-US-Sample.

   ![Script Python no notebook Jupyter com solicitações HTTP para Azure Search](media/search-get-started-python/connect-azure-search.png "Script Python no notebook Jupyter com solicitações HTTP para Azure Search")

   Por outro lado, uma coleção de índice vazia retorna essa resposta:`{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - Criar um índice

A menos que você esteja usando o portal, um índice deve existir no serviço antes que você possa carregar dados. Esta etapa usa a [API REST criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index) para enviar por push um esquema de índice para o serviço.

Os elementos necessários de um índice incluem um nome, uma coleção de campos e uma chave. A coleção Fields define a estrutura de um *documento*. Cada campo tem um nome, tipo e atributos que determinam como o campo é usado (por exemplo, se é pesquisável de texto completo, filtrável ou recuperável nos resultados da pesquisa). Dentro de um índice, um dos campos do tipo `Edm.String` deve ser designado como a *chave* para a identidade do documento.

Esse índice é denominado "Hotéis-QuickStart" e tem as definições de campo que você vê abaixo. É um subconjunto de um [índice de hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) maior usado em outros passo a passos. Nós o arrumamos neste guia de início rápido para fins de brevidade.

1. Na próxima célula, Cole o exemplo a seguir em uma célula para fornecer o esquema. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. Em outra célula, Formule a solicitação. Essa solicitação PUT visa a coleção de índices do serviço de pesquisa e cria um índice com base no esquema de índice que você forneceu na célula anterior.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Execute cada etapa.

   A resposta inclui a representação JSON do esquema. A captura de tela a seguir está mostrando apenas uma parte da resposta.

    ![Solicitação para criar um índice](media/search-get-started-python/create-index.png "Solicitação para criar um índice")

> [!Tip]
> Outra maneira de verificar a criação do índice é verificar a lista de índices no Portal.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Carregar documentos

Para enviar documentos por push, use uma solicitação HTTP POST para o ponto de extremidade da URL do índice. A API REST é [Adicionar, atualizar ou excluir documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Os documentos são originados em [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) no github.

1. Em uma nova célula, forneça quatro documentos que estejam de acordo com o esquema de índice. Especifique uma ação de carregamento para cada documento.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. Em outra célula, Formule a solicitação. Essa solicitação POST tem como alvo a coleção docs do índice Hotéis-QuickStart e envia os documentos fornecidos na etapa anterior.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Execute cada etapa para enviar os documentos por push a um índice em seu serviço de pesquisa. Os resultados devem ser semelhantes ao exemplo a seguir. 

    ![Enviar documentos para um índice](media/search-get-started-python/load-index.png "Enviar documentos para um índice")

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Esta etapa mostra como consultar um índice usando a [API REST pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. Em uma célula, forneça uma expressão de consulta que execute uma pesquisa vazia (Search = *), retornando uma lista não classificada (Pontuação de pesquisa = 1,0) de documentos arbitrários. Por padrão, Azure Search retorna 50 correspondências por vez. Como estruturado, essa consulta retorna uma estrutura de documento inteira e valores. Adicione $count = true para obter uma contagem de todos os documentos nos resultados.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. Em uma nova célula, forneça o exemplo a seguir para pesquisar os termos "Hotéis" e "WiFi". Adicione $select para especificar quais campos incluir nos resultados da pesquisa.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. Em outra célula, formule uma solicitação. Essa solicitação GET tem como alvo a coleção docs do índice Hotéis-QuickStart e anexa a consulta especificada na etapa anterior.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Execute cada etapa. Os resultados devem ser semelhantes à saída a seguir. 

    ![Pesquisar um índice](media/search-get-started-python/search-index.png "Pesquisar um índice")

1. Experimente alguns outros exemplos de consulta para ter uma ideia da sintaxe. Você pode substituir o `searchstring` com os exemplos a seguir e, em seguida, executar novamente a solicitação de pesquisa. 

   Aplicar um filtro: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Faça os dois primeiros resultados:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Ordenar por um campo específico:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Limpar

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

Como uma simplificação, este guia de início rápido usa uma versão abreviada do índice de hotéis. Você pode criar a versão completa para experimentar consultas mais interessantes. Para obter a versão completa e todos os documentos 50, execute o assistente de **importação de dados** , selecionando *Hotéis – exemplo* das fontes de dados de exemplo internas.

> [!div class="nextstepaction"]
> [Início Rápido: Criar um índice no portal do Azure](search-get-started-portal.md)
