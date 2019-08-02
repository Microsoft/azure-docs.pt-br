---
title: Tutorial do C# sobre a ordenação de resultados – Azure Search
description: Este tutorial baseia-se no projeto "Paginação dos resultados da pesquisa – Azure Search" para adicionar a ordenação dos resultados da pesquisa. Saiba como ordenar os resultados em uma propriedade primária e, para que os resultados tenham a mesma propriedade primária, como ordenar os resultados em uma propriedade secundária. Por fim, aprenda como ordenar os resultados conforme um perfil de pontuação.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/21/2019
ms.openlocfilehash: 32e253b4e131d753ab6937d0aa2a49bda471e091
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466529"
---
# <a name="c-tutorial-order-the-results---azure-search"></a>Tutorial do C#: Ordenar os resultados – Azure Search

Até este ponto da série de tutoriais, os resultados são retornados e exibidos em uma ordem padrão. Essa pode ser a ordem na qual os dados estão localizados ou, possivelmente, foi definido um _perfil de pontuação_ padrão que será usado quando nenhum parâmetro de ordenação for especificado. Neste tutorial, mostraremos como ordenar os resultados conforme uma propriedade primária e, em seguida, para os resultados que têm a mesma propriedade primária, como ordenar essa seleção em uma propriedade secundária. Como alternativa à ordenação conforme valores numéricos, o último exemplo mostra como ordenar conforme um perfil de pontuação personalizado. Também mostraremos em um pouco mais de detalhes a exibição de _tipos complexos_.

