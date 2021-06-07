---
title: Tutorial de C# sobre preenchimento automático e sugestões
titleSuffix: Azure Cognitive Search
description: Adicione preenchimento automático e sugestões para coletar entradas de termos de pesquisa dos usuários que usam a lista suspensa. Este tutorial se baseia em um projeto de hotéis existente.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/22/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 06c0b25bcf64cfce01b4144550ef69da8c96ee0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785847"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>Tutorial: Adicionar preenchimento automático e sugestões usando o SDK do .NET

Saiba como implementar o preenchimento automático (consultas de digitação antecipada e resultados sugeridos) quando um usuário começa a digitar em uma caixa de pesquisa. Neste tutorial, mostraremos as consultas com preenchimento automático e os resultados sugeridos separadamente e juntos. Talvez o usuário só precise digitar dois ou três caracteres para localizar todos os resultados disponíveis.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Adicionar sugestões
> * Adicionar realce às sugestões
> * Adicionar o preenchimento automático
> * Combinar o preenchimento automático e sugestões

## <a name="overview"></a>Visão geral

Este tutorial adiciona resultados com preenchimento automático e sugeridos ao tutorial [Adicionar paginação aos resultados da pesquisa](tutorial-csharp-paging.md) anterior.

Uma versão concluída do código neste tutorial pode ser encontrada no seguinte projeto:

