---
title: Tutorial de C# sobre como usar facetas para auxiliar a navegação
titleSuffix: Azure Cognitive Search
description: Continue de "Paginação de resultados" para adicionar a faceted navigation. Saiba como as facetas podem ser usadas para restringir uma pesquisa com mais facilidade.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 44a05ed5b4386f2787a1c84dfeb61a5d539c7cb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789796"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>Tutorial: Adicionar faceted navigation usando o SDK do .NET

As facetas habilitam a navegação autodirigida fornecendo um conjunto de links para filtrar os resultados. Neste tutorial, uma estrutura de faceted navigation é colocada no lado esquerdo da página, com rótulos e texto clicável para cortar os resultados.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Definir as propriedades do modelo como _IsFacetable_
> * Adicionar navegação de faceta ao seu aplicativo

## <a name="overview"></a>Visão geral

As facetas são baseadas nos campos em seu índice de pesquisa. Uma solicitação de consulta que inclui facet=[string] fornece o campo pelo qual facetar. É comum incluir múltiplas facetas, como `&facet=category&facet=amenities`, com cada uma delas separada por um caractere “e” comercial (&). A implementação de uma estrutura de faceted navigation requer que você especifique as facetas e os filtros. O filtro é usado em um evento de clique para restringir os resultados. Por exemplo, clicar em "orçamento" filtra os resultados com base nesses critérios.

Este tutorial estende o projeto de paginação criado no tutorial [Adicionar paginação aos resultados da pesquisa](tutorial-csharp-paging.md).

Uma versão concluída do código neste tutorial pode ser encontrada no seguinte projeto:

