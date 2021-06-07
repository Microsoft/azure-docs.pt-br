---
title: Tutorial de C# sobre a ordenação de resultados
titleSuffix: Azure Cognitive Search
description: Este tutorial de C# demostra como ordenar os resultados da pesquisa. Ele se baseia em um projeto de hotéis anterior, ordenando por propriedade primária e secundária, bem como inclui um perfil de pontuação para adicionar critérios de aumento.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1f8100dd6340383eadec5d10b7f23db59ba0ebdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786378"
---
# <a name="tutorial-order-search-results-using-the-net-sdk"></a>Tutorial: Ordenar os resultados da pesquisa usando o SDK do .NET

Ao longo desta série de tutoriais, os resultados foram retornados e exibidos em uma [ordem padrão](index-add-scoring-profiles.md#what-is-default-scoring). Neste tutorial, você adicionará critérios de classificação primários e secundários. Como alternativa à ordenação baseada em valores numéricos, o último exemplo mostra como classificar os resultados com base em um perfil de pontuação personalizado. Também mostraremos em um pouco mais de detalhes a exibição de _tipos complexos_.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Ordenar os resultados conforme uma propriedade
> * Ordenar os resultados conforme diversas propriedades
> * Ordenar os resultados conforme um perfil de pontuação

## <a name="overview"></a>Visão geral

Este tutorial estende o projeto de rolagem infinita criado no tutorial [Adicionar paginação a resultados da pesquisa](tutorial-csharp-paging.md).

Uma versão concluída do código neste tutorial pode ser encontrada no seguinte projeto:

* [5-order-results (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/5-order-results)

## <a name="prerequisites"></a>Pré-requisitos

* Solução [2b-add-infinite-scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll). Esse projeto pode ser sua versão criada no tutorial anterior ou uma cópia do GitHub.

Este tutorial foi atualizado para usar o pacote [Azure.Search.Documents (versão 11)](https://www.nuget.org/packages/Azure.Search.Documents/). Para obter uma versão anterior do SDK do .NET, confira o [exemplo de código Microsoft.Azure.Search (versão 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="order-results-based-on-one-property"></a>Ordenar os resultados conforme uma propriedade

Ao ordenar os resultados conforme uma propriedade, digamos, a classificação do hotel, queremos não apenas que os resultados sejam ordenados, como também a confirmação de que a ordem está correta. Adicionar o campo de classificação aos resultados nos permite confirmar se os resultados estão classificados corretamente.

Neste exercício, também vamos adicionar um pouco mais à exibição de resultados: a tarifa de hospedagem mais econômica e a tarifa de hospedagem mais cara, para cada hotel.

Não é necessário modificar nenhum dos modelos para habilitar a ordenação. Somente a exibição e o controlador precisam ser atualizados. Comece abrindo o controlador da tela inicial.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Adicionar a propriedade OrderBy aos parâmetros de pesquisa

1. Em HomeController.cs, adicione a opção **OrderBy** e inclua a propriedade Rating, com uma ordem de classificação decrescente. No método **Index(SearchData model)** , adicione a seguinte linha aos parâmetros de pesquisa.

    ```cs
    options.OrderBy.Add("Rating desc");
    ```

    >[!Note]
    > A ordem padrão é crescente, embora você possa adicionar `asc` à propriedade para deixar isso claro. A ordem decrescente é especificada adicionando `desc`.

1. Agora execute o aplicativo e insira qualquer termo de pesquisa comum. Os resultados podem ou não estar na ordem correta, pois nem você, como desenvolvedor, nem o usuário tem uma maneira fácil de verificar os resultados!

1. Vamos deixar claro que os resultados estão ordenados por classificação. Primeiro, substitua as classes **box1** e **box2** no arquivo hotels.css pelas classes a seguir (essas classes são todas as novas de que precisamos para este tutorial).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    > [!Tip]
    > Os navegadores geralmente armazenam os arquivos css em cache e isso pode fazer um arquivo css antigo ser usado, ignorando suas edições. Uma boa maneira de contornar isso é adicionar uma cadeia de consulta com um parâmetro de versão ao link. Por exemplo: 
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    > Atualize o número de versão se acreditar que o navegador está usando um arquivo css antigo.

1. Adicione a propriedade **Rating** ao parâmetro **Select**, no método **Index(SearchData model)** para que os resultados incluam os seguintes três campos:

    ```cs
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Rating");
    ```

1. Abra a exibição (index.cshtml) e substitua o loop de renderização ( **&lt;!-- Show the hotel data. --&gt;** ) pelo código a seguir.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var ratingText = $"Rating: {result[i].Document.Rating}";

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. A classificação precisa estar disponível tanto na primeira página exibida quanto nas páginas subsequentes chamadas por meio de rolagem infinita. Para a segunda dessas duas situações, é necessário atualizar tanto a ação **Avançar** no controlador quanto a função **rolado** na exibição. Começando com o controlador, altere o método **Next** para o código a seguir. Esse código cria e comunica o texto de classificação.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
    
            string fullDescription = result.Document.Description;
    
            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Agora atualize a função **rolado** na exibição para mostrar o texto de classificação.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 3) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Agora, execute o aplicativo novamente. Pesquise qualquer termo comum, como "wifi", e verifique se os resultados são ordenados em ordem decrescente de classificação de hotel.

    ![Ordenação baseada em classificação](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Você observará que diversos hotéis têm uma classificação idêntica, portanto, a ordem em que aparecem na exibição é, novamente, a ordem em que os dados são encontrados, que é arbitrária.

    Antes de analisarmos a adição de um segundo nível de ordenação, vamos adicionar algum código para exibir o intervalo de tarifas de hospedagem. Estamos adicionando esse código para mostrar a extração de dados de um _tipo complexo_ e também para que possamos discutir a ordenação dos resultados conforme o preço (talvez primeiro o mais barato).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Adicionar o intervalo de tarifas de hospedagem à exibição

1. Adicione propriedades contendo as tarifas de hospedagem mais caras e mais baratas ao modelo Hotel.cs.

    ```cs
    // Room rate range
    public double cheapest { get; set; }
    public double expensive { get; set; }
    ```

1. Calcule as tarifas de hospedagem no final da ação **Index(SearchData model)** no controlador da tela inicial. Adicione os cálculos depois de armazenar os dados temporários.

    ```cs
    // Ensure TempData is stored for the next call.
    TempData["page"] = page;
    TempData["searchfor"] = model.searchText;

    // Calculate the room rate ranges.
    await foreach (var result in model.resultList.GetResultsAsync())
    {
        var cheapest = 0d;
        var expensive = 0d;

        foreach (var room in result.Document.Rooms)
        {
            var rate = room.BaseRate;
            if (rate < cheapest || cheapest == 0)
            {
                cheapest = (double)rate;
            }
            if (rate > expensive)
            {
                expensive = (double)rate;
            }
        }
        model.resultList.Results[n].Document.cheapest = cheapest;
        model.resultList.Results[n].Document.expensive = expensive;
    }
    ```

1. Adicione a propriedade **Quartos** ao parâmetro **Select** no método de ação **Index(SearchData model)** do controlador.

    ```cs
    options.Select.Add("Rooms");
    ```

1. Altere o loop de renderização na exibição para exibir o intervalo de tarifas para a primeira página dos resultados.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Altere o método **Next** no controlador da tela inicial para comunicar o intervalo de tarifas para páginas subsequentes de resultados.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";

            string fullDescription = result.Document.Description;

            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(rateText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Atualize a função **rolado** na exibição para processar o texto de tarifas de hospedagem.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 4) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Execute o aplicativo e verifique se os intervalos de tarifas de hospedagem são exibidos.

    ![Exibição de intervalos de tarifas de hospedagem](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

A propriedade **OrderBy** dos parâmetros de pesquisa não aceitará uma entrada como **Rooms.BaseRate** para fornecer a tarifa de hospedagem mais barata, mesmo que os quartos já tenham sido classificados por tarifa. Nesse caso, as salas não são classificadas em taxa. Para exibir hotéis no conjunto de dados de exemplo, ordenados por tarifa de hospedagem, você precisa ordenar os resultados em seu controlador da tela inicial e enviar esses resultados para a exibição na ordem desejada.

## <a name="order-results-based-on-multiple-values"></a>Ordenar os resultados conforme diversos valores

A pergunta agora é como diferenciar entre hotéis com a mesma classificação. Uma abordagem pode ser uma ordenação secundária baseada na última vez que o hotel foi reformado, de modo que os hotéis reformados mais recentemente aparecem em posições mais altas nos resultados.

1. Para adicionar um segundo nível de ordenação, adicione **LastRenovationDate** aos resultados da pesquisa e **OrderBy** no método **Index (SearchData model)** .

    ```cs
    options.Select.Add("LastRenovationDate");

    options.OrderBy.Add("LastRenovationDate desc");
    ```

    >[!Tip]
    > Qualquer número de propriedades pode ser inserido na lista **OrderBy**. Se os hotéis tiverem a mesma classificação e a mesma data de reforma, uma terceira propriedade poderá ser inserida para diferenciá-los.

1. Novamente, é preciso ver a data de reforma na exibição para ter certeza de que a ordenação está correta. Para um critério como reforma, provavelmente apenas o ano é suficiente. Altere o loop de renderização na exibição para o código a seguir.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var lastRenovatedText = $"Last renovated: { result[i].Document.LastRenovationDate.Value.Year}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Altere o método **Next** no controlador da tela inicial para encaminhar o componente de ano da data da última reforma.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var ratingText = $"Rating: {result.Document.Rating}";
                var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
                var lastRenovatedText = $"Last renovated: {result.Document.LastRenovationDate.Value.Year}";

                string fullDescription = result.Document.Description;

                // Add strings to the list.
                nextHotels.Add(result.Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(fullDescription);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Altere a função **rolado** na exibição para mostrar o texto de reforma.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 5) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Execute o aplicativo. Pesquise um termo comuns, como "piscina" ou "vista" e verifique se os hotéis com a mesma classificação agora são exibidos em ordem decrescente de data de reforma.

    ![Ordenação por data de reforma](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="order-results-based-on-a-scoring-profile"></a>Ordenar os resultados conforme um perfil de pontuação

Os exemplos apresentados no tutorial até agora mostram como ordenar por valores numéricos (classificação e data de reforma), fornecendo uma sequência de ordenação _exata_. No entanto, algumas pesquisas e alguns dados não se prestam a uma comparação tão fácil entre dois elementos de dados. Para consultas de pesquisa de texto completo, o Cognitive Search inclui o conceito de _classificação_. Os _perfis de pontuação_ podem ser especificados para influenciar o modo como os resultados são classificados, fornecendo comparações mais complexas e qualitativas.

Os [perfis de pontuação](index-add-scoring-profiles.md) são definidos no esquema de índice. Vários perfis de pontuação foram configurados nos dados de hotéis. Vamos examinar como um perfil de pontuação é definido, então tente escrever código para pesquisá-los.

### <a name="how-scoring-profiles-are-defined"></a>Como os perfis de pontuação são definidos

Os perfis de pontuação são definidos em um índice de pesquisa em tempo de design. O índice de hotéis somente leitura hospedado pela Microsoft tem três perfis de pontuação. Essa seção explora os perfis de pontuação e mostra como usá-los no código.

1. Abaixo está o perfil de pontuação padrão para o conjunto de dados de hotéis, usado quando você não especifica nenhum parâmetro **OrderBy** ou **ScoringProfile**. Esse perfil aumenta a _pontuação_ de um hotel se o texto de pesquisa está presente no nome, na descrição ou na lista de marcas (comodidades) do hotel. Observe como os pesos da pontuação favorecem determinados campos. Se o texto da pesquisa aparecer em outro campo não listado abaixo, ele terá um peso de 1. Obviamente, uma pontuação maior fará o resultado aparecer antes na exibição.

     ```cs
    {
        "name": "boostByField",
        "text": {
            "weights": {
                "Tags": 3,
                "HotelName": 2,
                "Description": 1.5,
                "Description_fr": 1.5,
            }
        }
    }
    ```

1. O perfil de pontuação alternativo a seguir aumentará significativamente a pontuação se um parâmetro fornecido incluir uma ou mais da lista de marcas (que estamos chamando de "comodidades"). O ponto principal desse perfil é que _deve_ ser fornecido um parâmetro contendo texto. Se o parâmetro estiver vazio ou não for fornecido, será gerado um erro.

    ```cs
    {
        "name":"boostAmenities",
        "functions":[
            {
            "fieldName":"Tags",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":null,
            "distance":null,
            "tag":{
                "tagsParameter":"amenities"
            },
            "type":"tag",
            "boost":5
            }
        ],
        "functionAggregation":0
    },
    ```

1. Nesse terceiro perfil, a classificação do hotel aumenta a pontuação de modo significativo. A data da última reforma também aumentará a pontuação, mas somente se os dados estiverem dentro de 730 dias (2 anos) da data atual.

    ```cs
    {
        "name":"renovatedAndHighlyRated",
        "functions":[
            {
            "fieldName":"Rating",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":{
                "boostingRangeStart":0,
                "boostingRangeEnd":5,
                "constantBoostBeyondRange":false
            },
            "distance":null,
            "tag":null,
            "type":"magnitude",
            "boost":20
            },
            {
            "fieldName":"LastRenovationDate",
            "freshness":{
                "boostingDuration":"P730D"
            },
            "interpolation":"quadratic",
            "magnitude":null,
            "distance":null,
            "tag":null,
            "type":"freshness",
            "boost":10
            }
        ],
        "functionAggregation":0
    }
    ```

    Agora, vamos ver se esses perfis funcionam como acreditamos que deveriam.

### <a name="add-code-to-the-view-to-compare-profiles"></a>Adicione código à exibição para comparar os perfis

1. Abra o arquivo index.cshtml e substitua a seção &lt;corpo&gt; pelo código a seguir.

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
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

1. Abra o arquivo SearchData.cs e substitua a classe **SearchData** pelo código a seguir.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

1. Abra o arquivo hotels.css e adicione as classes HTML a seguir.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Adicionar código ao controlador para especificar um perfil de pontuação

1. Abra o arquivo do controlador da tela inicial. Adicione a seguinte instrução **using** (para ajudar na criação de listas).

    ```cs
    using System.Linq;
    ```

1. Para este exemplo, precisamos que a chamada inicial para **Index** faça um pouco mais do que apenas retornar a exibição inicial. O método agora pesquisa até 20 comodidades para mostrar na exibição.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchOptions options = new SearchOptions();
            // Search for up to 20 amenities.
            options.Facets.Add("Tags,count:20");

            SearchResults<Hotel> searchResult = await _searchClient.SearchAsync<Hotel>("*", options);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

1. Precisamos de dois métodos privados para salvar as facetas em um armazenamento temporário e recuperá-las do armazenamento temporário e preencher um modelo.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

1. Precisamos definir os parâmetros **OrderBy** e **ScoringProfile** conforme necessário. Substitua o método **Index(SearchData model)** existente pelo seguinte.

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Recover the facet text, and the facet check box settings.
                RecoverFacets(model, true);

                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First search with text. 
                // Recover the facet text, but ignore the check box settings, and use the current model settings.
                RecoverFacets(model, false);

                // First call. Check for valid text input, and valid scoring profile.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                if (model.scoring == null)
                {
                    model.scoring = "Default";
                }
                page = 0;
            }

            // Setup the search parameters.
            var options = new SearchOptions
            {
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Size = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalCount = true,
            };
            // Select the data properties to be returned.
            options.Select.Add("HotelName");
            options.Select.Add("Description");
            options.Select.Add("Tags");
            options.Select.Add("Rooms");
            options.Select.Add("Rating");
            options.Select.Add("LastRenovationDate");

            List<string> parameters = new List<string>();
            // Set the ordering based on the user's radio button selection.
            switch (model.scoring)
            {
                case "RatingRenovation":
                    // Set the ordering/scoring parameters.
                    options.OrderBy.Add("Rating desc");
                    options.OrderBy.Add("LastRenovationDate desc");
                    break;

                case "boostAmenities":
                    {
                        options.ScoringProfile = model.scoring;

                        // Create a string list of amenities that have been clicked.
                        for (int a = 0; a < model.facetOn.Length; a++)
                        {
                            if (model.facetOn[a])
                            {
                                parameters.Add(model.facetText[a]);
                            }
                        }

                        if (parameters.Count > 0)
                        {
                            options.ScoringParameters.Add($"amenities-{ string.Join(',', parameters)}");
                        }
                        else
                        {
                            // No amenities selected, so set profile back to default.
                            options.ScoringProfile = "";
                        }
                    }
                    break;

                case "renovatedAndHighlyRated":
                    options.ScoringProfile = model.scoring;
                    break;

                default:
                    break;
            }

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options);

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
            TempData["scoring"] = model.scoring;
            SaveFacets(model, true);

            // Calculate the room rate ranges.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var cheapest = 0d;
                var expensive = 0d;

                foreach (var room in result.Document.Rooms)
                {
                    var rate = room.BaseRate;
                    if (rate < cheapest || cheapest == 0)
                    {
                        cheapest = (double)rate;
                    }
                    if (rate > expensive)
                    {
                        expensive = (double)rate;
                    }
                }

                result.Document.cheapest = cheapest;
                result.Document.expensive = expensive;
            }
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    Leia os comentários de cada uma das seleções de **opção**.

1. Não será preciso fazer alterações à ação **Avançar** se você tiver concluído o código adicional para a seção anterior sobre ordenação conforme diversas propriedades.

### <a name="run-and-test-the-app"></a>Executar e testar o aplicativo

1. Execute o aplicativo. Você deve ver um conjunto completo de comodidades na exibição.

1. Para ordenar, selecionar "Por classificação numérica" apresentará a ordenação numérica que você já implementou neste tutorial, usando a data de reforma para decidir entre hotéis de mesma classificação.

   ![Ordenação de "praia" conforme a classificação](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

1. Agora, tente o perfil "por comodidades". Faça várias seleções de comodidades e verifique quais hotéis com essas comodidades são promovidos para cima na lista de resultados.

   ![Ordenação de "praia" conforme o perfil](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

1. Experimente o perfil "Por data de reforma/classificação" para saber se você obtém o esperado. Somente hotéis reformados recentemente devem receber um aumento de _atualização_.

### <a name="resources"></a>Recursos

Para obter mais informações, confira [Adicionar perfis de pontuação a um índice da Pesquisa Cognitiva do Azure](./index-add-scoring-profiles.md) a seguir.

## <a name="takeaways"></a>Observações

Considere as principais conclusões deste projeto:

* Os usuários esperarão que os resultados da pesquisa estejam ordenados, com o mais relevante primeiro.
* Os dados precisam ser estruturados para que a ordenação seja fácil. Não foi possível classificar por "mais barato primeiro" facilmente, pois os dados não estão estruturados para permitir que a classificação seja feita sem código adicional.
* Pode haver muitos níveis de ordenação para diferenciar entre resultados que têm o mesmo valor em um nível mais alto de ordenação.
* É natural que alguns resultados sejam ordenados em ordem crescente (digamos, distância de um ponto) e outros, em ordem decrescente (digamos, classificação dos hóspedes).
* É possível definir perfis de pontuação para um conjunto de dados quando comparações numéricas não estiverem disponíveis ou não forem inteligentes suficiente. Pontuar cada resultado ajudará a ordenar e exibir os resultados de modo inteligente.

## <a name="next-steps"></a>Próximas etapas

Você concluiu esta série de tutoriais de C# e deve ter adquirido conhecimentos valiosos sobre as APIs da Pesquisa Cognitiva do Azure.

Para obter mais referências e tutoriais, navegue pelo [Microsoft Learn](/learn/browse/?products=azure) ou por outros tutoriais na [Documentação da Pesquisa Cognitiva do Azure](./index.yml).