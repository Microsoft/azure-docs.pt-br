---
title: Criar um sugestor
titleSuffix: Azure Cognitive Search
description: Habilite ações de consulta de tipo antecipado no Azure Pesquisa Cognitiva criando sugestores e solicitações formular que invoquem os termos de consulta autocompletados ou autosugeridos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: a03ca4bcad9bb577db68e2728ff9dbebb5779a7a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626819"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Criar um Sugestor para habilitar o preenchimento automático e os resultados sugeridos em uma consulta

No Azure Pesquisa Cognitiva, typeahead ou "Pesquisar conforme o tipo" é habilitado por meio de um *Sugestor*. Um Sugestor fornece uma lista de campos que passam por geração de tokens adicionais, gerando sequências de prefixo para dar suporte a correspondências em termos parciais. Por exemplo, um Sugestor que inclui um campo cidade com um valor de "Seattle" terá combinações de prefixo "Sea", "assento", "assento" e "seattl" para dar suporte a typeahead.

As correspondências em termos parciais podem ser uma consulta autocompletada ou uma correspondência sugerida. O mesmo Sugestor dá suporte a ambas as experiências.

## <a name="typeahead-experiences-in-cognitive-search"></a>Typeahead experiências no Pesquisa Cognitiva

Typeahead pode ser *preenchimento automático*, que conclui uma entrada parcial para uma consulta de termo inteiro ou *sugestões* que o convite clica para uma correspondência específica. O preenchimento automático produz uma consulta. As sugestões produzem um documento correspondente.

