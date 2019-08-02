---
title: Conectar a API de Pesquisa de Notícias do Bing com os Serviços Conectados no Visual Studio e C#
titleSuffix: Azure Cognitive Services
description: Conecte a Pesquisa de Notícias do Bing de um aplicativo Web ASP.NET Core.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: ghogen
ms.openlocfilehash: 85afae087b1b1e572759943142412743744ee806
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203412"
---
# <a name="tutorial-connect-to-bing-news-search-api-with-connected-services-in-visual-studio-and-c"></a>Tutorial: Conectar a API de Pesquisa de Notícias do Bing com os Serviços Conectados no Visual Studio e C#

Ao usar a Pesquisa de Notícias do Bing, é possível habilitar aplicativos e serviços para aproveitar a potência de um mecanismo de pesquisa livre de anúncios com escopo definido para a Web. A Pesquisa de Notícias do Bing é um dos serviços de pesquisa disponíveis com os Serviços Cognitivos.

Este artigo fornece detalhes sobre o uso do recurso de Serviço Conectado do Visual Studio para pesquisa de Notícias do Bing. O recurso está disponível no Visual Studio 2017 15.7 ou posterior, com a extensão dos Serviços Cognitivos instalada.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2019 com a carga de trabalho de Desenvolvimento para a Web instalada. [Baixe agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Adicionar suporte ao projeto para API de Pesquisa de Notícias do Bing

1. Criar um novo projeto Web do ASP.NET Core nomeado MyWebApplication. Use o modelo de projeto **aplicativo Web (Model-View-Controller)** , com todas as configurações padrão. É importante nomear o projeto MyWebApplication, para que o namespace corresponda ao copiar o código no projeto. 

1. No **Gerenciador de Soluções**, selecione **Adicionar** > **Serviço Conectado**.
   A página Serviço Conectado é exibida com os serviços que podem ser adicionados ao projeto.

   ![Captura de tela do item de menu Adicionar Serviço Conectado](../media/vs-common/Connected-Service-Menu.PNG)

1. No menu de serviços disponíveis, escolha **Ofereça pesquisas inteligentes aos aplicativos**.

   ![Captura de tela da lista dos serviços conectados](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Se você tiver entrado no Visual Studio e tiver uma assinatura do Azure associada à sua conta, será exibida uma página com uma lista suspensa com suas assinaturas. Selecione a assinatura que você quer usar e escolha um nome para a API de Pesquisa de Notícias do Bing. Também é possível escolher **Editar** para modificar o nome gerado automaticamente.

   ![Captura de tela dos campos de nome e assinatura](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Escolha o grupo de recursos e o tipo de preço.

   ![Captura de tela dos campos do grupo de recursos e tipo de preço](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Se você quiser obter mais detalhes sobre tipos de preços, selecione **Revisar preços**.

1. Escolha **Adicionar** para adicionar suporte ao Serviço Conectado.
   O Visual Studio modifica o projeto para adicionar os pacotes NuGet, entradas do arquivo de configuração e outras alterações para dar suporte a uma conexão com a API de Pesquisa de Notícias do Bing. A saída mostra o log do que está acontecendo com o projeto. Você verá algo semelhante ao que se segue:

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   O arquivo appsettings.json agora contém as seguintes novas configurações:

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Use a API de Pesquisa de Notícias do Bing para adicionar funcionalidade de pesquisa a uma página da Web

Agora que você adicionou suporte para a API de Pesquisa de Notícias do Bing ao projeto, esse exemplo mostra como usar a API para adicionar pesquisa inteligente para uma página da Web.

1. Em *Startup.cs*, no método `ConfigureServices`, adicione uma chamada para `IServiceCollection.AddSingleton`. Isso torna o objeto de configuração que contém as principais configurações disponíveis para o código no projeto.
 
   ```csharp
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddMvc();
           services.AddSingleton<IConfiguration>(Configuration);
       }
   ```


1. Adicione um novo arquivo de classe na pasta **Modelos**, chamado *BingNewsModel.cs*. Se você nomeou o projeto de maneira diferente, use o namespace do próprio projeto, em vez do MyWebApplication. Substitua o conteúdo pelo código a seguir:
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   Este modelo é usado para armazenar os resultados de uma chamada para o serviço de Pesquisa de Notícias do Bing.
 
1. Na pasta **Controladores**, adicione um novo arquivo de classe chamado *IntelligentSearchController.cs*. Substitua o conteúdo pelo código a seguir:

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   Neste código, o construtor configura o objeto de configuração que contém as chaves. O método para a `Search` rota é apenas um redirecionamento para a `BingSearchResult` função. Isso chama o método `GetNewsSearchClient` para obter o objeto do cliente`NewsSearchAPI`.  O objeto do cliente `NewsSearchAPI` contém o método `SearchAsync` que realmente chama o serviço e retorna os resultados no modelo `SearchResult` que você acabou de criar. 

1. Adicione uma classe, `MyHandler`, que foi referenciada no código anterior. Isso delega a chamada assíncrona ao serviço de pesquisa para a classe base, `DelegatingHandler`.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. Para adicionar suporte e enviar pesquisas e visualizar os resultados, na pasta **Modos de Exibição**, crie uma nova pasta chamada **IntelligentSearch**. Nesta nova pasta, adicione uma exibição *BingSearchResult.cshtml*. Copie no código a seguir:

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. Inicie o aplicativo Web localmente, insira a URL da página que você acabou de criar (/IntelligentSearch/BingSearchResult) e publique uma solicitação de pesquisa usando o botão Pesquisar.

   ![Captura de tela dos resultados da Pesquisa de Notícias do Bing](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Limpar recursos

Quando o grupo de recursos não for mais necessário, você poderá excluí-lo. Isso exclui o serviço cognitivo e os recursos relacionados. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa de pesquisa na parte superior do portal. Selecione o grupo de recursos que você quer excluir.
2. Selecione **Excluir grupo de recursos**.
3. Na caixa **Digitar o nome do grupo de recursos**, insira o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a API de Pesquisa de Notícias do Bing, consulte [O que é Pesquisa de Notícias do Bing?](index.yml).
