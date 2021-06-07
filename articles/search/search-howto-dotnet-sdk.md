---
title: Usar o Azure.Search.Documents (v11) no .NET
titleSuffix: Azure Cognitive Search
description: Saiba como criar e gerenciar objetos de pesquisa em um aplicativo .NET usando C# e a biblioteca de cliente do Azure.Search.Documents (v11). Os trechos de código demonstram a conexão com o serviço, a criação de índices e consultas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 15a878eb863b71a4519e75def2598f013152dfb7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97881626"
---
# <a name="how-to-use-azuresearchdocuments-in-a-c-net-application"></a>Como usar Azure.Search.Documents em um aplicativo .NET em C#

Este artigo explica como criar e gerenciar objetos de pesquisa usando C# e a biblioteca de cliente do [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) (versão 11).

## <a name="about-version-11"></a>Sobre a versão 11

O SDK do Azure para .NET adiciona uma nova biblioteca de cliente [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) da equipe do SDK do Azure que é funcionalmente equivalente às bibliotecas de cliente [Microsoft. Azure. Search](/dotnet/api/overview/azure/search/client10) , mas utiliza abordagens e convenções comuns quando aplicável. Alguns exemplos incluem a [`AzureKeyCredential`](/dotnet/api/azure.azurekeycredential) autenticação de chave e [System.Text.Jsno. Serialização](/dotnet/api/system.text.json.serialization) para SERIALIZAÇÃO JSON.

Assim como nas versões anteriores, você pode usar essa biblioteca para:

+ Criar e gerenciar índices de pesquisa, fontes de dados, indexadores, habilidades e mapas de sinônimos
+ Carregar e gerenciar documentos de pesquisa em um índice
+ Execute consultas, tudo sem precisar lidar com os detalhes de HTTP e JSON

A biblioteca é distribuída como um único [Azure.Search.Docpacote NuGet do uments](https://www.nuget.org/packages/Azure.Search.Documents/), que inclui todas as APIs usadas para acesso programático a um serviço de pesquisa.

A biblioteca de cliente define classes como `SearchIndex`, `SearchField` e `SearchDocument`, e operações como `SearchIndexClient.CreateIndex` e `SearchClient.Search` nas classes `SearchIndexClient` e `SearchClient`. Essas classes são organizadas nos namespaces a seguir:

+ [`Azure.Search.Documents`](/dotnet/api/azure.search.documents)
+ [`Azure.Search.Documents.Indexes`](/dotnet/api/azure.search.documents.indexes)
+ [`Azure.Search.Documents.Indexes.Models`](/dotnet/api/azure.search.documents.indexes.models)
+ [`Azure.Search.Documents.Models`](/dotnet/api/azure.search.documents.models)

Azure.Search.Documents (versão 11) tem como destino [ `2020-06-30` a versão da API REST do Azure pesquisa cognitiva](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Azure.Search/preview/2020-06-30). 

A biblioteca de cliente não fornece [operações de gerenciamento de serviços](/rest/api/searchmanagement/), como criar e dimensionar serviços de pesquisa e gerenciar chaves de API. Se você precisar gerenciar seus recursos de pesquisa de um aplicativo .NET, use a biblioteca [Microsoft. Azure. Management. Search](/dotnet/api/overview/azure/search/management) no SDK do Azure para .net.

## <a name="upgrade-to-v11"></a>Atualizar para o v11

Se você estiver usando a versão anterior do SDK do .NET e quiser atualizar para a versão atual disponível, consulte [atualizar para o Azure pesquisa cognitiva SDK do .NET versão 11](search-dotnet-sdk-migration-version-11.md)

## <a name="sdk-requirements"></a>Requisitos do SDK

+ Visual Studio 2019 ou posterior.

+ Seu próprio serviço de Pesquisa Cognitiva do Azure. Para usar o SDK, você precisará do nome do serviço e de uma ou mais chaves de API. [Crie um serviço no portal](search-create-service-portal.md) se você não tiver um.

+ Baixe o [pacoteAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents) usando **ferramentas**  >  **Gerenciador de pacotes NuGet**  >  **gerenciar pacotes NuGet para solução** no Visual Studio. Procure o nome do pacote `Azure.Search.Documents` .

O SDK do Azure para .NET está em conformidade com o [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support), que significa .NET Framework 4.6.1 e .NET Core 2.0 como requisitos mínimos.

## <a name="example-application"></a>Aplicativo de exemplo

Este artigo "ensina por exemplo", contando com o exemplo de código [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) no GitHub para ilustrar conceitos fundamentais no Azure pesquisa cognitiva, especificamente, como criar, carregar e consultar um índice de pesquisa.

Para o restante deste artigo, suponha um novo índice chamado "Hotéis", preenchido com alguns documentos, com várias consultas que correspondem aos resultados.

Abaixo está o programa principal, mostrando o fluxo geral:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchIndexClient indexClient = CreateSearchIndexClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, indexClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, indexClient);

    SearchClient searchClient = indexClient.GetSearchClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(searchClient);

    SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

    Console.WriteLine("{0}", "Run queries...\n");
    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

