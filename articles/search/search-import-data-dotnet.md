---
title: Carregar dados (.NET - Azure Search) | Microsoft Docs
description: "Aprenda a carregar dados em um índice na Pesquisa do Azure usando o SDK do .NET."
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: 
ms.assetid: 0e0e7e7b-7178-4c26-95c6-2fd1e8015aca
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/13/2017
ms.author: brjohnst
ms.openlocfilehash: bdd952869143c6ca6374bb9264db5bcba1f32b50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Carregar dados para a Pesquisa do Azure usando o SDK do .NET
> [!div class="op_single_selector"]
> * [Visão geral](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Este artigo mostrará como usar o [SDK do .NET de Pesquisa do Azure](https://aka.ms/search-sdk) para importar os dados para um índice de Pesquisa do Azure.

Antes de começar este passo a passo, você já deve ter [criado um índice de Pesquisa do Azure](search-what-is-an-index.md). Este artigo também presume que você já criou um objeto `SearchServiceClient` , conforme mostrado em [Criar um índice de Pesquisa do Azure usando o SDK do .NET](search-create-index-dotnet.md#CreateSearchServiceClient).

> [!NOTE]
> Todos os códigos de exemplo neste artigo foram escritos em C#. Você pode encontrar o código-fonte completo [no GitHub](http://aka.ms/search-dotnet-howto). Você também pode ler sobre o [SDK .NET do Azure Search](search-howto-dotnet-sdk.md) para passo a passo mais detalhado do código de exemplo.

Para enviar por push documentos no índice usando o SDK do .NET, você precisa:

1. Crie um objeto `SearchIndexClient` para conectar o índice de pesquisa.
2. Criar um `IndexBatch` contendo os documentos a serem adicionados, modificados ou excluídos.
3. Chame o método `Documents.Index` do `SearchIndexClient` para enviar o `IndexBatch` para o índice de pesquisa.

## <a name="create-an-instance-of-the-searchindexclient-class"></a>Criar uma instância da classe SearchIndexClient
Para importar os dados para o índice usando o SDK do .NET de Pesquisa do Azure, você precisará criar uma instância da classe `SearchIndexClient` . Você pode construir essa instância por conta própria, mas será mais fácil se já tiver uma instância `SearchServiceClient` para chamar o método `Indexes.GetClient`. Por exemplo, aqui está como você obteria um `SearchIndexClient` para o índice chamado "hotéis" a partir de um `SearchServiceClient` denominado `serviceClient`:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> Em um aplicativo típico de pesquisa, o gerenciamento e o preenchimento do índice é tratado por um componente separado das consultas de pesquisa. `Indexes.GetClient` é conveniente para preencher um índice porque poupa o trabalho de fornecer outras `SearchCredentials`. Ele faz isso passando a chave de administrador que você usou para criar o `SearchServiceClient` ao novo `SearchIndexClient`. No entanto, na parte do aplicativo que executa consultas, é melhor criar o `SearchIndexClient` diretamente para que você possa passar uma chave de consulta em vez de uma chave de administrador. Isso é consistente com o [princípio do privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) e ajudará a tornar seu aplicativo mais seguro. Você pode encontrar mais informações sobre as chaves de administração e as chaves de consulta na [Referência da API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/).
> 
> 

`SearchIndexClient` tem uma propriedade `Documents`. Esta propriedade fornece todos os métodos que você precisa para adicionar, modificar, excluir ou consultar documentos no índice.

## <a name="decide-which-indexing-action-to-use"></a>Decidir qual ação de indexação será usada
Para importar os dados usando o SDK do .NET, você precisará empacotar seus dados em um objeto `IndexBatch` . Um `IndexBatch` encapsula uma coleção de objetos `IndexAction`, que contém, cada um deles, um documento e uma propriedade que informam à Pesquisa do Azure qual ação executar nesse documento (carregar, mesclar, excluir etc.). Dependendo de qual das ações abaixo você escolher, apenas determinados campos deverão ser incluídos em cada documento:

| Ação | Descrição | Campos necessários para cada documento | Observações |
| --- | --- | --- | --- |
| `Upload` |Uma ação `Upload` é semelhante a um "upsert", em que o documento será inserido se for novo e atualizado/substituído se existir. |chave, além de quaisquer outros campos que você quiser definir |Ao atualizar/substituir um documento existente, qualquer campo não especificado na solicitação terá seu campo definido para `null`. Isso ocorre mesmo quando o campo tiver sido definido anteriormente como um valor não nulo. |
| `Merge` |Atualiza um documento existente com os campos especificados. Se o documento não existir no índice, a mesclagem falhará. |chave, além de quaisquer outros campos que você quiser definir |Qualquer campo que você especificar em uma mesclagem substituirá o campo existente no documento. Isso inclui campos do tipo `DataType.Collection(DataType.String)`. Por exemplo, se o documento contiver um campo `tags` com o valor `["budget"]` e você executar uma mesclagem com o valor `["economy", "pool"]` para `tags`, o valor final do campo `tags` será `["economy", "pool"]`. Ele não será `["budget", "economy", "pool"]`. |
| `MergeOrUpload` |Essa ação se comportará como `Merge` se já existir um documento com a chave especificada no índice. Se o documento não existir, ele se comportará como `Upload` com um novo documento. |chave, além de quaisquer outros campos que você quiser definir |- |
| `Delete` |Remove o documento especificado do índice. |somente chave |Todos os campos que você especificar, exceto o campo de chave, serão ignorados. Se você quiser remover um campo individual de um documento, use `Merge` e apenas defina o campo explicitamente para null. |

Você pode especificar a ação que deseja usar com os diversos métodos estáticos das classes `IndexBatch` e `IndexAction`, conforme mostrado na próxima seção.

## <a name="construct-your-indexbatch"></a>Construa seu IndexBatch
Agora que você sabe quais ações executar em seus documentos, está pronto para construir o `IndexBatch`. O exemplo a seguir mostra como criar um lote com algumas ações diferentes. Observe que nosso exemplo usa uma classe personalizada denominada `Hotel` que mapeia para um documento no índice "hotéis".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

Nesse caso, estamos usando `Upload`, `MergeOrUpload` e `Delete` como nossas ações de pesquisa, conforme especificado pelos métodos chamados na classe `IndexAction`.

Suponha que o índice de exemplo "hotels" já esteja preenchido com vários documentos. Observe como não precisamos especificar todos os campos de documento possíveis ao usar `MergeOrUpload` e como especificamos apenas a chave do documento (`HotelId`) ao usar `Delete`.

Além disso, observe que você só pode incluir até 1000 documentos em uma única solicitação de indexação.

> [!NOTE]
> Neste exemplo, estamos aplicando ações diferentes para documentos diferentes. Se você quisesse executar as mesmas ações em todos os documentos do lote, em vez de chamar `IndexBatch.New`, poderia usar os outros métodos estáticos de `IndexBatch`. Por exemplo, você poderia criar lotes chamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` ou `IndexBatch.Delete`. Esses métodos usam um conjunto de documentos (objetos do tipo `Hotel` neste exemplo) em vez de objetos `IndexAction`.
> 
> 

## <a name="import-data-to-the-index"></a>Importar dados para o índice
Agora que você tem um objeto `IndexBatch` inicializado, pode enviá-lo para o índice chamando `Documents.Index` em seu objeto `SearchIndexClient`. O exemplo a seguir mostra como chamar `Index`, assim como algumas etapas adicionais que você precisa executar:

```csharp
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
```

Observe `try`/`catch` em torno da chamada para o método `Index`. O bloco catch lida com um caso de erro importante para a indexação. Se o serviço de Pesquisa do Azure não indexar alguns documentos no lote, uma `IndexBatchException` será lançada por `Documents.Index`. Isso pode acontecer se você estiver indexando documentos enquanto o serviço estiver sob carga pesada. **É altamente recomendável a manipulação explícita desse caso em seu código.** Você pode atrasar e repetir a indexação de documentos que falharam, ou você pode registrar em log e continuar, como faz o exemplo, ou pode alguma outra coisa, dependendo dos requisitos de consistência de dados do aplicativo.

Por fim, o código no exemplo acima, atrasa por dois segundos. A indexação ocorre de maneira assíncrona em seu serviço de Pesquisa do Azure, portanto, o exemplo de aplicativo precisa aguardar alguns instantes para garantir que os documentos estejam disponíveis para pesquisa. Normalmente, atrasos como esses só são necessários em demonstrações, testes e exemplos de aplicativos.

<a name="HotelClass"></a>

### <a name="how-the-net-sdk-handles-documents"></a>Como o SDK do .NET lida com documentos
Você pode estar se perguntando como o SDK do .NET da Pesquisa do Azure é capaz de carregar instâncias de uma classe definida pelo usuário, como `Hotel` , no índice. Para ajudar a responder a essa pergunta, examinaremos a classe `Hotel` , que mapeia para o esquema de índice definido em [Criar um Índice de pesquisa do Azure usando o SDK do .NET](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

A primeira coisa a observar é que cada propriedade pública de `Hotel` corresponde a um campo na definição do índice, mas com uma diferença fundamental: o nome de cada campo começa com uma letra minúscula ("minúsculas concatenadas"), enquanto o nome de cada propriedade pública de `Hotel` começa com uma letra maiúscula ("maiúsculas concatenadas"). Esse é um cenário comum em aplicativos .NET que executam associação de dados quando o esquema de destino está fora do controle do desenvolvedor do aplicativo. Em vez de violar as diretrizes de nomenclatura do .NET, usando minúscula para os nomes de propriedade, você pode informar ao SDK para mapear automaticamente os nomes de propriedade como minúscula com o atributo `[SerializePropertyNamesAsCamelCase]` .

> [!NOTE]
> O SDK do .NET de Pesquisa do Azure usa a biblioteca [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar e desserializar os objetos de modelo personalizados para e a partir do JSON. Se necessário, você pode personalizar essa serialização. Encontre mais detalhes em [Serialização personalizada com JSON.NET](search-howto-dotnet-sdk.md#JsonDotNet). Um exemplo disso é o uso do atributo `[JsonProperty]` na propriedade `DescriptionFr` no código de exemplo acima.
> 
> 

Um segundo fator importante sobre a classe `Hotel` são os tipos de dados das propriedades públicas. Os tipos .NET dessas propriedades são mapeados para seus tipos de campo equivalentes na definição do índice. Por exemplo, a propriedade de cadeia de caracteres `Category` mapeia para o campo `category`, que é do tipo `DataType.String`. Há mapeamentos de tipo semelhantes entre `bool?` e `DataType.Boolean`, `DateTimeOffset?` e `DataType.DateTimeOffset` etc. As regras específicas para o mapeamento de tipos estão documentadas com o método `Documents.Get` na [referência do SDK do .NET do Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_).

Essa capacidade de usar suas próprias classes como documentos funciona em ambas as direções. Você também pode recuperar os resultados da pesquisa e fazer com que o SDK desserialize-os automaticamente para um tipo de sua escolha, como mostrado no [próximo artigo](search-query-dotnet.md).

> [!NOTE]
> O SDK do .NET da Pesquisa do Azure também oferece suporte a documentos do tipo dinâmico usando a classe `Document`, que é um mapeamento de chave/valor de nomes de campo para valores de campo. Isso é útil em cenários nos quais você não conhece o esquema de índice no momento do design, ou nos quais seria inconveniente associar a classes de modelo específico. Todos os métodos no SDK que lidam com documentos têm sobrecargas que funcionam com a classe `Document` , bem como sobrecargas fortemente tipadas que utilizam um parâmetro de tipo genérico. Somente as últimas são usadas no código de exemplo neste artigo.
> 
> 

**Por que você deve usar tipos de dados anuláveis**

Ao criar suas próprias classes de modelo para mapear para um índice de Pesquisa do Azure, sugerimos declarar as propriedades dos tipos de valor como `bool` e `int` para serem anuláveis (por exemplo, `bool?` em vez de `bool`). Se você usar uma propriedade não anulável, será preciso **assegurar** que nenhum documento no índice contenha um valor nulo para o campo correspondente. Nem o SDK, nem o serviço Pesquisa do Azure ajudarão você a impor isso.

Isso não é apenas uma preocupação hipotética: imagine um cenário em que você adiciona um novo campo a um índice existente do tipo `DataType.Int32`. Depois de atualizar a definição de índice, todos os documentos terão um valor nulo para esse novo campo (já que todos os tipos são anuláveis na Pesquisa do Azure). Ao usar uma classe de modelo com uma propriedade não anulável `int` para esse campo, você obterá uma `JsonSerializationException` como esta ao tentar recuperar os documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por esse motivo, sugerimos que você use tipos anuláveis nas suas classes de modelo como uma prática recomendada.

## <a name="next-steps"></a>Próximas etapas
Depois de popular o índice de Pesquisa do Azure, você estará pronto para começar a emitir consultas para pesquisar documentos. Veja [Consultar seu Índice de Pesquisa do Azure](search-query-overview.md) para obter detalhes.

