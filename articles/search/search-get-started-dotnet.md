---
title: 'Início Rápido do C#: Criar, carregar e consultar índices usando o SDK do .NET – Azure Search'
description: Explica como criar um índice, carregar dados e executar consultas usando o C# e o SDK do .NET do Azure Search.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/11/2019
ms.openlocfilehash: 6138df5b80f479a54683ec0408b832dd78bff8e4
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67847089"
---
# <a name="quickstart-create-an-azure-search-index-in-c-using-the-net-sdk"></a>Início Rápido: Criar um índice do Azure Search no C# usando o SDK do .NET
> [!div class="op_single_selector"]
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Crie um aplicativo de console C# do .NET Core que cria, carrega e consulta um índice do Azure Search usando o Visual Studio e o [SDK do .NET do Azure Search](https://aka.ms/search-sdk). Este artigo explica como criar o aplicativo passo a passo. Como alternativa, você pode [baixar e executar o aplicativo completo](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> O código de demonstração deste artigo usa os métodos síncronos do SDK do .NET do Azure Search para manter a simplicidade. No entanto, para cenários de produção, recomendamos usar os métodos assíncronos em seus próprios aplicativos para mantê-los escalonáveis e dinâmicos. Por exemplo, você pode usar `CreateAsync` e `DeleteAsync` em vez de `Create` e `Delete`.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços e as ferramentas a seguir são necessários para este início rápido.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), qualquer edição. O código de exemplo e as instruções foram testados na edição gratuita da Comunidade.

+ [Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. É possível usar um serviço gratuito para este início rápido.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas ao serviço exigem um ponto de extremidade de URL e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Search à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

2. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   Obtenha a chave de consulta também. É uma melhor prática para emitir solicitações de consulta com acesso somente leitura.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="set-up-your-environment"></a>Configure seu ambiente

Comece abrindo o Visual Studio e criando um novo projeto de aplicativo de console que possa ser executado no .NET Core.

### <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

O [SDK do .NET do Azure Search](https://aka.ms/search-sdk) consiste em algumas bibliotecas de clientes que são distribuídas como pacotes NuGet.

Para este projeto, use a versão 9 do pacote NuGet `Microsoft.Azure.Search` e o pacote NuGet `Microsoft.Extensions.Configuration.Json` mais recente.

1. Em **Ferramentas** > **Gerenciador de Pacotes NuGet**, selecione **Gerenciar Pacotes NuGet para a Solução...** . 

1. Clique em **Procurar**.

1. Pesquise `Microsoft.Azure.Search` e selecione a versão 9.0.1 ou posterior.

1. Clique em **Instalar** à direita para adicionar o assembly ao projeto e à solução.

1. Repita a operação para `Microsoft.Extensions.Configuration.Json`, selecionando a versão 2.2.0 ou posterior.


### <a name="add-azure-search-service-information"></a>Adicionar informações de serviço do Azure Search

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Novo Item...** . 

1. Em Adicionar Novo Item, pesquise "JSON" para retornar uma lista relacionada ao JSON de tipos de itens.

1. Escolha **Arquivo JSON**, nomeie o arquivo "appsettings.json" e clique em **Adicionar**. 

1. Adicione o arquivo ao diretório de saída. Clique com o botão direito do mouse em appsettings.json e selecione **Propriedades**. Em **Copiar para Diretório de Saída**, selecione **Copiar se for o mais recente**.

1. Copie o JSON a seguir para o novo arquivo JSON. Substitua o nome do serviço de pesquisa (NOME-DO-SERVIÇO-DE-PESQUISA) e a chave de API de administração (CHAVE-DE-API-DE-ADMINISTRAÇÃO) por valores válidos. Se o ponto de extremidade de serviço for `https://mydemo.search.windows.net`, o nome do serviço será "mydemo".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Adicionar arquivos ".Method" da classe ao projeto

Ao imprimir os resultados na janela do console, os campos individuais do objeto Hotel precisam ser retornados como cadeias de caracteres. Você pode implementar [ToString()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) para executar essa tarefa, copiando o código necessário para dois novos arquivos.

1. Adicione duas definições de classe vazias ao projeto: Address.Methods.cs e Hotel.Methods.cs

1. Em Address.Methods.cs, substitua o conteúdo padrão pelo código a seguir, [linhas 1 a 32](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32).

1. Em Hotel.Methods.cs, copie as [linhas 1 a 66](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66).


## <a name="1---create-index"></a>1 – Criar o índice

O índice de hotéis consiste em campos simples e complexos, em que um campo simples é "HotelName" ou "Description" e os campos complexos são um endereço com subcampos ou uma coleção de quartos. Quando um índice incluir tipos complexos, isole as definições de campo complexo em classes separadas.

1. Adicione duas definições de classe vazias ao projeto: Address.cs e Hotel.cs

1. Em Address.cs, substitua o conteúdo padrão pelo seguinte código:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

1. Em Hotel.cs, a classe define a estrutura geral do índice, incluindo referências à classe de endereços.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    Os atributos no campo determinam como ele é usado em um aplicativo. Por exemplo, o atributo `IsSearchable` deve ser atribuído a todos os campos que devem ser incluídos em uma pesquisa de texto completo. 
    
    > [!NOTE]
    > No SDK do .Net, os campos devem ser atribuídos explicitamente como [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet), [`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet), [`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet) e [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet). Esse comportamento contrasta com a API REST, que habilita implicitamente a atribuição com base no tipo de dados (por exemplo, campos de cadeia de caracteres simples são pesquisáveis automaticamente).

    Exatamente um campo no índice do tipo `string` precisa ser o campo *key*, identificando exclusivamente cada documento. Nesse esquema, a chave é `HotelId`.

    Nesse índice, os campos de descrição usam a propriedade opcional [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet), especificada quando você deseja substituir o analisador Lucene padrão. O campo `description_fr` está usando o analisador Lucene em francês ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)) porque ele armazena o texto em francês. O `description` está usando o analisador de linguagem opcional da Microsoft ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)).