Em seguida, há uma captura de tela parcial da saída, supondo que você execute esse aplicativo com um nome de serviço válido e chaves de API:

:::image type="content" source="media/search-howto-dotnet-sdk/console-output.png" alt-text="Saída de console. WriteLine do programa de exemplo":::

### <a name="client-types"></a>Tipos de cliente

A biblioteca de cliente usa três tipos de cliente para várias operações: [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) para criar, atualizar ou excluir índices, [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) carregar ou consultar um índice e [`SearchIndexerClient`](/dotnet/api/azure.search.documents.indexes.searchindexerclient) trabalhar com indexadores e habilidades. Este artigo se concentra nas duas primeiras. 

No mínimo, todos os clientes exigem o nome do serviço ou o ponto de extremidade e uma chave de API. É comum fornecer essas informações em um arquivo de configuração, semelhante ao que você encontra no `appsettings.json` arquivo do [aplicativo de exemplo DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo). Para ler o arquivo de configuração, adicione `using Microsoft.Extensions.Configuration;` ao seu programa.

A instrução a seguir cria o cliente de índice usado para criar, atualizar ou excluir índices. Ele usa um ponto de extremidade de pesquisa e uma chave de API de administração.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceEndPoint), new AzureKeyCredential(adminApiKey));
    return indexClient;
}
```

A próxima instrução cria o cliente de pesquisa usado para carregar documentos ou executar consultas. `SearchClient` requer um índice. Você precisará de uma chave de API de administração para carregar documentos, mas você pode usar uma chave de API de consulta para executar consultas. 

```csharp
string indexName = configuration["SearchIndexName"];

private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

> [!NOTE]
> Se você fornecer uma chave inválida para a operação de importação (por exemplo, uma chave de consulta onde uma chave de administrador era necessária), o gerará `SearchClient` um `CloudException` com a mensagem de erro "proibido" na primeira vez em que você chamar um método de operação. Se isso acontecer com você, verifique novamente a chave de API.
>

### <a name="deleting-the-index"></a>Excluindo o índice

Nos estágios iniciais do desenvolvimento, talvez você queira incluir uma [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) instrução para excluir um índice de trabalho em andamento para que você possa recriá-lo com uma definição atualizada. O código de exemplo para o Azure Pesquisa Cognitiva geralmente inclui uma etapa de exclusão para que você possa executar novamente o exemplo.

As seguintes chamadas de linha `DeleteIndexIfExists` :

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, indexClient);
```

Esse método usa o fornecido `SearchIndexClient` para verificar se o índice existe e, em caso afirmativo, exclui-lo:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchIndexClient indexClient)
{
    try
    {
        if (indexClient.GetIndex(indexName) != null)
        {
            indexClient.DeleteIndex(indexName);
        }
    }
    catch (RequestFailedException e) when (e.Status == 404)
    {
        // Throw an exception if the index name isn't found
        Console.WriteLine("The index doesn't exist. No deletion occurred.");
```

> [!NOTE]
> O código de exemplo neste artigo usa os métodos síncronos para simplificar, mas você deve usar os métodos assíncronos em seus próprios aplicativos para mantê-los escalonáveis e responsivos. Por exemplo, no método acima, você poderia usar [`DeleteIndexAsync`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindexasync) em vez de [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) .
>

## <a name="create-an-index"></a>Crie um índice

Você pode usar [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) para criar um índice. 

O método a seguir cria um novo [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) objeto com uma lista de [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) objetos que definem o esquema do novo índice. Cada campo tem um nome, tipo de dados e vários atributos que definem seu comportamento de pesquisa. 

