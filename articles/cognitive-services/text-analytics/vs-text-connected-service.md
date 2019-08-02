---
title: 'Tutorial: Conectar o serviço de Análise de Texto com os Serviços Conectados no Visual Studio'
titleSuffix: Azure Cognitive Services
description: Saiba como conectar a Análise de Texto de um aplicativo Web do ASP.NET Core.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: ff4c703070d6a7ebd545de3043e5f59b764fe4c9
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478469"
---
# <a name="tutorial-connect-to-the-text-analytics-service-with-connected-services-in-visual-studio"></a>Tutorial: Conectar o serviço de Análise de Texto com os Serviços Conectados no Visual Studio

Usando o Serviço de Análise de Texto, é possível extrair informações avançadas para categorizar e processar dados visuais e executar moderação assistida por computador de imagens para ajudar a coletar os serviços.

Este artigo e os artigos complementares fornecem detalhes sobre como usar o recurso de Serviço Conectado do Visual Studio para Serviço de Análise de Texto. O recurso está disponível no Visual Studio 2019 ou posterior, com a extensão dos Serviços Cognitivos instalada.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2019 com a carga de trabalho de Desenvolvimento para a Web instalada. [Baixe agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Adicionar suporte ao projeto para o Serviço de Análise de Texto

1. Crie um novo projeto Web do ASP.NET Core chamado TextAnalyticsDemo. Use o modelo de projeto do Aplicativo Web (Model-View-Controller) com todas as configurações padrão. É importante nomear o projeto MyWebApplication, para que o namespace corresponda ao copiar o código no projeto.  O exemplo deste artigo usa o MVC, mas você pode usar o serviço conectado de Análise de Texto com qualquer tipo de projeto ASP.NET.

1. Em **Gerenciador de Soluções**, clique duas vezes no item **Serviço Conectado**.
   A página Serviço Conectado é exibida com os serviços que podem ser adicionados ao projeto.

   ![Captura de tela do Serviço Conectado no Gerenciador de Soluções](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. No menu de serviços disponíveis, escolha **Avaliar Sentimento com Análise de Texto**.

   ![Captura de tela da tela Serviços Conectados](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Se você tiver entrado no Visual Studio e tiver uma assinatura do Azure associada à sua conta, será exibida uma página com uma lista suspensa com suas assinaturas.

   ![Captura de tela da tela do Serviço Conectado da Análise de Texto](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Selecione a assinatura que você quer usar e, em seguida, escolha um nome para o Serviço de Análise de Texto ou escolha o link **Editar** para modificar o nome gerado automaticamente, escolha o grupo de recursos e Tipo de Preço.

   ![Captura de tela dos campos do grupo de recursos e tipo de preço](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Acompanhe o link para obter detalhes sobre os tipos de preços.

1. Escolha **Adicionar** para adicionar suporte ao Serviço Conectado.
   O Visual Studio modifica o projeto para adicionar os pacotes NuGet, entradas do arquivo de configuração e outras alterações para dar suporte a uma conexão com o Serviço de Análise de Texto. A **Janela de Saída** mostra o log do que está acontecendo com o projeto. A saída deverá ser semelhante à mostrada abaixo:

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.1'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Use o Serviço de Análise de Texto para detectar o idioma de um exemplo de texto.

1. Adicione o seguinte usando instruções no Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Adicione um campo de configuração e adicione um construtor que inicializa o campo de configuração na classe de Inicialização para habilitar a Configuração no programa.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Adicione um arquivo de classe na pasta *Controllers* chamada `DemoTextAnalyzeController` e substitua o conteúdo pelo seguinte código:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    O código inclui `GetTextAnalyzeClient` para obter o objeto de cliente para fazer chamadas à API de Análise de Texto e um manipulador de solicitações que chama DetectLanguage em determinado texto.

1. Adicione a classe auxiliar MyHandler que é usada pelo código anterior.

    ```csharp
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

1. Na pasta *Models*, adicione uma classe ao modelo.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. Adicione uma Exibição para mostrar o texto analisado, o idioma determinado e a classificação que representa o nível de confiança na análise. Para fazer isso, clique com o botão direito do mouse na pasta **Modos de Exibição**, escolha **Adicionar** e, em seguida, **Exibir**. Na caixa de diálogo exibida, forneça um nome _TextAnalyzeResult_, aceite os padrões para adicionar um novo arquivo chamado _TextAnalyzeResult.cshtml_ na pasta **Modos de Exibição** e copie o seguinte conteúdo no arquivo:
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. Compile e execute o exemplo localmente. Digite um texto e verifique qual idioma a Análise de Texto detecta.
   
## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos. Isso exclui o serviço cognitivo e os recursos relacionados. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa de pesquisa na parte superior do portal. Ao visualizar o grupo de recursos usado neste tutorial nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Na caixa **DIGITE O NOME DO GRUPO DE RECURSOS:** , digite o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Serviço de Análise de Texto, lendo as informações na [Documentação do Serviço de Análise de Texto](index.yml).
