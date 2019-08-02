---
title: Como usar o Azure Search de um aplicativo .NET – Azure Search
description: Saiba como usar o Azure Search em um aplicativo .NET usando o C# e o SDK do .NET. As tarefas baseadas em códigos incluem a conexão com o serviço, conteúdo de índice e consulta a um índice.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: brjohnst
ms.openlocfilehash: 9f0af40d442747181636b50612f7d2162ead6a86
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450006"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Como usar o Azure Search de um aplicativo .NET

Este artigo é um passo a passo para ajudar você a usar o [SDK do .NET do Azure Search](https://aka.ms/search-sdk). Você pode usar o SDK do .NET para implementar uma experiência de pesquisa avançada em seu aplicativo usando o Azure Search.

## <a name="whats-in-the-azure-search-sdk"></a>Novidades no SDK do Azure Search
O SDK consiste em algumas bibliotecas de clientes que permitem que você gerencie seus índices, fontes de dados, indexadores e mapas de sinônimos, carregue e gerencie documentos e execute consultas, sem a necessidade de lidar com os detalhes de HTTP e JSON. Essas bibliotecas de cliente são distribuídas como pacotes NuGet.

O principal pacote NuGet é `Microsoft.Azure.Search`, o que é um pacote meta que inclui todos os outros pacotes, como dependências. Use esse pacote se você estiver apenas começando ou se você souber que seu aplicativo precisará de todos os recursos do Azure Search.

Os outros pacotes NuGet no SDK são:
 
  - `Microsoft.Azure.Search.Data`: Use esse pacote se estiver desenvolvendo um aplicativo .NET usando o Azure Search e só precisa consultar ou atualizar documentos nos índices. Se você também precisa criar ou atualizar índices, mapas de sinônimo ou outros recursos de nível de serviço, use o `Microsoft.Azure.Search` pacote em vez disso.
  - `Microsoft.Azure.Search.Service`: Use esse pacote se estiver desenvolvendo a automação no .NET para gerenciar índices, mapas de sinônimos, indexadores, fontes de dados ou outros recursos no nível do serviço do Azure Search. Se você precisar apenas consultar ou atualizar em seus índices, use o `Microsoft.Azure.Search.Data` pacote em vez disso. Se você precisar de toda a funcionalidade do Azure Search, use o `Microsoft.Azure.Search` pacote em vez disso.
  - `Microsoft.Azure.Search.Common`: Tipos comuns necessários para as bibliotecas .NET do Azure Search. Você não precisa usar esse pacote diretamente em seu aplicativo. Só deve ser usado como uma dependência.

A diversas bibliotecas de clientes definem classes como `Index`, `Field` e `Document`, e operações como `Indexes.Create` e `Documents.Search` nas classes `SearchServiceClient` e `SearchIndexClient`. Essas classes são organizadas nos namespaces a seguir:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Se você quiser fornecer comentários para uma atualização futura do SDK, consulte nosso [página de comentários](https://feedback.azure.com/forums/263029-azure-search/) ou crie um problema no [GitHub](https://github.com/azure/azure-sdk-for-net/issues) e mencione "Azure Search" no título do problema.

O SDK do .NET dá suporte à versão `2019-05-06` da [API REST da Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Esta versão inclui suporte para [tipos complexos](search-howto-complex-data-types.md), [pesquisa cognitiva](cognitive-search-concept-intro.md), [AutoCompletar](https://docs.microsoft.com/rest/api/searchservice/autocomplete), e [JsonLines modo de análise](search-howto-index-json-blobs.md) quando a indexação de Blobs do Azure. 

Esse SDK não oferece suporte a [Operações de Gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/), como criar e dimensionar os serviços do Search e gerenciar chaves de API. Se você precisar gerenciar seus recursos de Pesquisa a partir de um aplicativo .NET, é possível usar o [SDK de Gerenciamento do .NET da Azure Search](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Atualizando para a última versão do SDK
Se você já estiver usando uma versão anterior do SDK do .NET do Azure Search e você gostaria de atualizar para a versão mais recente versão disponível, [deste artigo](search-dotnet-sdk-migration-version-9.md) explica como.

## <a name="requirements-for-the-sdk"></a>Requisitos para o SDK
1. Visual Studio 2017 ou posterior.
2. Seu próprio serviço de Azure Search. Para usar o SDK, você precisará do nome do serviço e de uma ou mais chaves de API. [Criar um serviço no portal](search-create-service-portal.md) ajudará você com estas etapas.
3. Baixe o [pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) do SDK do .NET do Azure Search usando "Gerenciar pacotes NuGet" no Visual Studio. Basta procurar o nome do pacote `Microsoft.Azure.Search` em NuGet.org (ou um dos outros nomes de pacote acima se você precisar somente de um subconjunto da funcionalidade).

O SDK do .NET do Azure Search oferece suporte a aplicativos destinados ao .NET Framework 4.5.2 e superior, como bem como o .NET Core 2.0 e superior.

## <a name="core-scenarios"></a>Principais cenários
Há várias coisas que você precisará fazer em seu aplicativo de pesquisa. Neste tutorial, abordaremos os principais cenários:

* Criando um índice
* Preenchimento do índice com documentos
* Procura por documentos usando filtros e pesquisa de texto completo

O código de exemplo a seguir ilustra cada um desses cenários. Fique à vontade para usar os snippets de código em seu próprio aplicativo.

### <a name="overview"></a>Visão geral
O exemplo de aplicativo que vamos explorar cria um novo índice chamado "hotéis", preenche-o com alguns documentos e, em seguida, executa algumas consultas de pesquisa. Este é o programa principal, mostrando o fluxo geral:

```csharp
// This sample shows how to delete, create, upload documents and query an index
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

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> Você pode encontrar o código-fonte completo do aplicativo de exemplo usado neste passo a passo no [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

Vamos examinar isso passo a passo. Primeiro, é necessário criar um novo `SearchServiceClient`. Esse objeto permite o gerenciamento de índices. Para construir um, você precisa fornecer o nome do serviço do Azure Search, bem como uma chave de API de administração. Você pode inserir essas informações no arquivo `appsettings.json` do [aplicativo de exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Se você fornecer uma chave incorreta (por exemplo, uma chave de consulta quando era necessário fornecer uma chave de administrador), o `SearchServiceClient` lançará uma `CloudException` com a mensagem de erro "Forbidden" na primeira vez que você chamar um método de operação, como `Indexes.Create`. Se isso ocorrer, verifique novamente a chave da API.
> 
> 

As próximas linhas chamam métodos para criação de um índice chamado "hotéis," excluindo-o primeiro caso ele já exista. Abordaremos esses métodos um pouco mais tarde.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Em seguida, o índice precisa ser preenchido. Para popular o índice, será necessário um `SearchIndexClient`. Há duas maneiras de obter um: construindo ou chamando `Indexes.GetClient` no `SearchServiceClient`. Usaremos o último por conveniência.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> Em um aplicativo de pesquisa típica, o preenchimento e o gerenciamento de índice podem ser manipuladas por um componente separado das consultas de pesquisa. `Indexes.GetClient` é conveniente para preencher um índice porque poupa o trabalho de fornecer adicional `SearchCredentials`. Ele faz isso passando a chave de administrador que você usou para criar o `SearchServiceClient` ao novo `SearchIndexClient`. No entanto, na parte de seu aplicativo que executa consultas, é melhor criar o `SearchIndexClient` diretamente para que você pode passar uma chave de consulta, que só permite que você leia os dados, em vez de uma chave de administração. Isso está de acordo com o princípio de privilégios mínimos e ajuda a tornar seu aplicativo mais seguro. Você pode saber mais sobre chaves de administração e chaves de consulta [aqui](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Agora que temos um `SearchIndexClient`, podemos preencher o índice. População do índice é feita por outro método que abordaremos mais tarde.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Por fim, executamos algumas consultas de pesquisa e exibimos os resultados. Dessa vez, usamos outro `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Posteriormente, faremos uma análise mais detalhada do método `RunQueries`. Este é o código para criar o novo `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Dessa vez, usaremos uma chave de consulta, já que não precisamos de acesso de gravação para o índice. Você pode inserir essas informações no arquivo `appsettings.json` do [aplicativo de exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Se você executar esse aplicativo com um nome de serviço válido e chaves de API, a saída deve ser semelhante a este exemplo: (Alguns saída do console foi substituída por "..." para fins de ilustração.)

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

O código-fonte completo do aplicativo é fornecido ao final deste artigo.

Em seguida, analisaremos com mais detalhes cada um dos métodos chamados pelo `Main`.

### <a name="creating-an-index"></a>Criando um índice
Depois de criar uma `SearchServiceClient`, `Main` exclui o índice "hotéis", caso ele já exista. Essa exclusão é feito pelo método a seguir:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Esse método usa o `SearchServiceClient` fornecido para verificar se o índice existe e, nesse caso, excluí-lo.

> [!NOTE]
> O exemplo código deste artigo usa os métodos síncronos do SDK do .NET do Azure Search para manter a simplicidade. Recomendamos que você use os métodos assíncronos em seus próprios aplicativos para mantê-los escalonáveis e responsivos. Por exemplo, no método acima você pode usar `ExistsAsync` e `DeleteAsync` em vez de `Exists` e `Delete`.
> 
> 

Em seguida, `Main` cria um novo índice "hotéis" chamando este método:

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

Esse método cria um novo objeto `Index` com uma lista de objetos `Field` que definem o esquema do novo índice. Cada campo tem um nome, tipo de dados e vários atributos que definem seu comportamento de pesquisa. A classe `FieldBuilder` usa a reflexão para criar uma lista de objetos `Field` para o índice, examinando os atributos e propriedades públicas da classe do modelo `Hotel` determinada. Posteriormente, faremos uma análise mais detalhada da classe `Hotel`.

> [!NOTE]
> Você pode criar quando desejar a lista de objetos `Field` diretamente, em vez de usar o `FieldBuilder` se necessário. Por exemplo, você pode não querer usar uma classe de modelo ou pode precisar usar uma classe de modelo existente que não deseja modificar adicionando atributos.
>
> 

Além dos campos, você também pode adicionar perfis de pontuação, sugestões ou opções de CORS para o índice (esses parâmetros são omitidos do exemplo para fins de brevidade). Você pode saber mais sobre o objeto Index e suas partes na [referência do SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index), bem como na [Referência da API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Preenchendo o índice
A próxima etapa no `Main` preenche o índice recém-criado. A população do índice é feita no método a seguir: (Alguns códigos substituído por "..." para fins de ilustração.  Consulte a solução de exemplo completo para o código de população completa de dados).

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Este método tem quatro partes. O primeiro cria uma matriz de 3 `Hotel` objetos, cada um com 3 `Room` objetos que servirão como nossos dados de entrada para carregar o índice. Esses dados são codificados para manter a simplicidade. Em seu próprio aplicativo, provavelmente seus dados virão de uma fonte de dados externa, como um banco de dados SQL.

A segunda parte cria um `IndexBatch` com os documentos. Especifique a operação que você quer aplicar ao lote no momento da criação dele, nesse caso, chamando `IndexBatch.Upload`. O lote é então carregado para o índice do Azure Search pelo método `Documents.Index` .

> [!NOTE]
> Neste exemplo, estamos carregando apenas documentos. Se você quiser mesclar alterações em documentos existentes ou excluir documentos, poderá criar lotes chamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` ou `IndexBatch.Delete`. Também é possível combinar diferentes operações em um único lote chamando `IndexBatch.New`, que usa uma coleção de objetos `IndexAction`, com cada um deles informando ao Azure Search para executar uma operação específica em um documento. Você pode criar cada `IndexAction` com sua própria operação chamando o método correspondente como `IndexAction.Merge`, `IndexAction.Upload` e assim por diante.
> 
> 

A terceira parte desse método é um bloco catch que trata um caso de erro importante para indexação. Se o serviço de Azure Search não indexar alguns documentos no lote, uma `IndexBatchException` será lançada por `Documents.Index`. Essa exceção poderá ocorrer se você estiver indexando documentos enquanto o serviço estiver sob carga pesada. **É altamente recomendável a manipulação explícita desse caso em seu código.** Você pode atrasar e repetir a indexação de documentos que falharam, ou você pode registrar em log e continuar, como faz o exemplo, ou pode alguma outra coisa, dependendo dos requisitos de consistência de dados do aplicativo.

> [!NOTE]
> Você pode usar o método [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) para construir um novo lote contendo apenas as ações que falharam em uma chamada anterior para `Index`. Há uma discussão sobre como usá-lo corretamente no [StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Por fim, o método `UploadDocuments` atrasa por dois segundos. A indexação ocorre de maneira assíncrona em seu serviço de Azure Search, portanto, o exemplo de aplicativo precisa aguardar alguns instantes para garantir que os documentos estejam disponíveis para pesquisa. Normalmente, atrasos como esses só são necessários em demonstrações, testes e exemplos de aplicativos.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Como o SDK do .NET lida com documentos
Você pode estar se perguntando como o SDK do .NET do Azure Search é capaz de carregar instâncias de uma classe definida pelo usuário, como `Hotel` , no índice. Para ajudar a responder a essa pergunta, vamos examinar a classe `Hotel` :

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
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

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

A primeira coisa a observar é que o nome de cada propriedade pública no `Hotel` classe será mapeado para um campo com o mesmo nome na definição do índice. Se você quiser que cada campo para começar com uma letra minúscula ("minúsculas concatenadas"), você pode informar ao SDK para mapear os nomes de propriedade como minúscula automaticamente com o `[SerializePropertyNamesAsCamelCase]` atributo na classe. Esse cenário é comum em aplicativos .NET que executam associação de dados em que o esquema de destino está fora do controle do desenvolvedor do aplicativo sem ter que violam "Pascal case" diretrizes no .NET de nomenclatura.

> [!NOTE]
> O SDK do .NET de Azure Search usa a biblioteca [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar e desserializar os objetos de modelo personalizados para e a partir do JSON. Se necessário, você pode personalizar essa serialização. Para obter mais informações, consulte [serialização personalizada com JSON.NET](#JsonDotNet).
> 
> 

A segunda coisa a observar é que cada propriedade é decorada com atributos como `IsFilterable`, `IsSearchable`, `Key`, e `Analyzer`. Esses atributos são mapeados diretamente para o [atributos de campo correspondente em um índice de Azure Search](https://docs.microsoft.com/rest/api/searchservice/create-index#request). O `FieldBuilder` classe usa essas propriedades para criar definições de campo para o índice.

O terceiro fator importante sobre o `Hotel` classe é os tipos de dados das propriedades públicas. Os tipos .NET dessas propriedades são mapeados para seus tipos de campo equivalentes na definição do índice. Por exemplo, a propriedade de cadeia de caracteres `Category` mapeia para o campo `category`, que é do tipo `Edm.String`. Há mapeamentos de tipo semelhantes entre `bool?`, `Edm.Boolean`, `DateTimeOffset?`, e `Edm.DateTimeOffset` e assim por diante. As regras específicas para o mapeamento de tipos estão documentadas com o método `Documents.Get` na [referência do SDK do .NET do Azure Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). A classe `FieldBuilder` cuida desse mapeamento para você, mas ainda pode ser útil para entender caso você precise solucionar problemas de serialização.

Aconteceu a observar o `SmokingAllowed` propriedade?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

O `JsonIgnore` atributo sobre esta propriedade instrui o `FieldBuilder` não serializá-lo para o índice como um campo.  Isso é uma ótima maneira de criar propriedades calculadas lado do cliente, que você pode usar como auxiliares em seu aplicativo.  Nesse caso, o `SmokingAllowed` propriedade reflete se qualquer `Room` no `Rooms` coleção permite fumar.  Se tudo for false, ele indica que o hotel inteiro não permitem fumar.

Algumas propriedades, como `Address` e `Rooms` são instâncias de classes do .NET.  Essas propriedades representam as estruturas de dados mais complexas e, assim, exigem campos com um [tipo de dados complexo](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) no índice.

O `Address` propriedade representa um conjunto de valores múltiplos no `Address` classe, definido abaixo:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
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

Essa classe contém os valores padrão usados para descrever endereços nos Estados Unidos ou Canadá. Você pode usar tipos como este para agrupar lógicos campos no índice.

O `Rooms` propriedade representa uma matriz de `Room` objetos:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

O modelo de dados em .NET e seu esquema de índice correspondentes deve ser projetado para dar suporte a experiência de pesquisa que você deseja dar ao seu usuário final. Cada objeto de nível superior no .NET, ou seja o documento no índice, corresponde a um resultado de pesquisa que você apresentaria na interface do usuário. Por exemplo, em um aplicativo de pesquisa do hotel, seus usuários finais poderá pesquisar por nome do hotel, recursos do hotel, ou as características de uma sala específica. Vamos abordar alguns exemplos de consulta um pouco mais tarde.

Essa capacidade de usar suas próprias classes para interagir com documentos no índice funciona em ambas as direções; Você também pode recuperar os resultados da pesquisa e tem o SDK os Desserialize automaticamente para um tipo de sua escolha, como veremos na próxima seção.

> [!NOTE]
> O SDK do .NET do Azure Search também oferece suporte a documentos do tipo dinâmico usando a classe `Document`, que é um mapeamento de chave/valor de nomes de campo para valores de campo. Isso é útil em cenários nos quais você não conhece o esquema de índice no momento do design, ou nos quais seria inconveniente associar a classes de modelo específico. Todos os métodos no SDK que lidam com documentos têm sobrecargas que funcionam com a classe `Document` , bem como sobrecargas fortemente tipadas que utilizam um parâmetro de tipo genérico. Somente as últimas são usadas no exemplo de código deste tutorial. A [`Document` classe](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) herda de `Dictionary<string, object>`.
> 
>

**Por que você deve usar tipos de dados anuláveis**

Ao criar suas próprias classes de modelo para mapear para um índice de Azure Search, sugerimos declarar as propriedades dos tipos de valor como `bool` e `int` para serem anuláveis (por exemplo, `bool?` em vez de `bool`). Se você usar uma propriedade não anulável, será preciso **assegurar** que nenhum documento no índice contenha um valor nulo para o campo correspondente. Nem o SDK, nem o serviço Azure Search ajudarão você a impor isso.

Isso não é apenas uma preocupação hipotética: imagine um cenário em que você adiciona um novo campo a um índice existente do tipo `Edm.Int32`. Depois de atualizar a definição de índice, todos os documentos terão um valor nulo para esse novo campo (já que todos os tipos são anuláveis no Azure Search). Ao usar uma classe de modelo com uma propriedade não anulável `int` para esse campo, você obterá uma `JsonSerializationException` como esta ao tentar recuperar os documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por esse motivo, sugerimos que você use tipos anuláveis nas suas classes de modelo como uma prática recomendada.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Serialização personalizada com JSON.NET
O SDK usa JSON.NET para serializar e desserializar documentos. Você pode personalizar a serialização e desserialização, se necessário, definindo seu próprio `JsonConverter` ou `IContractResolver`. Para obter mais informações, consulte o [documentação do JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm). Isso pode ser útil quando você quer adaptar uma classe de modelo existente de seu aplicativo para usar com o Azure Search e outros cenários mais avançados. Por exemplo, com a serialização personalizada, você pode:

* Incluir ou excluir determinadas propriedades da sua classe de modelo para serem armazenadas como campos de documento.
* Mapear os nomes de propriedade no código aos nomes de campo no índice.
* Crie atributos personalizados que podem ser usados para mapear propriedades para campos do documento.

Você pode encontrar exemplos de implementação de serialização personalizada nos testes de unidade para o SDK .NET do Azure Search no GitHub. Um bom ponto de partida é [esta pasta](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Ela contém classes que são usadas pelos testes de serialização personalizada.

### <a name="searching-for-documents-in-the-index"></a>Pesquisando documentos no índice
A última etapa no aplicativo de exemplo é procurar por documentos no índice:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

Cada vez que ele executa uma consulta, esse método cria, primeiro, um novo objeto `SearchParameters`. Esse objeto é usado para especificar opções adicionais para a consulta como classificação, filtragem, paginação e facetas. Nesse método, estamos definindo as propriedades de `Filter`, `Select`, `OrderBy` e `Top` para consultas diferentes. Todas as propriedades de `SearchParameters` são documentadas [aqui](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

A próxima etapa é executar a consulta de pesquisa. Executando a pesquisa é feita usando o `Documents.Search` método. Para cada consulta, passamos o texto de pesquisa a ser usado como uma cadeia de caracteres (ou `"*"` se não houver um texto de pesquisa) e também os parâmetros de pesquisa criados anteriormente. Também podemos especificar `Hotel` como o parâmetro de tipo para `Documents.Search`, que informa ao SDK para desserializar documentos nos resultados da pesquisa em objetos do tipo `Hotel`.

> [!NOTE]
> Você pode saber mais sobre a sintaxe de expressão da consulta de pesquisa [aqui](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Por fim, depois de cada consulta, esse método percorre todas as correspondências nos resultados da pesquisa, imprimindo cada documento no console:

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

Vejamos cada uma das consultas com mais detalhes. Este é o código para executar a primeira consulta:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Nesse caso, estamos pesquisando o índice inteiro para a palavra "motel" em qualquer campo pesquisável e queremos recuperar os nomes dos hotéis, conforme especificado pelo `Select` parâmetro. Estes são os resultados:

    Name: Secret Point Motel

    Name: Twin Dome Motel

A próxima consulta é um pouco mais interessante.  Queremos localizar quaisquer hotéis que têm uma sala com uma taxa de todas as noites de menos de US $100 e retornam somente a ID de hotel e a descrição:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Esta consulta usa uma expressão `$filter` do OData, `Rooms/any(r: r/BaseRate lt 100)`, para filtrar os documentos no índice. Isso usa o [qualquer operador](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) a ser aplicado a ' BaseRate lt 100' para cada item na coleção de salas. Você pode saber mais sobre a sintaxe do OData com suporte do Azure Search [aqui](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

Estes são alguns dos resultados da consulta:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Em seguida, queremos localizar os dois principais hotéis reformados mais recentemente e mostrar o nome dos hotéis e a data da última reforma. Eis o código: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Neste caso, usaremos novamente a sintaxe do OData para especificar o parâmetro `OrderBy` como `lastRenovationDate desc`. Também vamos definir `Top` como 2 para garantir que só iremos receber os dois principais documentos. Como antes, definimos `Select` para especificar quais campos devem ser retornados.

Estes são os resultados:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Por fim, queremos localizar os nomes de todos os hotéis que corresponde à palavra "hotel":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

Estes são os resultados, que incluem todos os campos uma vez que não especificamos a propriedade `Select`:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Essa etapa conclui o tutorial, mas não pare aqui. \* * Próximas etapas fornecem recursos adicionais para aprender mais sobre o Azure Search.

## <a name="next-steps"></a>Próximas etapas
* Procure as referências para o [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e a [API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Revise as [convenções de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) para saber as regras de nomeação de vários objetos.
* Examine os [tipos de dados com suporte](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) no Azure Search.