A captura de tela a seguir de [criar seu primeiro aplicativo em C#](tutorial-csharp-type-ahead-and-suggestions.md) ilustra ambos. O preenchimento automático prevê um prazo potencial, concluindo "TW" com "in". Sugestões são resultados de mini pesquisa, onde um campo como nome de Hotel representa um documento de pesquisa de Hotel correspondente do índice. Para obter sugestões, você pode retonar qualquer campo que forneça informações descritivas.

![Comparação visual de consultas autocompletas e sugeridas](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Comparação visual de consultas autocompletas e sugeridas")

Você pode usar esses recursos separadamente ou juntos. Para implementar esses comportamentos no Azure Pesquisa Cognitiva, há um componente de índice e consulta. 

+ Adicionar um Sugestor a uma definição de índice de pesquisa. O restante deste artigo se concentra na criação de um Sugestor.

+ Chame uma consulta habilitada para sugestão, na forma de uma solicitação de sugestão ou solicitação de preenchimento automático, usando uma das [APIs listadas abaixo](#how-to-use-a-suggester).

O suporte de pesquisa conforme o tipo é habilitado em uma base por campo para campos de cadeia de caracteres. Você pode implementar ambos os comportamentos do typeahead na mesma solução de pesquisa se desejar uma experiência semelhante à indicada na captura de tela. Ambas as solicitações direcionam a coleção de *documentos* de índice e respostas específicas são retornadas depois que um usuário tiver fornecido pelo menos uma cadeia de caracteres de entrada de três caracteres.

## <a name="how-to-create-a-suggester"></a>Como criar um Sugestor

Para criar um Sugestor, adicione um a uma [definição de índice](/rest/api/searchservice/create-index). Um Sugestor pega um nome e uma coleção de campos sobre os quais a experiência do typeahead está habilitada. O melhor momento para criar um Sugestor é quando você também está definindo o campo que o usará.

+ Use somente campos de cadeia de caracteres.

+ Se o campo de cadeia de caracteres fizer parte de um tipo complexo (por exemplo, um campo cidade dentro do endereço), inclua o pai no caminho do campo: `"Address/City"` (REST e C# e Python) ou `["Address"]["City"]` (JavaScript).

+ Use o analisador Lucene padrão padrão ( `"analyzer": null` ) ou um [analisador de linguagem](index-add-language-analyzers.md) (por exemplo, `"analyzer": "en.Microsoft"` ) no campo.

Se você tentar criar um Sugestor usando campos preexistentes, a API não permitirá isso. Os prefixos são gerados durante a indexação, quando termos parciais em duas ou mais combinações de caracteres são indexados ao longo de termos inteiros. Considerando que os campos existentes já foram indexados, você precisará recompilar o índice se quiser adicioná-los a um Sugestor. Para obter mais informações, consulte [como recriar um índice de pesquisa cognitiva do Azure](search-howto-reindex.md).

### <a name="choose-fields"></a>Selecionar campos

Embora um Sugestor tenha várias propriedades, ele é basicamente uma coleção de campos de cadeia de caracteres para os quais você está habilitando uma experiência de pesquisa conforme o tipo. Há um Sugestor para cada índice, portanto, a lista de sugestores deve incluir todos os campos que contribuem com conteúdo para sugestões e preenchimento automático.

O preenchimento automático beneficia-se de um pool maior de campos para desenhar, pois o conteúdo adicional tem mais potencial de conclusão do termo.

As sugestões, por outro lado, produzem resultados melhores quando a opção de seu campo é seletiva. Lembre-se de que a sugestão é um proxy para um documento de pesquisa, para que você queira campos que melhor representem um único resultado. Os nomes, títulos ou outros campos exclusivos que distinguem entre várias correspondências funcionam melhor. Se os campos consistem em valores repetitivos, as sugestões consistem em resultados idênticos e um usuário não saberá em qual deles clicar.

Para atender a experiências de pesquisa conforme o uso, adicione todos os campos necessários para preenchimento automático, mas, em seguida, use "$select", "$top", "$filter" e "searchFields" para controlar os resultados das sugestões.

### <a name="choose-analyzers"></a>Escolher analisadores

Sua escolha de um analisador determina como os campos são indexados e subsequentemente prefixados. Por exemplo, para uma cadeia de caracteres hifenizada como "sensível ao contexto", o uso de um analisador de linguagem resultará nessas combinações de token: "contexto", "confidencial", "contextual". Se você usou o analisador Lucene padrão, a cadeia de caracteres hifenizada não existiria. 

Ao avaliar os analisadores, considere usar a [API de análise de texto](/rest/api/searchservice/test-analyzer) para obter informações sobre como os termos são processados. Depois de criar um índice, você pode tentar vários analisadores em uma cadeia de caracteres para exibir a saída do token.

Os campos que usam [analisadores personalizados](index-add-custom-analyzers.md) ou [analisadores internos](index-add-custom-analyzers.md#built-in-analyzers) (com exceção do Lucene padrão) são explicitamente despermitidos para evitar resultados fracos.

> [!NOTE]
> Se você precisar contornar a restrição do analisador, por exemplo, se precisar de uma palavra-chave ou ngram Analyzer para determinados cenários de consulta, use dois campos separados para o mesmo conteúdo. Isso permitirá que um dos campos tenha um Sugestor, enquanto o outro pode ser configurado com uma configuração personalizada do analisador.

## <a name="create-using-the-portal"></a>Criar usando o portal

Ao usar o assistente para **Adicionar índice** ou **importar dados** para criar um índice, você tem a opção de habilitar um Sugestor:

1. Na definição de índice, insira um nome para o Sugestor.

1. Em cada definição de campo para novos campos, marque uma caixa de seleção na coluna Sugestor. Uma caixa de seleção está disponível somente em campos de cadeia de caracteres. 

Como observado anteriormente, a opção do analisador afeta a geração de tokens e a prefixação. Considere a definição de campo inteira ao habilitar sugestores. 

## <a name="create-using-rest"></a>Criar usando REST

Na API REST, adicione sugestores por meio de [CREATE INDEX](/rest/api/searchservice/create-index) ou [Update index](/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>Criar usando o .NET

Em C#, defina um [objeto SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester). `Suggesters` é uma coleção em um objeto SearchIndex, mas só pode pegar um item. Adicione um Sugestor à definição do índice.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
    definition.Suggesters.Add(suggester);

    indexClient.CreateOrUpdateIndex(definition);
}
```

## <a name="property-reference"></a>Referência de propriedade

|Propriedade      |Descrição      |
|--------------|-----------------|
| name        | Especificado na definição do Sugestor, mas também chamado em uma solicitação de AutoCompletar ou de sugestões. |
| sourceFields | Especificado na definição do Sugestor. É uma lista de um ou mais campos no índice que são a origem do conteúdo para sugestões. Os campos devem ser do tipo `Edm.String` e `Collection(Edm.String)` . Se um analisador for especificado no campo, ele deverá ser um analisador léxico nomeado [desta lista](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) (não um analisador personalizado). </br></br>Como prática recomendada, especifique somente os campos que se prestam a uma resposta esperada e apropriada, seja uma cadeia de caracteres completa em uma barra de pesquisa ou uma lista suspensa. </br></br>Um nome de Hotel é um bom candidato porque tem precisão. Campos detalhados, como descrições e comentários, são muito densos. Da mesma forma, campos repetitivos, como categorias e marcas, são menos eficazes. Nos exemplos, incluímos "category" de qualquer forma para demonstrar que você pode incluir vários campos. |
| searchMode  | Parâmetro somente REST, mas também visível no Portal. Esse parâmetro não está disponível no SDK do .NET. Indica a estratégia usada para pesquisar frases candidatas. O único modo com suporte no momento é `analyzingInfixMatching` , que corresponde atualmente ao início de um termo.|

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Usar um Sugestor

Um Sugestor é usado em uma consulta. Depois que um Sugestor for criado, chame uma das seguintes APIs para uma experiência de pesquisa conforme o tipo:

+ [API REST de sugestões](/rest/api/searchservice/suggestions)
+ [API REST de preenchimento automático](/rest/api/searchservice/autocomplete)
+ [Método SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [Método AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

Em um aplicativo de pesquisa, o código do cliente deve aproveitar uma biblioteca, como o [jQuery interface do usuário](https://jqueryui.com/autocomplete/) , para coletar a consulta parcial e fornecer a correspondência. Para obter mais informações sobre essa tarefa, consulte [Adicionar resultados automáticos ou sugeridos ao código do cliente](search-add-autocomplete-suggestions.md).

O uso da API é ilustrado na chamada a seguir para a API REST de preenchimento automático. Há dois argumentos neste exemplo. Primeiro, assim como acontece com todas as consultas, a operação é contra a coleção de documentos de um índice e a consulta inclui um parâmetro de "pesquisa", que neste caso fornece a consulta parcial. Em segundo lugar, você deve adicionar "suggesterName" à solicitação. Se um Sugestor não estiver definido no índice, uma chamada para preenchimento automático ou sugestões falhará.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Código de exemplo

+ [Adicionar pesquisa a um site (JavaScript)](tutorial-javascript-search-query-integration.md#azure-function-suggestions-from-the-catalog) usa um pacote de sugestões de código-fonte aberto para conclusão de termo parcial no aplicativo cliente.

+ [Crie seu primeiro aplicativo em C# (lição 3 – adicionar a pesquisa conforme o tipo)](tutorial-csharp-type-ahead-and-suggestions.md) exemplo demonstra consultas sugeridas, preenchimento automático e navegação facetada. Esse código fornece suporte nativo para typeahead em vez de usar um widget.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre solicitações \ formulação.

> [!div class="nextstepaction"]
> [Adicionar preenchimento automático e sugestões ao código do cliente](search-add-autocomplete-suggestions.md)