Os campos podem ser definidos de uma classe de modelo usando [`FieldBuilder`](/dotnet/api/azure.search.documents.indexes.fieldbuilder) . A classe `FieldBuilder` usa a reflexão para criar uma lista de objetos `SearchField` para o índice, examinando os atributos e propriedades públicas da classe do modelo `Hotel` determinada. Posteriormente, faremos uma análise mais detalhada da classe `Hotel`.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    indexClient.CreateOrUpdateIndex(definition);
}
```

Além dos campos, você também pode adicionar perfis de pontuação, sugestores ou opções de CORS ao índice (esses parâmetros são omitidos do exemplo para fins de brevidade). Você pode encontrar mais informações sobre o objeto SearchIndex e suas partes constituintes na [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) lista de propriedades, bem como na [referência da API REST](/rest/api/searchservice/).

> [!NOTE]
> Você pode criar quando desejar a lista de objetos `Field` diretamente, em vez de usar o `FieldBuilder` se necessário. Por exemplo, você pode não querer usar uma classe de modelo ou pode precisar usar uma classe de modelo existente que não deseja modificar adicionando atributos.
>

### <a name="call-createindex-in-main"></a>Chamar CreateIndex no Main ()

`Main` Cria um novo índice de "Hotéis" chamando o método acima:

```csharp
Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, indexClient);
```

## <a name="use-a-model-class-for-data-representation"></a>Usar uma classe de modelo para representação de dados

O exemplo DotNetHowTo usa classes de modelo para as estruturas de dados de [Hotel](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Hotel.cs), [endereço](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Address.cs)e [sala](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Room.cs) . `Hotel` referências `Address` , um tipo complexo de nível único (um campo de várias partes) e `Room` (uma coleção de campos de várias partes).

Você pode usar esses tipos para criar e carregar o índice e para estruturar a resposta de uma consulta:

```csharp
// Use-case: <Hotel> in a field definition
FieldBuilder fieldBuilder = new FieldBuilder();
var searchFields = fieldBuilder.Build(typeof(Hotel));

// Use-case: <Hotel> in a response
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Uma abordagem alternativa é adicionar campos a um índice diretamente. O exemplo a seguir mostra apenas alguns campos.

   ```csharp
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };
   ```

### <a name="field-definitions"></a>Definições de campo

Seu modelo de dados no .NET e seu esquema de índice correspondente devem dar suporte à experiência de pesquisa que você gostaria de dar ao usuário final. Cada objeto de nível superior no .NET, como um documento de pesquisa em um índice de pesquisa, corresponde a um resultado de pesquisa que você estaria presente na sua interface do usuário. Por exemplo, em um aplicativo de pesquisa de Hotel, seus usuários finais talvez queiram Pesquisar por nome de Hotel, recursos do hotel ou as características de um espaço específico. 

Dentro de cada classe, um campo é definido com um tipo de dados e atributos que determinam como ele é usado. O nome de cada propriedade pública em cada classe é mapeado para um campo com o mesmo nome na definição de índice. 

Examine o trecho a seguir que efetua pull de várias definições de campo da classe Hotel. Observe que o endereço e as salas são tipos C# com suas próprias definições de classe (consulte o código de exemplo se você quiser exibi-los). Ambos são tipos complexos. Para obter mais informações, consulte [como modelar tipos complexos](search-howto-complex-data-types.md).

```csharp
public partial class Hotel
{
    [SimpleField(IsKey = true, IsFilterable = true)]
    public string HotelId { get; set; }

    [SearchableField(IsSortable = true)]
    public string HotelName { get; set; }

    [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
    public string Description { get; set; }

    [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
    public string Category { get; set; }

    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [SearchableField]
    public Address Address { get; set; }

    public Room[] Rooms { get; set; }
```

#### <a name="choosing-a-field-class"></a>Escolhendo uma classe de campo

Ao definir campos, você pode usar a [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) classe base ou pode usar modelos auxiliares derivados que servem como "modelos", com propriedades pré-configuradas.

Exatamente um campo no índice deve servir como a chave do documento ( `IsKey = true` ). Ele deve ser uma cadeia de caracteres e deve identificar exclusivamente cada documento. Também é necessário ter `IsHidden = true` , o que significa que não pode ser visível nos resultados da pesquisa.

