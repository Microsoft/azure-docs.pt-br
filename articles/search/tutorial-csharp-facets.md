---
title: Tutorial do C# sobre como usar facetas para auxiliar a navegação – Azure Search
description: Este tutorial baseia-se no projeto "Paginação dos resultados da pesquisa – Azure Search" para adicionar navegação de faceta. Saiba que as facetas podem ser usadas para restringir uma pesquisa com mais facilidade.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/20/2019
ms.openlocfilehash: 62326ad3bc5f2d740ce744819df559bce8658eb7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443797"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-search"></a>Tutorial do C#: Usar facetas para auxiliar a navegação – Azure Search

As facetas são usadas para auxiliar a navegação, fornecendo ao usuário um conjunto de links a serem usados para focar a pesquisa. As facetas são atributos de dados (por exemplo, categoria, ou um recurso específico, de um hotel em nossos dados de exemplo).

Este tutorial baseia-se no projeto paginação criado no [Tutorial do C#: Paginação dos resultados da pesquisa – Azure Search](tutorial-csharp-paging.md).

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Definir as propriedades do modelo como _IsFacetable_
> * Adicionar navegação de faceta ao seu aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

Ter o projeto [Tutorial do C#: Paginação dos resultados da pesquisa – Azure Search](tutorial-csharp-paging.md) em funcionamento. O projeto pode ser sua própria versão ou pode ser instalado do GitHub: [Criar primeiro aplicativo](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Definir as propriedades do modelo como IsFacetable

Para uma propriedade de modelo a ser localizada em uma pesquisa de faceta, ela deve ser marcada com **IsFacetable**.

1. Examine a classe **Hotel**. **Categoria** e **Marcas**, por exemplo, são marcados como **IsFacetable**, mas **HotelName** e **Description** não são. 

    ```cs
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

2. Não vamos alterar nenhuma marca como parte deste tutorial, assim, feche o arquivo hotel.cs inalterado.

    > [!Note]
    > Uma pesquisa de faceta gerará um erro se um campo solicitado na pesquisa não estiver marcado apropriadamente.


## <a name="add-facet-navigation-to-your-app"></a>Adicionar navegação de faceta ao seu aplicativo

Para este exemplo, vamos permitir que o usuário selecione uma categoria de hotel ou uma comodidade nas listas de links mostradas à esquerda dos resultados. O usuário começa inserindo algum texto de pesquisa, depois, pode restringir os resultados da pesquisa selecionando uma categoria e restringi-los ainda mais selecionando uma comodidade ou pode selecionar a comodidade primeiro (a ordem não é importante).

Precisamos que o controlador passe as listas de facetas para a exibição. Precisamos manter as seleções do usuário conforme a pesquisa avança e, novamente, podemos usar o armazenamento temporário como o mecanismo para preservar os dados.

![Como usar navegação de faceta para restringir uma pesquisa de "piscina"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Adicionar cadeias de caracteres de filtro ao modelo SearchData

1. Abra o arquivo SearchData.cs e adicione propriedades de cadeia de caracteres à classe **SearchData** para conter as cadeias de caracteres de filtro de faceta.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Adicione o método de ação Facet

O controlador da tela inicial precisa de uma nova ação, **Facet**, e atualizações às suas ações **Index** e **Page** existentes, bem como atualizações ao método **RunQueryAsync**.

1. Abra o arquivo do controlador da tela inicial e adicione a instrução **using** para habilitar o constructo **List&lt;string&gt;** .

    ```cs
    using System.Collections.Generic;
    ```

2. Substitua o método de ação **Index(SearchData model)** .

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
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. Substitua o método de ação **Page(SearchData model)** .

    ```cs
        public async Task<ActionResult> Page(SearchData model)
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

4. Adicione um método de ação **Facet(SearchData model)** , a ser ativado quando o usuário clica em um link de faceta. O modelo conterá um filtro de pesquisa de categoria ou um filtro de pesquisa de comodidade. Talvez adicione-o após a ação **Page**.

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
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
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
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

1. Substitua o método **RunQueryAsync** pelo seguinte código. Basicamente, usa uma cadeia de caracteres de filtro de categoria e uma cadeia de caracteres de filtro de comodidade e define o parâmetro **Filter** de **SearchParameters**.

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

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
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

    Adicionamos a as propriedades **Categoria** e **Marcas** à lista de itens **Selecionar** a serem retornados. Essa adição não é um requisito para a navegação de faceta funcionar, mas usamos essas informações para verificar que estamos filtrando corretamente.

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

2. Para a exibição, organizamos a saída em uma tabela para alinhar claramente as listas de facetas à esquerda e os resultados à direita. Abra o arquivo index.cshtml. Substitua todo o conteúdo das marcas &lt;body&gt; HTML pelo código a seguir.

    ```cs
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
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
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
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
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
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
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
                                            @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
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
                                            @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
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
                                                @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                                            </p>
                                        }
                                    </td>
                                }

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
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
                                            @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
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

2. Clique na categoria **Resort e Spa**. Verifique se todos os resultados estão nesta categoria.

    ![Limitar a pesquisa a "Resort e Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Clique na comodidade **café da manhã continental**. Verifique se todos os resultados ainda estão na categoria "Resort e Spa" com a comodidade selecionada.

    ![Limitar a pesquisa a "café da manhã continental"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Tente selecionar qualquer outra categoria e então uma comodidade e veja os resultados da restrição. Em seguida, tente o oposto, uma comodidade e, em seguida, uma categoria.

    >[!Note]
    > Quando é feita uma seleção em uma lista de facetas (como categoria), ela substituirá qualquer seleção anterior na lista de categorias.

## <a name="takeaways"></a>Observações

Considere as principais conclusões deste projeto:

* É fundamental marcar cada propriedade como **IsFacetable** caso ela vá ser incluída na navegação de faceta.
* A navegação de faceta fornece ao usuário uma maneira fácil e intuitiva de restringir uma pesquisa.
* A navegação de faceta é dividida melhor em seções (categorias de hotel, comodidades de um hotel, faixas de preços, faixas de classificação etc.), cada seção com um título apropriado.

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, vamos examinar os resultados da ordenação. Até este ponto, os resultados são ordenados simplesmente na ordem em que estão localizados no banco de dados.

> [!div class="nextstepaction"]
> [Tutorial do C#: Ordenar os resultados – Azure Search](tutorial-csharp-orders.md)
