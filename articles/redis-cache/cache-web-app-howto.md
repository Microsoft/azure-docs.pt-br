---
title: Como criar um Aplicativo Web com o Cache Redis | Microsoft Docs
description: Saiba como criar um aplicativo Web com o Cache Redis
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: hero-article
ms.date: 05/09/2017
ms.author: sdanie
ms.openlocfilehash: 21dc87b3e8c26bfbda36202b31b3b4d44be32179
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-web-app-with-redis-cache"></a>Como criar um aplicativo Web com o Cache Redis
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Este tutorial mostra como criar e implantar um aplicativo Web ASP.NET em um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio 2017. O aplicativo de exemplo exibe uma lista de estatísticas da equipe de um banco de dados e mostra diferentes maneiras de usar o Cache Redis do Azure para armazenar e recuperar dados do cache. Ao concluir o tutorial, você terá um aplicativo Web em execução que lê e grava em um banco de dados, otimizado com o Cache Redis do Azure e hospedado no Azure.

O que você aprenderá:

* Como criar um aplicativo MVC 5 ASP.NET no Visual Studio.
* Como acessar dados de um banco de dados usando o Entity Framework.
* Como melhorar a taxa de transferência de dados e reduzir a carga do banco de dados armazenando e recuperando dados com o Cache Redis do Azure.
* Como usar um conjunto classificado do Redis para recuperar as cinco equipes principais.
* Como provisionar os recursos do Azure para o aplicativo usando um modelo do Resource Manager.
* Como publicar o aplicativo no Azure usando o Visual Studio.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir o tutorial, você deve ter os pré-requisitos a seguir.

