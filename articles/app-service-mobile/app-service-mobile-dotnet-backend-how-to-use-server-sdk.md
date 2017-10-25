---
title: "Como trabalhar com o SDK de servidor back-end do .NET para Aplicativos Móveis | Microsoft Docs"
description: "Aprenda como trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Serviço de Aplicativo do Azure."
keywords: "serviço de aplicativo, serviço de aplicativo do azure, aplicativo móvel, serviço móvel, escala, escalonável, implantação de aplicativo, implantação de aplicativo do azure"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 1728e1d76f075eae8f5500afa34674785f8e3848
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Este tópico mostra como usar o SDK do servidor de back-end do .NET nos principais cenários dos Aplicativos Móveis do Serviço de Aplicativo do Azure. Os Aplicativos Móveis SDK do Azure ajuda você a trabalhar com clientes móveis de seu aplicativo ASP.NET.

> [!TIP]
> O [SDK do .NET Server para Aplicativos Móveis do Azure][2] tem código aberto no GitHub. O repositório contém todo o código-fonte, incluindo o conjunto de testes de unidade do SDK para todo o servidor e alguns projetos de exemplo.
>
>

## <a name="reference-documentation"></a>Documentação de referência
A documentação de referência para o SDK do servidor está localizada aqui: [Referência do .NET dos Aplicativos Móveis do Azure][1].

## <a name="create-app"></a>Como criar um back-end do aplicativo móvel do .NET
Se você estiver começando um novo projeto, será possível criar um aplicativo do Serviço de Aplicativo usando o [Portal do Azure] ou o Visual Studio. Você pode executar o aplicativo do Serviço de Aplicativo localmente ou publicar o projeto em seu aplicativo móvel do Serviço de Aplicativo baseado em nuvem.

