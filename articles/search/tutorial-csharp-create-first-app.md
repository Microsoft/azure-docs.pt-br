---
title: Tutorial de C# para criar seu primeiro aplicativo
titleSuffix: Azure Cognitive Search
description: Saiba como criar seu primeiro aplicativo de pesquisa passo a passo em C#. O tutorial fornece tanto um link para download para um aplicativo em funcionamento no GitHub quanto o processo completo para criar o aplicativo desde o princípio.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 0a57e45b264badffd0305eb6ac5b3c8f7c42adf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695117"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Tutorial: Criar seu primeiro aplicativo de pesquisa usando o SDK do .NET

Este tutorial mostra como criar um aplicativo Web que consulta e retorna resultados de um índice de pesquisa usando o Azure Cognitive Search e o Visual Studio.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Configurar um ambiente de desenvolvimento
> * Modelar estruturas de dados
> * Criar uma página da Web para coletar entradas de consulta e exibir resultados
> * Definir um método de pesquisa
> * Testar o aplicativo

Você também aprenderá como uma chamada de pesquisa é simples. As principais instruções no código que você desenvolverá serão encapsuladas nas poucas linhas a seguir.

```csharp
var options = new SearchOptions()
{
    // The Select option specifies fields for the result set
    options.Select.Add("HotelName");
    options.Select.Add("Description");
};

var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
model.resultList = searchResult.Value.GetResults().ToList();
```

Apenas uma chamada consulta o índice e retorna resultados.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="Pesquisando *pool*" border="true":::

## <a name="overview"></a>Visão geral

Este tutorial usa o hotels-sample-index, o qual você pode criar rapidamente em seu próprio serviço de pesquisa percorrendo o [Guia de início rápido importar dados](search-get-started-portal.md). O índice contém dados fictícios de hotéis, que ficam disponíveis como uma fonte de dados interna em todos os serviços de pesquisa.

A primeira lição desse tutorial cria uma estrutura de consulta básica e uma página de pesquisa, as quais você aprimorará nas lições subsequentes para incluir paginação, facetas e uma experiência de preenchimento automático.

Uma versão finalizada do código pode ser encontrada no seguinte projeto:

