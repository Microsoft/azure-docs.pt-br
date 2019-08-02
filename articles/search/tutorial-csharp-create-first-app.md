---
title: Tutorial do C# para criar seu primeiro aplicativo – Azure Search
description: Este tutorial fornece um guia passo a passo para criar seu primeiro aplicativo do Azure Search. O tutorial fornece tanto um link para um aplicativo em funcionamento no GitHub quanto o processo completo para criar o aplicativo desde o princípio. Saiba mais sobre os componentes essenciais do Azure Search.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: d569437a3e6f6f05ddb9c6fa85f62c77ac51f72b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443810"
---
# <a name="c-tutorial-create-your-first-app---azure-search"></a>Tutorial do C#: Criar seu primeiro aplicativo – Azure Search

Saiba como criar uma interface Web para consultar e apresentar os resultados da pesquisa de um índice usando o Azure Search. Este tutorial começa com um índice hospedado existente para que você possa se concentrar na criação de uma página de pesquisa. O índice contém dados fictícios de hotel. Quando você tiver uma página básica, poderá melhorá-la nas lições subsequentes para incluir paginação, facetas e uma experiência de preenchimento automático.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Configurar um ambiente de desenvolvimento
> * Modelar estruturas de dados
> * Criar uma página da Web
> * Definir métodos
> * Testar o aplicativo

Você também aprenderá como uma chamada de pesquisa é simples. As principais instruções no código que você desenvolverá serão encapsuladas nas poucas linhas a seguir.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Essa chamada inicia uma pesquisa de dados do Azure e retorna os resultados.

