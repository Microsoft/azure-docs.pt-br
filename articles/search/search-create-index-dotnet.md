---
title: "Criar um índice (API .NET - Azure Search) | Microsoft Docs"
description: "Criar um índice no código usando o SDK .NET do Azure Search."
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 3a851647-fc7b-4fb6-8506-6aaa519e77cd
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/22/2017
ms.author: brjohnst
ms.openlocfilehash: fac41903c3e5731d17f832ff58145fe74dfa29f1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Criar um índice do Azure Search usando o SDK do .NET
> [!div class="op_single_selector"]
> * [Visão geral](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Este artigo o orientará ao longo do processo de criação de um [índice](https://docs.microsoft.com/rest/api/searchservice/Create-Index) do Azure Search usando o [SDK .NET do Azure Search](https://aka.ms/search-sdk).

Antes de seguir este guia e criar um índice, você já deverá ter [criado um serviço do Azure Search](search-create-service-portal.md).

> [!NOTE]
> Todos os códigos de exemplo neste artigo foram escritos em C#. Você pode encontrar o código-fonte completo [no GitHub](http://aka.ms/search-dotnet-howto). Você também pode ler sobre o [SDK .NET do Azure Search](search-howto-dotnet-sdk.md) para passo a passo mais detalhado do código de exemplo.


## <a name="identify-your-azure-search-services-admin-api-key"></a>Identificar a api-key do administrador de seu serviço do Azure Search
Agora que provisionou um serviço do Azure Search, você está quase pronto para emitir solicitações em relação ao ponto de extremidade de serviço usando o SDK do .NET. Primeiro, é necessário obter uma das chaves de API do administrador gerada para o serviço de pesquisa que você provisionou. O SDK do .NET enviará está chave de API em cada solicitação ao seu serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

1. Para localizar as api-keys do serviço, entre no [portal do Azure](https://portal.azure.com/)
2. Vá para a folha do serviço de Azure Search
3. Clique no ícone de "Chaves"

O serviço terá *chaves de administração* e *chaves de consulta*.

* Suas *chaves de administração* principal e secundária concedem direitos totais para todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados. Há duas chaves para que você possa continuar a usar a chave secundária se decidir regenerar a chave primária e vice-versa.
* As *chaves de consulta* concedem acesso somente leitura a índices e documentos e normalmente são distribuídas a aplicativos cliente que emitem solicitações de pesquisa.

Para criar um índice, você pode usar a chave de administração principal ou secundária.

<a name="CreateSearchServiceClient"></a>

## <a name="create-an-instance-of-the-searchserviceclient-class"></a>Criar uma instância da classe de SearchServiceClient
Para começar a usar o SDK .NET do Azure Search, você precisará criar uma instância da classe `SearchServiceClient` . Essa classe tem vários construtores. Aquele que você deseja usa o nome do seu serviço de pesquisa e um objeto `SearchCredentials` como parâmetros. `SearchCredentials` encapsula sua api-key.

O código a seguir cria um `SearchServiceClient` usando valores para o nome do serviço de pesquisa e a api-key armazenados no arquivo de configuração do aplicativo (`appsettings.json` em caso do [aplicativo de exemplo](http://aka.ms/search-dotnet-howto)):

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

O `SearchServiceClient` tem uma propriedade `Indexes`. Essa propriedade fornece todos os métodos necessários para criar, listar, atualizar ou excluir índices do Azure Search.

> [!NOTE]
> A classe `SearchServiceClient` gerencia conexões para o seu serviço de pesquisa. Para evitar abrir um número excessivo de conexões, você deve tentar compartilhar uma única instância de `SearchServiceClient` em seu aplicativo, se possível. Esses métodos são thread-safe para habilitar esse compartilhamento.
> 
> 

<a name="DefineIndex"></a>

## <a name="define-your-azure-search-index"></a>Defina seu índice do Azure Search
Uma única chamada para o método `Indexes.Create` criará o seu índice. Esse método aceita como um parâmetro um objeto `Index` , que define o índice do seu Azure Search. Você precisa criar um objeto `Index` e inicializá-lo da seguinte maneira:

1. Defina a propriedade `Name` do objeto `Index` como o nome do índice.
2. Defina a propriedade `Fields` do objeto `Index` para uma matriz de objetos `Field`. A maneira mais fácil de criar os objetos `Field` é chamando o método `FieldBuilder.BuildForType`, passando uma classe de modelo para o parâmetro de tipo. Uma classe de modelo tem propriedades que mapeiam para os campos do seu índice. Isso permite a vinculação de documentos do índice de pesquisa a instâncias da sua classe de modelo.

> [!NOTE]
> Se você não planeja usar uma classe de modelo, você ainda pode definir o índice criando objetos `Field` diretamente. Você pode fornecer o nome do campo ao construtor, junto com o tipo de dados (ou o analisador para os campos de cadeia de caracteres). Também é possível definir outras propriedades como `IsSearchable`, `IsFilterable`, etc.
>
>

É importante ter em mente as suas necessidades de negócios e experiência de usuário de pesquisa ao projetar o índice, pois cada campo deve ter as [propriedades adequadas](https://docs.microsoft.com/rest/api/searchservice/Create-Index) atribuídas a ele. Essas propriedades controlam quais recursos de pesquisa (filtragem, facetas, classificação, pesquisa de texto completo etc.) se aplicam a quais campos. Para qualquer propriedade que você não defina explicitamente, a classe `Field` desabilita por padrão o recurso de pesquisa correspondente, a menos que você o habilite especificamente.

Para o nosso exemplo, nomeamos o índice "hotels" e definimos os campos usando uma classe de modelo. Cada propriedade da classe de modelo tem atributos que determinam os comportamentos relacionados à pesquisa do campo de índice correspondente. A classe de modelo é definida da seguinte maneira:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
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
}
```

Escolhemos cuidadosamente os atributos para cada propriedade com base em como achamos que serão usados em um aplicativo. Por exemplo, é provável que ao pesquisarem por "hotels", as pessoas estejam interessadas em correspondências de palavra-chave no campo `description`, por isso habilitamos a pesquisa de texto completo para esse campo, adicionando o atributo `IsSearchable` à propriedade `Description`.

Observe que exatamente um campo no índice do tipo `string` deve ser designado como o campo *chave*, adicionando o atributo `Key` (confira `HotelId` no exemplo acima).

A definição de índice acima usa um analisador de idioma para o campo `description_fr` porque ele se destina a armazenar texto em francês. Consulte [o tópico de Suporte a idiomas no](https://docs.microsoft.com/rest/api/searchservice/Language-support), bem como a [postagem do blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) correspondente para obter mais informações sobre os analisadores de linguagem.

> [!NOTE]
> Por padrão, o nome de cada propriedade na classe de modelo é usado como o nome do campo correspondente no índice. Se você desejar mapear todos os nomes de propriedade para nomes de campo em minúsculas concatenadas, marque a classe com o `SerializePropertyNamesAsCamelCase` atributo. Se você deseja mapear para um nome diferente, você pode usar o atributo `JsonProperty` como a propriedade `DescriptionFr` acima. O atributo `JsonProperty` tem precedência sobre o atributo `SerializePropertyNamesAsCamelCase`.
> 
> 

Agora que definimos uma classe de modelo, podemos criar facilmente uma definição de índice:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="create-the-index"></a>Criar o índice
Agora que você tem um objeto `Index` inicializado, é possível criar o índice chamando `Indexes.Create` no seu objeto `SearchServiceClient`:

```csharp
serviceClient.Indexes.Create(definition);
```

Para uma solicitação bem-sucedida, o método retornará manualmente. Se houver um problema com a solicitação como um parâmetro inválido, o método gerará `CloudException`.

Quando o índice estiver pronto e você desejar excluí-lo, basta chamar o método `Indexes.Delete` em `SearchServiceClient`. Por exemplo, veja como podemos excluir o índice "hotels":

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> O exemplo código deste artigo usa os métodos síncronos do SDK do .NET do seu Azure Search para manter a simplicidade. Recomendamos que você use os métodos assíncronos em seus próprios aplicativos para mantê-los escalonáveis e responsivos. Por exemplo, nos exemplos acima você pode usar `CreateAsync` e `DeleteAsync` em vez de `Create` e `Delete`.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Após criar um índice do Azure Search, você estará pronto para [carregar o conteúdo no índice](search-what-is-data-import.md) para que possa começar a pesquisar os dados.

