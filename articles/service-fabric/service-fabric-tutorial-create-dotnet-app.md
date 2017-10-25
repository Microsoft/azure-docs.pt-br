---
title: Criar um aplicativo .NET para o Service Fabric | Microsoft Docs
description: "Saiba como criar um aplicativo com um front-end do ASP.NET Core e um serviço confiável de back-end com estado e implantar o aplicativo em um cluster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 51d580e9594bc3fb43a812395d1dda505a83ead1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Criar e implantar um aplicativo com um serviço de front-end de API Web do ASP.NET Core e um serviço de back-end com estado
Este tutorial é a primeira parte de uma série.  Você aprenderá a criar um aplicativo do Azure Service Fabric com um front-end da API Web do ASP.NET Core e um serviço de back-end com estado para armazenar seus dados. Quando terminar, você terá um aplicativo de votação com um front-end da Web do ASP.NET Core que salva os resultados da votação em um serviço de back-end com estado do cluster. Se você não quiser criar manualmente o aplicativo de votação, [baixe o código-fonte](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) do aplicativo concluído e vá direto para [Percorrer o aplicativo de exemplo votação](#walkthrough_anchor).

![Diagrama de aplicativo](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Criar um serviço de API Web do ASP.NET Core como um Reliable Services com estado
> * Criar um serviço de Aplicativo Web do ASP.NET Core como um serviço Web sem estado
> * Usar o proxy reverso para se comunicar com o serviço com estado

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um aplicativo do Service Fabric .NET
> * [Implantar o aplicativo em um cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Configurar CI/CD usando o Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurar monitoramento e diagnóstico para o aplicativo](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Instale o Visual Studio 2017](https://www.visualstudio.com/) e instale as cargas de trabalho de **desenvolvimento do Azure** e de **desenvolvimento para a Web e ASP.NET**.
- [Instalar o SDK do Service Fabric](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Criar um serviço de API Web ASP.NET como um serviço confiável
Primeiro, crie o front-end da Web do aplicativo de votação usando o ASP.NET Core. O ASP.NET Core é uma estrutura de desenvolvimento Web leve entre plataformas que permite a criação de uma interface do usuário Web e APIs Web modernas. Para obter uma compreensão completa de como o ASP.NET Core se integra ao Service Fabric, é altamente recomendável ler o artigo [ASP.NET Core nos Reliable Services do Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md). Por hora você pode seguir este tutorial para começar rapidamente. Para saber mais sobre o ASP.NET Core, confira a [Documentação do ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

> [!NOTE]
> Este tutorial se baseia nas [Ferramentas do ASP.NET Core para Visual Studio 2017](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc). As ferramentas do .NET Core para Visual Studio 2015 não estão mais sendo atualizadas.

1. Inicie o Visual Studio como um **administrador**.

2. Crie um projeto com **Arquivo**->**Novo**->**Projeto**

3. Na caixa de diálogo **Novo Projeto**, escolha **Nuvem > Aplicativo do Service Fabric**.

4. Nomeie o aplicativo **Votação** e pressione **OK**.

   ![Caixa de diálogo Novo projeto no Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Na página **Novo Serviço do Service Fabric** escolha **ASP.NET Core sem estado** e dê o nome **VotaçãoWeb** ao seu serviço.
   
   ![Escolhendo um serviço Web ASP.NET no diálogo Novo serviço](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. A próxima página fornece um conjunto de modelos de projeto do ASP.NET Core. Para este tutorial, escolha **Aplicativo Web**. 
   
   ![Escolha o tipo de projeto do ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   O Visual Studio cria um aplicativo e um projeto de serviço e os exibe no Gerenciador de Soluções.

   ![Gerenciador de Soluções após a criação de aplicativo com serviço da API Web do ASP.NET Core]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-angularjs-to-the-votingweb-service"></a>Adicionar AngularJS ao serviço VotaçãoWeb
Adicione [AngularJS](http://angularjs.org/) ao seu serviço usando o [Suporte ao Bower](/aspnet/core/client-side/bower) interno. Abra *bower.json* e adicione entradas para angular e inicialização angular e depois salve suas alterações.

```json
{
  "name": "asp.net",
  "private": true,
  "dependencies": {
    "bootstrap": "3.3.7",
    "jquery": "2.2.0",
    "jquery-validation": "1.14.0",
    "jquery-validation-unobtrusive": "3.2.6",
    "angular": "v1.6.5",
    "angular-bootstrap": "v1.1.0"
  }
}
```
Ao salvar o arquivo *bower.json*, Angular será instalado na pasta *wwwroot/lib* de seu projeto. Além disso, ele será listado dentro da pasta *Dependências/Bower*.

### <a name="update-the-sitejs-file"></a>Atualizar o arquivo site.js
Abra o arquivo *wwwroot/js/site.js*.  Substitua seu conteúdo pelo JavaScript usado pelas exibições iniciais:

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Atualizar o arquivo Index.cshtml
Abra o arquivo *Views/Home/Index.cshtml*, a exibição específica ao controlador Home.  Substitua o conteúdo pelo seguinte, depois salve as alterações.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-layoutcshtml-file"></a>Atualizar o arquivo _Layout.cshtml
Abra o arquivo *Views/Shared/_Layout.cshtml*, o layout padrão para o aplicativo ASP.NET.  Substitua o conteúdo pelo seguinte, depois salve as alterações.

```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="http://angularjs.org">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="~/css/site.css" rel="stylesheet" />

</head>
<body>
    <div class="container body-content">
        @RenderBody()
    </div>

    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
    <script src="~/lib/angular/angular.js"></script>
    <script src="~/lib/angular-bootstrap/ui-bootstrap-tpls.js"></script>
    <script src="~/js/site.js"></script>

    @RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Atualizar o arquivo VotaçãoWeb.cs
Abra o arquivo *VotaçãoWeb.cs*, que cria o WebHost do ASP.NET Core dentro do serviço sem estado usando o servidor Web WebListener.  Adicione a diretiva `using System.Net.Http;` à parte superior do arquivo.  Substitua a função `CreateServiceInstanceListeners()` pelo seguinte, depois salve as alterações.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
            {
                ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting WebListener on {url}");

                return new WebHostBuilder().UseWebListener()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<StatelessServiceContext>(serviceContext)
                                    .AddSingleton<HttpClient>())
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseApplicationInsights()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
            }))
    };
}
```

### <a name="add-the-votescontrollercs-file"></a>Adicionar o arquivo VotesController.cs
Adicione um controlador que define as ações de votação. Clique com o botão direito do mouse na pasta **Controladores** e selecione **Adicionar->Novo item->Classe**.  Nomeie o arquivo "VotesController.cs" e clique em **Adicionar**.  Substitua o conteúdo do arquivo pelo seguinte, depois salve as alterações.  Posteriormente, em [Atualizar o arquivo VotesController.cs](#updatevotecontroller_anchor), esse arquivo será modificado para ler e gravar dados de votação do serviço de back-end.  Por enquanto, o controlador retorna dados de cadeia de caracteres estática para a exibição.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using Newtonsoft.Json;
using System.Text;
using System.Net.Http;
using System.Net.Http.Headers;

namespace VotingWeb.Controllers
{
    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```



### <a name="deploy-and-run-the-application-locally"></a>Implantar e executar o aplicativo localmente
Agora você pode executar o aplicativo. Pressione `F5` no Visual Studio a fim de implantar o aplicativo para depuração. O `F5` falhará se você não abriu anteriormente o Visual Studio como **administrador**.

> [!NOTE]
> Na primeira vez em que você executar e implantar o aplicativo localmente, o Visual Studio criará um cluster local para depuração.  A criação do cluster pode demorar um pouco. O status de criação do cluster é exibido na janela de saída do Visual Studio.

Neste ponto, seu aplicativo Web deve parecer com o seguinte:

![Front-end do ASP.NET Core](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Para interromper a depuração do aplicativo, volte para o Visual Studio e pressione **Shift + F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Adicionar um serviço de back-end com estado ao seu aplicativo
Agora que temos um serviço de API Web ASP.NET em execução em nosso aplicativo, vamos prosseguir e adicionar um serviço confiável com estado para armazenar alguns dados em nosso aplicativo.

O Service Fabric permite que você armazene seus dados de forma consistente e confiável diretamente em seu serviço usando as coleções confiáveis. As coleções confiáveis são um conjunto de classes de coleção confiáveis altamente disponíveis que são familiares a todos aqueles que já usaram coleções de C#.

Neste tutorial, você cria um serviço que armazena um valor de contador em uma coleção confiável.

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **Serviços** no projeto de aplicativo e escolha **Adicionar > Novo Serviço do Fabric Service**.
   
    ![Adicionando um novo serviço a um aplicativo existente](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. Na caixa de diálogo **Novo Serviço do Service Fabric**, escolha **ASP.NET Core com Estado**, nomeie o serviço como **DadosVotação** e pressione **OK**.

    ![Caixa de diálogo Novo serviço no Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    Depois de criar seu projeto de serviço, você terá dois serviços em seu aplicativo. Conforme continua a criar o aplicativo, você pode adicionar mais serviços da mesma forma. Cada um pode seu próprio controle de versão e ser atualizado de forma independente.

3. A próxima página fornece um conjunto de modelos de projeto do ASP.NET Core. Neste tutorial, escolhemos **API Web**.

    ![Escolha o tipo de projeto do ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog2.png)

    O Visual Studio cria um projeto de serviço e o exibe no Gerenciador de Soluções.

    ![Gerenciador de Soluções](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Adicionar o arquivo VoteDataController.cs

No projeto **DadosVotação**, clique com o botão direito do mouse na pasta **Controladores** e selecione **Adicionar->Novo item->Classe**. Nomeie o arquivo "VotesDataController.cs" e clique em **Adicionar**. Substitua o conteúdo do arquivo pelo seguinte, depois salve as alterações.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.ServiceFabric.Data;
using System.Threading;
using Microsoft.ServiceFabric.Data.Collections;

namespace VotingData.Controllers
{
    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            var ct = new CancellationToken();

            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                var list = await votesDictionary.CreateEnumerableAsync(tx);

                var enumerator = list.GetAsyncEnumerator();

                var result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```


## <a name="connect-the-services"></a>Conectar os serviços
Nesta próxima etapa, vamos conectar os dois serviços e fazer com que o Aplicativo Web do front-end obtenha e defina informações de votação do serviço de back-end.

O Service Fabric fornece total flexibilidade na comunicação com Reliable Services. Em um único aplicativo, você pode ter serviços que sejam acessíveis por meio de TCP. Outros serviços que podem ser acessados por meio de uma API REST de HTTP e ainda outros serviços podem estar acessíveis por meio de soquetes da Web. Para saber mais sobre as opções disponíveis e as compensações envolvidas, confira [Comunicação com os serviços](service-fabric-connect-and-communicate-with-services.md).

Neste tutorial, usamos a [API Web ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md).

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Atualizar o arquivo VotesController.cs
No projeto **VotaçãoWeb**, abra o arquivo *Controllers/VotesController.cs*.  Substitua o conteúdo de definição de classe `VotesController` pelo seguinte, depois salve as alterações.

```csharp
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;
        string serviceProxyUrl = "http://localhost:19081/Voting/VotingData/api/VoteData";
        string partitionKind = "Int64Range";
        string partitionKey = "0";

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            IEnumerable<KeyValuePair<string, int>> votes;

            HttpResponseMessage response = await this.httpClient.GetAsync($"{serviceProxyUrl}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            votes = JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync());

            return Json(votes);
        }

        // PUT: api/Votes/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            string payload = $"{{ 'name' : '{name}' }}";
            StringContent putContent = new StringContent(payload, Encoding.UTF8, "application/json");
            putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

            string proxyUrl = $"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}";

            HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent);

            return new ContentResult()
            {
                StatusCode = (int)response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }

        // DELETE: api/Votes/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            HttpResponseMessage response = await this.httpClient.DeleteAsync($"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            return new OkResult();

        }
    }
```
<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Percorrer o aplicativo de exemplo de votação
O aplicativo de votação consiste em dois serviços:
- Serviço de front-end da Web (VotingWeb) – um serviço de front-end da Web do ASP.NET Core, que fornece a página da Web e expõe APIs Web para se comunicar com o serviço de back-end.
- Serviço de back-end (VotingData) – um serviço Web do ASP.NET Core, que expõe uma API para armazenar os resultados da votação em um dicionário confiável persistido em disco.

![Diagrama de aplicativo](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Quando você vota no aplicativo, os seguintes eventos ocorrem:
1. Um JavaScript envia a solicitação de votação para a API Web no serviço de front-end da Web como uma solicitação HTTP PUT.

2. O serviço de front-end da Web usa um proxy para localizar e encaminhar uma solicitação HTTP PUT para o serviço de back-end.

3. O serviço de back-end recebe a solicitação de entrada e armazena o resultado atualizado em um dicionário confiável, que é replicado em vários nós no cluster e persistido em disco. Todos os dados do aplicativo são armazenados no cluster e, portanto, nenhum banco de dados é necessário.

## <a name="debug-in-visual-studio"></a>Depuração no Visual Studio
Ao depurar o aplicativo no Visual Studio, você usa um cluster de desenvolvimento local do Service Fabric. Você tem a opção de ajustar sua experiência de depuração para seu cenário. Neste aplicativo, armazenamos dados em nosso serviço de back-end, usando um dicionário confiável. O Visual Studio remove o aplicativo por padrão quando você interrompe o depurador. A remoção do aplicativo faz com que os dados no serviço de back-end também sejam removidos. Para persistir os dados entre as sessões de depuração, altere o **Modo de Depuração de Aplicativo** como uma propriedade no projeto **Votação** do Visual Studio.

Para ver o que acontece no código, conclua as seguintes etapas:
1. Abra o arquivo **VotesController.cs** e defina um ponto de interrupção no método **Put** da API Web (linha 47). Pesquise o arquivo no Gerenciador de Soluções no Visual Studio.

2. Abra o arquivo **VoteDataController.cs** e defina um ponto de interrupção no método **Put** dessa API Web (linha 50).

3. Volte para o navegador e clique em uma opção de votação ou adicione uma nova opção de votação. Você chegou ao primeiro ponto de interrupção no controlador de API do front-end da Web.
    
    1. Esse é o local em que o JavaScript no navegador envia uma solicitação para o controlador da API Web no serviço de front-end.
    
    ![Adicionar Serviço de Front-end de Voto](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

    2. Primeiro, construímos a URL para o ReverseProxy para nosso serviço de back-end **(1)**.
    3. Então, podemos enviar a Solicitação PUT HTTP para o ReverseProxy **(2)**.
    4. Por fim, retornamos a resposta do serviço de back-end para o cliente **(3)**.

4. Pressione **F5** para continuar
    1. Agora você está no ponto de interrupção no serviço de back-end.
    
    ![Adicionar Serviço de Back-End de Voto](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

    2. Na primeira linha do método **(1)**, estamos usando o `StateManager` para obter ou adicionar um dicionário confiável chamado `counts`.
    3. Todas as interações com valores em um dicionário confiável exigem uma transação e, portanto, o uso da instrução **(2)** cria essa transação.
    4. Na transação, depois, atualizamos o valor da chave relevante para a opção de votação e confirmamos a operação **(3)**. Depois que o método de confirmação for retornado, os dados serão atualizados no dicionário e replicados em outros nós no cluster. Os dados agora estão armazenados com segurança no cluster e o serviço de back-end pode fazer failover para outros nós, ainda tendo os dados disponíveis.
5. Pressione **F5** para continuar

Para interromper a sessão de depuração, pressione **Shift + F5**.


## <a name="next-steps"></a>Próximas etapas
Nesta parte do tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um serviço de API Web do ASP.NET Core como um Reliable Services com estado
> * Criar um serviço de Aplicativo Web do ASP.NET Core como um serviço Web sem estado
> * Usar o proxy reverso para se comunicar com o serviço com estado

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Implantar o aplicativo no Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)