* [4-add-facet-navigation (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/4-add-facet-navigation)

## <a name="prerequisites"></a>Pré-requisitos

* Solução [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging). Esse projeto pode ser sua versão criada no tutorial anterior ou uma cópia do GitHub.

Este tutorial foi atualizado para usar o pacote [Azure.Search.Documents (versão 11)](https://www.nuget.org/packages/Azure.Search.Documents/). Para obter uma versão anterior do SDK do .NET, confira o [exemplo de código Microsoft.Azure.Search (versão 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="set-model-properties-as-isfacetable"></a>Definir as propriedades do modelo como IsFacetable

Para uma propriedade de modelo a ser localizada em uma pesquisa de faceta, ela deve ser marcada com **IsFacetable**.

1. Examine a classe **Hotel**. **Categoria** e **Marcas**, por exemplo, são marcados como **IsFacetable**, mas **HotelName** e **Description** não são. 

    ```cs
    public partial class Hotel
    {
        [SimpleField(IsFilterable = true, IsKey = true)]
        public string HotelId { get; set; }

        [SearchableField(IsSortable = true)]
        public string HotelName { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
        public string Description { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
        [JsonPropertyName("Description_fr")]
        public string DescriptionFr { get; set; }

        [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public string Category { get; set; }

        [SearchableField(IsFilterable = true, IsFacetable = true)]
        public string[] Tags { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public bool? ParkingIncluded { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true)]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }
    }
    ```

1. Não vamos alterar nenhuma marca como parte deste tutorial, assim, feche o arquivo hotel.cs inalterado.

    > [!Note]
    > Uma pesquisa de faceta gerará um erro se um campo solicitado na pesquisa não estiver marcado apropriadamente.

## <a name="add-facet-navigation-to-your-app"></a>Adicionar navegação de faceta ao seu aplicativo

Para este exemplo, vamos permitir que o usuário selecione uma categoria de hotel ou uma comodidade nas listas de links mostradas à esquerda dos resultados. O usuário começa inserindo um texto de pesquisa e, depois, restringe progressivamente os resultados da pesquisa selecionando uma categoria ou comodidade.

É função do controlador passar as listas de facetas para a exibição. A fim de manter as seleções do usuário conforme a pesquisa avança, usamos o armazenamento temporário como o mecanismo para preservar os dados.

![Como usar navegação de faceta para restringir uma pesquisa de "piscina"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Adicionar cadeias de caracteres de filtro ao modelo SearchData

1. Abra o arquivo SearchData.cs e adicione propriedades de cadeia de caracteres à classe **SearchData** para conter as cadeias de caracteres de filtro de faceta.

    ```cs
    public string categoryFilter { get; set; }
    public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Adicione o método de ação Facet

O controlador da página inicial precisa de uma nova ação, **Facet**, e atualizações às suas ações **Index** e **Page** existentes, bem como ao método **RunQueryAsync**.

<!-- 1. Open the home controller file, and add the **using** statement, to enable the **List&lt;string&gt;** construct.

    ```cs
    using System.Collections.Generic; 
    ```-->

1. Substitua o método de ação **Index(SearchData model)** .

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            // Ensure the search string is valid.
            if (model.searchText == null)
            {
                model.searchText = "";
            }

            // Make the search call for the first page.
            await RunQueryAsync(model, 0, 0, "", "").ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View(model);
    }
    ```

1. Substitua o método de ação **PageAsync(SearchData model)** .

    ```cs
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

            // Calculate the page that should be displayed.
            switch (model.paging)
            {
                case "prev":
                    page = (int)TempData["page"] - 1;
                    break;

                case "next":
                    page = (int)TempData["page"] + 1;
                    break;

                default:
                    page = int.Parse(model.paging);
                    break;
            }

            // Recover the leftMostPage.
            int leftMostPage = (int)TempData["leftMostPage"];

            // Recover the filters.
            string catFilter = TempData["categoryFilter"].ToString();
            string ameFilter = TempData["amenityFilter"].ToString();

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Search for the new page.
            await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

1. Adicione um método de ação **FacetAsync(SearchData model)** , a ser ativado quando o usuário clica em um link de faceta. O modelo conterá um filtro de pesquisa de categoria ou de comodidade. Adicione-o após a ação **PageAsync**.

    ```cs
    public async Task<ActionResult> FacetAsync(SearchData model)
    {
        try
        {
            // Filters set by the model override those stored in temporary data.
            string catFilter;
            string ameFilter;
            if (model.categoryFilter != null)
            {
                catFilter = model.categoryFilter;
            } else
            {
                catFilter = TempData["categoryFilter"].ToString();
            }

            if (model.amenityFilter != null)
            {
                ameFilter = model.amenityFilter;
            } else
            {
                ameFilter = TempData["amenityFilter"].ToString();
            }

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Initiate a new search.
            await RunQueryAsync(model, 0, 0, catFilter, ameFilter).ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }

        return View("Index", model);
    }
    ```

### <a name="set-up-the-search-filter"></a>Configurar o filtro de pesquisa

Quando um usuário seleciona uma determinada faceta, por exemplo, clica na categoria **Resort e Spa**, apenas hotéis especificados como estando nessa categoria devem ser retornados nos resultados. Para restringir uma pesquisa dessa forma, é preciso configurar um _filtro_.

1. Substitua o método **RunQueryAsync** pelo seguinte código. Basicamente, ele usa uma cadeia de caracteres de filtro de categoria e uma cadeia de caracteres de filtro de comodidade e define o parâmetro **Filter** de **SearchOptions**.

    ```cs
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
    {
        InitSearch();

        string facetFilter = "";

        if (catFilter.Length > 0 && ameFilter.Length > 0)
        {
            // Both facets apply.
            facetFilter = $"{catFilter} and {ameFilter}"; 
        } else
        {
            // One, or zero, facets apply.
            facetFilter = $"{catFilter}{ameFilter}";
        }

        var options = new SearchOptions
        {
            Filter = facetFilter,

            SearchMode = SearchMode.All,

            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true,
        };

        // Return information on the text, and number, of facets in the data.
        options.Facets.Add("Category,count:20");
        options.Facets.Add("Tags,count:20");

        // Enter Hotel property names into this list, so only these values will be returned.
        options.Select.Add("HotelName");
        options.Select.Add("Description");
        options.Select.Add("Category");
        options.Select.Add("Tags");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);

        // This variable communicates the total number of pages to the view.
        model.pageCount = ((int)model.resultList.TotalCount + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

        // This variable communicates the page number being displayed to the view.
        model.currentPage = page;

        // Calculate the range of page numbers to display.
        if (page == 0)
        {
            leftMostPage = 0;
        }
        else if (page <= leftMostPage)
        {
            // Trigger a switch to a lower page range.
            leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
        }
        else if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
        {
            // Trigger a switch to a higher page range.
            leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
        }
        model.leftMostPage = leftMostPage;

        // Calculate the number of page numbers to display.
        model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

        // Ensure Temp data is stored for the next call.
        TempData["page"] = page;
        TempData["leftMostPage"] = model.leftMostPage;
        TempData["searchfor"] = model.searchText;
        TempData["categoryFilter"] = catFilter;
        TempData["amenityFilter"] = ameFilter;

        // Return the new view.
        return View("Index", model);
    }
    ```

    Observe que as propriedades **Categoria** e **Marcas** são adicionadas à lista de itens **Selecionar** a serem retornados. Essa adição não é um requisito para a navegação de faceta funcionar, mas usamos essas informações para verificar se os filtros estão funcionando corretamente.

### <a name="add-lists-of-facet-links-to-the-view"></a>Adicionar listas de links de faceta à exibição

A exibição exigirá algumas alterações significativas. 

1. Comece abrindo o arquivo hotels.css (na pasta wwwroot/css) e, em seguida, adicione as seguintes classes.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

1. Para a exibição, organizamos a saída em uma tabela para alinhar claramente as listas de facetas à esquerda e os resultados à direita. Abra o arquivo index.cshtml. Substitua todo o conteúdo das marcas &lt;body&gt; HTML pelo código a seguir.

    ```html
    <body>
        @using (Html.BeginForm("Index", "Home", FormMethod.Post))
        {
            <table>
                <tr>
                    <td></td>
                    <td>
                        <h1 class="sampleTitle">
                            <img src="~/images/azure-logo.png" width="80" />
                            Hotels Search - Facet Navigation
                        </h1>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td>
                        <!-- Display the search text box, with the search icon to the right of it.-->
                        <div class="searchBoxForm">
                            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                        </div>
                    </td>
                </tr>

                <tr>
                    <td valign="top">
                        <div id="facetplace" class="facetchecks">

                            @if (Model != null && Model.resultList != null)
                            {
                                List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                                if (categories.Count > 0)
                                {
                                    <h5 class="facetheader">Category:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < categories.Count; c++)
                                        {
                                            var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                            </li>
                                        }
                                    </ul>
                                }

                                List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                                if (tags.Count > 0)
                                {
                                    <h5 class="facetheader">Amenities:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < tags.Count; c++)
                                        {
                                            var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                            </li>
                                        }
                                    </ul>
                                }
                            }
                        </div>
                    </td>
                    <td valign="top">
                        <div id="resultsplace">
                            @if (Model != null && Model.resultList != null)
                            {
                                // Show the result count.
                                <p class="sampleText">
                                    @Model.resultList.TotalCount Results
                                </p>

                                var results = Model.resultList.GetResults().ToList();

                                @for (var i = 0; i < results.Count; i++)
                                {
                                    string amenities = string.Join(", ", results[i].Document.Tags);

                                    string fullDescription = results[i].Document.Description;
                                    fullDescription += $"\nCategory: {results[i].Document.Category}";
                                    fullDescription += $"\nAmenities: {amenities}";


                                    // Display the hotel name and description.
                                    @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                                    @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                                }
                            }
                        </div>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td valign="top">
                        @if (Model != null && Model.pageCount > 1)
                        {
                            // If there is more than one page of results, show the paging buttons.
                            <table>
                                <tr>
                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("|<", "PageAsync", "Home", new { paging = "0" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">|&lt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("<", "PageAsync", "Home", new { paging = "prev" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&lt;</p>
                                        }
                                    </td>

                                    @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                    {
                                        <td class="tdPage">
                                            @if (Model.currentPage == pn)
                                            {
                                                // Convert displayed page numbers to 1-based and not 0-based.
                                                <p class="pageSelected">@(pn + 1)</p>
                                            }
                                            else
                                            {
                                                <p class="pageButton">
                                                    @Html.ActionLink((pn + 1).ToString(), "PageAsync", "Home", new { paging = @pn }, null)
                                                </p>
                                            }
                                        </td>
                                    }

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">", "PageAsync", "Home", new { paging = "next" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">|", "PageAsync", "Home", new { paging = Model.pageCount - 1 }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;|</p>
                                        }
                                    </td>
                                </tr>
                            </table>
                        }
                    </td>
                </tr>
            </table>
        }
    </body>
    ```

    Observe o uso da chamada **ActionLink**. Essa chamada comunica as cadeias de caracteres de filtro válidas para o controlador quando o usuário clica em um link de faceta. 

### <a name="run-and-test-the-app"></a>Executar e testar o aplicativo

A vantagem da navegação de faceta para o usuário é que ele pode restringir a pesquisa com um único clique, o que podemos mostrar na sequência a seguir.

1. Execute o aplicativo e digite "aeroporto" como o texto de pesquisa. Verifique se a lista de facetas aparece de modo organizado à esquerda. Essas facetas são tudo o que se aplica a hotéis que têm "aeroporto" em seus dados de texto, com uma contagem da frequência com que ocorrem.

    ![Como usar navegação de faceta para restringir uma pesquisa de "aeroporto"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

1. Clique na categoria **Resort e Spa**. Verifique se todos os resultados estão nesta categoria.

    ![Limitar a pesquisa a "Resort e Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

1. Clique na comodidade **café da manhã continental**. Verifique se todos os resultados ainda estão na categoria "Resort e Spa" com a comodidade selecionada.

    ![Limitar a pesquisa a "café da manhã continental"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

1. Tente selecionar qualquer outra categoria e então uma comodidade e veja os resultados da restrição. Em seguida, tente o oposto, uma comodidade e, em seguida, uma categoria. Envie uma pesquisa vazia para redefinir a página.

    >[!Note]
    > Quando é feita uma seleção em uma lista de facetas (como categoria), ela substituirá qualquer seleção anterior na lista de categorias.

## <a name="takeaways"></a>Observações

Considere as principais conclusões deste projeto:

* É fundamental marcar cada campo com faceta com a propriedade **IsFacetable** para inclusão na navegação da faceta.
* As facetas são combinadas com filtros para reduzir os resultados.
* As facetas são cumulativas, com cada seleção se baseando na anterior para obter resultados mais restritos.

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, vamos examinar os resultados da ordenação. Até este ponto, os resultados são ordenados simplesmente na ordem em que estão localizados no banco de dados.

> [!div class="nextstepaction"]
> [Tutorial do C#: Ordenar os resultados – Pesquisa Cognitiva do Azure](tutorial-csharp-orders.md)
