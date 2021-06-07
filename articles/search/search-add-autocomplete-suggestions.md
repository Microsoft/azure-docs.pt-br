---
title: Adicionar preenchimento automático a uma caixa de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite as ações de consulta de pesquisa conforme o tipo no Azure Pesquisa Cognitiva criando sugestores e solicitações formulars que completam a caixa de pesquisa com termos ou frases concluídas. Você também pode retornar correspondências sugeridas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 6bc45bb6eec9dbf46e039dd1e2c32197820bb09d
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626700"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps-using-azure-cognitive-search"></a>Adicionar preenchimento automático e sugestões para aplicativos cliente usando o Azure Pesquisa Cognitiva

O Search-as-Type é uma técnica comum para melhorar a produtividade da consulta. No Azure Pesquisa Cognitiva, essa experiência tem suporte por meio de *preenchimento automático*, que termina um termo ou frase com base na entrada parcial (concluindo "micro" com "Microsoft"). Uma segunda experiência de usuário é *sugestões* ou uma lista curta de documentos correspondentes (retornando títulos de livros com uma ID para que você possa vincular a uma página de detalhes sobre esse livro). O preenchimento automático e as sugestões são predicadas em uma correspondência no índice. O serviço não oferecerá consultas que retornam zero resultados.

Para implementar essas experiências no Azure Pesquisa Cognitiva, será necessário:

+ Uma definição de *sugestão* que é inserida no esquema de índice.
+ Uma *consulta* que especifica a API de [sugestões](/rest/api/searchservice/suggestions) ou de [preenchimento automático](/rest/api/searchservice/autocomplete) na solicitação.
+ Um *controle de interface do usuário* para manipular interações de pesquisa conforme o tipo no aplicativo cliente. É recomendável usar uma biblioteca JavaScript existente para essa finalidade.

No Azure Pesquisa Cognitiva, as consultas autocompletadas e os resultados sugeridos são recuperados do índice de pesquisa, dos campos selecionados que você registrou com um Sugestor. Um Sugestor faz parte do índice e especifica quais campos fornecerão conteúdo que conclui uma consulta, sugere um resultado ou ambos. Quando o índice é criado e carregado, uma estrutura de dados de sugestão é criada internamente para armazenar prefixos usados para correspondência em consultas parciais. Para sugestões, escolher campos adequados que sejam exclusivos, ou pelo menos não repetitivo, é essencial para a experiência. Para obter mais informações, consulte [criar um Sugestor](index-add-suggesters.md).

O restante deste artigo tem como foco as consultas e o código do cliente. Ele usa JavaScript e C# para ilustrar os principais pontos. Exemplos de API REST são usados para representar de forma concisa cada operação. Para obter links para exemplos de código de ponta a ponta, consulte [próximas etapas](#next-steps).

## <a name="set-up-a-request"></a>Configurar uma solicitação

Os elementos de uma solicitação incluem uma das APIs de pesquisa conforme o tipo, uma consulta parcial e um Sugestor. O script a seguir ilustra os componentes de uma solicitação, usando a API REST de preenchimento automático como um exemplo.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

O "suggesterName" fornece os campos que reconhecem o sugestores usados para concluir os termos ou sugestões. Para sugestões em particular, a lista de campos deve ser composta por aquelas que oferecem opções claras entre os resultados correspondentes. Em um site que vende jogos de computador, o campo pode ser o título do jogo.

O parâmetro "Search" fornece a consulta parcial, em que os caracteres são alimentados para a solicitação de consulta por meio do controle de preenchimento automático do jQuery. No exemplo acima, "minecraf" é uma ilustração estática do que o controle pode ter passado.

As APIs não impõem requisitos de comprimento mínimo na consulta parcial; Ele pode ser tão pequeno quanto um caractere. No entanto, a autocompletação do jQuery fornece um comprimento mínimo. Um mínimo de dois ou três caracteres é típico.

As correspondências estão no início de um termo em qualquer lugar na cadeia de caracteres de entrada. Dada "The Quick Brown raposa", o preenchimento automático e as sugestões serão correspondentes em versões parciais de "The", "Quick", "Brown" ou "Fox", mas não em termos de infixos parciais como "rown" ou "Ox". Além disso, cada correspondência define o escopo para expansões downstream. Uma consulta parcial de "Quick br" corresponderá a "Quick Brown" ou "pão rápido", mas nem "Brown" ou "pão" por si só seriam correspondentes, a menos que "rápido" os preceda.

### <a name="apis-for-search-as-you-type"></a>APIs para pesquisa conforme o tipo

Siga estes links para as páginas de referência REST e SDK do .NET:

+ [API REST de sugestões](/rest/api/searchservice/suggestions) 
+ [API REST de preenchimento automático](/rest/api/searchservice/autocomplete) 
+ [Método SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [Método AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

## <a name="structure-a-response"></a>Estruturar uma resposta

As respostas para preenchimento automático e sugestões são as que você pode esperar para o padrão: o [preenchimento automático](/rest/api/searchservice/autocomplete#response) retorna uma lista de termos, as [sugestões](/rest/api/searchservice/suggestions#response) retornam termos mais uma ID de documento para que você possa buscar o documento (use a API de [documento de pesquisa](/rest/api/searchservice/lookup-document) para buscar o documento específico para uma página de detalhes).

As respostas são moldadas pelos parâmetros na solicitação:

+ Para preenchimento automático, defina [autocompleteMode](/rest/api/searchservice/autocomplete#query-parameters) para determinar se a conclusão do texto ocorre em um ou dois termos. 

+ Para sugestões, defina [$Select](/rest/api/searchservice/suggestionse#query-parameters) para retornar campos que contenham valores exclusivos ou diferenciais, como nomes e descrição. Evite campos que contenham valores duplicados (como uma categoria ou cidade).

Os seguintes parâmetros adicionais se aplicam a AutoCompletar e sugestões, mas talvez sejam mais necessários para sugestões, especialmente quando um Sugestor inclui vários campos.

| Parâmetro | Uso |
|-----------|-------|
| searchFields | Restringir a consulta a campos específicos. |
| $filter | Aplicar critérios de correspondência no conjunto de resultados ( `$filter=Category eq 'ActionAdventure'` ). |
| $top | Limitar os resultados a um número específico ( `$top=5` ).|

## <a name="add-user-interaction-code"></a>Adicionar código de interação do usuário

O preenchimento automático de um termo de consulta ou a remoção de uma lista de links correspondentes requer o código de interação do usuário, normalmente o JavaScript, que pode consumir solicitações de fontes externas, como preenchimento automático ou consultas de sugestão em um Azure Search índice cognitiva.

Embora você possa escrever esse código nativamente, é muito mais fácil usar funções da biblioteca JavaScript existente, como uma das opções a seguir. 

+ O [widget preenchimento automático (IU do jQuery)](https://jqueryui.com/autocomplete/) aparece no trecho de código de sugestão. Você pode criar uma caixa de pesquisa e, em seguida, referenciá-la em uma função JavaScript que usa o widget preenchimento automático. As propriedades no widget definem a origem (uma função de AutoCompletar ou de sugestões), o comprimento mínimo dos caracteres de entrada antes que a ação seja executada e o posicionamento.

+ O [plug-in de preenchimento automático do XDSoft](https://xdsoft.net/jqplugins/autocomplete/) aparece no trecho de código de preenchimento automático.

+ as [sugestões](https://www.npmjs.com/package/suggestions) são exibidas no [tutorial do JavaScript](tutorial-javascript-overview.md) e no exemplo de código.

Use essas bibliotecas no cliente para criar uma caixa de pesquisa com suporte a sugestões e preenchimento automático. As entradas coletadas na caixa de pesquisa podem ser emparelhadas com sugestões e ações de preenchimento automático no serviço de pesquisa.

## <a name="suggestions"></a>Sugestões

Esta seção orienta você pela implementação de resultados sugeridos, começando com a definição da caixa de pesquisa. Ele também mostra como e script que invoca a primeira biblioteca de preenchimento automático de JavaScript referenciada neste artigo.

### <a name="create-a-search-box"></a>Criar uma caixa de pesquisa

Supondo que a [biblioteca de preenchimento automático da interface do usuário do jQuery](https://jqueryui.com/autocomplete/) e um projeto do MVC em C#, você pode definir a caixa de pesquisa usando JavaScript no arquivo **index. cshtml** . A biblioteca adiciona a interação de pesquisa conforme o tipo à caixa de pesquisa, fazendo chamadas assíncronas para o controlador MVC para recuperar sugestões.

Em **index. cshtml** sob a pasta \Views\Home, uma linha para criar uma caixa de pesquisa pode ser a seguinte:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Esse exemplo é uma caixa de texto de entrada simples com uma classe para definir o estilo, uma ID para referência pelo JavaScript e o texto de espaço reservado.  

Dentro do mesmo arquivo, incorpore o JavaScript que faz referência à caixa de pesquisa. A função a seguir chama a API de sugestão, que solicita documentos correspondentes sugeridos com base em entradas de termo parcial:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

O `source` informa à função preenchimento automático da interface do usuário do jQuery onde obter a lista de itens a serem mostrados na caixa de pesquisa. Como esse projeto é um projeto do MVC, ele chama a função de **sugestão** em **HomeController. cs** que contém a lógica para retornar sugestões de consulta. Essa função também passa alguns parâmetros para controlar destaques, correspondências difusas e termos. A API JavaScript de preenchimento automático adiciona o parâmetro de termo.

O `minLength: 3` garante que as recomendações serão mostradas apenas quando houver pelo menos três caracteres na caixa de pesquisa.

### <a name="enable-fuzzy-matching"></a>Habilitar correspondência difusa

A pesquisa difusa permite que você obtenha resultados com base em correspondências próximas, mesmo se o usuário errou a escrita de uma palavra na caixa de pesquisa. A distância de edição é 1, o que significa que pode haver uma discrepância máxima de um caractere entre a entrada do usuário e uma correspondência. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Habilitar realce

O realce aplica o estilo da fonte aos caracteres no resultado que correspondem à entrada. Por exemplo, se a entrada parcial for "micro", o resultado apareceria como **micro** Soft, **micro** Scope e assim por diante. O realce é baseado nos parâmetros HighlightPreTag e HighlightPostTag, definidos embutidos com a função Suggestion.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Função de sugestão

Se você estiver usando C# e um aplicativo MVC, o arquivo **HomeController. cs** no diretório de controladores será onde você poderá criar uma classe para os resultados sugeridos. No .NET, uma função de sugestão é baseada no [método SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync). Para obter mais informações sobre o SDK do .NET, consulte [como usar o Azure pesquisa cognitiva de um aplicativo .net](search-howto-dotnet-sdk.md).

O `InitSearch` método cria um cliente de índice http autenticado para o serviço de pesquisa cognitiva do Azure. As propriedades na classe [sugiraoptions](/dotnet/api/azure.search.documents.suggestoptions) determinam quais campos são pesquisados e retornados nos resultados, o número de correspondências e se a correspondência difusa é usada. 

Para preenchimento automático, a correspondência difusa é limitada a uma distância de edição (um caractere omitido ou de local incorreto). Observe que a correspondência difusa em consultas de preenchimento automático pode, às vezes, produzir resultados inesperados dependendo do tamanho do índice e de como ele é fragmentado. Para obter mais informações, consulte [conceitos de particionamento e fragmentação](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

```csharp
public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    var options = new SuggestOptions()
    {
        UseFuzzyMatching = fuzzy,
        Size = 8,
    };

    if (highlights)
    {
        options.HighlightPreTag = "<b>";
        options.HighlightPostTag = "</b>";
    }

    // Only one suggester can be specified per index.
    // The suggester for the Hotels index enables autocomplete/suggestions on the HotelName field only.
    // During indexing, HotelNames are indexed in patterns that support autocomplete and suggested results.
    var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

    // Return the list of suggestions.
    return new JsonResult(suggestions);
}
```

A função SuggestAsync usa dois parâmetros que determinam se os realces de ocorrências são retornados ou se a correspondência difusa é usada, além da entrada do termo de pesquisa. Até oito correspondências podem ser incluídas nos resultados sugeridos. O método cria um [objeto sugiraoptions](/dotnet/api/azure.search.documents.suggestoptions), que é passado para a API de sugestão. O resultado, em seguida, é convertido em JSON para que ele possa ser exibido no cliente.

## <a name="autocomplete"></a>Preenchimento automático

Até agora, o código UX de pesquisa foi centralizado em sugestões. O próximo bloco de código mostra o preenchimento automático, usando a função de preenchimento automático da interface do usuário do XDSoft jQuery, passando uma solicitação para preenchimento automático de Pesquisa Cognitiva do Azure. Assim como nas sugestões, em um aplicativo C#, o código que dá suporte à interação do usuário vai em **index. cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.8 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Função de preenchimento automático

O preenchimento automático é baseado no [método AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync). Assim como acontece com as sugestões, esse bloco de código ficaria no arquivo **HomeController. cs** .

```csharp
public async Task<ActionResult> AutoCompleteAsync(string term)
{
    InitSearch();

    // Setup the autocomplete parameters.
    var ap = new AutocompleteOptions()
    {
        Mode = AutocompleteMode.OneTermWithContext,
        Size = 6
    };
    var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

    // Convert the autocompleteResult results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

    return new JsonResult(autocomplete);
}
```

A função Autocomplete usa a entrada do termo de pesquisa. O método cria um [objeto AutoCompleteParameters](/rest/api/searchservice/autocomplete). O resultado, em seguida, é convertido em JSON para que ele possa ser exibido no cliente.

## <a name="next-steps"></a>Próximas etapas

Siga estes links para obter instruções de ponta a ponta ou código que demonstram experiências de pesquisa conforme o tipo. O exemplo demonstra a implementação híbrida de sugestões e o preenchimento automático em conjunto.

+ [Adicionar pesquisa a um site (JavaScript)](tutorial-javascript-search-query-integration.md#azure-function-suggestions-from-the-catalog) usa um pacote de sugestões de código-fonte aberto para conclusão de termo parcial no aplicativo cliente.
+ [Tutorial: Crie seu primeiro aplicativo em C# (lição 3)](tutorial-csharp-type-ahead-and-suggestions.md) com o  [exemplo de código do c# associado: Azure-Search-dotnet-Samples/Create-First-app/3-Add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead) demonstre o suporte nativo para typeahead.