| Tipo de campo | Descrição e uso |
|------------|-----------------------|
| [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) | Classe base, com a maioria das propriedades definidas como NULL, exceto `Name` o que é necessário e `AnalyzerName` que usa como padrão o Lucene padrão. |
| [`SimpleField`](/dotnet/api/azure.search.documents.indexes.models.simplefield) | Modelo auxiliar. Pode ser qualquer tipo de dados, sempre é não pesquisável (ignorado para consultas de pesquisa de texto completo) e é recuperável (não está oculto). Os outros atributos estão desativados por padrão, mas podem ser habilitados. Você pode usar um `SimpleField` para IDs ou campos de documentos usados somente em filtros, facetas ou perfis de pontuação. Nesse caso, lembre-se de aplicar todos os atributos necessários para o cenário, como `IsKey = true` para uma ID de documento. Para obter mais informações, confira [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) no código-fonte. |
| [`SearchableField`](/dotnet/api/azure.search.documents.indexes.models.searchablefield) | Modelo auxiliar. Deve ser uma cadeia de caracteres e sempre é pesquisável e recuperável. Os outros atributos estão desativados por padrão, mas podem ser habilitados. Como esse tipo de campo é pesquisável, ele dá suporte a sinônimos e ao complemento completo de propriedades do analisador. Para obter mais informações, confira [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) no código-fonte. |

Se você usar a API `SearchField` básica ou um dos modelos auxiliares, precisará habilitar explicitamente os atributos de filtro, faceta e classificação. Por exemplo, [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) e [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) devem ser explicitamente atribuídos, conforme mostrado no exemplo acima.

#### <a name="adding-field-attributes"></a>Adicionando atributos de campo

Observe como cada campo é decorado com atributos como `IsFilterable` , `IsSortable` , `IsKey` e `AnalyzerName` . Esses atributos são mapeados diretamente para os [atributos de campo correspondentes em um índice de pesquisa cognitiva do Azure](/rest/api/searchservice/create-index). A `FieldBuilder` classe usa essas propriedades para construir definições de campo para o índice.

#### <a name="field-type-mapping"></a>Mapeamento de tipo de campo

Os tipos .NET das propriedades são mapeados para seus tipos de campo equivalentes na definição do índice. Por exemplo, a propriedade de cadeia de caracteres `Category` mapeia para o campo `category`, que é do tipo `Edm.String`. Há mapeamentos de tipo semelhantes entre `bool?` , `Edm.Boolean` , `DateTimeOffset?` e `Edm.DateTimeOffset` e assim por diante. 

Você observou a `SmokingAllowed` Propriedade?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

O `JsonIgnore` atributo nessa propriedade informa ao `FieldBuilder` para não serializá-lo para o índice como um campo.  Essa é uma ótima maneira de criar propriedades calculadas no lado do cliente que você pode usar como auxiliares em seu aplicativo.  Nesse caso, a `SmokingAllowed` propriedade reflete se alguma `Room` na `Rooms` coleção permite fumante. Se todos forem falsos, isso indica que o Hotel inteiro não permite fumante.

## <a name="load-an-index"></a>Carregue um índice

A próxima etapa em `Main` popula o índice "Hotéis" criado recentemente. Essa população de índice é feita no seguinte método: (algum código foi substituído por "..." para fins de ilustração. Consulte a solução de exemplo completo para obter o código completo de população de dados.)

```csharp
private static void UploadDocuments(SearchClient searchClient)
{
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
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
            }),
        IndexDocumentsAction.Upload(
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
            }),
        IndexDocumentsAction.Upload(
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

    try
    {
        IndexDocumentsResult result = searchClient.IndexDocuments(batch);
    }
    catch (Exception)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine("Failed to index some of the documents: {0}");
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
```

Este método tem quatro partes. O primeiro cria uma matriz de três `Hotel` objetos cada um com três `Room` objetos que servirão como nossos dados de entrada para carregar no índice. Esses dados são codificados para manter a simplicidade. Em um aplicativo real, os dados provavelmente serão provenientes de uma fonte de dados externa, como um SQL Database.

A segunda parte cria um [`IndexDocumentsBatch`](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) contendo os documentos. Você especifica a operação que deseja aplicar ao lote no momento em que criá-lo, nesse caso chamando [`IndexDocumentsAction.Upload`](/dotnet/api/azure.search.documents.models.indexdocumentsaction.upload) . Em seguida, o lote é carregado no índice de Pesquisa Cognitiva do Azure pelo [`IndexDocuments`](/dotnet/api/azure.search.documents.searchclient.indexdocuments) método.

