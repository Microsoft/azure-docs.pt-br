---
title: 'Tutorial: Criar um aplicativo Web (cache-aside) – Cache do Azure para Redis'
description: Saiba como criar um Aplicativo Web com o Cache do Azure para Redis que usa o padrão cache-aside.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc
ms.date: 03/30/2018
ms.openlocfilehash: 90e60044e227ea1a18ea032d302b29abda1ea2e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536837"
---
# <a name="tutorial-create-a-cache-aside-leaderboard-on-aspnet"></a>Tutorial: Criar um placar de líderes cache-aside no ASP.NET

Neste tutorial, você atualizará o aplicativo Web ASP.NET *ContosoTeamStats*, criado no [início rápido do ASP.NET para o Cache do Azure para Redis](cache-web-app-howto.md), para incluir um placar de líderes que usa o [padrão cache-aside](/azure/architecture/patterns/cache-aside) com o Cache do Azure para Redis. O aplicativo de exemplo exibe uma lista de estatísticas da equipe de um banco de dados e demonstra diferentes maneiras de usar o Cache do Azure para Redis para armazenar e recuperar dados do cache para melhorar o desempenho. Ao concluir o tutorial, você terá um aplicativo Web em execução que lê e grava em um banco de dados, otimizado com o Cache do Azure para Redis e hospedado no Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Melhore a taxa de transferência de dados e reduza a carga do banco de dados armazenando e recuperando dados com o Cache do Azure para Redis.
> * Use um conjunto classificado do Redis para recuperar as cinco equipes principais.
> * Provisione os recursos do Azure para o aplicativo usando um modelo do Resource Manager.
> * Publique o aplicativo no Azure usando o Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, você deve ter os pré-requisitos a seguir:

* Este tutorial continua na etapa em que você parou no [Início rápido do ASP.NET para o Cache do Azure para Redis](cache-web-app-howto.md). Se você ainda não fez isso, siga primeiro o início rápido.
* Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
    * Desenvolvimento Web e ASP.NET
    * Desenvolvimento do Azure
    * Desenvolvimento de área de trabalho do .NET com o SQL Server Express LocalDB ou [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express).

## <a name="add-a-leaderboard-to-the-project"></a>Adicionar um placar de líderes ao projeto

Nesta seção do tutorial, você configura o projeto *ContosoTeamStats* com um placar de líderes que relata as estatísticas de vitórias, perdas e empates para uma lista de equipes fictícias.

### <a name="add-the-entity-framework-to-the-project"></a>Adicionar o Entity Framework ao projeto

1. No Visual Studio, abra a Solução *ContosoTeamStats* que você criou no [Início rápido do ASP.NET para o Cache do Azure para Redis](cache-web-app-howto.md).
2. Clique em **Ferramentas > Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.
3. Execute o seguinte comando na janela **Console do Gerenciador de Pacotes** para instalar o EntityFramework:

    ```powershell
    Install-Package EntityFramework
    ```