![Pesquisando por "piscina"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

[Instalar o Visual Studio](https://visualstudio.microsoft.com/) para usar como o IDE.

### <a name="install-and-run-the-project-from-github"></a>Instalar e executar o projeto usando o GitHub

1. Localize o exemplo no GitHub: [Criar primeiro aplicativo](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Selecione **Clonar ou baixar** e faça sua cópia local particular do projeto.
1. Usando o Visual Studio, navegue até a solução e abra-a na página de pesquisa básica e selecione **Iniciar sem depuração** (ou pressione F5).
1. Digite algumas palavras (por exemplo, "wifi", "vista", "bar", "estacionamento") e examine os resultados!

    ![Pesquisando por "wifi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Esperamos que este projeto transcorra sem problemas e você tenha o aplicativo do Azure em execução. Muitos dos componentes essenciais para pesquisas mais sofisticadas estão incluídos neste aplicativo, portanto, é uma boa ideia repassá-lo e recriá-lo passo a passo.

Para criar este projeto desde o princípio e, portanto, ajuda a reforçar os componentes do Azure Search em sua mente, repasse as etapas a seguir.

## <a name="set-up-a-development-environment"></a>Configurar um ambiente de desenvolvimento

1. No Visual Studio 2017 ou posterior, selecione **Novo/Projeto** e, em seguida, **Aplicativo Web ASP.NET Core**. Dê ao projeto um nome como "FirstAzureSearchApp".

    ![Como criar um projeto de nuvem](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Depois de clicar em **OK** para esse tipo de projeto, você receberá um segundo conjunto de opções que se aplicam a ele. Selecione **Aplicativo Web (Model-View-Controller)** .

    ![Como criar um projeto MVC](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Em seguida, no menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet** e, em seguida, **Gerenciar Pacotes do NuGet para a Solução…** . Há um pacote que precisamos instalar. Selecione a guia **Procurar** e digite "Azure Search" na caixa de pesquisa. Instale **Microsoft.Azure.Search** quando ele for exibido na lista (versão 9.0.1 ou posterior). Você precisará clicar em algumas caixas de diálogo adicionais para concluir a instalação.

    ![Como usar o NuGet para adicionar bibliotecas do Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-search"></a>Inicializar o Azure Search

Para este exemplo, estamos usando dados de hotel disponíveis ao público. Esses dados são uma coleção arbitrária de 50 nomes e descrições de hotel fictícios criados exclusivamente para fornecer dados de demonstração. Para acessar esses dados, especifique um nome e uma chave para eles.

1. Abra o arquivo appsettings.json no novo projeto e substitua as linhas padrão pelo seguinte nome e chave. A chave de API mostrada aqui não é um exemplo de uma chave, ela é _exatamente_ a chave de que você precisa para acessar os dados de hotel. Agora, seu arquivo appsettings.json deve estar assim.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Ainda não terminamos de trabalhar neste arquivo. Selecione as propriedades para ele altere a configuração **Copiar para o Diretório de Saída** para **Copiar se mais recente**.

    ![Como copiar as configurações do aplicativo para a saída](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Modelar estruturas de dados

Os modelos (classes C#) são usados para comunicar dados entre o cliente (a exibição), o servidor (o controlador) e também a nuvem do Azure usando a arquitetura MVC (modelo, exibição, controlador). Normalmente, esses modelos refletirão a estrutura dos dados que estão sendo acessados. Além disso, precisamos de um modelo para processar as comunicações de exibição/controlador.

1. Abra a pasta **Modelos** do seu projeto usando o Gerenciador de Soluções e você verá um modelo padrão lá: **ErrorViewModel.cs**.

2. Clique com o botão direito do mouse na pasta **Modelos** e selecione **Adicionar** e, em seguida, **Novo Item**. Então, na caixa de diálogo que aparece, selecione **ASP.NET Core** e a primeira opção, **Classe**. Altere o nome do arquivo .cs para Hotel.cs e clique em **Adicionar**. Substitua todo o conteúdos de Hotel.cs pelo código a seguir. Observe os membros **Endereço** e **Quarto** da classe; esses campos são classes em si, assim, precisaremos de modelos para eles também.

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
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
    }
    ```

3. Siga o mesmo processo de criação de um modelo para a classe de **Endereço**, mas dê ao arquivo o nome de Address.cs. Substitua o conteúdo pelo seguinte.

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. Novamente, siga o mesmo processo para criar a classe **Quarto**, dando ao arquivo o nome de Room.cs. Mais uma vez, substitua o conteúdo pelo seguinte.

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. O conjunto de classes de **Hotel**, **Endereço** e **Quarto** é conhecido no Azure como [_tipos complexos_](search-howto-complex-data-types.md), um recurso importante do Azure Search. Tipos complexos podem ter muitos níveis de profundidade de classes e subclasses e possibilitam a representação de estruturas de dados muito mais complexas do que usando _tipos simples_ (uma classe contendo somente membros primitivos). Precisamos de mais um modelo, então repasse o processo de criação de uma nova classe de modelo outra vez, mas agora chame a classe SearchData.cs e substitua o código padrão pelo seguinte.

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    Essa classe contém a entrada do usuário (**searchText**) e a saída da pesquisa (**resultList**). O tipo de saída é crítico, **DocumentSearchResult&lt;Hotel&gt;** , pois ele corresponde exatamente aos resultados da pesquisa e precisamos passar essa referência para a exibição.



## <a name="create-a-web-page"></a>Criar uma página da Web

O projeto criado criará por padrão diversas exibições do cliente. As exibições exatas dependem da versão do .NET Core que você está usando (neste exemplo, usamos a 2.1). Todas elas estão na pasta **Exibições** do projeto. Você precisará modificar apenas o arquivo Index.cshtml (na pasta **Exibições/Página Inicial**).

Exclua o conteúdo de Index.cshtml em sua totalidade e recompile o arquivo nas etapas a seguir.

1. Usamos duas imagens pequenas na exibição. Você pode usar a sua própria ou copiar as imagens do projeto do GitHub: azure-logo.png e search.png. Essas duas imagens devem ser colocadas na pasta **wwwroot/images**.

2. A primeira linha de Index.cshtml deve fazer referência ao modelo que usaremos para comunicar dados entre o cliente (a exibição) e o servidor (o controlador), que é o modelo **SearchData** que criamos. Adicione essa linha ao arquivo Index.cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. É uma prática padrão inserir um título para a exibição, portanto, as próximas linhas devem ser:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Após o título, insira uma referência a uma folha de estilos HTML, que será criada em breve.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Agora, vamos para o cerne da exibição. É importante lembrar que a exibição precisa lidar com duas situações. Primeiro, precisa lidar com a exibição quando o aplicativo é iniciado pela primeira vez e o usuário ainda não inseriu nenhum texto de pesquisa. Segundo, precisa lidar com a exibição dos resultados, além da caixa de texto de pesquisa, para uso repetido pelo usuário. Para lidar com essas duas situações, precisamos verificar se o modelo fornecido para a exibição é nulo ou não. Um modelo nulo indica que estamos na primeira das duas situações (a execução inicial do aplicativo). Adicione o seguinte ao arquivo Index. cshtml e leia os comentários.

    ```cs
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Por fim, adicionamos a folha de estilos. No Visual Studio, no menu **Arquivo**, selecione **Novo/Arquivo** e, em seguida, **Folha de Estilos** (com a opção **Geral** realçada). Substitua o código padrão pelo seguinte. Não vamos entrar em mais detalhes sobre esse arquivo, os estilos são HTML padrão.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

7. Salve o arquivo de folha de estilos como hotels.css na pasta wwwroot/css juntamente com o arquivo site.css padrão.

Isso conclui nossa exibição. Estamos fazendo um bom progresso. Os modelos e as exibições estão concluídos, falta apenas o controlador para unir tudo.

## <a name="define-methods"></a>Definir métodos

Precisamos modificar o conteúdo de um controlador (**Controlador da Tela Inicial**), que é criado por padrão.

1. Abra o arquivo HomeController.cs e substitua as instruções **using** pelo seguinte.

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>Adicionar métodos Index

Precisamos de dois métodos **Index**, um sem parâmetros (para quando o aplicativo é aberto pela primeira vez) e outro usando um modelo como parâmetro (para quando o usuário inseriu o texto de pesquisa). O primeiro desses métodos é criado por padrão. 

1. Adicione o seguinte método após o método **Index()** padrão.

    ```cs
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Observe a declaração **async** do método e a chamada **await** para **RunQueryAsync**. Essas palavras-chave tornam nossas chamadas assíncronas e portanto, evitam bloquear threads no servidor.

    O bloco **catch** usa o modelo de erro criado por padrão.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Observe o tratamento de erro e outras exibições e métodos padrão

Dependendo de qual versão do .NET Core você está usando, um conjunto ligeiramente diferente de exibições padrão é criado por padrão. Para o .NET Core 2.1, as exibições padrão são Índice, Sobre, Contato, Privacidade e Erro. Para o .NET Core 2.2, por exemplo, as exibições padrão são Índice, Privacidade e Erro. Em qualquer caso, você pode exibir essas páginas padrão ao executar o aplicativo e examinar como elas são processadas no controlador.

Testaremos a exibição de Erro mais adiante neste tutorial.

No exemplo do GitHub, excluímos as exibições não utilizadas e suas ações associadas.

### <a name="add-the-runqueryasync-method"></a>Adicionar o método RunQueryAsync

A chamada do Azure Search é encapsulada em nosso método **RunQueryAsync**.

1. Primeiro, adicione algumas variáveis estáticas para configurar o serviço do Azure e uma chamada para iniciá-las.

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. Agora, adicione o próprio método **RunQueryAsync**.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    Nesse método, primeiro vamos garantir que a configuração do Azure tenha sido iniciada, então definiremos alguns parâmetros de pesquisa. Os nomes dos campos no parâmetro **Select** correspondem exatamente aos nomes de propriedade na classe **hotel**. É possível deixar o parâmetro **Select** de fora, caso em que todas as propriedades serão retornadas. Entretanto, não configurar nenhum parâmetro **Select** será ineficaz se estivermos interessados apenas em um subconjunto dos dados. Ao especificar as propriedades que estamos interessados, somente tais propriedades são retornadas.

    A chamada assíncrona para a pesquisa (**model.resultList = await _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, parameters);** ) é o foco deste tutorial e deste aplicativo. A classe **DocumentSearchResult** é interessante e é uma boa ideia (quando o aplicativo está em execução) definir um ponto de interrupção aqui e usar um depurador para examinar o conteúdo de **model.resultList**. Você verá que é um processo intuitivo, fornecendo os dados que você pediu e não muito mais.

Chegou é a hora da verdade.

### <a name="test-the-app"></a>Testar o aplicativo

Agora, vamos verificar se o aplicativo é executado corretamente.

1. Selecione **Depurar/Iniciar Sem Depurar** ou pressione a tecla F5. Se você tiver codificado tudo corretamente, obterá a exibição de Índice inicial.

     ![Como abrir o aplicativo](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Insira um texto como "praia" (ou qualquer texto que lhe venha à cabeça) e clique no ícone de pesquisa. Você deverá obter alguns resultados.

     ![Pesquisando "praia"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Tente inserir "cinco estrelas". Observe como você não obtém nenhum resultado. Uma pesquisa mais sofisticada trataria "cinco estrelas" como sinônimo de "luxo" e retornaria esses resultados. O uso de sinônimos está disponível no Azure Search, embora não seja coberto nos primeiros tutoriais.
 
4. Tente inserir "hot" como texto de pesquisa. Isso _não_ retorna entradas com a palavra "hotel". Nossa pesquisa só está localizando palavras inteiras, embora alguns resultados sejam retornados.

5. Experimente outras palavras: "piscina", "sol", "vista", o que for. Você verá o Azure Search trabalhando em seu nível mais simples, mas ainda convincente.

## <a name="test-edge-conditions-and-errors"></a>Testar condições de borda e erros

É importante verificar se nossos recursos de tratamento de erro funcionam como deveriam, mesmo quando as coisas estão funcionando perfeitamente. 

1. No método **Index**, depois da chamada **try {** , insira a linha **Throw new Exception()** . Essa exceção forçará um erro quando pesquisarmos no texto.

2. Execute o aplicativo, insira "bar" como o texto de pesquisa e clique no ícone de pesquisa. A exceção deve resultar na exibição de erro.

     ![Forçar um erro](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Retornar os números de erro internos em páginas de erro é considerado um risco de segurança. Caso seu aplicativo destina-se a uso geral, investigue melhores práticas e segurança com relação ao que retornar quando ocorre um erro.

3. Remova **Throw new Exception()** quando estiver convencido de que o tratamento de erro funciona como deveria.

## <a name="takeaways"></a>Observações

Considere as principais conclusões deste projeto:

* Uma chamada do Azure Search é concisa e é fácil interpretar os resultados.
* Chamadas assíncronas adicionam um pouco de complexidade ao controlador, mas são a melhor prática caso você pretenda desenvolver aplicativos de qualidade.
* Este aplicativo realizou uma pesquisa de texto simples, definida pelo que é configurado em **searchParameters**. No entanto, essa classe pode ser preenchida com muitos membros que adicionam sofisticação a uma pesquisa. Não é necessário muito trabalho adicional para tornar este aplicativo consideravelmente mais eficiente.

## <a name="next-steps"></a>Próximas etapas

Para proporcionar a melhor experiência de usuário usando o Azure Search, precisamos adicionar mais recursos, principalmente, paginação (seja usando números de página ou rolagem infinita) e sugestões/preenchimento automático. Também devemos considerar os parâmetros de pesquisa mais sofisticados (por exemplo, pesquisas geográficas de hotéis dentro de um raio especificado de um determinado ponto e ordenação dos resultados da pesquisa).

Essas próximas etapas são tratadas em uma série de tutoriais. Vamos começar com paginação.

> [!div class="nextstepaction"]
> [Tutorial do C#: Paginação de resultados da pesquisa – Azure Search](tutorial-csharp-paging.md)


