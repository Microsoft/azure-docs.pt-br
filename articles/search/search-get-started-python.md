---
title: 'Início Rápido: Criar um índice de pesquisa em Python'
titleSuffix: Azure Cognitive Search
description: Saiba como criar um índice de pesquisa, carregar dados e executar consultas usando o Python, o Jupyter Notebook e a biblioteca de clientes Azure.Documents.Search para Python.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/12/2021
ms.custom: devx-track-python
ms.openlocfilehash: 8b9c4792fa6dbdc70f657ce3c5f1757473a22fda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225210"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebook"></a>Início rápido: Criar um índice do Azure Cognitive Search no Python usando o Jupyter Notebook

> [!div class="op_single_selector"]
> * [Python](search-get-started-python.md)
> * [PowerShell (REST)](search-get-started-powershell.md)
> * [C#](search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Portal](search-get-started-portal.md)
>

Crie um notebook que cria, carrega e consulta um índice do Azure Cognitive Search usando o Python e a [biblioteca azure-search-documents](/python/api/overview/azure/search-documents-readme) no SDK do Azure para Python. Este artigo explica como criar um passo a passo do notebook. Como alternativa, você pode [baixar e executar um notebook Python Jupyter concluído](https://github.com/Azure-Samples/azure-search-python-samples).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços e as ferramentas a seguir são necessários para este início rápido.

* [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), que fornece o Python 3.x e o Jupyter Notebook.

* [Pacote azure-search-documents](https://pypi.org/project/azure-search-documents/)

* [Crie um serviço de pesquisa](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. É possível usar a camada gratuita deste início rápido. 

## <a name="copy-a-key-and-url"></a>Copiar uma chave e URL

As chamadas REST exigem a URL do serviço e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou a Pesquisa Cognitiva do Azure à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   ![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-rest/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="connect-to-azure-cognitive-search"></a>Conectar-se à Pesquisa Cognitiva do Azure

Nesta tarefa, inicie o Jupyter Notebook e verifique se você pode se conectar ao Azure Cognitive Search. Você fará isso solicitando uma lista de índices de seu serviço. No Windows com Anaconda3, você pode usar o Anaconda Navigator para iniciar um notebook.

1. Crie um notebook Python3.

1. Na primeira célula, carregue as bibliotecas do SDK do Azure para Python, incluindo [azure-search-documents](/python/api/azure-search-documents).

   ```python
    !pip install azure-search-documents --pre
    !pip show azure-search-documents
    
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.search.documents.indexes import SearchIndexClient 
    from azure.search.documents import SearchClient
    from azure.search.documents.indexes.models import (
        ComplexField,
        CorsOptions,
        SearchIndex,
        ScoringProfile,
        SearchFieldDataType,
        SimpleField,
        SearchableField
    )
   ```

1. Na segunda célula, insira os elementos de solicitação que serão constantes em cada solicitação. Forneça o nome do serviço de pesquisa, a chave de API de administração e a chave de API de consulta, copiados em uma etapa anterior. Essa célula também configura os clientes que serão usados para operações específicas: [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) para criar um índice e [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) para consultar um índice.

   ```python
    service_name = "YOUR-SEARCH-SERIVCE-NAME"
    admin_key = "YOUR-SEARCH-SERVICE-ADMIN-API-KEY"
    
    index_name = "hotels-quickstart"
    
    # Create an SDK client
    endpoint = "https://{}.search.windows.net/".format(service_name)
    admin_client = SearchIndexClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
    
    search_client = SearchClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
   ```

1. Na terceira célula, execute uma operação delete_index para limpar seu serviço de índices *hotels-quickstart* existentes. A exclusão do índice permite que você crie outro índice *hotels-quickstart* de mesmo nome.

   ```python
    try:
        result = admin_client.delete_index(index_name)
        print ('Index', index_name, 'Deleted')
    except Exception as ex:
        print (ex)
   ```

1. Execute cada etapa.

## <a name="1---create-an-index"></a>1 - Criar um índice

Os elementos necessários de um índice incluem um nome, uma coleção de campos e uma chave. A coleção de campos define a estrutura de um *documento de pesquisa* lógico, usado para carregar dados e retornar resultados. 

Cada campo tem um nome, tipo e atributos que determinam como o campo é usado (por exemplo, se for pesquisável de texto completo, filtrável ou recuperável nos resultados da pesquisa). Dentro de um índice, um dos campos do tipo `Edm.String` deve ser designado como a *chave* para a identidade do documento.

Esse índice é denominado "hotels-quickstart" e tem as definições de campo que você vê abaixo. É um subconjunto de um [Índice de hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) maior usado em outros passo a passos. Nós o cortamos neste guia de início rápido para fins de brevidade.

1. Na próxima célula, cole o exemplo a seguir em uma célula para fornecer o esquema.

    ```python
    # Specify the index schema
    name = index_name
    fields = [
            SimpleField(name="HotelId", type=SearchFieldDataType.String, key=True),
            SearchableField(name="HotelName", type=SearchFieldDataType.String, sortable=True),
            SearchableField(name="Description", type=SearchFieldDataType.String, analyzer_name="en.lucene"),
            SearchableField(name="Description_fr", type=SearchFieldDataType.String, analyzer_name="fr.lucene"),
            SearchableField(name="Category", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
        
            SearchableField(name="Tags", collection=True, type=SearchFieldDataType.String, facetable=True, filterable=True),
    
            SimpleField(name="ParkingIncluded", type=SearchFieldDataType.Boolean, facetable=True, filterable=True, sortable=True),
            SimpleField(name="LastRenovationDate", type=SearchFieldDataType.DateTimeOffset, facetable=True, filterable=True, sortable=True),
            SimpleField(name="Rating", type=SearchFieldDataType.Double, facetable=True, filterable=True, sortable=True),
    
            ComplexField(name="Address", fields=[
                SearchableField(name="StreetAddress", type=SearchFieldDataType.String),
                SearchableField(name="City", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="StateProvince", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="PostalCode", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="Country", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
            ])
        ]
    cors_options = CorsOptions(allowed_origins=["*"], max_age_in_seconds=60)
    scoring_profiles = []
    suggester = [{'name': 'sg', 'source_fields': ['Tags', 'Address/City', 'Address/Country']}]
    ```

1. Em outra célula, formule a solicitação. Essa solicitação create_index direciona a coleção de índices de seu serviço de pesquisa e cria um [SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) com base no esquema de índice fornecido na célula anterior.

    ```python
    index = SearchIndex(
        name=name,
        fields=fields,
        scoring_profiles=scoring_profiles,
        suggesters = suggester,
        cors_options=cors_options)
    
    try:
        result = admin_client.create_index(index)
        print ('Index', result.name, 'created')
    except Exception as ex:
        print (ex)
    ```

1. Execute cada etapa.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Carregar documentos

Para carregar documentos, crie uma coleção de documentos, usando uma [ação de índice](/python/api/azure-search-documents/azure.search.documents.models.indexaction) para o tipo de operação (carregar, mesclar e carregar e assim por diante). Os documentos são originados de [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) no GitHub.

1. Em uma nova célula, forneça quatro documentos em conformidade com o esquema de índice. Especifique uma ação de upload para cada documento.

    ```python
    documents = [
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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
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
    ```  

1. Em outra célula, formule a solicitação. Essa solicitação upload_documents direciona a coleção de documentos do índice hotels-quickstart e efetua push dos documentos fornecidos na etapa anterior para o índice do Cognitive Search.

    ```python
    try:
        result = search_client.upload_documents(documents=documents)
        print("Upload of new document succeeded: {}".format(result[0].succeeded))
    except Exception as ex:
        print (ex.message)
    ```

1. Execute cada etapa para efetuar push dos documentos para um índice em seu serviço de pesquisa.

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Esta etapa mostra como consultar um índice usando o método **search** da [classe search.client](/python/api/azure-search-documents/azure.search.documents.searchclient).

1. A etapa a seguir executa uma pesquisa vazia (`search=*`), retornando uma lista não classificada (pontuação de pesquisa = 1.0) de documentos arbitrários. Como não há nenhum critério, todos os documentos são incluídos nos resultados. Essa consulta imprime apenas dois dos campos em cada documento. Ela também adiciona `include_total_count=True` para obter uma contagem de todos os documentos (4) nos resultados.

    ```python
    results =  search_client.search(search_text="*", include_total_count=True)
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. A próxima consulta adiciona termos inteiros à expressão de pesquisa ("wifi"). Essa consulta especifica que os resultados contêm apenas os campos na instrução `select`. Limitar os campos retornados minimiza a quantidade de dados enviados de volta pela rede e reduz a latência de pesquisa.

    ```python
    results =  search_client.search(search_text="wifi", include_total_count=True, select='HotelId,HotelName,Tags')
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}: {}".format(result["HotelId"], result["HotelName"], result["Tags"]))
    ```

1. Em seguida, aplique uma expressão de filtro, que retorna apenas os hotéis com uma classificação maior que 4, organizados em ordem decrescente.

    ```python
    results =  search_client.search(search_text="hotels", select='HotelId,HotelName,Rating', filter='Rating gt 4', order_by='Rating desc')
    
    for result in results:
        print("{}: {} - {} rating".format(result["HotelId"], result["HotelName"], result["Rating"]))
    ```

1. Adicione `search_fields` à correspondência de consulta de escopo a um único campo.

    ```python
    results =  search_client.search(search_text="sublime", search_fields='HotelName', select='HotelId,HotelName')
    
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. As facetas são rótulos que podem ser usados para compor a estrutura de navegação da faceta. Essa consulta retorna facetas e contagens para a Categoria.

    ```python
    results =  search_client.search(search_text="*", facets=["Category"])
    
    facets = results.get_facets()
    
    for facet in facets["Category"]:
        print("    {}".format(facet))
    ```

1. Neste exemplo, pesquise um documento específico com base na chave dele. Normalmente, seria interessante retornar um documento quando um usuário clicasse em um documento em um resultado de pesquisa.

    ```python
    result = search_client.get_document(key="3")
    
    print("Details for hotel '3' are:")
    print("Name: {}".format(result["HotelName"]))
    print("Rating: {}".format(result["Rating"]))
    print("Category: {}".format(result["Category"]))
    ```

1. Neste exemplo, usaremos a função de preenchimento automático. Normalmente, isso é usado em uma caixa de pesquisa para ajudar a preencher automaticamente as possíveis correspondências à medida que o usuário realiza a digitação na caixa de pesquisa.

   Quando o índice foi criado, um sugestor chamado "sg" também foi criado como parte da solicitação. Uma definição de sugestor especifica quais campos podem ser usados para encontrar possíveis correspondências para as solicitações do sugestor. Neste exemplo, esses campos são "Marcas", "Endereço/Cidade", "Endereço/País". Para simular o preenchimento automático, passe as letras "sa" como uma cadeia de caracteres parcial. O método de preenchimento automático de [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) envia de volta correspondências de potenciais termos.

    ```python
    search_suggestion = 'sa'
    results = search_client.autocomplete(search_text=search_suggestion, suggester_name="sg", mode='twoTerms')
    
    print("Autocomplete for:", search_suggestion)
    for result in results:
        print (result['text'])
    ```

## <a name="clean-up"></a>Limpeza

Quando já estiver trabalhando na sua assinatura, analise se você ainda precisa dos recursos criados no fim de um projeto. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

Como uma simplificação, este início rápido usa uma versão abreviada do índice Hotels. É possível criar a versão completa para tentar consultas mais interessantes. Para obter a versão completa e todos os 50 documentos, execute o assistente **Importar dados**, selecionando *hotels–sample* nas fontes de dados de exemplo internas.

> [!div class="nextstepaction"]
> [Início Rápido: Criar um índice no portal do Azure](search-get-started-portal.md)