---
title: 'Início Rápido: Criar um índice de pesquisa no .NET'
titleSuffix: Azure Cognitive Search
description: Neste início rápido do C#, saiba como criar um índice, carregar dados e executar consultas usando a biblioteca de clientes do Azure.Search.Documents.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f0d912d5b14932c43d109f8f955d5f16381cf773
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98180091"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Início Rápido: Criar um índice de pesquisa usando a biblioteca de clientes do Azure.Search.Documents

Use a nova [biblioteca de clientes do Azure.Search.Documents (versão 11)](/dotnet/api/overview/azure/search.documents-readme) para criar um aplicativo de console do .NET Core no C# que cria, carrega e consulta um índice de pesquisa.

Você pode [baixar o código-fonte](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) para começar com um projeto concluído ou siga as etapas neste artigo para criar o seu.

> [!NOTE]
> Procurando uma versão anterior? Confira [Criar um índice de pesquisa usando Microsoft.Azure.Search v10](search-get-started-dotnet-v10.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, tenha as seguintes ferramentas e serviços:

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/).

+ Um serviço do Azure Cognitive Search. [Criar um serviço](search-create-service-portal.md) ou [localizar um serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). É possível usar um serviço gratuito para este início rápido. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), qualquer edição. O código de exemplo foi testado na edição Comunidade gratuita do Visual Studio 2019.

Ao configurar seu projeto, você baixará o [pacote NuGet Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/).

O SDK do Azure para .NET está em conformidade com o [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support), que significa .NET Framework 4.6.1 e .NET Core 2.0 como requisitos mínimos.

## <a name="set-up-your-project"></a>Configurar o projeto

Reúna as informações de conexão do serviço e inicie o Visual Studio para criar um projeto de aplicativo de console que possa ser executado no .NET Core.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Copiar uma chave e um ponto de extremidade

As chamadas ao serviço exigem um ponto de extremidade de URL e uma chave de acesso em cada solicitação. Como uma primeira etapa, localize a chave de API e a URL para adicioná-las ao projeto. Você especificará os dois valores ao criar o cliente em uma etapa posterior.

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

2. Em **Configurações** > **Chaves**, obtenha uma chave de administração para obter direitos totais sobre o serviço, necessários se você estiver criando ou excluindo objetos. Há duas chaves primárias e secundárias intercambiáveis. Você pode usar qualquer uma delas.

   ![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-rest/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

### <a name="install-the-nuget-package"></a>Instalar o pacote NuGet

Depois que o projeto for criado, adicione a biblioteca de cliente. O [pacote Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) consiste em uma biblioteca de cliente que fornece todas as APIs usadas para trabalhar com um serviço de pesquisa no .NET.

1. Inicie o Visual Studio e crie um aplicativo de console do .NET Core.

1. Em **Ferramentas** > **Gerenciador de Pacotes NuGet**, selecione **Gerenciar Pacotes NuGet para a Solução...** . 

1. Clique em **Procurar**.

1. Procure `Azure.Search.Documents` e selecione a versão 11.0 ou posterior.

1. Clique em **Instalar** à direita para adicionar o assembly ao projeto e à solução.

### <a name="create-a-search-client"></a>Criar um cliente de pesquisa

1. Em **Program.cs**, altere o namespace para `AzureSearch.SDK.Quickstart.v11` e, em seguida, adicione as seguintes diretivas `using`.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Crie dois clientes: [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) cria o índice e [SearchClient](/dotnet/api/azure.search.documents.searchclient) carrega e consulta um índice existente. Ambos precisam do ponto de extremidade de serviço e de uma chave de API de administração para autenticação com direitos de criação/exclusão.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

        // Create a SearchIndexClient to send create/delete index commands
        Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
        AzureKeyCredential credential = new AzureKeyCredential(apiKey);
        SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);

        // Create a SearchClient to load and query documents
        SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - Criar um índice

Este guia de início rápido cria um índice de Hotéis que você carregará com os dados de hotéis e no qual executará consultas. Nesta etapa, defina os campos no índice. Cada definição de campo inclui nome, tipo de dados e atributos que determinam como o campo é usado.