Para comparar facilmente os resultados retornados, este projeto baseia-se no projeto de rolagem infinita criado no [Tutorial do C#: Paginação dos resultados da pesquisa – Azure Search](tutorial-csharp-paging.md).

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Ordenar os resultados conforme uma propriedade
> * Ordenar os resultados conforme diversas propriedades
> * Filtrar os resultados conforme uma distância de um ponto geográfico
> * Ordenar os resultados conforme um perfil de pontuação

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

Ter a versão de rolagem infinita do projeto [Tutorial do C#: Paginação dos resultados da pesquisa – Azure Search](tutorial-csharp-paging.md) em funcionamento. O projeto pode ser sua própria versão ou pode ser instalado do GitHub: [Criar primeiro aplicativo](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Ordenar os resultados conforme uma propriedade

Ao ordenarmos os resultados conforme uma propriedade, digamos, a classificação do hotel, queremos não apenas que os resultados sejam ordenados, como também a confirmação de que a ordem está correta. Em outras palavras, se ordenarmos por classificação, deveremos mostrar a classificação na exibição.

Neste tutorial, também vamos adicionar um pouco mais à exibição de resultados, a tarifa de hospedagem mais econômica e a tarifa de hospedagem mais cara, para cada hotel. Conforme nos aprofundamos na ordenação, também podemos adicionar valores para garantir que o que estamos ordenando também seja mostrado na exibição.

Não é necessário modificar nenhum dos modelos para habilitar a ordenação. A exibição e o controlador precisam ser atualizados. Comece abrindo o controlador da tela inicial.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Adicionar a propriedade OrderBy aos parâmetros de pesquisa

1. Para ordenar os resultados conforme uma única propriedade numérica, basta definir o parâmetro **OrderBy** como o nome da propriedade. No método **Index(SearchData model)** , adicione a seguinte linha aos parâmetros de pesquisa.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > A ordem padrão é crescente, embora você possa adicionar **asc** à propriedade para deixar isso claro. A ordem decrescente é especificada adicionando **desc**.

2. Agora execute o aplicativo e insira qualquer termo de pesquisa comum. Os resultados podem ou não estar na ordem correta, pois nem você, como desenvolvedor, nem o usuário tem uma maneira fácil de verificar os resultados!

3. Vamos deixar claro que os resultados estão ordenados por classificação. Primeiro, substitua as classes **box1** e **box2** no arquivo hotels.css pelas classes a seguir (essas classes são todas as novas de que precisamos para este tutorial).

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

    >[!Tip]
    >Os navegadores geralmente armazenam os arquivos css em cache e isso pode fazer um arquivo css antigo ser usado, ignorando suas edições. Uma boa maneira de contornar isso é adicionar uma cadeia de consulta com um parâmetro de versão ao link. Por exemplo:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Atualize o número de versão se acreditar que o navegador está usando um arquivo css antigo.

4. Adicionar a propriedade **Classificação** ao parâmetro **Selecionar**, no método **Index(SearchData model)** .

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Abra a exibição (index.cshtml) e substitua o loop de renderização ( **&lt;!-- Show the hotel data. --&gt;** ) pelo código a seguir.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. A classificação precisa estar disponível tanto na primeira página exibida quanto nas páginas subsequentes chamadas por meio de rolagem infinita. Para a segunda dessas duas situações, é necessário atualizar tanto a ação **Avançar** no controlador quanto a função **rolado** na exibição. Começando com o controlador, altere o método **Next** para o código a seguir. Esse código cria e comunica o texto de classificação.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. Agora atualize a função **rolado** na exibição para mostrar o texto de classificação.

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

8. Agora, execute o aplicativo novamente. Pesquise qualquer termo comum, como "wifi", e verifique se os resultados são ordenados em ordem decrescente de classificação de hotel.

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

2. Calcule as tarifas de hospedagem no final da ação **Index(SearchData model)** no controlador da tela inicial. Adicione os cálculos depois de armazenar os dados temporários.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
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

3. Adicione a propriedade **Quartos** ao parâmetro **Select** no método de ação **Index(SearchData model)** do controlador.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Altere o loop de renderização na exibição para exibir o intervalo de tarifas para a primeira página dos resultados.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. Altere o método **Next** no controlador da tela inicial para comunicar o intervalo de tarifas para páginas subsequentes de resultados.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. Atualize a função **rolado** na exibição para processar o texto de tarifas de hospedagem.

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

7. Execute o aplicativo e verifique se os intervalos de tarifas de hospedagem são exibidos.

    ![Exibição de intervalos de tarifas de hospedagem](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

A propriedade **OrderBy** dos parâmetros de pesquisa não aceitará uma entrada como **Rooms.BaseRate** para fornecer a tarifa de hospedagem mais barata, mesmo que os quartos já tenham sido classificados por tarifa (o que não é o caso). Para exibir hotéis no conjunto de dados de exemplo, ordenados por tarifa de hospedagem, você precisa ordenar os resultados em seu controlador da tela inicial e enviar esses resultados para a exibição na ordem desejada.

## <a name="order-results-based-on-multiple-values"></a>Ordenar os resultados conforme diversos valores

A pergunta agora é como diferenciar entre hotéis com a mesma classificação. Uma boa maneira seria ordenar conforme a última vez em que o hotel foi reformado. Em outras palavras, quanto mais recente for a reforma do hotel, mais alto o hotel aparecerá na lista de resultados.

1. Para adicionar um segundo nível de ordenação, altere as propriedades **OrderBy** e **Select** no método **Index(SearchData model)** para incluir a propriedade **LastRenovationDate**.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Qualquer número de propriedades pode ser inserido na lista **OrderBy**. Se os hotéis tiverem a mesma classificação e a mesma data de reforma, uma terceira propriedade poderá ser inserida para diferenciá-los.

2. Novamente, é preciso ver a data de reforma na exibição para ter certeza de que a ordenação está correta. Para um critério como reforma, provavelmente apenas o ano é necessário. Altere o loop de renderização na exibição para o código a seguir.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. Altere o método **Next** no controlador da tela inicial para encaminhar o componente de ano da data da última reforma.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Altere a função **rolado** na exibição para mostrar o texto de reforma.

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

5. Execute o aplicativo. Pesquise um termo comuns, como "piscina" ou "vista" e verifique se os hotéis com a mesma classificação agora são exibidos em ordem decrescente de data de reforma.

    ![Ordenação por data de reforma](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtrar os resultados conforme uma distância de um ponto geográfico

Classificação e data de reforma são exemplos de propriedades cuja exibição é melhor em ordem decrescente. Uma listagem alfabética seria um exemplo de um bom uso de ordem crescente (por exemplo, se houvesse apenas uma propriedade **OrderBy** e ela estivesse definida como **HotelName**, uma ordem alfabética seria exibida ). No entanto, para nossos dados de exemplo, a distância de um ponto geográfico seria mais apropriada.

Para exibir os resultados conforme a distância geográfica, são necessárias várias etapas.

1. Filtre para remover todos os hotéis que estão fora de um raio especificado de determinado ponto inserindo um filtro com parâmetros de longitude, latitude e raio. Longitude é fornecida primeiro para a função POINT. O raio é apresentado em quilômetros.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. O filtro acima _não_ ordena os resultados conforme a distância, apenas remove as exceções. Para ordenar os resultados, insira uma configuração de **OrderBy** que especifique o método geoDistance.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Embora os resultados tenham sido retornados pelo Azure Search usando um filtro de distância, a distância calculada entre os dados e o ponto especificado _não_ é retornada. Recalcule esse valor na exibição (ou no controlador) se desejar exibi-lo nos resultados.

    O código a seguir calculará a distância entre dois pontos de latitude/longitude.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. Agora você precisa unir esses conceitos. No entanto, nosso tutorial vai até esses snippets de código, criar um aplicativo baseado em mapa fica como um exercício para o leitor. Para aprofundar este exemplo, considere inserir um nome de cidade com um raio ou localizar um ponto em um mapa e selecionar um raio. Para investigar melhor essas opções, confira os seguintes recursos:

* [Documentação dos Mapas do Azure](https://docs.microsoft.com/azure/azure-maps/)
* [Localizar um endereço usando o serviço de pesquisa do Azure Mapas](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Ordenar os resultados conforme um perfil de pontuação

Os exemplos apresentados no tutorial até agora mostram como ordenar por valores numéricos (classificação, data de reforma, distância geográfica), fornecendo um processo de ordenação _exato_. No entanto, algumas pesquisas e alguns dados não se prestam a uma comparação tão fácil entre dois elementos de dados. O Azure Search inclui o conceito de _pontuação_. É possível especificar _perfis de pontuação_ para um conjunto de dados para permitir comparações mais complexas e qualitativas, que devem ter mais valor ao, digamos, comparar dados baseados em texto para decidir qual deve ser exibido primeiro.

Normalmente são os administradores do conjunto de dados, e não os usuários, que definem os perfis de pontuação. Vários perfis de pontuação foram configurados nos dados de hotéis. Vamos examinar como um perfil de pontuação é definido, então tente escrever código para pesquisá-los.

### <a name="how-scoring-profiles-are-defined"></a>Como os perfis de pontuação são definidos

Vejamos três exemplos de perfis de pontuação e considere como cada um _deve_ afetar a ordem dos resultados. Como desenvolvedor de aplicativos, você não escreve esses perfis, eles são escritos pelo administrador de dados. No entanto, é útil examinar a sintaxe.

1. Este é o perfil de pontuação padrão para o conjunto de dados de hotéis, usado quando você não especifica nenhum parâmetro **OrderBy** ou **ScoringProfile**. Esse perfil aumenta a _pontuação_ de um hotel se o texto de pesquisa está presente no nome, na descrição ou na lista de marcas (comodidades) do hotel. Observe como os pesos da pontuação favorecem determinados campos. Se o texto da pesquisa aparecer em outro campo não listado abaixo, ele terá um peso de 1. Obviamente, uma pontuação maior fará o resultado aparecer antes na exibição.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. O seguinte perfil de pontuação aumentará significativamente a pontuação se um parâmetro fornecido incluir uma ou mais da lista de marcas (que estamos chamando de "comodidades"). O ponto principal desse perfil é que _deve_ ser fornecido um parâmetro contendo texto. Se o parâmetro estiver vazio ou não for fornecido, será gerado um erro.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. Neste terceiro exemplo, a classificação aumenta a pontuação de modo significativo. A data da última reforma também aumentará a pontuação, mas somente se os dados estiverem dentro de 730 dias (2 anos) da data atual.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    Agora, vamos ver se esses perfis funcionam como acreditamos que deveriam!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Adicione código à exibição para comparar os perfis

1. Abra o arquivo index.cshtml e substitua a seção &lt;corpo&gt; pelo código a seguir.

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

2. Abra o arquivo SearchData.cs e substitua a classe **SearchData** pelo código a seguir.

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

3. Abra o arquivo hotels.css e adicione as classes HTML a seguir.

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

2.  Para este exemplo, precisamos que a chamada inicial para **Index** faça um pouco mais do que apenas retornar a exibição inicial. O método agora pesquisa até 20 comodidades para mostrar na exibição.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

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

3. Precisamos de dois métodos privados para salvar as facetas em um armazenamento temporário e recuperá-las do armazenamento temporário e preencher um modelo.

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

4. Precisamos definir os parâmetros **OrderBy** e **ScoringProfile** conforme necessário. Substitua o método **Index(SearchData model)** existente pelo seguinte.

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
                    RecoverFacets(model,false);

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

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
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
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
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
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Leia os comentários de cada uma das seleções de **opção**.

5. Não será preciso fazer alterações à ação **Avançar** se você tiver concluído o código adicional para a seção anterior sobre ordenação conforme diversas propriedades.

### <a name="run-and-test-the-app"></a>Executar e testar o aplicativo

1. Execute o aplicativo. Você deve ver um conjunto completo de comodidades na exibição.

2. Para ordenar, selecionar "Por classificação numérica" apresentará a ordenação numérica que você já implementou neste tutorial, usando a data de reforma para decidir entre hotéis de mesma classificação.

![Ordenação de "praia" conforme a classificação](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Agora, tente o perfil "por comodidades". Faça várias seleções de comodidades e verifique quais hotéis com essas comodidades são promovidos para cima na lista de resultados.

![Ordenação de "praia" conforme o perfil](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Experimente o perfil "Por data de reforma/classificação" para saber se você obtém o esperado. Somente hotéis reformados recentemente devem receber um aumento de _atualização_.

### <a name="resources"></a>Recursos

Para obter mais informações, confira [Adicionar perfis de pontuação a um índice do Azure Search](https://docs.microsoft.com/azure/search/index-add-scoring-profiles) a seguir.

## <a name="takeaways"></a>Observações

Considere as principais conclusões deste projeto:

* Os usuários esperarão que os resultados da pesquisa estejam ordenados, com o mais relevante primeiro.
* Os dados precisam ser estruturados para que a ordenação seja fácil. Não foi possível classificar por "mais barato primeiro" facilmente, pois os dados não estão estruturados para permitir que a classificação seja feita sem código adicional.
* Pode haver muitos níveis de ordenação para diferenciar entre resultados que têm o mesmo valor em um nível mais alto de ordenação.
* É natural que alguns resultados sejam ordenados em ordem crescente (digamos, distância de um ponto) e outros, em ordem decrescente (digamos, classificação dos hóspedes).
* É possível definir perfis de pontuação para um conjunto de dados quando comparações numéricas não estiverem disponíveis ou não forem inteligentes suficiente. Pontuar cada resultado ajudará a ordenar e exibir os resultados de modo inteligente.

## <a name="next-steps"></a>Próximas etapas

Você concluiu esta série de tutoriais do C#, e deve ter obtido um conhecimento valioso das APIs do Azure Search.

Para mais referência e tutoriais, navegue no [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure) ou em outros tutoriais na [Documentação do Azure Search](https://docs.microsoft.com/azure/search/).