> [!NOTE]
> Neste exemplo, estamos carregando apenas documentos. Se você quiser mesclar alterações em documentos existentes ou excluir documentos, poderá criar lotes chamando `IndexDocumentsAction.Merge`, `IndexDocumentsAction.MergeOrUpload` ou `IndexDocumentsAction.Delete`. Você também pode misturar operações diferentes em um único lote chamando `IndexBatch.New` , que usa uma coleção de `IndexDocumentsAction` objetos, cada um dos quais diz ao Azure pesquisa cognitiva executar uma operação específica em um documento. Você pode criar cada `IndexDocumentsAction` com sua própria operação chamando o método correspondente como `IndexDocumentsAction.Merge`, `IndexAction.Upload` e assim por diante.
> 

A terceira parte desse método é um bloco catch que trata um caso de erro importante para indexação. Se o serviço de pesquisa não indexar alguns dos documentos no lote, um `IndexBatchException` será lançado pelo `IndexDocuments` . Essa exceção pode ocorrer se você estiver indexando documentos enquanto seu serviço está sob carga pesada. **É altamente recomendável a manipulação explícita desse caso em seu código.**  Você pode atrasar e repetir a indexação de documentos que falharam, ou você pode registrar em log e continuar, como faz o exemplo, ou pode alguma outra coisa, dependendo dos requisitos de consistência de dados do aplicativo.

Por fim, o método `UploadDocuments` atrasa por dois segundos. A indexação ocorre de forma assíncrona em seu serviço de pesquisa, portanto, o aplicativo de exemplo precisa aguardar um pouco de tempo para garantir que os documentos estejam disponíveis para pesquisa. Normalmente, atrasos como esses só são necessários em demonstrações, testes e exemplos de aplicativos.

### <a name="call-uploaddocuments-in-main"></a>Chamar UploadDocuments no Main ()