Neste exemplo, os métodos síncronos da biblioteca Azure.Search.Documents são usados para simplificar e facilitar a legibilidade. No entanto, para cenários de produção, você deve usar métodos assíncronos para manter seu aplicativo escalonável e responsivo. Por exemplo, você usaria [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync), em vez de [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

1. Adicione uma definição de classe vazia ao seu projeto: **Hotel.cs**

1. Copie o código a seguir para **Hotel.cs** para definir a estrutura de um documento do hotel. Os atributos no campo determinam como ele é usado em um aplicativo. Por exemplo, o atributo `IsFilterable` deve ser atribuído a cada campo que dá suporte a uma expressão de filtro.

    ```csharp
    using System;
    using System.Text.Json.Serialization;
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;

    namespace AzureSearch.Quickstart
    {
        public partial class Hotel
        {
            [SimpleField(IsKey = true, IsFilterable = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            [SearchableField]
            public Address Address { get; set; }
        }
    }
    ```

   Na biblioteca de clientes Azure.Search.Documents, use [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) e [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) para simplificar as definições de campo. Ambos são derivativos de um [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) e podem simplificar o código:

   + `SimpleField` pode ser qualquer tipo de dados, sempre é não pesquisável (ignorado em consultas de pesquisa de texto completo) e é recuperável (não está oculto). Os outros atributos estão desativados por padrão, mas podem ser habilitados. Você pode usar um `SimpleField` para IDs ou campos de documentos usados somente em filtros, facetas ou perfis de pontuação. Nesse caso, lembre-se de aplicar todos os atributos necessários para o cenário, como `IsKey = true` para uma ID de documento. Para obter mais informações, confira [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) no código-fonte.

   + `SearchableField` precisa ser uma cadeia de caracteres e sempre é pesquisável e recuperável. Os outros atributos estão desativados por padrão, mas podem ser habilitados. Como esse tipo de campo é pesquisável, ele dá suporte a sinônimos e ao complemento completo de propriedades do analisador. Para obter mais informações, confira [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) no código-fonte.

   Se você usar a API `SearchField` básica ou um dos modelos auxiliares, precisará habilitar explicitamente os atributos de filtro, faceta e classificação. Por exemplo, [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) e [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) devem ser explicitamente atribuídos, conforme mostrado no exemplo acima. 

1. Adicione uma segunda definição de classe vazia ao seu projeto: **Address.cs**.  Copie o código a seguir para a classe.

   ```csharp
   using Azure.Search.Documents.Indexes;

    namespace AzureSearch.Quickstart
    {
        public partial class Address
        {
            [SearchableField(IsFilterable = true)]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
   ```

1. Crie mais duas classes: **Hotel.Methods.cs** e **Address.Methods.cs** para substituições ToString(). Essas classes são usadas para renderizar os resultados da pesquisa na saída do console.  O conteúdo dessas classes não é fornecido neste artigo, mas você pode copiar o código dos [arquivos no GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11/AzureSearchQuickstart-v11).

1. Em **Program.cs**, crie um objeto [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) e chame o método [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) para expressar o índice no serviço de pesquisa. O índice também inclui um [SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester) para habilitar o preenchimento automático nos campos especificados.

   ```csharp
    // Create hotels-quickstart index
    private static void CreateIndex(string indexName, SearchIndexClient adminClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));

        var definition = new SearchIndex(indexName, searchFields);

        var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
        definition.Suggesters.Add(suggester);

        adminClient.CreateOrUpdateIndex(definition);
    }
   ```

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Carregar documentos

O Azure Cognitive Search pesquisa no conteúdo armazenado no serviço. Nesta etapa, você carregará documentos JSON que estão em conformidade com o índice do hotel que você acabou de criar.

No Azure Cognitive Search, os documentos de pesquisa são estruturas de dados que são entradas para indexação e saídas de consultas. Conforme obtido de uma fonte de dados externa, as entradas de documento podem ser linhas em um banco de dados, blobs no Armazenamento de Blobs ou documentos JSON no disco. Neste exemplo, estamos usando um atalho e inserindo documentos JSON para quatro hotéis no próprio código. 

Ao carregar documentos, você deve usar um objeto [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1). Um objeto `IndexDocumentsBatch` contém uma coleção de [Ações](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions), cada um contendo um documento e uma propriedade que informa o Azure Cognitive Search de qual ação deve ser executada ([upload, merge, delete e mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. Em **Program.cs**, crie uma matriz de documentos e ações de índice e passe a matriz para `IndexDocumentsBatch`. Os documentos abaixo estão em conformidade com o índice hotels-quickstart, conforme definido pela classe de hotéis.

    ```csharp
    // Upload documents in a single Upload request.
    private static void UploadDocuments(SearchClient searchClient)
    {
        IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.60,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                })
            );

        try
        {
            IndexDocumentsResult result = searchClient.IndexDocuments(batch);
        }
        catch (Exception)
        {
            // If for some reason any documents are dropped during indexing, you can compensate by delaying and
            // retrying. This simple demo just logs the failed document keys and continues.
            Console.WriteLine("Failed to index some of the documents: {0}");
        }
    }
    ```

    Depois de inicializar o objeto [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1), você pode enviá-lo ao índice chamando [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) no objeto [SearchClient](/dotnet/api/azure.search.documents.searchclient).

1. Adicione as linhas a seguir a Main(). O carregamento de documentos é feito usando SearchClient, mas a operação também requer direitos de administrador no serviço, que normalmente é associado a SearchIndexClient. Uma forma de configurar essa operação é obter SearchClient por meio de SearchIndexClient (adminClient neste exemplo).

   ```csharp
    SearchClient ingesterClient = adminClient.GetSearchClient(indexName);

    // Load documents
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(ingesterClient);
   ```

1. Como esse é um aplicativo de console que executa todos os comandos sequencialmente, adicione um tempo de espera de dois segundos entre a indexação e as consultas.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    O atraso de 2 segundos compensa a indexação, que é assíncrona, de modo que todos os documentos possam ser indexados antes da execução das consultas. Normalmente, a codificação em um atraso só é necessária em demonstrações, testes e aplicativos de exemplo.

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Você poderá obter os resultados da consulta, assim que o primeiro documento for indexado, mas o teste real do índice deverá aguardar até todos os documentos serem indexados.

Esta seção adiciona duas funcionalidades: lógica da consulta e resultados. Para consultas, use o método [Search](/dotnet/api/azure.search.documents.searchclient.search). Esse método usa o texto de pesquisa (a cadeia de consulta), bem como outras [opções](/dotnet/api/azure.search.documents.searchoptions).

A classe [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) representa os resultados.

1. Em **Program.cs**, crie um método **WriteDocuments** que imprime os resultados da pesquisa no console.

    ```csharp
    // Write search results to console
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.GetResults())
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Crie um método **RunQueries** para executar consultas e retornar resultados. Os resultados são os objetos Hotel. Este exemplo mostra a assinatura do método e a primeira consulta. Essa consulta demonstra o parâmetro Select que permite compor o resultando usando os campos do documento.

    ```csharp
    // Run queries, use WriteDocuments to print output
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on empty term '*' to return all documents, showing a subset of fields...\n");

        options = new SearchOptions()
        {
            IncludeTotalCount = true,
            Filter = "",
            OrderBy = { "" }
        };

        options.Select.Add("HotelId");
        options.Select.Add("HotelName");
        options.Select.Add("Address/City");

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    ```

1. Na segunda consulta, pesquise um termo, adicione um filtro que seleciona documentos em que a Classificação é maior que 4 e organize por Classificação em ordem descendente. Um filtro é uma expressão booliana avaliada em campos [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) em um índice. As consultas de filtro incluem ou excluem valores. Assim, não há nenhuma pontuação de relevância associada a uma consulta de filtro. 

    ```csharp
    Console.WriteLine("Query #2: Search on 'hotels', filter on 'Rating gt 4', sort by Rating in descending order...\n");

    options = new SearchOptions()
    {
        Filter = "Rating gt 4",
        OrderBy = { "Rating desc" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Rating");

    response = srchclient.Search<Hotel>("hotels", options);
    WriteDocuments(response);
    ```

1. A terceira consulta demonstra searchFields, usado para fazer o escopo de uma operação de pesquisa de texto completo em campos específicos.

    ```csharp
    Console.WriteLine("Query #3: Limit search to specific fields (pool in Tags field)...\n");

    options = new SearchOptions()
    {
        SearchFields = { "Tags" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Tags");

    response = srchclient.Search<Hotel>("pool", options);
    WriteDocuments(response);
    ```

1. A quarta consulta demonstra facetas, que podem ser usadas para estruturar uma estrutura de navegação facetada. 

   ```csharp
    Console.WriteLine("Query #4: Facet on 'Category'...\n");

    options = new SearchOptions()
    {
        Filter = ""
    };

    options.Facets.Add("Category");

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Category");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
   ```

1. Na quinta consulta, retorne um documento específico. Uma pesquisa de documento é uma resposta típica ao evento OnClick em um conjunto de resultados.

   ```csharp
    Console.WriteLine("Query #5: Look up a specific document...\n");

    Response<Hotel> lookupResponse;
    lookupResponse = srchclient.GetDocument<Hotel>("3");

    Console.WriteLine(lookupResponse.Value.HotelId);
   ```

1. A última consulta mostra a sintaxe para o preenchimento automático, simulando uma entrada de usuário parcial de "sa" que resolve duas possíveis correspondências no sourceFields associado ao sugestor que você definiu no índice.

   ```csharp
    Console.WriteLine("Query #6: Call Autocomplete on HotelName that starts with 'sa'...\n");

    var autoresponse = srchclient.Autocomplete("sa", "sg");
    WriteDocuments(autoresponse);
   ```

1. Adicione **RunQueries** a Main().

    ```csharp
    // Call the RunQueries method to invoke a series of queries
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);

    // End the program
    Console.WriteLine("{0}", "Complete. Press any key to end this program...\n");
    Console.ReadKey();
    ```

As consultas anteriores mostram várias [maneiras de corresponder termos em uma consulta](search-query-overview.md#types-of-queries): pesquisa de texto completo, filtros e preenchimento automático.

A pesquisa de texto completo e os filtros são executados usando o método [SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search). Uma consulta de pesquisa pode ser passada na cadeia de caracteres `searchText`, enquanto uma expressão de filtro pode ser passada na propriedade [Filtro](/dotnet/api/azure.search.documents.searchoptions.filter) da classe [SearchOptions](/dotnet/api/azure.search.documents.searchoptions). Para filtrar sem pesquisar, basta passar `"*"` para o parâmetro `searchText` do método de [Search](/dotnet/api/azure.search.documents.searchclient.search). Para pesquisar sem filtrar, deixe a propriedade `Filter` não definida ou simplesmente não passe uma instância `SearchOptions`.

## <a name="run-the-program"></a>Executar o programa

Pressione F5 para recompilar o aplicativo e executar o programa em sua totalidade. 

A saída inclui mensagens de [Console.WriteLine](/dotnet/api/system.console.writeline), com a adição de resultados e informações de consulta.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido do C#, você trabalhou com um conjunto de tarefas para criar um índice, carregá-lo com documentos e executar consultas. Em diferentes estágios, usamos atalhos para simplificar o código a fim de facilitar a leitura e a compreensão. Caso você esteja familiarizado com os conceitos básicos, recomendaremos o próximo artigo para uma exploração das abordagens alternativas e dos conceitos que aprofundarão seu conhecimento. 

> [!div class="nextstepaction"]
> [Como realizar o desenvolvimento no .NET](search-howto-dotnet-sdk.md)

Deseja otimizar e reduzir seus gastos com a nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar os custos com o Gerenciamento de Custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