Se você estiver adicionando recursos móveis a um projeto existente, veja a seção [Baixar e inicializar o SDK](#install-sdk) .

### <a name="create-a-net-backend-using-the-azure-portal"></a>Criar um back-end do .NET usando o Portal do Azure
Para criar um back-end móvel do Serviço de Aplicativo, siga o [Tutorial de início rápido][3] ou execute estas etapas:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

De volta à folha *Introdução*, em **Criar uma API de tabela**, escolha **C#** como a **Linguagem de back-end**. Clique em **Download**, extraia os arquivos compactados do projeto em seu computador local e abra a solução no Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2013-and-visual-studio-2015"></a>Criar um back-end do .NET usando o Visual Studio 2013 e o Visual Studio 2015
Instale o [SDK do Azure para .NET][4] (versão 2.9.0 ou posterior) para criar um projeto dos Aplicativos Móveis no Visual Studio. Depois de instalar o SDK, crie um aplicativo ASP.NET usando as seguintes etapas:

1. Abra a caixa de diálogo **Novo Projeto** (de *Arquivo* > **Novo** > **Projeto...**).
2. Expanda **Modelos** > **Visual C#** e selecione **Web**.
3. Selecione **Aplicativo Web do ASP.NET**.
4. Preencha o nome do projeto. Em seguida, clique em **OK**.
5. Em *Modelos do ASP.NET 4.5.2*, selecione **Aplicativo Móvel do Azure**. Marque **Host na nuvem** para criar um novo back-end móvel na nuvem no qual você possa publicar esse projeto.
6. Clique em **OK**.

## <a name="install-sdk"></a>Como baixar e inicializar o SDK
O SDK está disponível em [NuGet.org]. Este pacote inclui a funcionalidade básica necessária para começar a usar o SDK. Para inicializar o SDK, você precisa executar ações no objeto **HttpConfiguration** .

### <a name="install-the-sdk"></a>Instalar o SDK
Para instalar o SDK, clique com o botão direito do mouse no projeto do servidor no Visual Studio, selecione **Gerenciar Pacotes NuGet**, procure o pacote [Microsoft.Azure.Mobile.Server] e clique em **Instalar**.

### <a name="server-project-setup"></a> Inicializar o projeto de servidor
Um projeto do servidor back-end .NET é inicializado de modo semelhante a outros projetos do ASP.NET, pela inclusão de uma classe de inicialização do OWIN. Não se esqueça de referenciar o pacote NuGet `Microsoft.Owin.Host.SystemWeb`. Para adicionar essa classe no Visual Studio, clique com o botão direito do mouse em seu projeto do servidor e escolha **Adicionar** >
**Novo Item**, **Web** > **Geral** > **Classe de inicialização OWIN**.  Uma classe é gerada com o seguinte atributo:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

No método `Configuration()` da classe de inicialização OWIN, use um objeto **HttpConfiguration** para configurar o ambiente de Aplicativos Móveis do Azure.
O exemplo a seguir inicializa o projeto do servidor, sem nenhum recurso adicional:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Para habilitar recursos individuais, você deve chamar os métodos de extensão no objeto **MobileAppConfiguration** antes de chamar **ApplyTo**. Por exemplo, o código a seguir adiciona as rotas padrão para todos os controladores de API que têm o atributo `[MobileAppController]` durante a inicialização:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

O início rápido do servidor do portal do Azure chama **UseDefaultConfiguration()**. Isso equivale à configuração a seguir:

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

Os métodos de extensão usados são:

* `AddMobileAppHomeController()` fornece a home page padrão dos Aplicativos Móveis do Azure.
* `MapApiControllers()` fornece recursos personalizados de API para controladores de API Web decorados com o atributo `[MobileAppController]`.
* `AddTables()` fornece um mapeamento dos pontos de extremidade `/tables` para controladores de tabela.
* `AddTablesWithEntityFramework()` é uma abreviação para mapeamento de pontos de extremidade `/tables` usando os controladores baseados no Entity Framework.
* `AddPushNotifications()` fornece um método simples de registro de dispositivos para Hubs de Notificação.
* `MapLegacyCrossDomainController()` fornece os cabeçalhos de CORS padrão para o desenvolvimento local.

### <a name="sdk-extensions"></a>Extensões SDK
Os seguintes pacotes com base em extensão no NuGet fornecem vários recursos móveis que podem ser usados pelo seu aplicativo. Habilitar extensões durante a inicialização usando o objeto **MobileAppConfiguration** .

* [Microsoft.Azure.Mobile.Server.Quickstart] Dá suporte à configuração básica dos Aplicativos Móveis. Adicionado à configuração chamando o método de extensão **UseDefaultConfiguration** durante a inicialização. Essa extensão inclui as seguintes extensões: Notificações, Autenticação, Entidade, Tabelas, pacotes Crossdomain e Home. Esse pacote é usado pelo Início rápido dos Aplicativos Móveis disponíveis no portal do Azure.
* [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementa a página padrão *este aplicativo móvel está em execução* na raiz do site. Adicione à configuração chamando o método de extensão **AddMobileAppHomeController** .
* [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) inclui classes para trabalhar com dados e define o pipeline de dados. Adicione à configuração chamando o método de extensão **AddTables** .
* [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) permite que o Entity Framework acesse dados no Banco de Dados SQL. Adicionar à configuração chamando o método de extensão **AddTablesWithEntityFramework** .
* [Microsoft.Azure.Mobile.Server.Authentication] Permite a autenticação e define o middleware OWIN usado para validar tokens. Adicionar à configuração chamando os métodos de extensão **AddAppServiceAuthentication** e **IAppBuilder**.**UseAppServiceAuthentication**.
* [Microsoft.Azure.Mobile.Server.Notifications] permite as notificações por push e define um ponto de extremidade de registro push. Adicionar à configuração chamando o método de extensão **AddPushNotifications** .
* [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) Cria um controlador que fornece dados para os navegadores da Web herdados do seu Aplicativo Móvel. Adicione à configuração chamando o método de extensão **MapLegacyCrossDomainController** .
* [Microsoft.Azure.Mobile.Server.Login] fornece o método AppServiceLoginHandler.CreateToken(), que é um método estático usado nos cenários de autenticação personalizada.

## <a name="publish-server-project"></a>Como publicar o projeto do servidor
Essa seção mostra como publicar seu projeto de back-end do .NET a partir do Visual Studio. Você também pode implantar seu projeto de back-end usando o [Git](../app-service/app-service-deploy-local-git.md) ou qualquer um dos outros métodos disponíveis.

1. No Visual Studio, recompile o projeto para restaurar os pacotes do NuGet.
2. No Gerenciador de Soluções, clique com o botão direito no projeto e clique em **Publicar**. Na primeira vez que você publicar, precisará definir um perfil de publicação. Quando já tiver um perfil definido, selecione-o e clique em **Publicar**.
3. Se solicitado a selecionar um destino de publicação, clique em **Serviço de Aplicativo do Microsoft Azure** > **Avançar** e (se necessário) entre com suas credenciais do Azure.
   O Visual Studio baixa e armazena suas configurações de publicação com segurança diretamente do Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Escolha sua **Assinatura**, selecione o **Tipo de Recurso** em **Exibição**, expanda **Aplicativo Móvel**, clique no back-end do Aplicativo Móvel e então clique em **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Verifique as informações de perfil de publicação e clique em **Publicar**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Quando o back-end do aplicativo móvel for publicado com êxito, você verá uma página de aterrissagem indicando êxito.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a> Como definir um controlador de tabela
Defina um Controlador de Tabela para expor uma tabela SQL a clientes móveis.  A configuração de um Controlador de Tabela exige três etapas:

1. Criar uma classe DTO (Objeto de Transferência de Dados).
2. Configurar uma referência de tabela na classe DbContext móvel.
3. Criar um controlador de tabela.

Um DTO (objeto de transferência de dados) é um objeto C# simples que herda de `EntityData`.  Por exemplo:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

O DTO é usado para definir a tabela no Banco de Dados SQL.  Para criar a entrada de banco de dados, adicione uma propriedade `DbSet<>` ao DbContext que você está usando.  No modelo de projeto padrão para Aplicativos Móveis do Azure, o DbContext é chamado de `Models\MobileServiceContext.cs`:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Se você tiver o SDK do Azure instalado, poderá criar um controlador de tabela de modelo da seguinte maneira:

1. Clique com o botão direito do mouse na pasta Controladores e selecione **Adicionar** > **Controlador...**.
2. Selecione a opção **Controlador de Tabela de Aplicativos Móveis do Azure** e clique em **Adicionar**.
3. No diálogo **Adicionar controlador** :
   * Na lista suspensa **Classe modelo** , selecione o novo DTO.
   * Na lista suspensa **DbContext** , selecione a classe DbContext do serviço móvel.
   * O nome do controlador é criado para você.
4. Clique em **Adicionar**.

O projeto de início rápido do servidor contém um exemplo de um simples **TodoItemController**.

### <a name="adjust-pagesize"></a>Como ajustar o tamanho de paginação da tabela
Por padrão, os Aplicativos Móveis do Azure retornam 50 registros por solicitação.  A paginação garante que o cliente não associará o thread de interface do usuário nem o servidor por muito tempo, garantindo uma boa experiência do usuário. Para alterar o tamanho da tabela de paginação, aumente o "tamanho de consulta permitido" e o tamanho de página do lado do cliente. O “tamanho de consulta permitido" no lado do servidor é ajustado usando o atributo `EnableQuery`:

    [EnableQuery(PageSize = 500)]

Verifique se PageSize é igual ou maior do que o tamanho solicitado pelo cliente.  Consulte a documentação de instruções específicas do cliente para obter detalhes sobre a alteração do tamanho de página do cliente.

## <a name="how-to-define-a-custom-api-controller"></a>Como definir um controlador da API personalizada
O controlador da API personalizada fornece as funções mais básicas de back-end do Aplicativo Móvel, expondo um ponto de extremidade. Você pode registrar um controlador da API específico do dispositivo móvel usando o atributo [MobileAppController]. O atributo `MobileAppController` registra a rota, configura o serializador JSON dos Aplicativos Móveis e ativa a [verificação de versão de cliente](app-service-mobile-client-and-server-versioning.md).

1. No Visual Studio, clique com o botão direito do mouse na pasta Controladores, clique em **Adicionar** > **Controlador**, selecione **Controlador da API Web 2&mdash;Vazio** e clique em **Adicionar**.
2. Forneça um **Nome do controlador**, como `CustomController`, e clique em **Adicionar**.
3. No novo arquivo classe de controlador, adicione a seguinte instrução de uso.

        using Microsoft.Azure.Mobile.Server.Config;
4. Aplique o atributo **[MobileAppController]** à definição de classe do controlador de API, como no exemplo a seguir:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. No arquivo App_Start/Startup.MobileApp.cs, adicione uma chamada ao método de extensão **MapApiControllers**, como no seguinte exemplo:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Você também pode usar o método de extensão `UseDefaultConfiguration()` em vez de `MapApiControllers()`. Qualquer controlador que não tenha o **MobileAppControllerAttribute** aplicado ainda poderá ser acessado pelos clientes, mas não será consumido corretamente por clientes que usem qualquer SDK do cliente do Aplicativo Móvel.

## <a name="how-to-work-with-authentication"></a>Como trabalhar com autenticação
Os Aplicativos Móveis do Azure usam autenticação/autorização do Serviço de Aplicativo para proteger seu back-end móvel.  Esta seção mostra como executar as seguintes tarefas relacionadas à autenticação em seu projeto de servidor de back-end do .NET:

* [Como: Adicionar autenticação a um projeto do servidor](#add-auth)
* [Como usar a autenticação personalizada para o seu aplicativo](#custom-auth)
* [Como recuperar informações do usuário autenticado](#user-info)
* [Como restringir o acesso a dados para usuários autorizados](#authorize)

### <a name="add-auth"></a>Como: Adicionar autenticação a um projeto do servidor
Você pode adicionar autenticação ao seu projeto de servidor estendendo o objeto **MobileAppConfiguration** e configurando o middleware OWIN. Quando você instala o pacote [Microsoft.Azure.Mobile.Server.Quickstart] e chamar o método de extensão **UseDefaultConfiguration** , você pode pular a etapa 3.

1. No Visual Studio, instale o pacote [Microsoft.Azure.Mobile.Server.Authentication] .
2. No arquivo de projeto Startup.cs, adicione a seguinte linha de código ao início do método **Configuração** :

        app.UseAppServiceAuthentication(config);

    Este componente de middleware OWIN valida os tokens emitidos pelo gateway do Serviço de Aplicativo associado.
3. Adicione o atributo `[Authorize]` a qualquer controlador ou método que exija autenticação.

Para saber como autenticar clientes no back-end dos Aplicativos Móveis, veja [Adicionar autenticação ao seu aplicativo](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Como usar a autenticação personalizada para o seu aplicativo
Caso não queria usar um dos provedores de Autenticação/Autorização do Serviço de Aplicativo, você pode implementar seu próprio sistema de logon. Instale o pacote [Microsoft.Azure.Mobile.Server.Login] para ajudá-lo na geração de token de autenticação.  Forneça seu próprio código para validar as credenciais do usuário. Por exemplo, você pode comparar com senhas com sal e hash aplicados em um banco de dados. No exemplo abaixo, o método `isValidAssertion()` (definido em outro lugar) é responsável por essas verificações.

A autenticação personalizada é exposta criando um ApiController e expondo as ações `register` e `login`. O cliente deve usar uma interface do usuário personalizada para coletar as informações do usuário.  As informações são enviadas para a API com uma chamada HTTP POST padrão. Depois que o servidor valida a asserção, um token é emitido usando o método `AppServiceLoginHandler.CreateToken()` .  O ApiController **não deve** usar o atributo `[MobileAppController]`.

Uma ação `login` de exemplo:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

No exemplo anterior, LoginResult e LoginResultUser são objetos serializáveis que expõem as propriedades necessárias. O cliente espera que as respostas de logon sejam retornadas como objetos JSON na forma:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

O método `AppServiceLoginHandler.CreateToken()` inclui um parâmetro *audience* e um parâmetro *issuer*. Ambos os parâmetros são definidos para a URL da raiz do seu aplicativo usando o esquema HTTPS. Da mesma forma, você deve definir *secretKey* como o valor da chave de conexão do seu aplicativo. Não distribua a chave de assinatura em um cliente, já que ela pode ser usada para forjar chaves e se fazer passar por usuários. Você poderá obter a chave de assinatura enquanto estiver hospedado no Serviço de Aplicativo consultando a variável de ambiente *WEBSITE\_AUTH\_SIGNING\_KEY*. Se for necessário em um contexto de depuração local, siga as instruções na seção [Depuração local com autenticação](#local-debug) para recuperar a chave e armazená-la como uma configuração de aplicativo.

O token emitido também pode incluir outras declarações e uma data de expiração.  No mínimo, o token emitido deve incluir uma declaração (**sub**) de assunto.

Você pode dar suporte ao método `loginAsync()` de cliente padrão sobrecarregando a rota de autenticação.  Se o cliente chama `client.loginAsync('custom');` para fazer logon, a rota deve ser `/.auth/login/custom`.  Você pode definir a rota para o controlador de autenticação personalizada usando `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Usar a abordagem `loginAsync()` garante que o token de autenticação está conectado a todas as chamadas subsequentes ao serviço.
>
>

### <a name="user-info"></a>Como recuperar informações do usuário autenticado
Quando um usuário é autenticado pelo serviço de aplicativo, você pode acessar a ID de usuário atribuída e outras informações no seu código de back-end do .NET. As informações do usuário podem ser usadas para tomar decisões de autorização no back-end. O código abaixo obtém a ID do usuário associada a uma solicitação:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

A SID é derivado da ID de usuário específica do provedor e é estática para um determinado usuário e o provedor de logon.  O SID é nulo para tokens de autenticação inválidos.

O Serviço de Aplicativo também permite solicitar declarações específicas do provedor de logon. Cada provedor de identidade pode fornecer mais informações usando o SDK do provedor de identidade.  Por exemplo, você pode usar a API do Graph do Facebook para obter informações de amigos.  Você pode especificar as declarações solicitadas na folha do provedor no portal do Azure. Algumas declarações exigem configuração adicional com o provedor de identidade.

O código a seguir chama o método de extensão **GetAppServiceIdentityAsync** para obter as credenciais de logon, que incluem o token de acesso necessário para fazer solicitações na Graph API do Facebook:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Adicione uma instrução using `System.Security.Principal` para fornecer o método de extensão **GetAppServiceIdentityAsync** .

### <a name="authorize"></a>Como restringir o acesso a dados para usuários autorizados
Na seção anterior, mostramos como recuperar a ID de usuário de um usuário autenticado. Você pode restringir o acesso a dados e outros recursos com base nesse valor. Por exemplo, adicionar uma coluna userId às tabelas e filtrar os resultados da consulta segundo a ID de usuário é uma maneira simples de limitar os dados retornados apenas aos usuários autorizados. O código a seguir retorna linhas de dados somente quando o SID corresponde ao valor na coluna UserId na tabela TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

O método `Query()` retorna um `IQueryable` que pode ser manipulado pelo LINQ para tratar da filtragem.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Adicionar notificações por push para um projeto do servidor
Adicione notificações por push ao seu projeto do servidor estendendo o objeto **MobileAppConfiguration** e criando um cliente dos Hubs de Notificação.

1. No Visual Studio, clique com o botão direito do mouse no projeto do servidor e clique em **Gerenciar pacotes NuGet**, pesquise por `Microsoft.Azure.Mobile.Server.Notifications` e clique em **Instalar**.
2. Repita essa etapa para instalar o pacote `Microsoft.Azure.NotificationHubs` , que inclui a biblioteca do cliente dos Hubs de Notificação.
3. Em App_Start/Startup.MobileApp.cs, adicione uma chamada ao método de extensão **AddPushNotifications** durante a inicialização:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Adicione o código a seguir que cria um cliente de Hubs de Notificação:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Você agora pode usar o cliente de Hubs de Notificação para enviar notificações por push para dispositivos registrados. Para obter mais informações, veja [Adicionar notificações por push ao seu aplicativo](app-service-mobile-ios-get-started-push.md). Para saber mais sobre os Hubs de Notificação, confira [Visão geral dos Hubs de Notificação](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Como habilitar envios direcionados por push usando marcações
Os Hubs de Notificação permitem que você envie notificações direcionadas para registros específicos usando marcas. Várias marcações são criadas automaticamente:

* A ID de instalação identifica um dispositivo específico.
* A ID de usuário baseada no SID autenticado identifica um usuário específico.

A ID de instalação pode ser acessada na propriedade **installationId** do **MobileServiceClient**.  O exemplo a seguir mostra como usar uma ID de instalação para adicionar uma marca a uma instalação específica nos Hubs de Notificação:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

As marcações fornecidas pelo cliente durante o registro de notificação por push são ignoradas pelo back-end ao criar a instalação. Para habilitar um cliente a adicionar marcas à instalação, você deverá criar uma API personalizada que adiciona marcas usando o padrão anterior.

Confira [Marcações de notificação por push adicionadas pelo cliente][5] no exemplo de início rápido concluído dos Aplicativos Móveis do Serviço de Aplicativo para obter um exemplo.

## <a name="push-user"></a>Como enviar notificações por push para um usuário autenticado
Quando um usuário autenticado se registra para notificações por push, uma marca de ID de usuário é adicionada automaticamente ao registro. Usando essa marca, você pode enviar notificações por push para todos os dispositivos registrados por aquela pessoa. O código abaixo obtém o SID do usuário que fez a solicitação e envia um modelo de notificação por push para cada registro de dispositivo daquela pessoa:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Ao se registrar para notificações por push de um cliente autenticado, verifique se a autenticação foi concluída antes de tentar o registro. Para saber mais, confira [Enviar por push para usuários][6] no exemplo de início rápido dos Aplicativos Móveis do Serviço de Aplicativo para back-end do .NET.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Como depurar e solucionar problemas do SDK do .NET Server
O Serviço de Aplicativo do Azure fornece várias técnicas de depuração e de solução de problemas para aplicativos ASP.NET.

* [Monitorando um Serviço de Aplicativo do Azure](../app-service/web-sites-monitor.md)
* [Habilitar o registro em log de diagnósticos no Serviço de Aplicativo do Azure](../app-service/web-sites-enable-diagnostic-log.md)
* [Solucionar problemas de um Serviço de Aplicativo do Azure no Visual Studio](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Registro em log
É possível gravar em logs de diagnóstico do Serviço de Aplicativo usando a gravação de rastreamento padrão do ASP.NET. Antes de gravar os logs, habilite o diagnóstico no back-end do aplicativo móvel.

Para habilitar o diagnóstico e gravar logs:

1. Siga as etapas em [Como habilitar o diagnóstico](../app-service/web-sites-enable-diagnostic-log.md#enablediag).
2. Adicione a seguinte instrução de uso em seu arquivo de código:

        using System.Web.Http.Tracing;
3. Crie um gravador de rastreamento para gravar de back-end do .NET para os logs de diagnóstico da seguinte maneira:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Republique seu projeto de servidor e acesse o back-end do aplicativo móvel para executar o caminho de código com o registro em log.
5. Baixe e avalie os logs, conforme descrito em [Como baixar logs](../app-service/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Depuração local com autenticação
Você pode executar seu aplicativo localmente a fim de testar as alterações antes de publicá-lo na nuvem. Para a maioria dos back-ends dos Aplicativos Móveis do Azure, pressione *F5* enquanto estiver no Visual Studio. No entanto, há algumas considerações adicionais ao usar a autenticação.

Você deve ter um aplicativo móvel baseado em nuvem com a Autenticação/Autorização do Serviço de Aplicativo configurado, e o cliente deve ter o ponto de extremidade de nuvem especificado como o host de logon alternativo. Confira a documentação da sua plataforma cliente para saber as etapas específicas necessárias.

Verifique se seu back-end móvel tem o [Microsoft.Azure.Mobile.Server.Authentication] instalado. Em seguida, na classe de inicialização OWIN do aplicativo, adicione o seguinte, após aplicar `MobileAppConfiguration` ao seu `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

No exemplo anterior, você deve definir as configurações de aplicativo *authAudience* e *authIssuer* no arquivo Web.config para que cada uma seja a URL da raiz do aplicativo usando o esquema HTTPS. Da mesma forma, você deve definir *authSigningKey* como o valor da chave de autenticação de seu aplicativo.
Para obter a chave de assinatura:

1. Navegue até o aplicativo no [Portal do Azure]
2. Clique em **Ferramentas**, **Kudu**, **Ir**.
3. No site de gerenciamento do Kudu, clique em **Ambiente**.
4. Localize o valor para *WEBSITE\_AUTH\_SIGNING\_KEY*.

Use a chave de assinatura para o parâmetro *authSigningKey* em sua configuração de aplicativo local.  O back-end móvel agora está equipado para validar tokens quando em execução localmente, cujo token o cliente obtém do ponto de extremidade baseados na nuvem.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Portal do Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