* [3-add-typeahead (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/3-add-typeahead)

## <a name="prerequisites"></a>Pré-requisitos

* Solução [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging). Esse projeto pode ser sua versão criada no tutorial anterior ou uma cópia do GitHub.

Este tutorial foi atualizado para usar o pacote [Azure.Search.Documents (versão 11)](https://www.nuget.org/packages/Azure.Search.Documents/). Para obter uma versão anterior do SDK do .NET, confira o [exemplo de código Microsoft.Azure.Search (versão 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="add-suggestions"></a>Adicionar sugestões

Vamos começar com o caso mais simples de oferta de alternativas para o usuário: uma lista suspensa de resultados sugeridos.

1. No arquivo index.cshtml, altere `@id` da instrução **TextBoxFor** para **azureautosuggest**.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Depois dessa instrução, após o **&lt;/div&gt;** de fechamento, insira esse script. Esse script utiliza o [widget AutoComplete](https://api.jqueryui.com/autocomplete/) da biblioteca de interface do usuário do jQuery de software livre para apresentar a lista suspensa de resultados sugeridos.

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/SuggestAsync?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    A ID `"azureautosuggest"` conecta o script acima à caixa de pesquisa. A opção de origem do widget é definida como um método sugerido que chama a API de sugestão com dois parâmetros de consulta: **highlights** e **fuzzy**, ambos definidos como false nessa instância. Além disso, são necessários no mínimo dois caracteres para disparar a pesquisa.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Adicionar referências aos scripts jQuery à exibição

1. Para acessar a biblioteca do jQuery, altere a seção &lt;head&gt; do arquivo de exibição para o seguinte código:

    ```html
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Como estamos introduzindo uma nova referência do jQuery, também precisamos remover ou comentar a referência do jQuery padrão no arquivo _Layout.cshtml (na pasta **Views/Shared**). Localize as linhas a seguir e exclua por meio de comente a primeira linha do script, conforme mostrado. Essa alteração evita conflitos entre as referências ao jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Agora podemos usar as funções de preenchimento automático predefinidas do jQuery.

### <a name="add-the-suggest-action-to-the-controller"></a>Adicionar a ação de Sugerir ao controlador

1. No controlador da página inicial, adicione a ação **SuggestAsync** (após a ação **PageAsync**).

    ```cs
    public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
    {
        InitSearch();

        // Setup the suggest parameters.
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

        // Only one suggester can be specified per index. It is defined in the index schema.
        // The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg", and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

        // Convert the suggested query results to a list that can be displayed in the client.
        List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

        // Return the list of suggestions.
        return new JsonResult(suggestions);
    }
    ```

    O parâmetro **Size** especifica quantos resultados retornar (se não for especificado, o padrão será cinco). Um _sugestor_ é especificado no índice de pesquisa quando o índice é criado. No índice de hotéis de exemplo hospedado pela Microsoft, o nome do sugestor é "sg", e ele pesquisa por correspondências sugeridas exclusivamente no campo **HotelName**.

    A correspondência difusa permite incluir "quase acertos" na saída, para até uma distância de edição. Se o parâmetro **highlight** estiver definido como true, marcas HTML de negrito serão adicionadas à saída. Definiremos ambos os parâmetros como true na próxima seção.

2. Talvez você receba alguns erros de sintaxe. Se receber, adicione as duas instruções **using** a seguir na parte superior do arquivo.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Execute o aplicativo. Você recebe uma variedade de opções quando digita "po", por exemplo? Agora, tente "pa".

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-po.png" alt-text="Digitar *po* revela duas sugestões" border="false":::

    Observe que as letras que você insere _devem_ iniciar uma palavra, e não simplesmente fazer parte da palavra.

4. No script de exibição, defina **&fuzzy** como true e execute o aplicativo novamente. Agora, insira "po". Observe que a pesquisa pressupõe que você errou uma letra.
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png" alt-text="Digitando *pa* com a pesquisa difusa definida como true" border="false":::

    Se você estiver interessado, [Sintaxe de consulta Lucene na Pesquisa Cognitiva do Azure](./query-lucene-syntax.md) descreve em detalhes a lógica usada em pesquisas difusas.

## <a name="add-highlighting-to-the-suggestions"></a>Adicionar realce às sugestões

Podemos melhorar a aparência das sugestões ao usuário definindo o parâmetro **highlights** como true. No entanto, primeiro precisamos adicionar algum código à exibição para exibir o texto em negrito.

1. Na exibição (index.cshtml), adicione o script a seguir após o script `"azureautosuggest"` descrito anteriormente.

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

1. Agora, altere a ID da caixa de texto para que indique o seguinte.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Execute o aplicativo novamente e você deverá ver o texto inserido em negrito nas sugestões. Tente digitar "pa".
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png" alt-text="Digitando *pa* com realce" border="false":::

   A lógica usada no script de realce acima não é infalível. Se você inserir um termo que aparece duas vezes no mesmo nome, os resultados em negrito não serão exatamente o que você gostaria. Tente digitar "mo".

   Uma das perguntas que um desenvolvedor precisa responder é: quando um script está funcionando "bem o bastante" e quando suas peculiaridades devem ser tratadas? Não analisaremos os realces em mais detalhes neste tutorial, mas encontrar um algoritmo preciso é algo a ser considerado se os realces não são eficazes para os seus dados. Para obter mais informações, confira [Realce de ocorrência](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Adicionar o preenchimento automático

Outra variação, levemente diferente das sugestões, é o preenchimento automático (às vezes chamado de "digitação antecipada") que completa um termo de consulta. Novamente, vamos começar com a implementação mais simples antes de passarmos para o aprimoramento da experiência do usuário.

1. Insira o seguinte script na exibição após seus scripts anteriores.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

1. Agora, altere a ID da caixa de texto para que indique o seguinte.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. No controlador da página inicial, insira a ação **AutocompleteAsync** após a ação **SuggestAsync**.

    ```cs
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

    Observe que estamos usando a mesma função *sugestor*, chamada "sg", na pesquisa de preenchimento automático, como fizemos para sugestões (portanto, estamos apenas tentando Preencher automaticamente os nomes de hotéis).

    Há uma variedade de configurações de **AutocompleteMode**, e estamos usando **OneTermWithContext**. Confira [API de preenchimento automático](/rest/api/searchservice/autocomplete) para obter uma descrição das opções adicionais.

1. Execute o aplicativo. Observe como a gama de opções exibidas na lista suspensa consiste em palavras individuais. Tente digitar palavras que começam com "re". Observe como o número de opções diminui conforme mais letras são digitadas.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png" alt-text="Como digitar com preenchimento automático básico" border="false":::

    Como está, o script de sugestões que você executou anteriormente é provavelmente mais útil do que este script de preenchimento automático. Para tornar o preenchimento automático mais amigável, considere usá-lo com resultados sugeridos.

## <a name="combine-autocompletion-and-suggestions"></a>Combinar o preenchimento automático e sugestões

Combinar preenchimento automático e sugestões é a opção mais complexa e provavelmente proporciona a melhor experiência do usuário. Nós queremos exibir, embutida no texto que está sendo digitado, a primeira opção do Azure Cognitive Search para realizar o preenchimento automático do texto. Além disso, queremos uma gama de sugestões como uma lista suspensa.

Há bibliotecas que oferecem essa funcionalidade, frequentemente chamada de "preenchimento automático embutido" ou um nome semelhante. No entanto, vamos implementar esse recurso nativamente para que você possa explorar as APIs. Vamos começar a trabalhar no controlador primeiro neste exemplo.

1. Adicione ao controlador uma ação que retorne apenas um resultado de preenchimento automático, juntamente com um número especificado de sugestões. Chamaremos essa ação de **AutoCompleteAndSuggestAsync**. No controlador da tela inicial, adicione a seguinte ação, seguindo suas outras novas ações.

    ```cs
    public async Task<ActionResult> AutoCompleteAndSuggestAsync(string term)
    {
        InitSearch();

        // Setup the type-ahead search parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 1,
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap);

        // Setup the suggest search parameters.
        var sp = new SuggestOptions()
        {
            Size = 8,
        };

        // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg" and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", sp).ConfigureAwait(false);

        // Create an empty list.
        var results = new List<string>();

        if (autocompleteResult.Value.Results.Count > 0)
        {
            // Add the top result for type-ahead.
            results.Add(autocompleteResult.Value.Results[0].Text);
        }
        else
        {
            // There were no type-ahead suggestions, so add an empty string.
            results.Add("");
        }

        for (int n = 0; n < suggestResult.Value.Results.Count; n++)
        {
            // Now add the suggestions.
            results.Add(suggestResult.Value.Results[n].Text);
        }

        // Return the list.
        return new JsonResult(results);
    }
    ```

    Uma opção de preenchimento automático é retornada na parte superior da lista de **resultados**, seguida por todas as sugestões.

1. Na exibição, vamos primeiro implementar um truque para que uma palavra de preenchimento automático cinza-clara seja renderizada diretamente sob o texto em negrito que o usuário está inserindo. HTML inclui o posicionamento relativo para esse fim. Altere a instrução **TextBoxFor** (e suas instruções &lt;div&gt; ao redor) para o seguinte, observando que uma segunda caixa de pesquisa identificada como **abaixo** está imediatamente sob nossa caixa de pesquisa normal, deslocando essa caixa de pesquisa 39 pixels de sua localização padrão!

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Observe que estamos alterando a ID novamente, para **azureautocomplete**, neste caso.

1. Ainda na exibição, insira o script a seguir após todos os scripts que você já inseriu até o momento. O script é longo e complexo devido à variedade de comportamentos de entrada que ele manipula.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggestAsync?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    Observe como a função **interval** é usada tanto para limpar o texto subjacente quando ele não corresponde mais ao que o usuário está digitando quanto para definir a mesma caixa (maiúscula ou minúscula) que o usuário está digitando (uma vez que "pa" corresponde a "PA", "pA", "Pa" ao pesquisar), para que o texto sobreposto fique organizado.

    Leia os comentários no script para obter uma compreensão mais completa.

1. Por fim, precisamos fazer um pequeno ajuste a duas classes HTML para torná-las transparente. Adicione a seguinte linha às classes **searchBoxForm** e **searchBox** no arquivo hotels.css.

    ```html
    background: rgba(0,0,0,0);
    ```

1. Agora, execute o aplicativo. Insira "pa" na caixa de pesquisa. Você obtém "palace" como a sugestão de preenchimento automático, junto com dois hotéis que contêm "pa"?

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png" alt-text="Como digitar com sugestões e preenchimento automático embutido" border="false":::

1. Tente usar a tecla Tab para aceitar a sugestão de preenchimento automático e tente selecionar sugestões usando as teclas de direção e a tecla Tab, então tente novamente usando o mouse e um único clique. Verifique se o script trata de todas essas situações de modo adequado.

    Você pode decidir que é mais simples carregar em uma biblioteca que oferece esse recurso para você, mas agora você conhece pelo menos uma forma de fazer o preenchimento automático embutido funcionar.

## <a name="takeaways"></a>Observações

Considere as principais conclusões deste projeto:

* O preenchimento automático (também conhecido como "digitação antecipada") e as sugestões podem permitir que o usuário digite apenas algumas teclas para localizar exatamente o que deseja.
* O preenchimento automático e as sugestões funcionando juntos podem proporcionar uma experiência de usuário avançada.
* Sempre teste as funções de preenchimento automático com todas as formas de entrada.
* Pode ser útil usar a função **setInterval** para verificar e corrigir os elementos da interface do usuário.

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, vamos analisar outra maneira de melhorar a experiência do usuário, usando facetas para restringir a pesquisa com um único clique.

> [!div class="nextstepaction"]
> [Tutorial do C#: Usar facetas para auxiliar a navegação – Pesquisa Cognitiva do Azure](tutorial-csharp-facets.md)