* [1-basic-search-page (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page)

Este tutorial foi atualizado para usar o pacote Azure.Search.Documents (versão 11). Para obter uma versão anterior do SDK do .NET, confira o [exemplo de código Microsoft.Azure.Search (versão 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="prerequisites"></a>Pré-requisitos

* [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

* Crie o hotels-sample-index usando as instruções do [Guia de início rápido: criar um índice de pesquisa](search-get-started-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/)

* [Biblioteca de clientes do Azure Cognitive Search (versão 11)](https://www.nuget.org/packages/Azure.Search.Documents/)

### <a name="install-and-run-the-project-from-github"></a>Instalar e executar o projeto usando o GitHub

Se você quiser avançar para um aplicativo em funcionamento, siga as etapas abaixo para baixar e executar o código concluído.

1. Localize o exemplo no GitHub: [Criar primeiro aplicativo](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11).

1. Na [pasta raiz](https://github.com/Azure-Samples/azure-search-dotnet-samples), selecione **Código**, seguido por **Clonar** ou **Baixar ZIP** para fazer sua cópia local privada do projeto.

1. Usando o Visual Studio, navegue até e abra a solução para a página de pesquisa básica ("1-basic-search-page") e selecione **Iniciar sem depurar** (ou pressione F5) para compilar e executar o programa.

1. Este é um índice de hotéis, portanto, digite algumas palavras que você possa usar para pesquisar hotéis (por exemplo, "WiFi", "vista", "bar", "estacionamento") e examine os resultados.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="Pesquisando *wifi*" border="true":::

Esperamos que este projeto transcorra sem problemas e você tenha o aplicativo Web execução. Muitos dos componentes essenciais para pesquisas mais sofisticadas estão incluídos neste aplicativo, portanto, é uma boa ideia repassá-lo e recriá-lo passo a passo. As seções a seguir abrangem essas etapas.

## <a name="set-up-a-development-environment"></a>Configurar um ambiente de desenvolvimento

Para criar esse projeto do zero e, assim, reforçar os conceitos do Azure Cognitive Search que você tem em mente, comece com um projeto do Visual Studio.

1. No Visual Studio, selecione **Novo** > **Projeto** e **Aplicativo Web ASP.NET Core**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="Como criar um projeto de nuvem" border="true":::

1. Dê um nome ao projeto, como "FirstSearchApp", e defina a localização. Selecione **Criar**.

1. Escolha o modelo de projeto **Aplicativo Web (Model-View-Controller)** .

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="Como criar um projeto MVC" border="true":::

1. Instalar a biblioteca de clientes. Em **Ferramentas** > **Gerenciador de Pacotes do NuGet** > **Gerenciar Pacotes do NuGet para a Solução...** , selecione **Procurar** e pesquise por "azure.search.documents". Instale o **Azure.Search.Documents** (versão 11 ou posterior), aceitando os contratos de licença e as dependências.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Como usar o NuGet para adicionar bibliotecas do Azure" border="true":::

### <a name="initialize-azure-cognitive-search"></a>Inicializar a Pesquisa Cognitiva do Azure

Para este exemplo, você está usando dados de hotel disponíveis ao público. Esses dados são uma coleção arbitrária de 50 nomes e descrições de hotel fictícios criados exclusivamente para fornecer dados de demonstração. Para acessar esses dados, especifique um nome e uma chave de API.

1. Abra **appsettings.jsno** e substitua as linhas padrão pelo URL do serviço de pesquisa (no formato `https://<service-name>.search.windows.net`) e uma [chave de API de consulta ou administrador](search-security-api-keys.md) do seu serviço de pesquisa. Como você não precisa criar ou atualizar um índice, você pode usar a chave de consulta para esse tutorial.

    ```csharp
    {
        "SearchServiceName": "<YOUR-SEARCH-SERVICE-URI>",
        "SearchServiceQueryApiKey": "<YOUR-SEARCH-SERVICE-API-KEY>"
    }
    ```

1. Em Gerenciador de Soluções, selecione o arquivo e, em Propriedades, altere a configuração **Copiar para o Diretório de Saída** para **Copiar se for mais recente**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="Como copiar as configurações do aplicativo para a saída" border="true":::

## <a name="model-data-structures"></a>Modelar estruturas de dados

Os modelos (classes C#) são usados para comunicar dados entre o cliente (a exibição), o servidor (o controlador) e também a nuvem do Azure usando a arquitetura MVC (modelo, exibição, controlador). Normalmente, esses modelos refletirão a estrutura dos dados que estão sendo acessados.

Nesta etapa, você modelará as estruturas de dados do índice de pesquisa, bem como a cadeia de pesquisa usada em comunicações de exibição/controlador. No índice de hotéis, cada hotel tem muitos quartos e cada hotel tem um endereço de várias partes. No geral, a representação completa de um hotel é uma estrutura de dados hierárquica e aninhada. Você precisará de três classes para criar cada componente.

O conjunto das classes **Hotel**, **Endereço** e **Quarto** é conhecido como [*tipos complexos*](search-howto-complex-data-types.md), um recurso importante do Azure Cognitive Search. Tipos complexos podem ter muitos níveis de profundidade de classes e subclasses e possibilitam a representação de estruturas de dados muito mais complexas do que usando *tipos simples* (uma classe contendo somente membros primitivos).

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **Modelos** > **Adicionar** > **Novo Item**.

1. Selecione **Classe** e dê ao item o nome de Hotel.cs. Substitua todo o conteúdos de Hotel.cs pelo código a seguir. Observe os membros **Endereço** e **Quarto** da classe; esses campos são classes em si, assim, você precisa de modelos para eles também.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using Microsoft.Spatial;
    using System;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
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
    }
    ```

1. Repita o mesmo processo de criação de um modelo para a classe de **Endereços**, dando ao arquivo o nome de Address.cs. Substitua o conteúdo pelo seguinte.

    ```csharp
    using Azure.Search.Documents.Indexes;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [SearchableField]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
    ```

1. Novamente, siga o mesmo processo para criar a classe **Quarto**, dando ao arquivo o nome de Room.cs.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnMicrosoft)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrMicrosoft)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string Type { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public double? BaseRate { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string BedOptions { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public int SleepsCount { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public bool? SmokingAllowed { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }
        }
    }
    ```

1. O último modelo que você criará neste tutorial é uma classe chamada **SearchData** e representa a entrada do usuário (**searchText**) e a saída da pesquisa (**resultList**). O tipo de saída é crítico, **SearchResult&lt;Hotel&gt;** , pois ele corresponde exatamente aos resultados da pesquisa e você precisa passar essa referência para a exibição. Substitua o modelo padrão pelo código a seguir.

    ```csharp
    using Azure.Search.Documents.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public SearchResults<Hotel> resultList;
        }
    }
    ```

## <a name="create-a-web-page"></a>Criar uma página da Web

Os modelos de projeto vêm com várias exibições de cliente localizadas na pasta **Views**. As exibições exatas dependem da versão do .NET Core que você está usando (este exemplo usa a 3.1). Para este tutorial, você modificará **index.cshtml** para incluir os elementos de uma página de pesquisa.

Exclua o conteúdo de Index.cshtml em sua totalidade e recompile o arquivo nas etapas a seguir.

1. O tutorial usa duas imagens pequenas na exibição: um logotipo do Azure e um ícone de lupa de pesquisa (azure-logo.png e search.png). Copie entre as imagens do projeto do GitHub para a pasta **wwwroot/images** em seu projeto.

1. A primeira linha de Index.cshtml deve fazer referência ao modelo usado para comunicar dados entre o cliente (a exibição) e o servidor (o controlador), que é o modelo **SearchData** criado antes. Adicione essa linha ao arquivo Index.cshtml.

    ```csharp
    @model FirstAzureSearchApp.Models.SearchData
    ```

1. É uma prática padrão inserir um título para a exibição, portanto, as próximas linhas devem ser:

    ```csharp
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

1. Após o título, insira uma referência a uma folha de estilos HTML, que você criará em breve.

    ```csharp
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

1. O corpo da exibição lida com dois casos de uso. Primeiro, ele deve fornecer uma página vazia no primeiro uso antes da inserção de qualquer texto de pesquisa. Em segundo lugar, ele deve lidar com os resultados, além da caixa de texto de pesquisa, para consultas repetidas.

   Para lidar com ambos os casos, você precisa verificar se o modelo fornecido para a exibição é nulo. Um modelo nulo indica o primeiro caso de uso (a execução inicial do aplicativo). Adicione o seguinte ao arquivo Index. cshtml e leia os comentários.

    ```csharp
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
                @Model.resultList.Count Results
            </p>

            @for (var i = 0; i < Model.resultList.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => m.resultList[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

1. Adicione a folha de estilos. No Visual Studio, em **Arquivo**> **Novo** > **Arquivo**, selecione **Folha de Estilos** (com **Geral** realçado).

   Substitua o código padrão pelo seguinte. Não vamos entrar em mais detalhes sobre esse arquivo, os estilos são HTML padrão.

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

1. Salve o arquivo de folha de estilos como hotels.css na pasta **wwwroot/css** juntamente com o arquivo site.css padrão.

Isso conclui nossa exibição. Neste ponto, os modelos e as exibições são concluídos. Somente o controlador é deixado para unir tudo.

## <a name="define-methods"></a>Definir métodos

Nesta etapa, modifique o conteúdo de **Controlador da Página Inicial**.

1. Abra o arquivo HomeController.cs e substitua as instruções **using** pelo seguinte.

    ```csharp
    using Azure;
    using Azure.Search.Documents;
    using Azure.Search.Documents.Indexes;
    using FirstAzureSearchApp.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Diagnostics;
    using System.Linq;
    using System.Threading.Tasks;
    ```

### <a name="add-index-methods"></a>Adicionar métodos Index

Em um aplicativo MVC, o método **Index()** é um método de ação padrão para qualquer controlador. Ele abre a página de índice HTML. O método padrão, que não usa parâmetros, é usado neste tutorial para o caso de uso de inicialização do aplicativo: renderizar uma página de pesquisa vazia.

Nesta seção, estendemos o método para dar suporte a um segundo caso de uso: renderizar a página quando um usuário insere o texto de pesquisa. Para dar suporte a esse caso, o método de índice é estendido para pegar um modelo como um parâmetro.

1. Adicione o seguinte método após o método **Index()** padrão.

    ```csharp
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

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Observe a declaração **async** do método e a chamada **await** para **RunQueryAsync**. Essas palavras-chave cuidam da realização das chamadas assíncronas, portanto, evitam bloquear threads no servidor.

    O bloco **catch** usa o modelo de erro criado por padrão.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Observe o tratamento de erro e outras exibições e métodos padrão

Dependendo de qual versão do .NET Core você está usando, um conjunto ligeiramente diferente de exibições padrão é criado por padrão. Para o .NET Core 3.1, as exibições padrão são Índice, Privacidade e Erro. Você pode exibir essas páginas padrão ao executar o aplicativo e examinar como elas são processadas no controlador.

Você testará a exibição de Erro mais adiante neste tutorial.

No exemplo do GitHub, exibições não utilizadas e suas ações associadas são excluídas.

### <a name="add-the-runqueryasync-method"></a>Adicionar o método RunQueryAsync

A chamada à Pesquisa Cognitiva do Azure é encapsulada em nosso método **RunQueryAsync**.

1. Primeiro, adicione algumas variáveis estáticas para configurar o serviço do Azure e uma chamada para iniciá-las.

    ```csharp
        private static SearchClient _searchClient;
        private static SearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using appsettings.json
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Read the values from appsettings.json
            string searchServiceUri = _configuration["SearchServiceUri"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(queryApiKey));
            _searchClient = _indexClient.GetSearchClient("hotels");
        }
    ```

2. Agora, adicione o próprio método **RunQueryAsync**.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model)
    {
        InitSearch();

        var options = new SearchOptions() { };

        // Enter Hotel property names into this list so only these values will be returned.
        // If Select is empty, all values will be returned, which can be inefficient.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
        model.resultList = searchResult.Value.GetResults().ToList();

        // Display the results.
        return View("Index", model);
    }
    ```

    Nesse método, primeiro inicie a configuração do Azure e então defina algumas opções de pesquisa. A opção **Selecionar** especifica quais campos retornar nos resultados e, portanto, corresponde aos nomes das propriedades na classe **hotel**. Se você omitir **Selecionar**, todos os campos não ocultos serão retornados, o que poderá ser ineficiente se você estiver interessado apenas em um subconjunto de todos os campos possíveis.

    A chamada assíncrona para pesquisar formula a solicitação (modelada como **searchText**) e a resposta (modelada como **searchResult**). Se você está depurando esse código, a classe **SearchResult** é um bom candidato para definir um ponto de interrupção se você precisa examinar o conteúdo de **model.resultList**. Você verá que é um processo intuitivo, fornecendo os dados que você pediu e não muito mais.

### <a name="test-the-app"></a>Testar o aplicativo

Agora, vamos verificar se o aplicativo é executado corretamente.

1. Selecione **Depurar** > **Iniciar Sem Depurar** ou pressione **F5**. Se o aplicativo for executado conforme o esperado, você deverá obter a exibição de índice inicial.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="Como abrir o aplicativo" border="true":::

1. Insira uma cadeia de consulta como "praia" (ou qualquer texto que lhe venha à cabeça) e clique no ícone de pesquisa para enviar a solicitação.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="Pesquisando *praia*" border="true":::

1. Tente inserir "cinco estrelas". Observe que essa consulta não retorna nenhum resultado. Uma pesquisa mais sofisticada trataria "cinco estrelas" como sinônimo de "luxo" e retornaria esses resultados. O suporte para [sinônimos](search-synonyms.md) está disponível no Azure Cognitive Search, mas não é abordado nesta série de tutoriais.

1. Tente inserir "hot" como texto de pesquisa. Isso _não_ retorna entradas com a palavra "hotel". Nossa pesquisa só está localizando palavras inteiras, embora alguns resultados sejam retornados.

1. Experimente outras palavras: "piscina", "sol", "vista", o que for. Você verá a Pesquisa Cognitiva do Azure trabalhando em seu nível mais simples, mas ainda convincente.

## <a name="test-edge-conditions-and-errors"></a>Testar condições de borda e erros

É importante verificar se nossos recursos de tratamento de erro funcionam como deveriam, mesmo quando as coisas estão funcionando perfeitamente. 

1. No método **Index**, depois da chamada **try {** , insira a linha **Throw new Exception()** . Essa exceção forçará um erro ao pesquisar no texto.

2. Execute o aplicativo, insira "bar" como o texto de pesquisa e clique no ícone de pesquisa. A exceção deve resultar na exibição de erro.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="Forçar um erro" border="true":::

    > [!Important]
    > Retornar os números de erro internos em páginas de erro é considerado um risco de segurança. Caso seu aplicativo destina-se a uso geral, investigue melhores práticas e segurança com relação ao que retornar quando ocorre um erro.

3. Remova **Throw new Exception()** quando estiver convencido de que o tratamento de erro funciona como deveria.

## <a name="takeaways"></a>Observações

Considere as principais conclusões deste projeto:

* Uma chamada da Pesquisa Cognitiva do Azure é concisa e é fácil interpretar os resultados.
* Chamadas assíncronas adicionam um pouco de complexidade ao controlador, mas são a melhor prática caso você pretenda desenvolver aplicativos de qualidade.
* Este aplicativo realizou uma pesquisa de texto simples, definida pelo que é configurado em **searchOptions**. No entanto, essa classe pode ser preenchida com muitos membros que adicionam sofisticação a uma pesquisa. Não é necessário muito trabalho adicional para tornar este aplicativo consideravelmente mais eficiente.

## <a name="next-steps"></a>Próximas etapas

Para aprimorar a experiência do usuário, adicione recursos, especialmente paginação (usando números de página ou rolagem infinita) e preenchimento automático/sugestões. Também é possível considerar opções de pesquisa mais sofisticadas (por exemplo, pesquisas geográficas de hotéis dentro de um raio especificado de um determinado ponto e ordenação dos resultados da pesquisa).

As próximas etapas são abordadas nos tutoriais restantes. Vamos começar com paginação.

> [!div class="nextstepaction"]
> [Tutorial do C#: Paginação dos resultados da pesquisa – Pesquisa Cognitiva do Azure](tutorial-csharp-paging.md)