1. Em Program.cs, crie uma instância da classe [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) para se conectar ao serviço, usando valores que são armazenados no arquivo de configuração do aplicativo (appsettings.json). 

   `SearchServiceClient` tem uma propriedade [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet), que fornece todos os métodos necessários para criar, listar, atualizar ou excluir índices do Azure Search. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
            static void Main(string[] args)
            {
                IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
                IConfigurationRoot configuration = builder.Build();

                SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

                string indexName = configuration["SearchIndexName"];

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteIndexIfExists(indexName, serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateIndex(indexName, serviceClient);

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Se possível, compartilhe uma única instância de `SearchServiceClient` em seu aplicativo para evitar abrir um número excessivo de conexões. Os métodos de classe são thread-safe para habilitar esse compartilhamento.

   A classe tem vários construtores. Aquele que você deseja usa o nome do seu serviço de pesquisa e um objeto `SearchCredentials` como parâmetros. `SearchCredentials` encapsula sua api-key.

    Na definição de índice, a maneira mais fácil de criar os objetos `Field` é chamando o método `FieldBuilder.BuildForType`, passando uma classe de modelo para o parâmetro de tipo. Uma classe de modelo tem propriedades que mapeiam para os campos do seu índice. Esse mapeamento permite a vinculação de documentos do índice de pesquisa a instâncias da sua classe de modelo.

    > [!NOTE]
    > Se você não planeja usar uma classe de modelo, você ainda pode definir o índice criando objetos `Field` diretamente. Você pode fornecer o nome do campo ao construtor, junto com o tipo de dados (ou o analisador para os campos de cadeia de caracteres). Também é possível definir outras propriedades como `IsSearchable`, `IsFilterable`, para citar algumas.
    >

1. Pressione F5 para compilar o aplicativo e criar o índice. 

    Se o projeto for compilado com êxito, uma janela do console será aberta, gravando mensagens de status na tela para excluir e criar o índice. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Carregar documentos

No Azure Search, os documentos são estruturas de dados que são entradas para indexação e saídas de consultas. Conforme obtido de uma fonte de dados externa, as entradas de documento podem ser linhas em um banco de dados, blobs no Armazenamento de Blobs ou documentos JSON no disco. Neste exemplo, estamos usando um atalho e inserindo documentos JSON para quatro hotéis no próprio código. 

Ao carregar documentos, você precisa usar um objeto [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet). Um `IndexBatch` contém uma coleção de objetos [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet), cada um contendo um documento e uma propriedade que instrui o Azure Search sobre qual ação executar ([fazer upload, mesclar, excluir e mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. Em Program.cs, crie uma matriz de documentos e ações de índice e, em seguida, passe a matriz para `IndexBatch`. Os documentos abaixo estão em conformidade com o índice hotel-quickstart, conforme definido pelas classes de hotéis e endereços.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
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
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    Depois de inicializar o objeto `IndexBatch`, você poderá enviá-lo para o índice chamando [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) no objeto [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet). `Documents` é uma propriedade de `SearchIndexClient` que fornece métodos para adicionar, modificar, excluir ou consultar documentos no índice.

    O `try`/`catch` em torno da chamada ao método `Index` captura falhas de indexação, que poderão ocorrer se o serviço estiver sob carga pesada. No código de produção, você pode atrasar e, em seguida, tentar novamente a indexação de documentos que falharam ou registrá-los em log e continuar como a amostra faz ou, ainda, manipulá-los de alguma outra forma que atenda aos requisitos de consistência de dados do aplicativo.

    O atraso de 2 segundos compensa a indexação, que é assíncrona, de modo que todos os documentos possam ser indexados antes da execução das consultas. Normalmente, a codificação em um atraso só é necessária em demonstrações, testes e aplicativos de exemplo.

1. Em Program.cs, em main, remova a marca de comentário das linhas de "2 – Carregar documentos". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Pressione F5 para recompilar o aplicativo. 

    Se o projeto for compilado com êxito, uma janela do console será aberta, gravando mensagens de status, desta vez, com uma mensagem sobre o upload de documentos. No portal do Azure, na página **Visão Geral** do serviço de pesquisa, o índice hotels-quickstart agora deverá ter quatro documentos.

Para obter mais informações sobre o processamento de documentos, consulte ["Como o SDK do .NET trata documentos"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Você poderá obter os resultados da consulta, assim que o primeiro documento for indexado, mas o teste real do índice deverá aguardar até todos os documentos serem indexados. 

Esta seção adiciona duas funcionalidades: lógica da consulta e resultados. Para consultas, use o método [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
). Esse método usa o texto de pesquisa, bem como outros [parâmetros](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet). 

A classe [`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) representa os resultados.


1. Em Program.cs, crie um método WriteDocuments que imprime os resultados da pesquisa no console.

    ```csharp
    private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.Results)
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Crie um método RunQueries para executar consultas e retornar resultados. Os resultados são os objetos Hotel. Você pode usar o parâmetro de seleção para exibir campos individuais. Se um campo não estiver incluído no parâmetro de seleção, sua propriedade Hotel correspondente será nula.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    Há duas [maneiras de encontrar a correspondência de termos em uma consulta](search-query-overview.md#types-of-queries): pesquisa de texto completo e filtros. Uma consulta de pesquisa de texto completo pesquisa um ou mais termos em campos `IsSearchable` no índice. Um filtro é uma expressão booliana que é avaliada em campos `IsFilterable` em um índice. Você pode usar a pesquisa de texto completo e os filtros juntos ou separadamente.

    As pesquisas e os filtros são executados usando o método `Documents.Search` . Uma consulta de pesquisa pode ser passada no parâmetro `searchText`, enquanto uma expressão de filtro pode ser passada na propriedade `Filter` da classe `SearchParameters`. Para filtrar sem pesquisar, basta passar `"*"` para o parâmetro `searchText`. Para pesquisar sem filtrar, deixe a propriedade `Filter` indefinida ou simplesmente não passe uma instância `SearchParameters`.

1. Em Program.cs, em main, remova a marca de comentário das linhas de "3 – Pesquisar". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. Agora a solução foi concluída. Pressione F5 para recompilar o aplicativo e executar o programa em sua totalidade. 

    A saída inclui as mesmas mensagens de antes, com a adição de informações de consulta e resultados.

## <a name="clean-up"></a>Limpar

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

Neste início rápido do C#, você trabalhou com uma série de tarefas para criar um índice, carregá-lo com documentos e executar consultas. Em diferentes estágios, usamos atalhos para simplificar o código a fim de facilitar a leitura e a compreensão. Caso você esteja familiarizado com os conceitos básicos, recomendaremos o próximo artigo para uma exploração das abordagens alternativas e dos conceitos que aprofundarão seu conhecimento. 

O código de exemplo e o índice são versões expandidas deste. A amostra a seguir adiciona uma coleção Rooms, usa diferentes classes e ações e examina mais detalhadamente como funciona o processamento.

> [!div class="nextstepaction"]
> [Como realizar o desenvolvimento no .NET](search-howto-dotnet-sdk.md)