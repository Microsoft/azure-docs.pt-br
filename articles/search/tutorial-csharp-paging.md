---
title: Tutorial de C# para paginação de resultados da pesquisa – Azure Search
description: Este tutorial é baseado no projeto "Criar seu primeiro aplicativo – Azure Search", com a escolha de dois tipos de paginação. O primeiro usa uma série de botões de número de página, bem como os botões para primeira, próxima, anterior e última página. O segundo sistema de paginação usa a rolagem infinita, acionada movendo uma barra de rolagem vertical até seu limite inferior.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: 7e6c433168b73c6b58d13d4698bed55d7c18ec58
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434620"
---
# <a name="c-tutorial-search-results-pagination---azure-search"></a>Tutorial do C#: Paginação de resultados da pesquisa – Azure Search

Saiba como implementar dois sistemas de paginação diferentes, o primeiro baseado nos números de página e o segundo na rolagem infinita. Os dois sistemas de paginação são amplamente utilizados e selecionar o sistema certo depende da experiência do usuário que você deseja ter com os resultados. Este tutorial insere os sistemas de paginação no projeto criado no [Tutorial do C#: Criar seu primeiro aplicativo – Azure Search](tutorial-csharp-create-first-app.md).

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Estender seu aplicativo com paginação numerada
> * Estender seu aplicativo com rolagem infinita

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

Ter o projeto [Tutorial do C#: Criar seu primeiro aplicativo – Azure Search](tutorial-csharp-create-first-app.md) em funcionamento. O projeto pode ser sua própria versão ou pode ser instalado do GitHub: [Criar primeiro aplicativo](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Estender seu aplicativo com paginação numerada

A paginação numerada é o sistema de paginação escolhido pelos principais mecanismos de pesquisa da Internet e pela maioria dos outros sites de pesquisa. Normalmente, a paginação inclui as opções "próximo" e "anterior" além de um intervalo de números de página. As opções de "última página" e "primeira página" também poderão estar disponíveis. Essas opções certamente oferecem ao usuário controle sobre a navegação pelos resultados baseado em página.

Adicionaremos um sistema que inclui as opções de primeira, anterior, próxima e última página, bem como os números de página, que não começam por 1, mas cercam a página em que o usuário está (portanto, se o usuário estiver, por exemplo, na página 10, talvez os números de página 8 9, 10, 11 e 12 serão exibidos).

O sistema será flexível o suficiente para permitir que o número de números de página visíveis seja definido em uma variável global.

O sistema tratará os botões de página à extrema esquerda e à extrema direita como especiais, o que significa que eles dispararão a alteração do intervalo de números de página exibidos. Por exemplo, se os números de página 8, 9, 10, 11 e 12 estiverem exibidos e o usuário clicar em 8, o intervalo de números de página exibido será alterado para 6, 7, 8, 9 e 10. E haverá uma mudança semelhante para a direita se ele selecionar 12.

### <a name="add-paging-fields-to-the-model"></a>Adicionar campos de paginação ao modelo

Abra a solução de página de pesquisa básica.

1. Abra o arquivo de modelo SearchData.cs.

2. Primeiro, adicione algumas variáveis globais. No MVC, variáveis globais são declaradas em sua própria classe estática. **ResultsPerPage** define o número de resultados por página. **MaxPageRange** determina o número de números de página visíveis no modo de exibição. **PageRangeDelta** determina quantas páginas à esquerda ou à direita o intervalo de páginas deverá ser deslocado quando o número de páginas à extrema esquerda ou à extrema direita for selecionado. Normalmente, este último número é cerca da metade de **MaxPageRange**. Adicione o código a seguir ao namespace.

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Se estiver executando este projeto em um dispositivo com uma tela menor, como um laptop, considere alterar **ResultsPerPage** para 2.

3. Adicione as propriedades de paginação à classe **SearchData**, digamos, após a propriedade **searchText**.

    ```cs
        // The current page being displayed.
        public int currentPage { get; set; }

        // The total number of pages of results.
        public int pageCount { get; set; }

        // The left-most page number to display.
        public int leftMostPage { get; set; }

        // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
        public int pageRange { get; set; }

        // Used when page numbers, or next or prev buttons, have been selected.
        public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Adicionar uma tabela de opções de paginação ao modo de exibição

1. Abra o arquivo index.cshtml e adicione o seguinte código logo antes da tag de fechamento &lt;/body&gt;. Este novo código apresenta uma tabela de opções de paginação: primeira, anterior, 1, 2, 3, 4, 5, próxima e última.

    ```cs
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
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

            <td>
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
                <td>
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

            <td>
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

            <td>
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
    ```

    Usamos uma tabela HTML para alinhar as coisas claramente. No entanto, toda a ação é proveniente das instruções @Html.ActionLink, cada uma chamando o controlador com um modelo **new** criado com entradas diferentes para a propriedade **paging** que adicionamos anteriormente.

    As opções de primeira e última página não enviam cadeias de caracteres como "first" e "last"; em vez disso, enviam os números de página corretos.

2. Adicione algumas classes de paginação à lista de estilos HTML no arquivo hotels.css. A classe **pageSelected** está lá para identificar a página que o usuário está exibindo (colocando o número em negrito) na lista de números de página.

    ```html
        .pageButton {
            border: none;
            color: darkblue;
            font-weight: normal;
            width: 50px;
        }

        .pageSelected {
            border: none;
            color: black;
            font-weight: bold;
            width: 50px;
        }

        .pageButtonDisabled {
            border: none;
            color: lightgray;
            font-weight: bold;
            width: 50px;
        }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Adicionar uma ação Page ao controlador

1. Abra o arquivo HomeController.cs e adicione a ação **Page**. Essa ação responde a qualquer uma das opções de página selecionadas.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

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

                // Recover the search text and search for the data for the new page.
                model.searchText = TempData["searchfor"].ToString();

                await RunQueryAsync(model, page, leftMostPage);

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
                TempData["page"] = (object)page;
                TempData["searchfor"] = model.searchText;
                TempData["leftMostPage"] = model.leftMostPage;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

    Agora, o método **RunQueryAsync** mostrará um erro de sintaxe devido ao terceiro parâmetro, a que chegaremos em breve.

    > [!Note]
    > As chamadas de **TempData** armazenam um valor (um **objeto**) no armazenamento temporário, embora esse armazenamento persista para _apenas_ uma chamada. Se armazenarmos algo nos dados temporários, o item ficará disponível para a próxima chamada para uma ação do controlador, mas certamente não estará mais disponível para a chamada seguinte! Devido a essa vida útil curta, armazenamos as propriedades de texto de pesquisa e de paginação no armazenamento temporário a cada chamada para **Page**.

2. A ação **Index(model)** precisa ser atualizada para armazenar as variáveis temporárias e para adicionar o parâmetro de página à extrema esquerda à chamada **RunQueryAsync**.

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
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. O método **RunQueryAsync** precisa ser atualizado significativamente. Podemos usar os campos **Skip**, **Top** e **IncludeTotalResultCount** da classe **SearchParameters** para solicitar apenas uma página de resultados, começando pela configuração **Skip**. Também precisamos calcular as variáveis de paginação para nossa exibição. Substitua todo o método pelo seguinte código.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
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
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

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

            return View("Index", model);
        }
    ```

4. Por fim, precisamos fazer uma pequena alteração na exibição. A variável **resultsList.Results.Count** agora conterá o número de resultados retornados em uma página (3 em nosso exemplo), não o número total. Como definimos **IncludeTotalResultCount** como true, a variável **resultsList.Count** agora contém o número total de resultados. Sendo assim, localize onde o número de resultados é exibido na exibição e altere-o para o código a seguir.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Há um impacto sobre o desempenho, embora normalmente não seja grande, ao definir **IncludeTotalResultCount** como true, pois esse total precisa ser calculado pelo Azure Search. Com conjuntos de dados complexos, há um aviso de que o valor retornado é uma _aproximação_. Para nossos dados de hotel, ele será preciso.

### <a name="compile-and-run-the-app"></a>Compilar e executar o aplicativo

Agora, selecione **Iniciar Sem Depurar** (ou pressione a tecla F5).

1. Pesquise algum texto que retornará muitos resultados (por exemplo, "wifi"). É possível paginar claramente pelos resultados?

    ![Paginação numerada pelos resultados de "piscina"](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Tente clicar nos números de página à extrema direita e, em seguida, à extrema esquerda. Os números de página se ajustam corretamente de forma a centralizar a página em que você está?

3. As opções "primeira" e "última" são úteis? Algumas pesquisas da Web populares usam essas opções, outras não.

4. Vá para a última página de resultados. A última página é a única que pode conter menos de **ResultsPerPage** resultados.

    ![Examinando a última página de "wifi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Digite "cidade" e clique em Pesquisar. Nenhuma opção de paginação será exibida se houver menos de uma página de resultados.

    ![Pesquisando por "cidade"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Agora, salve este projeto e vamos tentar uma alternativa a essa forma de paginação.

## <a name="extend-your-app-with-infinite-scrolling"></a>Estender seu aplicativo com rolagem infinita

A rolagem infinita é acionada quando um usuário rola uma barra de rolagem vertical até o último dos resultados que está sendo exibido. Nesse caso, é feita uma chamada para o servidor para a próxima página de resultados. Se não houver mais resultados, nada será retornado e a barra de rolagem vertical não será alterada. Se houver mais resultados, eles serão acrescentados à página atual e a barra de rolagem será alterada de modo a mostrar que há mais resultados disponíveis.

O aspecto importante aqui é que a página em exibição não é substituída, mas os novos resultados são acrescentados a ela. O usuário sempre pode rolar de volta para cima para ver os primeiros resultados da pesquisa.

Para implementar a rolagem infinita, vamos começar com o projeto antes que qualquer um dos elementos da rolagem número de página tivessem sido adicionados. Portanto, se necessário, faça outra cópia da página de pesquisa básica do GitHub: [Criar primeiro aplicativo](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Adicionar campos de paginação ao modelo

1. Primeiro, adicione uma propriedade **paging** à classe **SearchData** (no arquivo de modelo SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Essa variável é uma cadeia de caracteres que irá conter "next" caso a próxima página de resultados deva ser enviada ou será nula para a primeira página de uma pesquisa.

2. No mesmo arquivo e dentro do namespace, adicione uma classe de variável global com uma propriedade. No MVC, variáveis globais são declaradas em sua própria classe estática. **ResultsPerPage** define o número de resultados por página. 

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Adicionar uma barra de rolagem vertical à exibição

1. Localize a seção do arquivo index.cshtml que exibe os resultados (ela começa com **@if (Model != null)** ).

2. Substitua a seção pelo código a seguir. A nova seção **&lt;div&gt;** está próxima da área que deve ser rolável e adiciona um atributo **overflow-y** e uma chamada para uma função **onscroll** chamada "scrolled()", da seguinte forma.

    ```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Logo abaixo do loop, após a tag &lt;/div&gt;, adicione a função **scrolled**.

    ```javascript
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 2) {
                                div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                            }
                        });
                    }
                }
        </script>
    ```

    A instrução **if** no script acima testa se o usuário rolou até o final da barra de rolagem vertical. Se ele tiver rolado, uma chamada para o controlador **Home** será feita para uma ação chamada **Next**. O controlador não precisa de nenhuma outra informação, ele retornará a próxima página de dados. Em seguida, esses dados serão formatados usando estilos HTML idênticos ao da página original. Se nenhum resultado for retornado, nada será acrescentado e tudo permanecerá como está.

### <a name="handle-the-next-action"></a>Lidar com a ação Next

Apenas três ações precisam ser enviadas ao controlador: a primeira execução do aplicativo, que chama **Index()** , a primeira pesquisa do usuário, que chama **Index(model)** e as chamadas posteriores para obter mais resultados via **Next(model)** .

1. Abra o arquivo do controlador da tela inicial e exclua o método **RunQueryAsync** do tutorial original.

2. Substitua a ação **Index(model)** pelo seguinte código. Agora, ela trata do campo **paging** quando ele é nulo ou definido como "next" e lida com a chamada para o Azure Search.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First call. Check for valid text input.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    page = 0;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
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

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    De forma semelhante ao método paginação numerada, podemos usar as configurações de pesquisa **Skip** e **Top** para solicitar apenas os dados que precisamos que sejam retornados.

3. Adicione a ação **Next** ao controlador da tela inicial. Observe que ela retorna uma lista, com cada hotel adicionando dois elementos à lista: um nome e uma descrição de hotel. Esse formato é configurado de modo a corresponder ao uso que a função **scrolled** faz dos dados retornados na exibição.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Se estiver recebendo um erro de sintaxe em **List&lt;string&gt;** , adicione a seguinte diretiva **using** ao cabeçalho do arquivo do controlador.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Compilar e executar o projeto

Agora, selecione **Iniciar Sem Depurar** (ou pressione a tecla F5).

1. Insira um termo que fornecerá muitos resultados (por exemplo, "piscina") e, em seguida, teste a barra de rolagem vertical. Ela dispara uma nova página de resultados?

    ![Rolagem infinita nos resultados de "piscina"](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Para garantir que uma barra de rolagem apareça na primeira página, a primeira página de resultados deverá ultrapassar ligeiramente a altura da área em que eles estão sendo exibidos. Em nosso exemplo, **.box1** tem altura de 30 pixels, **.box2** tem altura de 100 pixels _e_ uma margem inferior de 24 pixels. Sendo assim, cada entrada usa 154 pixels. Três entradas ocuparão 154 x 3 = 462 pixels. Para garantir que uma barra de rolagem vertical apareça, deverá ser configurada uma altura menor do que 462 pixels para a área de exibição, até mesmo 461 funciona. Isso ocorre apenas na primeira página, depois disso, uma barra de rolagem certamente será exibida. A linha a ser atualizada é: **&lt;div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()"&gt;** .

2. Role para baixo até o final dos resultados. Observe que todas as informações agora estão na página com um modo de exibição. Você pode rolar de volta até a parte superior sem acionar chamadas do servidor.

Sistemas de rolagem infinita mais sofisticados podem usar a roda do mouse (ou outro mecanismo semelhante) para disparar o carregamento de uma nova página de resultados. Não detalharemos mais a rolagem infinita nestes tutoriais, mas ela tem seu charme pois evita cliques do mouse extra e talvez você queira investigar outras opções mais detalhadamente!

## <a name="takeaways"></a>Observações

Considere as principais conclusões deste projeto:

* A paginação numerada é boa para pesquisas em que a ordem dos resultados é arbitrária, ou seja, também pode haver algo do interesse de seus usuários nas páginas posteriores.
* A rolagem infinita é boa quando a ordem dos resultados é particularmente importante. Por exemplo, se os resultados estiverem ordenados com base na distância do centro de uma cidade de destino.
* A paginação numerada permite uma navegação melhor. Por exemplo, um usuário pode se lembrar de que um resultado interessante estava na página 6, enquanto essa referência fácil não existe na rolagem infinita.
* A rolagem infinita tem um apelo fácil pela possibilidade de rolar para cima e para baixo sem precisar clicar em números de página.
* Um recurso importante da rolagem infinita é que os resultados são acrescentados a uma página existente, sem substituir essa página, o que é eficiente.
* O armazenamento temporário persiste para apenas uma chamada e precisa ser redefinido para sobreviver a chamadas adicionais.


## <a name="next-steps"></a>Próximas etapas

A paginação é fundamental para pesquisas na Internet. Com a paginação bem detalhada, a etapa seguinte é melhorar ainda mais a experiência do usuário adicionando pesquisas de preenchimento automático.

> [!div class="nextstepaction"]
> [Tutorial do C#: Adicionar preenchimento automático e sugestões – Azure Search](tutorial-csharp-type-ahead-and-suggestions.md)