Para saber mais sobre este pacote, consulte a página do NuGet [EntityFramework](https://www.nuget.org/packages/EntityFramework/).

### <a name="add-the-team-model"></a>Adicionar o modelo Team

1. Clique com o botão direito do mouse em **Modelos** no **Gerenciador de Soluções** e escolha **Adicionar**, **Classe**.

1. Insira `Team` como o nome da classe e clique em **Adicionar**.

    ![Adicionar classe de modelo](./media/cache-web-app-cache-aside-leaderboard/cache-model-add-class-dialog.png)

1. Substitua as instruções `using` na parte superior do arquivo *Team.cs* pelas seguintes instruções `using`:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. Substitua a definição da classe `Team` pelo snippet de código a seguir, que contém uma definição de classe `Team` atualizada, bem como algumas outras classes auxiliares do Entity Framework. Este tutorial usa a abordagem de Code First com o Entity Framework. Essa abordagem permite que o Entity Framework crie o banco de dados com base no código. Para saber mais sobre a abordagem de code first do Entity Framework que é usada neste tutorial, confira [Code first para um novo banco de dados](/ef/ef6/modeling/code-first/workflows/new-database).

    ```csharp
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

1. No **Gerenciador de Soluções**, clique duas vezes em **Web.config** para abri-lo.

    ![Web.config](./media/cache-web-app-cache-aside-leaderboard/cache-web-config.png)

1. Adicione a seção `connectionStrings` a seguir dentro da seção `configuration`. O nome da cadeia de conexão deve corresponder ao nome da classe de contexto de banco de dados do Entity Framework, que é `TeamContext`.

    Essa cadeia de conexão pressupõe que você atendeu aos [Pré-requisitos](#prerequisites) e instalou o SQL Server Express LocalDB, que faz parte da carga de trabalho *Desenvolvimento para área de trabalho com .NET* instalada com o Visual Studio 2019.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    O exemplo a seguir mostra a nova seção `connectionStrings` após `configSections` dentro da seção `configuration`:

    ```xml
    <configuration>
        <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
        </configSections>
        <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>
        ...
    ```

### <a name="add-the-teamscontroller-and-views"></a>Adicionar o TeamsController e exibições

1. No Visual Studio, crie o projeto. 

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Controladores** e escolha **Adicionar**, **Controlador**.

1. Escolha **Controlador MVC 5 com modos de exibição, usando o Entity Framework** e clique em **Adicionar**. Se você receber um erro após clicar em **Adicionar**, verifique se criou o projeto primeiro.

    ![Adicionar classe de controlador](./media/cache-web-app-cache-aside-leaderboard/cache-add-controller-class.png)

1. Selecione **Equipe (ContosoTeamStats.Models)** na lista suspensa **Classe de modelo**. Selecione **TeamContext (ContosoTeamStats.Models)** na lista suspensa **Classe de contexto de dados**. Digite `TeamsController` na caixa de texto de nome **Controlador** (se ela não for populada automaticamente). Clique em **Adicionar** para criar a classe de controlador e adicionar os modos de exibição padrão.

    ![Configurar o controlador](./media/cache-web-app-cache-aside-leaderboard/cache-configure-controller.png)

1. No **Gerenciador de Soluções**, expanda **Global.asax** e clique duas vezes em **Global.asax.cs** para abri-lo.

    ![Global.asax.cs](./media/cache-web-app-cache-aside-leaderboard/cache-global-asax.png)

1. Adicione as duas seguintes instruções `using` na parte superior do arquivo abaixo das outras instruções `using`:

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. Adicione a seguinte linha de código ao final do método `Application_Start` :

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. No **Gerenciador de Soluções**, expanda `App_Start` e clique duas vezes em `RouteConfig.cs`.

    ![RouteConfig.cs](./media/cache-web-app-cache-aside-leaderboard/cache-RouteConfig-cs.png)

1. No método `RegisterRoutes`, substitua `controller = "Home"` na rota `Default` por `controller = "Teams"`, conforme mostrado no seguinte exemplo:

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-layout-view"></a>Configurar a exibição Layout

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições** e a pasta **Compartilhado** e clique duas vezes em **_Layout.cshtml**. 

    ![_Layout.cshtml.](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml.png)

1. Altere o conteúdo do elemento `title` e substitua `My ASP.NET Application` por `Contoso Team Stats`, conforme é mostrado no exemplo a seguir:

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. Na seção `body`, adicione a nova instrução `Html.ActionLink` a seguir a *Estatísticas da Equipe da Contoso* logo abaixo do link para *Testar o Cache do Azure para Redis*.

    ```csharp
    @Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
    ```

    ![Alterações de código](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml-code.png)

1. Pressione **Ctrl+F5** para compilar e executar o aplicativo. Essa versão do aplicativo lê os resultados diretamente do banco de dados. Observe as ações **Criar Novo**, **Editar**, **Detalhes** e **Excluir** que foram adicionadas automaticamente ao aplicativo pelo scaffold Controlador **MVC 5 com modos de exibição, usando o Entity Framework**. Na próxima seção do tutorial, você adicionará o Cache do Azure para Redis para otimizar o acesso a dados e fornecer recursos adicionais ao aplicativo.

    ![Aplicativo de início](./media/cache-web-app-cache-aside-leaderboard/cache-starter-application.png)

## <a name="configure-the-app-for-azure-cache-for-redis"></a>Configurar o aplicativo para o Cache do Azure para Redis

Nesta seção do tutorial, você configura o aplicativo de exemplo para armazenar e recuperar estatísticas de equipe da Contoso de uma instância do Cache do Azure para Redis usando o cliente de cache [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

### <a name="add-a-cache-connection-to-the-teams-controller"></a>Adicionar uma conexão de cache ao Teams Controller

Você já instalou o pacote da biblioteca de clientes *StackExchange.Redis* no início rápido. Você também já definiu a configuração do aplicativo *CacheConnection* a ser usada localmente e com o Serviço de Aplicativo publicado. Use essa mesma biblioteca de clientes e as informações de *CacheConnection* no *TeamsController*.

1. No **Gerenciador de Soluções**, expanda a pasta **Controladores** e clique duas vezes em **TeamsController.cs** para abri-lo.

    ![Controlador de equipes](./media/cache-web-app-cache-aside-leaderboard/cache-teamscontroller.png)

1. Adicione as duas instruções `using` a seguir a **TeamsController.cs**:

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

1. Adicione as duas propriedades a seguir à classe `TeamsController`:

    ```csharp
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

### <a name="update-the-teamscontroller-to-read-from-the-cache-or-the-database"></a>Atualizar o TeamsController para ler do cache ou o banco de dados

Neste exemplo, as estatísticas de equipe podem ser recuperadas do banco de dados ou do cache. As estatísticas de equipe são armazenadas no cache como um `List<Team>`serializado e também como um conjunto ordenado usando tipos de dados Redis. Ao recuperar itens de um conjunto ordenado, você pode recuperar alguns ou todos os itens ou consultar determinados itens. Nesta amostra, você consultará o conjunto classificado para obter as cinco equipes principais classificadas pelo número de vitórias.

Não é necessário armazenar as estatísticas de equipe em vários formatos no cache para usar o Cache do Azure para Redis. Este tutorial usa vários formatos para demonstrar algumas das diferentes maneiras e tipos de dados que você pode usar para armazenar dados em cache.

1. Adicione as instruções `using` a seguir ao arquivo `TeamsController.cs` na parte superior, com as outras instruções `using`:

    ```csharp
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

1. Substitua a implementação do método `public ActionResult Index()` atual pela implementação a seguir:

    ```csharp
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

1. Adicione os três métodos a seguir à classe `TeamsController` para implementar os tipos de ação `playGames`, `clearCache` e `rebuildDB` da instrução switch adicionada no snippet de código anterior.

    O método `PlayGames` atualiza as estatísticas de equipe simulando uma temporada de jogos, salva os resultados no banco de dados e limpa os dados agora obsoletos do cache.

    ```csharp
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

    ```csharp
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

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    }
    ```

1. Adicione os quatro métodos a seguir à classe `TeamsController` para implementar as várias maneiras de recuperar as estatísticas de equipe do cache e do banco de dados. Cada um desses métodos retorna uma `List<Team>`, que é então mostrado pela exibição.

    O método `GetFromDB` lê as estatísticas de equipe do banco de dados.
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t;

        return results.ToList<Team>();
    }
    ```

    O método `GetFromList` lê as estatísticas de equipe do cache como um `List<Team>` serializado. Se as estatísticas não estiverem presentes no cache, ocorrerá uma perda no cache. Em caso de perda no cache, as estatísticas da equipe são lidas do banco de dados e, em seguida, armazenadas no cache para a próxima solicitação. Nesta amostra, a serialização de JSON.NET é usada para serializar os objetos .NET bidirecionalmente no cache. Para saber mais, confira [Como trabalhar com objetos .NET no Cache do Azure para Redis](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```csharp
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

    ```csharp
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

    O método `GetFromSortedSetTop5` lê as cinco equipes principais do conjunto classificado armazenado em cache. Ele começa verificando a existência da chave `teamsSortedSet` no cache. Se a chave não estiver presente, o método `GetFromSortedSet` será chamado para ler as estatísticas de equipe e armazená-las no cache. Em seguida, o conjunto classificado armazenado em cache é consultado para fornecer as cinco equipes principais, que são retornadas.

    ```csharp
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

O código de scaffolding gerado como parte do exemplo inclui métodos para adicionar, editar e excluir equipes. Sempre que uma equipe é adicionada, editada ou removida, os dados no cache tornam-se desatualizados. Nesta seção, você modificará esses três métodos para limpar as equipes armazenadas em cache, de modo que o cache seja atualizado.

1. Navegue até o método `Create(Team team)` na classe `TeamsController`. Adicione uma chamada ao método `ClearCachedTeams`, conforme mostrado no exemplo a seguir:

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
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

2. Navegue até o método `Edit(Team team)` na classe `TeamsController`. Adicione uma chamada ao método `ClearCachedTeams`, conforme mostrado no exemplo a seguir:

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
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

3. Navegue até o método `DeleteConfirmed(int id)` na classe `TeamsController`. Adicione uma chamada ao método `ClearCachedTeams`, conforme mostrado no exemplo a seguir:

    ```csharp
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

### <a name="add-caching-methods-to-the-teams-index-view"></a>Adicionar métodos de cache à exibição Teams Index

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições** e a pasta **Equipes** e clique duas vezes em **Index.cshtml**.

    ![Index.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-views-teams-index-cshtml.png)

1. Na parte superior do arquivo, procure o elemento de parágrafo a seguir:

    ![Tabela de ação](./media/cache-web-app-cache-aside-leaderboard/cache-teams-index-table.png)

    Este link cria uma nova equipe. Substitua o elemento de parágrafo pela tabela a seguir. A tabela contém links de ação para criar uma nova equipe, reproduzir uma nova temporada de jogos, limpar o cache, recuperar as equipes do cache em vários formatos, recuperar as equipes do banco de dados e recriar o banco de dados com dados de exemplo atualizados.

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

1. Role até a parte inferior do arquivo **index.cshtml** e adicione o elemento `tr` a seguir para que ele seja a última linha na última tabela do arquivo:

    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
    Essa linha exibe o valor de `ViewBag.Msg` que contém um relatório de status sobre a operação atual. O `ViewBag.Msg` é definido quando você clica em qualquer um dos links de ação da etapa anterior.

    ![Mensagem de status](./media/cache-web-app-cache-aside-leaderboard/cache-status-message.png)

1. Pressione **F6** para compilar o projeto.

## <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Execute o aplicativo localmente no computador para verificar a funcionalidade que foi adicionada para dar suporte às equipes.

Neste teste, o aplicativo e banco de dados estão sendo executados localmente. No entanto, o Cache do Azure para Redis é hospedado remotamente no Azure. Portanto, o cache provavelmente terá um desempenho ligeiramente mais baixo em relação ao banco de dados. Para obter o melhor desempenho, o aplicativo cliente e a instância do Cache do Azure para Redis devem estar na mesma localização. Na próxima seção, você implantará todos os recursos no Azure para melhorar o desempenho do uso de um cache.

Para executar o aplicativo localmente:

1. Pressione **CTRL+F5** para executar o aplicativo.

    ![Aplicativo em execução localmente](./media/cache-web-app-cache-aside-leaderboard/cache-local-application.png)

1. Teste cada um dos novos métodos que foram adicionados à exibição. Como o cache é remoto nesses testes, o desempenho do banco de dados deve ligeiramente superar o do cache.

## <a name="publish-and-run-in-azure"></a>Publicar e executar no Azure

### <a name="provision-a-database-for-the-app"></a>Provisionar um banco de dados para o aplicativo

Nesta seção, você provisionará um novo banco de dados no Banco de Dados SQL para que o aplicativo use enquanto está hospedado no Azure.

1. No [portal do Azure](https://portal.azure.com/), clique em **Criar um recurso** no canto superior esquerdo do portal do Azure.

1. Na página **Novo**, clique em **Bancos de Dados** > **Banco de Dados SQL**.

1. Use as seguintes configurações para o novo Banco de Dados SQL:

   | Configuração       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome do banco de dados** | *ContosoTeamsDatabase* | Para ver os nomes do banco de dados válidos, consulte [Identificadores do Banco de Dados](/sql/relational-databases/databases/database-identifiers). |
   | **Assinatura** | *Sua assinatura*  | Selecione a mesma assinatura usada para criar o cache e hospedar o Serviço de Aplicativo. |
   | **Grupo de recursos**  | *TestResourceGroup* | Clique em **Usar existente** e use o mesmo grupo de recursos em que você colocou o cache e o Serviço de Aplicativo. |
   | **Selecionar fonte** | **Banco de dados em branco** | Comece com um banco de dados em branco. |

1. Em **Servidor**, clique em **Definir as configurações necessárias** > **Criar um novo servidor**, forneça as seguintes informações e clique no botão **Selecionar**:

   | Configuração       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome do servidor** | Qualquer nome exclusivo globalmente | Para ver os nomes do servidor válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). |
   | **Logon de administrador do servidor** | Qualquer nome válido | Para ver os nomes de logon válidos, consulte [Identificadores do Banco de Dados](/sql/relational-databases/databases/database-identifiers). |
   | **Senha** | Qualquer senha válida | Sua senha deve ter, pelo menos, 8 caracteres e deve conter caracteres de três das seguintes categorias: caracteres com letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos. |
   | **Localidade** | *Leste dos EUA* | Selecione a mesma região onde você criou o cache e o Serviço de Aplicativo. |

1. Clique em **Fixar no painel** e, em seguida, em **Criar** para criar o novo banco de dados e servidor.

1. Depois de criar o novo banco de dados, clique em **Mostrar cadeias de conexão de banco de dados** e copie a cadeia de conexão **ADO.NET**.

    ![Mostrar cadeias de conexão](./media/cache-web-app-cache-aside-leaderboard/cache-show-connection-strings.png)

1. No portal do Azure, navegue para o Serviço de Aplicativo, clique em **Configurações do Aplicativo** e, em seguida, em **Adicionar nova cadeia de conexão** na seção Cadeias de conexão.

1. Adicione uma nova cadeia de conexão chamada *TeamContext* para corresponder à classe de contexto de banco de dados do Entity Framework. Cole a cadeia de conexão do novo banco de dados como o valor. Lembre-se de substituir os seguintes espaços reservados na cadeia de conexão e clique em **Salvar**:

    | Espaço reservado | Valor sugerido |
    | --- | --- |
    | *{seu_nomedeusuário}* | Use o **logon de administrador do servidor** do servidor recém-criado. |
    | *{sua_senha}* | Use a senha do servidor recém-criado. |

    Ao adicionar o nome de usuário e a senha como uma Configuração de Aplicativo, seu nome de usuário e sua senha não são incluídos no código. Essa abordagem ajuda a proteger essas credenciais.

### <a name="publish-the-application-updates-to-azure"></a>Publicar as atualizações do aplicativo no Azure

Nesta etapa do tutorial, você publicará as atualizações do aplicativo no Azure para executá-lo na nuvem.

1. Clique com o botão direito do mouse no projeto **ContosoTeamStats** no Visual Studio e escolha **Publicar**.

    ![Publicar](./media/cache-web-app-cache-aside-leaderboard/cache-publish-app.png)

2. Clique em **Publicar** para usar o mesmo perfil de publicação criado no início rápido.

3. Quando a publicação é concluída, o Visual Studio inicia o aplicativo no navegador da Web padrão.

    ![Cache adicionado](./media/cache-web-app-cache-aside-leaderboard/cache-added-to-application.png)

    A tabela a seguir descreve cada link de ação do aplicativo de exemplo:

    | Ação | Descrição |
    | --- | --- |
    | Criar Novo |Criar uma nova Equipe. |
    | Reproduzir Temporada |Reproduzir uma temporada de jogos, atualizar as estatísticas de equipes e limpar dados de equipe desatualizados do cache. |
    | Limpar cache |Limpar as estatísticas de equipes do cache. |
    | Listar do Cache |Recuperar as estatísticas de equipe do cache. Se houver um erro de cache, carregar as estatísticas do banco de dados e salvar no cache para a próxima vez. |
    | Conjunto Ordenado do Cache |Recuperar as estatísticas de equipes do cache usando um conjunto ordenado. Se houver um erro de cache, carregar as estatísticas do banco de dados e salvar no cache usando um conjunto ordenado. |
    | Cinco Principais Equipes do Cache |Recuperar as cinco principais equipes do cache usando um conjunto ordenado. Se houver um erro de cache, carregar as estatísticas do banco de dados e salvar no cache usando um conjunto ordenado. |
    | Carregar do banco de dados |Recuperar as estatísticas de equipes do banco de dados. |
    | Recriar o banco de dados |Recriar o banco de dados e recarregá-lo com dados de equipes de exemplo. |
    | Editar/Detalhes/Excluir |Editar uma equipe, exibir detalhes de uma equipe, excluir uma equipe. |

Clique em algumas das ações e experimente recuperar os dados de diferentes fontes. Observe as diferenças no tempo necessário para concluir as várias maneiras de recuperar os dados do banco de dados e do cache.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao terminar de usar o aplicativo do tutorial de exemplo, você poderá excluir os recursos do Azure usados para economizar custos e recursos. Todos os recursos devem estar contidos no mesmo grupo de recursos; você pode excluí-los juntos em uma única operação excluindo o grupo de recursos. Para as instruções deste tópico, foi usado um grupo de recursos chamado *TestResources*.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível, e o grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Se você criou os recursos para hospedar esta amostra dentro de um grupo de recursos existente, que contém recursos que você deseja manter, exclua cada recurso individualmente de suas respectivas folhas.
>

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.
2. Digite o nome do grupo de recursos na caixa de texto **Filtrar itens...** .
3. Clique em **...** à direita do grupo de recursos e clique em **Excluir grupo de recursos**.

    ![Excluir](./media/cache-web-app-cache-aside-leaderboard/cache-delete-resource-group.png)

4. Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Digite o nome do grupo de recursos para confirmar e clique em **Excluir**.

    Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como dimensionar o Cache do Azure para Redis](./cache-how-to-scale.md)