O trecho de código a seguir configura uma instância do [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) usando o [`GetSearchClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient.getsearchclient) método de indexClient. O indexClient usa uma chave de API de administração em suas solicitações, o que é necessário para carregar ou atualizar documentos.

Uma abordagem alternativa é chamar `SearchClient` diretamente, passando uma chave de API de administração no `AzureKeyCredential` .

```csharp
SearchClient searchClient = indexClient.GetSearchClient(indexName);

Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(searchClient);
```

## <a name="run-queries"></a>Executar consultas

Primeiro, configure um `SearchClient` que leia o ponto de extremidade de pesquisa e a chave de API de consulta de **appsettings.jsem**:

```csharp
private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

Em segundo lugar, defina um método que envia uma solicitação de consulta. 

Cada vez que o método executa uma consulta, ele cria um novo [`SearchOptions`](/dotnet/api/azure.search.documents.searchoptions) objeto. Esse objeto é usado para especificar opções adicionais para a consulta, como classificação, filtragem, paginação e faceta. Nesse método, estamos definindo a `Filter` `Select` propriedade, e `OrderBy` para consultas diferentes. Para obter mais informações sobre a sintaxe de expressão de consulta de pesquisa, [sintaxe de consulta simples](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).

A próxima etapa é executar a consulta de pesquisa. A execução da pesquisa é feita usando o `SearchClient.Search` método. Para cada consulta, passe o texto de pesquisa para usar como uma cadeia de caracteres (ou `"*"` se não houver nenhum texto de pesquisa), além das opções de pesquisa criadas anteriormente. Também podemos especificar `Hotel` como o parâmetro de tipo para `SearchClient.Search`, que informa ao SDK para desserializar documentos nos resultados da pesquisa em objetos do tipo `Hotel`.

```csharp
private static void RunQueries(SearchClient searchClient)
{
    SearchOptions options;
    SearchResults<Hotel> results;

    Console.WriteLine("Query 1: Search for 'motel'. Return only the HotelName in results:\n");

    options = new SearchOptions();
    options.Select.Add("HotelName");

    results = searchClient.Search<Hotel>("motel", options);

    WriteDocuments(results);

    Console.Write("Query 2: Apply a filter to find hotels with rooms cheaper than $100 per night, ");
    Console.WriteLine("returning the HotelId and Description:\n");

    options = new SearchOptions()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)"
    };
    options.Select.Add("HotelId");
    options.Select.Add("Description");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.Write("Query 3: Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    options =
        new SearchOptions()
        {
            Size = 2
        };
    options.OrderBy.Add("LastRenovationDate desc");
    options.Select.Add("HotelName");
    options.Select.Add("LastRenovationDate");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.WriteLine("Query 4: Search the HotelName field for the term 'hotel':\n");

    options = new SearchOptions();
    options.SearchFields.Add("HotelName");

    //Adding details to select, because "Location" is not supported yet when deserialize search result to "Hotel"
    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Category");
    options.Select.Add("Tags");
    options.Select.Add("ParkingIncluded");
    options.Select.Add("LastRenovationDate");
    options.Select.Add("Rating");
    options.Select.Add("Address");
    options.Select.Add("Rooms");

    results = searchClient.Search<Hotel>("hotel", options);

    WriteDocuments(results);
}
```

Em terceiro lugar, defina um método que grave a resposta, imprimindo cada documento no console:

```csharp
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="call-runqueries-in-main"></a>Chamar RunQueries no Main ()

```csharp
SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

Console.WriteLine("{0}", "Running queries...\n");
RunQueries(indexClientForQueries);
```

### <a name="explore-query-constructs"></a>Explorar construções de consulta

Vejamos cada uma das consultas com mais detalhes. Este é o código para executar a primeira consulta:

```csharp
options = new SearchOptions();
options.Select.Add("HotelName");

results = searchClient.Search<Hotel>("motel", options);

WriteDocuments(results);
```

Nesse caso, Estamos pesquisando o índice inteiro para a palavra "Motel" em qualquer campo pesquisável e só desejamos recuperar os nomes dos hotéis, conforme especificado pela `Select` opção. Estes são os resultados:

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

Na segunda consulta, use um filtro para selecionar sala com uma taxa noturna de menos de $100. Retorne apenas a ID e a descrição do Hotel nos resultados:

```csharp
options = new SearchOptions()
{
    Filter = "Rooms/any(r: r/BaseRate lt 100)"
};
options.Select.Add("HotelId");
options.Select.Add("Description");

results = searchClient.Search<Hotel>("*", options);
```

A consulta acima usa uma `$filter` expressão OData, `Rooms/any(r: r/BaseRate lt 100)` , para filtrar os documentos no índice. Isso usa o [operador any](./search-query-odata-collection-operators.md) para aplicar o ' BaseRate lt 100 ' a todos os itens na coleção Rooms. Para obter mais informações, consulte [sintaxe de filtro OData](./query-odata-filter-orderby-syntax.md).

Na terceira consulta, encontre os dois principais hotéis que foram mais recentemente renovadosdos e mostre o nome do hotel e a data da última reforma. Eis o código: 

```csharp
options =
    new SearchOptions()
    {
        Size = 2
    };
options.OrderBy.Add("LastRenovationDate desc");
options.Select.Add("HotelName");
options.Select.Add("LastRenovationDate");

results = searchClient.Search<Hotel>("*", options);

WriteDocuments(results);
```

Na última consulta, localize todos os nomes de hotéis que correspondem à palavra "Hotel":

```csharp
options.Select.Add("HotelId");
options.Select.Add("HotelName");
options.Select.Add("Description");
options.Select.Add("Category");
options.Select.Add("Tags");
options.Select.Add("ParkingIncluded");
options.Select.Add("LastRenovationDate");
options.Select.Add("Rating");
options.Select.Add("Address");
options.Select.Add("Rooms");

results = searchClient.Search<Hotel>("hotel", options);

WriteDocuments(results);
```

Esta seção conclui esta introdução ao SDK do .NET, mas não pare aqui. A próxima seção sugere recursos adicionais para aprender mais sobre a programação com o Azure Pesquisa Cognitiva.

## <a name="next-steps"></a>Próximas etapas

+ Procurar a documentação de referência da API para [Azure.Search.Documents](/dotnet/api/azure.search.documents) e a [API REST](/rest/api/searchservice/)

+ Procure outros exemplos de código com base em Azure.Search.Documents no [Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) e [Search-Getting Started-dotnet](https://github.com/Azure-Samples/search-dotnet-getting-started)

+ Examinar as [convenções de nomenclatura](/rest/api/searchservice/Naming-rules) para aprender as regras para nomear vários objetos

+ Examinar [tipos de dados com suporte](/rest/api/searchservice/Supported-data-types)
