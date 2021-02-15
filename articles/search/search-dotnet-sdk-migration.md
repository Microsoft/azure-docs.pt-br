---
title: Atualizar para Azure Search SDK do .NET versão 3
titleSuffix: Azure Cognitive Search
description: Migre o código para o SDK do .NET do Azure Search versão 3 de versões mais antigas. Conheça as novidades e quais alterações de código são necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e9f4602582a8c24f74d6603b732e7b91d97fa8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89002684"
---
# <a name="upgrade-to-azure-search-net-sdk-version-3"></a>Atualizar para Azure Search SDK do .NET versão 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Se você estiver usando a visualização da versão 2.0 ou mais antiga do [SDK .NET do Azure Search](/dotnet/api/overview/azure/search), este artigo ajudará você a atualizar seu aplicativo para usar a versão 3, mais recente.

Para obter uma explicação mais geral do SDK, incluindo exemplos, confira [Como usar o Azure Search de um aplicativo .NET](search-howto-dotnet-sdk.md).

A versão 3 do SDK do .NET do Azure Search contém algumas alterações de versões anteriores. A maioria das alterações é leve e, portanto, a alteração do seu código não deve exigir muito. Confira [Etapas da atualização](#UpgradeSteps) para obter instruções sobre como alterar o seu código para usar a nova versão do SDK.

> [!NOTE]
> Se você estiver usando a visualização da versão 1.0.2 ou mais antiga, será necessário primeiro atualizar para a versão 1.1 e, em seguida, atualizar para a versão 3. Confira [Atualizando para o SDK do .NET do Azure Search versão 1.1](search-dotnet-sdk-migration-version-1.md) para obter instruções.
>
> Sua instância de serviço do Azure Search dá suporte a várias versões da API REST, incluindo a última. Você pode continuar usando uma versão quando ela não for mais a última, mas aconselhamos a migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro api-version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se estiver usando um SDK mais antigo, você poderá continuar executando esse código sem alterações, mesmo se o serviço for atualizado para dar suporte a uma versão de API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Novidades na versão 3
A versão 3 do SDK do .NET do Azure Search destina-se à versão mais recente disponível da API REST do Azure Search, especificamente a 2016-09-01. Isso possibilita o uso de vários recursos novos do Azure Search por meio de um aplicativo .NET, incluindo o seguinte:

* [Analisadores personalizados](index-add-custom-analyzers.md)
* Suporte do indexador do [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) e do [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
* Personalização do indexador por meio de [mapeamentos de campo](search-indexer-field-mappings.md)
* O ETags dá suporte à habilitação da atualização simultânea segura de definições de índice, indexadores e fontes de dados
* Suporte para a criação de definições de campo de índice declarativamente decorando a classe de modelo e usando a nova classe `FieldBuilder`.
* Suporte para .NET Core e .NET Portable Profile 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas da atualização
Primeiro, atualize sua referência NuGet para `Microsoft.Azure.Search` usando o Console do Gerenciador de Pacotes NuGet ou clicando com o botão direito do mouse nas referências do projeto e selecionando “Gerenciar pacotes NuGet...” no Visual Studio.

Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto. Dependendo de como o código está estruturado, ele poderá ser recriado com êxito. Nesse caso, você está pronto para começar!

Se o build falhar, você verá um erro de build semelhante ao seguinte:

```output
Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)
```

A próxima etapa é corrigir esse erro de build. Veja [Alterações significativas na versão 3](#ListOfChanges) para ver mais detalhes sobre a causa do erro e como corrigi-lo.

Você pode ver avisos de build adicionais relacionados a propriedades ou métodos obsoletos. Os avisos incluirão instruções sobre o que deve ser usado no lugar do recurso preterido. Por exemplo, se seu aplicativo usar a propriedade `IndexingParameters.Base64EncodeKeys`, você deverá obter um aviso que diz `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Depois de corrigir os erros de build, será possível fazer alterações em seu aplicativo para tirar proveito da nova funcionalidade, se desejar. Novos recursos no SDK são detalhados em [Novidades na versão 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Alterações significativas na versão 3
Há algumas alterações significativas na versão 3 que podem exigir alterações de código, além da recompilação do aplicativo.

### <a name="indexesgetclient-return-type"></a>Tipo de retorno de Indexes.GetClient
O método `Indexes.GetClient` contém um novo tipo de retorno. Anteriormente, ele retornava `SearchIndexClient`, mas isso foi alterado para `ISearchIndexClient` na visualização da versão 2.0 e essa alteração foi trazida para a versão 3. Essa mudança ocorreu para dar suporte a clientes que querem simular o método `GetClient` para testes de unidade retornando uma implementação fictícia do `ISearchIndexClient`.

#### <a name="example"></a>Exemplo
Se o seu código tiver esta aparência:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Você poderá alterá-lo para corrigir os erros de compilação:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, DataType e outros não são implicitamente conversíveis para cadeias de caracteres
Há muitos tipos no SDK do .NET do Azure Search que derivam de `ExtensibleEnum`. Antes, todos esses tipos eram implicitamente conversíveis para o tipo `string`. No entanto, um bug foi descoberto na implementação `Object.Equals` para essas classes e foi necessário desabilitar essa conversão implícita para corrigi-lo. A conversão explícita para `string` ainda é permitida.

#### <a name="example"></a>Exemplo
Se o seu código tiver esta aparência:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

Você poderá alterá-lo para corrigir os erros de compilação:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Remover membros obsoletos

Você pode ver erros de build relacionados a métodos ou propriedades que foram marcados como obsoletos na visualização da versão 2.0 e subsequentemente removidos na versão 3. Se você encontrar esses erros, veja aqui como resolvê-los:

- Se usava o construtor: `ScoringParameter(string name, string value)`, use este em vez dele: `ScoringParameter(string name, IEnumerable<string> values)`
- Se você estiver usando a propriedade `ScoringParameter.Value`, use a propriedade `ScoringParameter.Values` ou o método `ToString` em vez dela.
- Se você estiver usando a propriedade `SearchRequestOptions.RequestId`, use a propriedade `ClientRequestId` em vez dela.

### <a name="removed-preview-features"></a>Recursos de visualização removidos

Se você estiver atualizando da visualização da versão 2.0 para a versão 3, lembre-se de que o suporte a análise JSON e CSV para a indexadores de Blobs foi removido, visto que esses recursos ainda estão em visualização. Especificamente, os seguintes métodos da classe `IndexingParametersExtensions` foram removidos:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Se seu aplicativo tiver uma dependência rígida desses recursos, você não poderá atualizar para a versão 3 do SDK do .NET do Azure Search. Você pode continuar a usar a visualização da versão 2.0. No entanto, lembre-se que **não é recomendável usar SDKs de visualização em aplicativos de produção**. Os recursos de visualização destinam-se apenas a fins de avaliação e podem ser alterados.

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre como usar o SDK do .NET do Azure Search, confira o [Tutorial .NET](search-howto-dotnet-sdk.md).

Apreciamos os seus comentários sobre o SDK. Se você encontrar problemas, sinta-se à vontade para nos pedir ajuda sobre [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se encontrar um bug, você poderá apresentar um problema no [repositório GitHub sobre o SDK do .NET do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Não deixe de colocar o prefixo "[Azure Search]" no título do problema.

Obrigado por usar o Azure Search!