* [Conta do Azure](#azure-account)
* [Visual Studio 2017 com o SDK do Azure para .NET](#visual-studio-2017-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Conta do Azure
Você precisa de uma conta do Azure para concluir este tutorial. Você pode:

* [Abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Obtenha créditos que possam ser usados para experimentar os serviços pagos do Azure. Mesmo depois que os créditos são usados, você pode manter a conta e usar os serviços e recursos gratuitos do Azure.
* [Ativar benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Todos os meses, sua assinatura do MSDN lhe oferece créditos que podem ser usados para serviços pagos do Azure.

### <a name="visual-studio-2017-with-the-azure-sdk-for-net"></a>Visual Studio 2017 com o SDK do Azure para .NET
O tutorial é escrito para o Visual Studio 2017 com o [SDK do Azure para .NET](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#azuretools). O SDK do Azure 2.9.5 está incluído com o instalador do Visual Studio.

Se você tiver o Visual Studio 2015, siga o tutorial com o [SDK do Azure para .NET](../dotnet-sdk.md) 2.8.2 ou posterior. [Baixe o SDK mais recente do Azure para Visual Studio 2015 aqui](http://go.microsoft.com/fwlink/?linkid=518003). O Visual Studio será instalado automaticamente com o SDK caso você ainda não o tenha. Algumas telas podem parecer diferentes das ilustrações mostradas neste tutorial.

Se tiver o Visual Studio 2013, você poderá [baixar o SDK do Azure mais recente para o Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Algumas telas podem parecer diferentes das ilustrações mostradas neste tutorial.

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio
1. Abra o Visual Studio e clique em **Arquivo**, **Novo**, **Projeto**.
2. Expanda o nó **Visual C#** na lista **Modelos**, selecione **Nuvem** e clique em **Aplicativo Web ASP.NET**. Verifique se **.NET Framework 4.5.2** ou superior está selecionado.  Digite **ContosoTeamStats** na caixa de texto **Nome** e clique em **OK**.
   
    ![Criar projeto][cache-create-project]
3. Selecione **MVC** como o tipo de projeto. 

    Certifique-se de que a opção **Sem Autenticação** esteja especificada para as configurações **Autenticação**. Dependendo de sua versão do Visual Studio, o padrão pode ser definido para algo diferente. Para alterá-lo, clique em **Alterar Autenticação**e selecione **Sem Autenticação**.

    Se você estiver acompanhando com o Visual Studio 2015, desmarque a caixa de seleção **Host na nuvem**. Você vai [provisionar os recursos do Azure](#provision-the-azure-resources) e [publicar o aplicativo no Azure](#publish-the-application-to-azure) nas etapas subsequentes do tutorial. Para obter um exemplo de provisionamento de um aplicativo Web do Serviço de Aplicativo do Visual Studio deixando a opção **Host na nuvem** marcada, confira [Introdução aos aplicativos Web no Serviço de Aplicativo do Azure, usando ASP.NET e Visual Studio](../app-service/app-service-web-get-started-dotnet.md).
   
    ![Selecionar modelo de projeto][cache-select-template]
4. Clique em **OK** para criar o projeto.

## <a name="create-the-aspnet-mvc-application"></a>Criar o aplicativo MVC ASP.NET
Nesta seção do tutorial, você criará o aplicativo básico que lê e exibe estatísticas de equipe de um banco de dados.

* [Adicionar o pacote NuGet do Entity Framework](#add-the-entity-framework-nuget-package)
* [Adicionar o modelo](#add-the-model)
* [Adicionar controlador](#add-the-controller)
* [Configurar os modos de exibição](#configure-the-views)

### <a name="add-the-entity-framework-nuget-package"></a>Adicionar o pacote NuGet do Entity Framework

1. Clique em **Gerenciador de Pacotes Nuget**, **Console do Gerenciador de Pacotes** no menu **Ferramentas**.
2. Execute o seguinte comando na janela **Console do Gerenciador de Pacotes**.
    
    ```
    Install-Package EntityFramework
    ```

Para saber mais sobre este pacote, consulte a página do NuGet [EntityFramework](https://www.nuget.org/packages/EntityFramework/).

### <a name="add-the-model"></a>Adicionar o modelo
1. Clique com o botão direito do mouse em **Modelos** no **Gerenciador de Soluções** e escolha **Adicionar**, **Classe**. 
   
    ![Adicionar modelo][cache-model-add-class]
2. Insira `Team` como o nome da classe e clique em **Adicionar**.
   
    ![Adicionar classe de modelo][cache-model-add-class-dialog]
3. Substitua as instruções `using` na parte superior do arquivo `Team.cs` pelas instruções `using` a seguir.

    ```c#
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```


1. Substitua a definição da classe `Team` pelo trecho de código a seguir, que contém uma definição de classe `Team` atualizada, bem como algumas outras classes auxiliares do Entity Framework. Para saber mais sobre a abordagem de code first do Entity Framework que é usada neste tutorial, confira [Code first para um novo banco de dados](https://msdn.microsoft.com/data/jj193542).

    ```c#
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }
    
        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();
    
            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }
    
    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }
    
        public DbSet<Team> Teams { get; set; }
    }
    
    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };
    
            Team.PlayGames(teams);
    
            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }
    
    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```


1. No **Gerenciador de Soluções**, clique duas vezes em **web.config** para abri-lo.
   
    ![Web.config][cache-web-config]
2. Adicione a seção `connectionStrings` a seguir. O nome da cadeia de conexão deve corresponder ao nome da classe de contexto de banco de dados do Entity Framework, que é `TeamContext`.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Adicione a nova seção `connectionStrings` para que ela siga `configSections`, conforme mostrado no exemplo a seguir.

    ```xml
    <configuration>
      <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
      </configSections>
      <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
      </connectionStrings>
      ...
      ```

    > [!NOTE]
    > A cadeia de conexão pode ser diferente dependendo da versão do Visual Studio e da edição do SQL Server Express usadas para concluir o tutorial. O modelo web.config deve ser configurado para corresponder à sua instalação e pode conter `Data Source` entradas como `(LocalDB)\v11.0` (a partir do SQL Server Express 2012) ou `Data Source=(LocalDB)\MSSQLLocalDB` (do SQL Server 2014 Express e mais recente). Para saber mais sobre cadeias de caracteres de conexão e versões do SQL Express, veja [LocalDB do SQL Server 2016 Express](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-2016-express-localdb).

### <a name="add-the-controller"></a>Adicionar controlador
1. Pressione **F6** para compilar o projeto. 
2. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Controladores** e escolha **Adicionar**, **Controlador**.
   
    ![Adicionar controlador][cache-add-controller]
3. Escolha **Controlador MVC 5 com modos de exibição, usando o Entity Framework** e clique em **Adicionar**. Se você receber um erro após clicar em **Adicionar**, verifique se criou o projeto primeiro.
   
    ![Adicionar classe de controlador][cache-add-controller-class]
4. Selecione **Equipe (ContosoTeamStats.Models)** na lista suspensa **Classe de modelo**. Selecione **TeamContext (ContosoTeamStats.Models)** na lista suspensa **Classe de contexto de dados**. Digite `TeamsController` na caixa de texto de nome **Controlador** (se ela não for populada automaticamente). Clique em **Adicionar** para criar a classe de controlador e adicionar os modos de exibição padrão.
   
    ![Configurar o controlador][cache-configure-controller]
5. No **Gerenciador de Soluções**, expanda **Global.asax** e clique duas vezes em **Global.asax.cs** para abri-lo.
   
    ![Global.asax.cs][cache-global-asax]
6. Adicione as duas seguintes instruções `using` na parte superior do arquivo abaixo das outras instruções `using`.

    ```c#
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```


1. Adicione a linha de código a seguir ao fim do método `Application_Start` .

    ```c#
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```


1. No **Gerenciador de Soluções**, expanda `App_Start` e clique duas vezes em `RouteConfig.cs`.
   
    ![RouteConfig.cs][cache-RouteConfig-cs]
2. Substitua `controller = "Home"` no código a seguir no método `RegisterRoutes` por `controller = "Teams"`, conforme mostrado no exemplo a seguir.

    ```c#
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```


### <a name="configure-the-views"></a>Configurar os modos de exibição
1. No **Gerenciador de Soluções**, expanda a pasta **Exibições** e a pasta **Compartilhado** e clique duas vezes em **_Layout.cshtml**. 
   
    ![_Layout.cshtml.][cache-layout-cshtml]
2. Altere o conteúdo do elemento `title` e substitua `My ASP.NET Application` por `Contoso Team Stats`, conforme é mostrado no exemplo a seguir.

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```


1. Na seção `body`, atualize a primeira instrução `Html.ActionLink`, substitua `Application name` por `Contoso Team Stats` e substitua `Home` por `Teams`.
   
   * Antes: `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
   * Após: `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
     
     ![Alterações de código][cache-layout-cshtml-code]
2. Pressione **Ctrl+F5** para compilar e executar o aplicativo. Essa versão do aplicativo lê os resultados diretamente do banco de dados. Observe as ações **Criar Novo**, **Editar**, **Detalhes** e **Excluir** que foram adicionadas automaticamente ao aplicativo pelo scaffold Controlador **MVC 5 com modos de exibição, usando o Entity Framework**. Na próxima seção do tutorial, você adicionará o Cache Redis para otimizar o acesso a dados e fornecer recursos adicionais ao aplicativo.

![Aplicativo de início][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Configurar o aplicativo para usar o Cache Redis
Nesta seção do tutorial, você configurará o aplicativo de exemplo para armazenar e recuperar estatísticas de equipe da Contoso de uma instância do Cache Redis do Azure usando o cliente de cache [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .

* [Configurar o aplicativo para usar StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [Atualizar a classe TeamsController para retornar resultados do cache ou do banco de dados](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
* [Atualizar os métodos Create, Edit e Delete para trabalhar com o cache](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
* [Atualizar o modo de exibição de Índice de Equipes para trabalhar com o cache](#update-the-teams-index-view-to-work-with-the-cache)

### <a name="configure-the-application-to-use-stackexchangeredis"></a>Configurar o aplicativo para usar StackExchange.Redis
1. Para configurar um aplicativo cliente no Visual Studio usando o pacote NuGet do StackExchange.Redis, clique em **Gerenciador de Pacotes NuGet**, **Console do Gerenciador de Pacotes** no menu **Ferramentas**.
2. Execute o comando a seguir na janela `Package Manager Console`.
    
    ```
    Install-Package StackExchange.Redis
    ```
   
    Os downloads de pacote NuGet acrescentam as referências de assembly necessárias para o seu aplicativo de cliente para acessar o cache Redis do Azure com o cliente de cache StackExchange.Redis. Se você preferir usar uma versão de nome forte da biblioteca de cliente `StackExchange.Redis`, instale o pacote `StackExchange.Redis.StrongName`.
3. No **Gerenciador de Soluções**, expanda a pasta **Controladores** e clique duas vezes em **TeamsController.cs** para abri-lo.
   
    ![Controlador de equipes][cache-teamscontroller]
4. Adicione as duas instruções `using` a seguir a **TeamsController.cs**.

    ```c#   
    using System.Configuration;
    using StackExchange.Redis;
    ```

5. Adicione as duas propriedades a seguir à classe `TeamsController` .

    ```c#   
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

6. Crie no computador um arquivo chamado `WebAppPlusCacheAppSecrets.config` e coloque-o em um local do qual não será feito check-in com o código-fonte do aplicativo de exemplo, se você decidir fazer check-in dele em algum lugar. Neste exemplo, o arquivo `AppSettingsSecrets.config` está localizado em `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.
   
    Edite o arquivo `WebAppPlusCacheAppSecrets.config` e adicione o conteúdo a seguir. Se você executar o aplicativo localmente, essas informações serão usadas para se conectar à instância do Cache Redis do Azure. Mais adiante no tutorial, você provisionará uma instância do Cache Redis do Azure e atualizará o nome e a senha do cache. Se você não planeja executar o aplicativo de exemplo localmente, pode ignorar a criação desse arquivo e as etapas subsequentes que fazem referência a ele, pois quando você implanta no Azure, o aplicativo recupera as informações de conexão de cache da configuração do aplicativo Web, não desse arquivo. Como o `WebAppPlusCacheAppSecrets.config` não é implantado no Azure com seu aplicativo, você não precisa dele, a menos que pretenda executar o aplicativo localmente.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. No **Gerenciador de Soluções**, clique duas vezes em **web.config** para abri-lo.
   
    ![Web.config][cache-web-config]
2. Adicione o atributo `file` a seguir ao elemento `appSettings`. Se você usou um nome de arquivo ou local diferente, substitua esses valores pelos mostrados no exemplo.
   
   * Antes: `<appSettings>`
   * Após: ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`
     
   O tempo de execução do ASP.NET mescla o conteúdo do arquivo externo com a marcação no elemento `<appSettings>` . O tempo de execução ignorará o atributo de arquivo se o arquivo especificado não puder ser encontrado. Seus segredos (a cadeia de conexão do cache) não são incluídos como parte do código-fonte do aplicativo. Quando você implantar o aplicativo Web no Azure, o arquivo `WebAppPlusCacheAppSecrests.config` não será implantado (isso é o que você deseja). Há várias maneiras de especificar os segredos no Azure e, neste tutorial, elas serão configuradas automaticamente quando você [provisionar os recursos do Azure](#provision-the-azure-resources) em uma etapa posterior do tutorial. Para saber mais sobre como trabalhar com segredos no Azure, confira [Práticas recomendadas para implantar senhas e outros dados confidenciais no ASP.NET e no Serviço de Aplicativo do Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>Atualizar a classe TeamsController para retornar resultados do cache ou do banco de dados
Neste exemplo, as estatísticas de equipe podem ser recuperadas do banco de dados ou do cache. As estatísticas de equipe são armazenadas no cache como um `List<Team>`serializado e também como um conjunto ordenado usando tipos de dados Redis. Ao recuperar itens de um conjunto ordenado, você pode recuperar alguns ou todos os itens ou consultar determinados itens. Neste exemplo, você consultará o conjunto ordenado para as cinco equipes principais classificadas pelo número de vitórias.

> [!NOTE]
> Não é necessário armazenar as estatísticas de equipe em vários formatos no cache para usar o Cache Redis do Azure. Este tutorial usa vários formatos para demonstrar algumas das diferentes maneiras e tipos de dados que você pode usar para armazenar dados em cache.
> 
> 

1. Adicione as instruções `using` a seguir ao arquivo `TeamsController.cs` na parte superior, com as outras instruções `using`.

    ```c#   
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

2. Substitua a implementação do método `public ActionResult Index()` atual pela implementação a seguir.

    ```c#
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```


1. Adicione os três métodos a seguir à classe `TeamsController` para implementar os tipos de ação `playGames`, `clearCache` e `rebuildDB` da instrução switch adicionada no trecho de código anterior.
   
    O método `PlayGames` atualiza as estatísticas de equipe simulando uma temporada de jogos, salva os resultados no banco de dados e limpa os dados agora obsoletos do cache.

    ```c#
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    O método `RebuildDB` reinicializa o banco de dados com o conjunto padrão de equipes, gera estatísticas para elas e limpa os dados agora obsoletos do cache.

    ```c#
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    O método `ClearCachedTeams` remove do cache todas as estatísticas de equipe armazenadas em cache.

    ```c#
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    } 
    ```


1. Adicione os quatro métodos a seguir à classe `TeamsController` para implementar as várias maneiras de recuperar as estatísticas de equipe do cache e do banco de dados. Cada um desses métodos retorna um `List<Team>` , que é exibido pelo modo de exibição.
   
    O método `GetFromDB` lê as estatísticas de equipe do banco de dados.
   
    ```c#
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t; 

        return results.ToList<Team>();
    }
    ```

    O método `GetFromList` lê as estatísticas de equipe do cache como um `List<Team>` serializado. Se houver um erro de cache, as estatísticas de equipe serão lidas do banco de dados e, em seguida, armazenadas em cache para a próxima vez. Neste exemplo, usamos a serialização de JSON.NET para serializar os objetos .NET para e do cache. Para saber mais, confira [Como trabalhar com objetos .NET no Cache Redis do Azure](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```c#
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    O método `GetFromSortedSet` lê as estatísticas de equipe de um conjunto ordenado armazenado em cache. Se houver um erro de cache, as estatísticas de equipe serão lidas do banco de dados e armazenadas no cache como um conjunto ordenado.

    ```c#
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    O método `GetFromSortedSetTop5` lê as cinco equipes principais do conjunto ordenado armazenado em cache. Ele começa verificando a existência da chave `teamsSortedSet` no cache. Se a chave não estiver presente, o método `GetFromSortedSet` será chamado para ler as estatísticas de equipe e armazená-las no cache. Em seguida, o conjunto ordenado armazenado em cache é consultado para fornecer as cinco equipes principais, que são retornadas.

    ```c#
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Atualizar os métodos Create, Edit e Delete para trabalhar com o cache
O código de scaffolding gerado como parte do exemplo inclui métodos para adicionar, editar e excluir equipes. Sempre que uma equipe é adicionada, editada ou removida, os dados no cache tornam-se desatualizados. Nesta seção, você modificará esses três métodos para limpar as equipes armazenadas em cache, para que o cache não fique fora de sincronia com o banco de dados.

1. Navegue até o método `Create(Team team)` na classe `TeamsController`. Adicione uma chamada ao método `ClearCachedTeams` , conforme mostrado no exemplo a seguir.

    ```c#
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```


1. Navegue até o método `Edit(Team team)` na classe `TeamsController`. Adicione uma chamada ao método `ClearCachedTeams` , conforme mostrado no exemplo a seguir.

    ```c#
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```


1. Navegue até o método `DeleteConfirmed(int id)` na classe `TeamsController`. Adicione uma chamada ao método `ClearCachedTeams` , conforme mostrado no exemplo a seguir.

    ```c#
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>Atualizar o modo de exibição de Índice de Equipes para trabalhar com o cache
1. No **Gerenciador de Soluções**, expanda a pasta **Exibições** e a pasta **Equipes** e clique duas vezes em **Index.cshtml**.
   
    ![Index.cshtml][cache-views-teams-index-cshtml]
2. Na parte superior do arquivo, procure o elemento de parágrafo a seguir.
   
    ![Tabela de ação][cache-teams-index-table]
   
    Esse é o link para criar uma nova equipe. Substitua o elemento de parágrafo pela tabela a seguir. A tabela contém links de ação para criar uma nova equipe, reproduzir uma nova temporada de jogos, limpar o cache, recuperar as equipes do cache em vários formatos, recuperar as equipes do banco de dados e recriar o banco de dados com dados de exemplo atualizados.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>    
    </table>
    ```


1. Role até a parte inferior do arquivo **index.cshtml** e adicione o elemento `tr` a seguir para que ele seja a última linha na última tabela do arquivo.
   
    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
   
    Essa linha exibe o valor de `ViewBag.Msg` que contém um relatório de status sobre a operação atual. O `ViewBag.Msg` é definido quando você clica em qualquer um dos links de ação da etapa anterior.   
   
    ![Mensagem de status][cache-status-message]
2. Pressione **F6** para compilar o projeto.

## <a name="provision-the-azure-resources"></a>provisionar os recursos do Azure
Para hospedar seu aplicativo no Azure, primeiro você deve provisionar os serviços do Azure que o aplicativo requer. O aplicativo de exemplo deste tutorial usa os serviços do Azure a seguir.

* Cache Redis do Azure
* Aplicativo Web do Serviço de Aplicativo
* Banco de dados SQL

Para implantar esses serviços em um grupo de recursos novo ou existente de sua escolha, clique no botão **Implantar no Azure** a seguir.

[![Implantar no Azure][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

O botão **Implantar no Azure** usa o modelo [Criar um aplicativo Web e o Cache Redis e o Banco de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) [Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates) para provisionar esses serviços e definir a cadeia de conexão para o Banco de Dados SQL e o aplicativo de configuração para a cadeia de conexão do Cache Redis do Azure.

> [!NOTE]
> Se não tiver uma conta do Azure, você poderá [criar uma conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) em apenas alguns minutos.
> 
> 

Se clicar no botão **Implantar no Azure** , você será levado para o portal do Azure, e será iniciado o processo de criação dos recursos descritos pelo modelo.

![Implantar no Azure][cache-deploy-to-azure-step-1]

1. Na folha **Fundamentos** , selecione a assinatura do Azure a ser usada, selecione um grupo de recursos existente ou crie um novo e especifique o local do grupo de recursos.
2. Na seção **Configurações**, especifique um **Logon de Administrador** (não use **administrador**), **Senha de Logon do Administrador** e o **Nome do Banco de Dados**. Os outros parâmetros são configurados para um plano de hospedagem do Serviço de Aplicativo gratuito e opções de custo mais baixo para o Banco de Dados SQL e o Cache Redis do Azure, que não vêm com uma camada gratuita.

    ![Implantar no Azure][cache-deploy-to-azure-step-2]

3. Após definir as configurações desejadas, role até o fim da página, leia os termos e condições e marque a caixa de seleção **Concordo com os termos e condições indicados acima**.
4. Para iniciar o provisionamento de recursos, clique em **Comprar**.

Para exibir o andamento da implantação, clique no ícone de notificação e clique em **Implantação iniciada**.

![Implantação iniciada][cache-deployment-started]

Você pode exibir o status da implantação na folha **Microsoft.Template** .

![Implantar no Azure][cache-deploy-to-azure-step-3]

Quando o provisionamento for concluído, você poderá publicar o aplicativo no Azure por meio do Visual Studio.

> [!NOTE]
> Os erros que ocorrerem durante o processo de provisionamento serão exibidos na folha **Microsoft.Template** . Alguns erros comuns são muitos Servidores SQL ou muitos planos de hospedagem do Serviço de Aplicativo gratuitos por assinatura. Resolva os erros e reinicie o processo clicando em **Reimplantar** na folha **Microsoft.Template** ou no botão **Implantar no Azure** neste tutorial.
> 
> 

## <a name="publish-the-application-to-azure"></a>Publicar o aplicativo no Azure
Nesta etapa do tutorial, você publicará o aplicativo no Azure e o executará na nuvem.

1. Clique com o botão direito do mouse no projeto **ContosoTeamStats** no Visual Studio e escolha **Publicar**.
   
    ![Publicar][cache-publish-app]
2. Clique em **Serviço de Aplicativo do Microsoft Azure**, escolha **Selecionar Existente** e clique em **Publicar**.
   
    ![Publicar][cache-publish-to-app-service]
3. Selecione a assinatura usada ao criar os recursos do Azure, expanda o grupo de recursos que contém os recursos, selecione o aplicativo Web desejado. Se você tiver usado o botão **Implantar no Azure**, o nome do aplicativo Web começará com **webSite**, seguido de alguns caracteres adicionais.
   
    ![Selecionar aplicativo Web][cache-select-web-app]
4. Clique em **OK** para iniciar o processo de publicação. Após alguns instantes, o processo de publicação é concluído e um navegador será iniciado com o aplicativo de exemplo em execução. Se você receber um erro DNS ao validar ou publicar, e o processo de provisionamento de recursos do Azure para o aplicativo tiver sido concluído recentemente, aguarde um momento e tente novamente.
   
    ![Cache adicionado][cache-added-to-application]

A tabela a seguir descreve cada link de ação do aplicativo de exemplo.

| Ação | Descrição |
| --- | --- |
| Criar Novo |Criar uma nova Equipe. |
| Reproduzir Temporada |Reproduzir uma temporada de jogos, atualizar as estatísticas de equipes e limpar dados de equipe desatualizados do cache. |
| Limpar Cache |Limpar as estatísticas de equipes do cache. |
| Listar do Cache |Recuperar as estatísticas de equipe do cache. Se houver um erro de cache, carregar as estatísticas do banco de dados e salvar no cache para a próxima vez. |
| Conjunto Ordenado do Cache |Recuperar as estatísticas de equipes do cache usando um conjunto ordenado. Se houver um erro de cache, carregar as estatísticas do banco de dados e salvar no cache usando um conjunto ordenado. |
| Cinco Principais Equipes do Cache |Recuperar as cinco principais equipes do cache usando um conjunto ordenado. Se houver um erro de cache, carregar as estatísticas do banco de dados e salvar no cache usando um conjunto ordenado. |
| Carregar do banco de dados |Recuperar as estatísticas de equipes do banco de dados. |
| Recriar o banco de dados |Recriar o banco de dados e recarregá-lo com dados de equipes de exemplo. |
| Editar/Detalhes/Excluir |Editar uma equipe, exibir detalhes de uma equipe, excluir uma equipe. |

Clique em algumas das ações e experimente recuperar os dados de diferentes fontes. Observe as diferenças no tempo necessário para concluir as várias maneiras de recuperar dados do banco de dados e do cache.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Exclua os recursos quando tiver terminado de usar o aplicativo
Ao terminar de usar o aplicativo do tutorial de exemplo, você poderá excluir os recursos do Azure usados para economizar custos e recursos. Se usar o botão **Implantar no Azure** na seção [Provisionar os recursos do Azure](#provision-the-azure-resources) e todos os recursos estiverem contidos no mesmo grupo de recursos, você poderá excluí-los juntos em uma única operação excluindo o grupo de recursos.

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.
2. Digite o nome do grupo de recursos na caixa de texto **Filtrar itens...** .
3. Clique em **...** à direita do grupo de recursos.
4. Clique em **Excluir**.
   
    ![Excluir][cache-delete-resource-group]
5. Digite o nome do grupo de recursos e clique em **Excluir**.
   
    ![Confirmar exclusão][cache-delete-confirm]

Após alguns instantes, o grupo de recursos e todos os seus recursos contidos serão excluídos.

> [!IMPORTANT]
> Observe que a exclusão de um grupo de recursos é irreversível e que o grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Se tiver criado os recursos para hospedar este exemplo dentro de um grupo de recursos existente, você poderá excluir cada recurso individualmente de suas respectivas folhas.
> 
> 

## <a name="run-the-sample-application-on-your-local-machine"></a>Executar o aplicativo de exemplo no computador local
Para executar o aplicativo localmente em seu computador, você precisa de uma instância do Cache Redis do Azure na qual armazenar os dados em cache. 

* Se tiver publicado o aplicativo no Azure conforme descrito na seção anterior, você poderá usar a instância do Cache Redis do Azure fornecida naquela etapa.
* Se tiver outra instância do Cache Redis do Azure existente, você poderá usá-la para executar esse exemplo localmente.
* Se precisar criar uma instância do Cache Redis do Azure, você poderá seguir as etapas em [Criar um cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Após selecionar ou criar o cache a ser usado, navegue até o cache no portal do Azure e recupere o [nome do host](cache-configure.md#properties) e as [chaves de acesso](cache-configure.md#access-keys) para o cache. Para obter instruções, confira [Definir configurações de cache Redis](cache-configure.md#configure-redis-cache-settings).

1. Abra o arquivo `WebAppPlusCacheAppSecrets.config` criado durante a etapa [Configurar o aplicativo para usar o Cache Redis](#configure-the-application-to-use-redis-cache) deste tutorial usando o editor de sua escolha.
2. Edite o atributo `value`, substitua `MyCache.redis.cache.windows.net` pelo [nome do host](cache-configure.md#properties) do cache e especifique a [chave primária ou secundária](cache-configure.md#access-keys) do cache como a senha.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. Pressione **CTRL+F5** para executar o aplicativo.

> [!NOTE]
> Observe que, como o aplicativo, incluindo o banco de dados, é executado localmente e o Cache Redis é hospedado no Azure, o cache poderá parecer ter desempenho inferior ao banco de dados. Para obter o melhor desempenho, o aplicativo cliente e a instância do Cache Redis do Azure devem estar no mesmo local. 
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Saiba mais na [Introdução ao MVC 5 ASP.NET](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) no site do [ASP.NET](http://asp.net/).
* Para obter mais exemplos de criação de um aplicativo Web ASP.NET no Serviço de Aplicativo, veja [Criar e implantar um aplicativo Web ASP.NET no Serviço de Aplicativo do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) na [demonstração](https://github.com/Microsoft/HealthClinic.biz) do [HealthClinic.biz](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) 2015 Connect.
  * Para ver mais guias de início rápido da demonstração do HealthClinic.biz, consulte [Azure Developer Tools Quickstarts (Guias de início rápido das ferramentas de desenvolvedor do Azure)](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
* Saiba mais sobre a abordagem [Code first para um novo banco de dados](https://msdn.microsoft.com/data/jj193542) para o Entity Framework que é usada neste tutorial.
* Saiba mais sobre [aplicativos Web no Serviço de Aplicativo do Azure](../app-service/app-service-web-overview.md).
* Saiba como [monitorar](cache-how-to-monitor.md) o cache no portal do Azure.
* Explore os recursos premium do Cache Redis do Azure
  
  * [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md)
  * [Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md)
  * [Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md)
  * Confira as [Perguntas frequentes sobre o Cache Redis do Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para saber mais sobre tamanho, taxa de transferência e largura de banda com caches premium.

<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

