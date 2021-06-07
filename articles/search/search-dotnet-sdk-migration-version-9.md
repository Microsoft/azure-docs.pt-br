---
title: Atualizar para Azure Search SDK do .NET versão 9
titleSuffix: Azure Cognitive Search
description: Migre o código para o Azure Search SDK do .NET versão 9 de versões mais antigas. Conheça as novidades e quais alterações de código são necessárias.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 76cb2f94c949217c36fdccb8c64a53555bab227c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606327"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Atualizar para Azure Search SDK do .NET versão 9

Se você estiver usando a versão 7,0-Preview ou anterior do [SDK Azure Search .net](/dotnet/api/overview/azure/search), este artigo ajudará você a atualizar seu aplicativo para usar a versão 9.

> [!NOTE]
> Se você quiser usar a versão 8,0-Preview para avaliar recursos que ainda não estão disponíveis, você também pode seguir as instruções neste artigo para atualizar para 8,0-preview de versões anteriores.

Para obter uma explicação mais geral do SDK, incluindo exemplos, confira [Como usar o Azure Search de um aplicativo .NET](search-howto-dotnet-sdk.md).

A versão 9 do SDK do .NET Azure Search contém muitas alterações de versões anteriores. Algumas dessas alterações são significativas, mas elas devem exigir apenas alterações relativamente secundárias em seu código. Confira [Etapas da atualização](#UpgradeSteps) para obter instruções sobre como alterar o seu código para usar a nova versão do SDK.

> [!NOTE]
> Se você estiver usando a versão 4,0-Preview ou mais antiga, atualize para a versão 5 primeiro e, em seguida, atualize para a versão 9. Consulte [Atualizando para o SDK do .net Azure Search versão 5](search-dotnet-sdk-migration-version-5.md) para obter instruções.
>
> Sua instância de serviço do Azure Search dá suporte a várias versões da API REST, incluindo a última. Você pode continuar usando uma versão quando ela não for mais a última, mas aconselhamos a migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro api-version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se estiver usando um SDK mais antigo, você poderá continuar executando esse código sem alterações, mesmo se o serviço for atualizado para dar suporte a uma versão de API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>O que há de novo na versão 9
A versão 9 do SDK do .NET Azure Search tem como destino a versão 2019-05-06 da API REST do Azure Search, com os seguintes recursos:

* O [enriquecimento de ia](cognitive-search-concept-intro.md) é a capacidade de extrair texto de imagens, BLOBs e outras fontes de dados não estruturadas – enriquecendo o conteúdo para torná-lo mais pesquisável em um índice de Azure Search.
* O suporte para [tipos complexos](search-howto-complex-data-types.md) permite modelar quase todas as estruturas JSON aninhadas em um índice de Azure Search.
* O [preenchimento automático](search-add-autocomplete-suggestions.md) fornece uma alternativa para a API de **sugestão** para implementar o comportamento de pesquisa conforme o tipo. O preenchimento automático "termina" a palavra ou expressão que um usuário está digitando no momento.
* O [modo de análise de JsonLines](search-howto-index-json-blobs.md), parte da indexação de BLOBs do Azure, cria um documento de pesquisa por entidade JSON que é separado por uma nova linha.

### <a name="new-preview-features-in-version-80-preview"></a>Novos recursos de visualização na versão 8,0-Preview
Versão 8,0-visualização da API de destinos do SDK do .NET Azure Search versão 2017-11-11-Preview. Esta versão inclui todos os mesmos recursos da versão 9, além de:

* [As chaves de criptografia gerenciadas pelo cliente](search-security-manage-encryption-keys.md) para criptografia do lado do serviço em repouso são um novo recurso de visualização. Além da criptografia interna em repouso gerenciada pela Microsoft, você pode aplicar uma camada adicional de criptografia em que você é o único proprietário das chaves.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas da atualização
Primeiro, atualize sua referência NuGet para `Microsoft.Azure.Search` usando o Console do Gerenciador de Pacotes NuGet ou clicando com o botão direito do mouse nas referências do projeto e selecionando “Gerenciar pacotes NuGet...” no Visual Studio.

Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto. Dependendo de como o código está estruturado, ele poderá ser recriado com êxito. Nesse caso, você está pronto para começar!

Se sua compilação falhar, você precisará corrigir cada erro de compilação. Consulte [alterações significativas na versão 9](#ListOfChanges) para obter detalhes sobre como resolver cada erro de compilação em potencial.

Você pode ver avisos de build adicionais relacionados a propriedades ou métodos obsoletos. Os avisos incluirão instruções sobre o que deve ser usado no lugar do recurso preterido. Por exemplo, se seu aplicativo usar a `DataSourceType.DocumentDb` propriedade, você deverá receber um aviso dizendo que "Este membro foi preterido. Use CosmosDb em vez disso ".

Após corrigir todos os avisos ou erros de build, você poderá fazer alterações no aplicativo para aproveitar as novas funcionalidades, se desejar. Os novos recursos do SDK são detalhados no [que há de novo na versão 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Alterações recentes na versão 9

Há várias alterações significativas na versão 9 que podem exigir alterações de código, além de recompilar seu aplicativo.

> [!NOTE]
> A lista de alterações abaixo não é exaustiva. Algumas alterações provavelmente não resultarão em erros de compilação, mas são tecnicamente quebradas, pois elas violam a compatibilidade binária com assemblies que dependem de versões anteriores do Azure Search assemblies do SDK do .NET. Essas alterações não estão listadas abaixo. Recompile seu aplicativo ao atualizar para a versão 9 para evitar problemas de compatibilidade binária.

### <a name="immutable-properties"></a>Propriedades imutáveis

As propriedades públicas de várias classes de modelo agora são imutáveis. Se você precisar criar instâncias personalizadas dessas classes para teste, poderá usar os novos construtores com parâmetros:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Alterações no campo

A `Field` classe foi alterada agora que ela também pode representar campos complexos.

As propriedades a seguir `bool` agora são anuláveis:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Isso ocorre porque essas propriedades agora devem estar `null` no caso de campos complexos. Se você tiver um código que leia essas propriedades, ele precisa estar preparado para lidar `null` . Observe que todas as outras propriedades de `Field` sempre foram e continuam anuláveis, e algumas delas também estarão `null` no caso de campos complexos, especificamente o seguinte:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

O construtor sem parâmetros de `Field` foi feito `internal` . De agora em diante, cada `Field` um exige um nome explícito e um tipo de dados no momento da construção.

### <a name="simplified-batch-and-results-types"></a>Tipos de resultados e lote simplificados

Na versão 7,0-Preview e versões anteriores, as várias classes que encapsulam grupos de documentos foram estruturadas em hierarquias de classe paralelas:

  -  `DocumentSearchResult` e `DocumentSearchResult<T>` herdado de `DocumentSearchResultBase`
  -  `DocumentSuggestResult` e `DocumentSuggestResult<T>` herdado de `DocumentSuggestResultBase`
  -  `IndexAction` e `IndexAction<T>` herdado de `IndexActionBase`
  -  `IndexBatch` e `IndexBatch<T>` herdado de `IndexBatchBase`
  -  `SearchResult` e `SearchResult<T>` herdado de `SearchResultBase`
  -  `SuggestResult` e `SuggestResult<T>` herdado de `SuggestResultBase`

Os tipos derivados sem um parâmetro de tipo genérico foram destinados a serem usados em cenários de "tipo dinâmico" e no uso assumido do `Document` tipo.

A partir da versão 8,0-Preview, as classes base e as classes derivadas não genéricas foram removidas. Para cenários de tipo dinâmico, você pode usar `IndexBatch<Document>` , `DocumentSearchResult<Document>` e assim por diante.
 
### <a name="removed-extensibleenum"></a>ExtensibleEnum removido

A classe de base `ExtensibleEnum` foi removida. Todas as classes que derivam dela são agora structs, como `AnalyzerName` , `DataType` e `DataSourceType` por exemplo. Seus `Create` métodos também foram removidos. Você pode apenas remover chamadas para `Create` , pois esses tipos são implicitamente conversíveis de cadeias de caracteres. Se isso resultar em erros de compilador, você poderá invocar explicitamente o operador de conversão via conversão para tipos de ambiguidade. Por exemplo, você pode alterar um código como este:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

para isto:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

As propriedades que mantinham valores opcionais desses tipos agora são explicitamente digitadas como anuláveis para que continuem a ser opcionais.

### <a name="removed-facetresults-and-hithighlights"></a>FacetResults e HitHighlights removidos

As `FacetResults` classes e foram `HitHighlights` removidas. Os resultados da faceta agora são digitados como `IDictionary<string, IList<FacetResult>>` e os realces de clique como `IDictionary<string, IList<string>>` . Uma maneira rápida de resolver erros de compilação apresentados por essa alteração é adicionar `using` aliases na parte superior de cada arquivo que usa os tipos removidos. Por exemplo:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Alterar para SynonymMap 

O construtor `SynonymMap` não tem mais um parâmetro `enum` para `SynonymMapFormat`. Essa enumeração tinha apenas um valor e, portanto, era redundante. Se você observar erros de build como um resultado disso, basta remover as referências para o parâmetro `SynonymMapFormat`.

### <a name="miscellaneous-model-class-changes"></a>Alterações de classe de modelo diversas

A `AutocompleteMode` propriedade de `AutocompleteParameters` não é mais anulável. Se você tiver um código ao qual atribui essa propriedade `null` , poderá simplesmente removê-la e a propriedade será inicializada automaticamente para o valor padrão.

A ordem dos parâmetros para o `IndexAction` Construtor foi alterada agora que esse construtor é gerado automaticamente. Em vez de usar o construtor, é recomendável usar os métodos de fábrica `IndexAction.Upload` , `IndexAction.Merge` , e assim por diante.

### <a name="removed-preview-features"></a>Recursos de visualização removidos

Se você estiver atualizando da versão 8,0-Preview para a versão 9, lembre-se de que a criptografia com chaves gerenciadas pelo cliente foi removida, pois esse recurso ainda está em versão prévia. Especificamente, as `EncryptionKey` Propriedades de `Index` e foram `SynonymMap` removidas.

Se seu aplicativo tiver uma dependência difícil desse recurso, você não poderá atualizar para a versão 9 do SDK do .NET Azure Search. Você pode continuar a usar a versão 8,0-Preview. No entanto, lembre-se que **não é recomendável usar SDKs de visualização em aplicativos de produção**. Os recursos de visualização destinam-se apenas a fins de avaliação e podem ser alterados.

> [!NOTE]
> Se você criou índices criptografados ou mapas de sinônimos usando a versão 8,0-preview do SDK, ainda poderá usá-los e modificar suas definições usando a versão 9 do SDK sem afetar negativamente seu status de criptografia. A versão 9 do SDK não enviará a `encryptionKey` propriedade para a API REST e, como resultado, a API REST não alterará o status de criptografia do recurso. 

### <a name="behavioral-change-in-data-retrieval"></a>Alteração comportamental na recuperação de dados

Se você estiver usando as APIs "tipificadas dinamicamente" `Search` , `Suggest` ou `Get` que retornam instâncias do tipo, lembre-se de `Document` que agora elas desserializam matrizes JSON vazias para `object[]` em vez de `string[]` .

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre como usar o SDK do .NET do Azure Search, confira o [Tutorial .NET](search-howto-dotnet-sdk.md).

Apreciamos os seus comentários sobre o SDK. Se você encontrar problemas, sinta-se à vontade para nos pedir ajuda sobre [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se encontrar um bug, você poderá apresentar um problema no [repositório GitHub sobre o SDK do .NET do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Não deixe de colocar o prefixo "[Azure Search]" no título do problema.

Obrigado por usar o Azure Search!