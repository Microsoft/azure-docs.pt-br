---
title: 'Tutorial: Aplicativo ASP.NET com o Banco de Dados SQL do Azure'
description: Saiba como implantar um aplicativo C# ASP.NET no Azure e no Banco de Dados SQL do Azure
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 06/25/2018
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: a427fbc6fad1566ae10e11b61de981aded32e64a
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92000313"
---
# <a name="tutorial-deploy-an-aspnet-app-to-azure-with-azure-sql-database"></a>Tutorial: Implantar um aplicativo ASP.NET no Azure com o Banco de Dados SQL do Azure

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este tutorial mostra como implantar um aplicativo ASP.NET controlado por dados no Serviço de Aplicativo e conectá-lo ao [Banco de Dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md). Quando terminar, você terá um aplicativo ASP.NET em execução no Azure e conectado ao Banco de Dados SQL.

![Aplicativo ASP.NET publicado no Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Criar um banco de dados no Banco de Dados SQL do Azure
> * Conectar um aplicativo ASP.NET ao Banco de Dados SQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs do Azure para seu terminal
> * Gerenciar o aplicativo no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

Instale o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> com a carga de trabalho de **desenvolvimento Web e do ASP.NET**.

Se você já instalou o Visual Studio, adicione as cargas de trabalho no Visual Studio clicando em **Ferramentas** > **Obter Ferramentas e Recursos**.

## <a name="download-the-sample"></a>Baixar o exemplo

* [Baixar o projeto de exemplo](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).
* Extrair (descompactar) o arquivo *dotnet-sqldb-tutorial-master.zip*.

Esse projeto de exemplo contém um aplicativo CRUD (criar-ler-atualizar-excluir) básico do [ASP.NET MVC](https://www.asp.net/mvc) usando o [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-app"></a>Executar o aplicativo

Abra o arquivo *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* no Visual Studio.

Tipo `Ctrl+F5` para executar o aplicativo sem depuração. O aplicativo é exibido no navegador padrão. Selecione o link **Criar novo** e crie alguns itens *de tarefas*.

![Caixa de diálogo Novo Projeto ASP .NET](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

Teste os links **Editar**, **Detalhes** e **Excluir**.

O aplicativo usa um contexto de banco de dados para se conectar com o banco de dados. Neste exemplo, o contexto do banco de dados usa uma cadeia de conexão chamada `MyDbConnection`. Essa cadeia de conexão é definida no arquivo *Web. config* e é referenciada no arquivo *Models/MyDatabaseContext.cs*. O nome da cadeia de conexão é usado mais adiante no tutorial para conectar o aplicativo do Azure a um Banco de Dados SQL do Azure.

## <a name="publish-aspnet-application-to-azure"></a>Publicar um aplicativo ASP.NET no Azure

No **Gerenciador de Soluções**, clique com botão direito no projeto **DotNetAppSqlD** e selecione **Publicar**.

![Publicar no Gerenciador de Soluções](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Selecione o **Azure** como seu destino e clique em avançar. Verifique se o **Serviço de Aplicativo do Azure (Windows)** está selecionado e clique em avançar novamente.

![Publicar na página de visão geral do projeto](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

### <a name="sign-in-to-azure"></a>Entrar no Azure

Na caixa de diálogo **Publicar**, clique em **Adicionar uma conta** na lista suspensa do gerente de conta e entre em sua assinatura do Azure. Se você já entrou em uma conta da Microsoft, verifique se a conta tem sua assinatura do Azure. Se a conta da Microsoft conectada não tiver sua assinatura do Azure, clique nela para adicionar a conta correta.

![Entrar no Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

> [!NOTE]
> Se você já estiver conectado, não selecione **Criar** ainda.

### <a name="configure-the-web-app-name"></a>Configurar o nome do aplicativo Web

Você pode manter o nome do aplicativo Web gerado ou alterá-lo para outro nome exclusivo (caracteres válidos são `a-z`, `0-9` e `-`). O nome do aplicativo Web é usado como parte da URL padrão para seu aplicativo (`<app_name>.azurewebsites.net`, onde `<app_name>` é o nome do aplicativo Web). O nome do aplicativo Web precisa ser exclusivo em todos os aplicativos no Azure.

![Criar caixa de diálogo do serviço de aplicativo](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource-group](../../includes/resource-group.md)]

1. Ao lado de **Grupo de Recursos**, clique em **Novo**.

   ![Ao lado do Grupo de Recursos, clique em Novo.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

2. Nomeie o grupo de recursos **myResourceGroup**.

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Lado do **Plano do Serviço de Aplicativo**, clique em **Novo**.

2. Na caixa de diálogo **Configurar Plano do Serviço de Aplicativo**, configure o novo plano do Serviço de Aplicativo com as seguintes definições:

   ![Criar plano de Serviço de Aplicativo](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

   | Configuração  | Valor sugerido | Para obter mais informações |
   | ----------------- | ------------ | ----|
   |**Plano do Serviço de Aplicativo**| myAppServicePlan | [Planos do Serviço de Aplicativo](../app-service/overview-hosting-plans.md) |
   |**Localidade**| Europa Ocidental | [Regiões do Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
   |**Tamanho**| Grátis | [Tipos de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

3. A caixa de diálogo **Publicar** mostrará os recursos que você configurou. Clique em **Concluir**.

   ![os recursos que você criou](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)


### <a name="create-a-server"></a>Criar um servidor

Antes de criar um banco de dados, você precisará ter um [servidor SQL lógico](../azure-sql/database/logical-servers.md). Um servidor SQL lógico é um constructo lógico que contém um grupo de bancos de dados gerenciados como um grupo.

1. Clique em **Configurar** ao lado do Banco de Dados do SQL Server em **Serviços Conectados**.

   ![Criar um banco de dados SQL](media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

2. Na caixa de diálogo **Banco de Dados SQL do Azure**, clique em **Novo** ao lado de **Servidor de Banco de Dados**.

   Um nome do servidor único é gerado. Esse nome é usado como parte da URL padrão do servidor, `<server_name>.database.windows.net`. Ele precisa ser exclusivo em todos os servidores do SQL do Azure. Você pode alterar o nome do servidor, mas para este tutorial, mantenha o valor gerado.

3. Adicione um nome de usuário administrador e a senha. Para requisitos de complexidade de senha, consulte [Política de Senha](/sql/relational-databases/security/password-policy).

   Lembre desse nome de usuário e senha. Você precisará deles para gerenciar o servidor mais tarde.

   ![Criar servidor](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

   > [!IMPORTANT]
   > Mesmo que a senha nas cadeias de conexão esteja mascarada (no Visual Studio e também no Serviço de Aplicativo), o fato de que ela foi mantida em algum lugar aumenta a superfície de ataque do seu aplicativo. O Serviço de Aplicativo pode usar [identidades de serviço gerenciadas](overview-managed-identity.md) para eliminar esse risco removendo a necessidade de manter os segredos na configuração de aplicativo ou no código. Para saber mais, confira as [Próximas etapas](#next-steps).

4. Clique em **OK**. Não feche a caixa de diálogo **Configurar Banco de Dados SQL** ainda.

### <a name="create-a-database-in-azure-sql-database"></a>Criar um banco de dados no Banco de Dados SQL do Azure

1. Na caixa de diálogo **Banco de Dados SQL do Azure**:

   * Mantenha o **Nome do banco de dados** padrão gerado.
   * Selecione **Criar**.

    ![Configurar o banco de dados](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

2. Em **Nome da Cadeia de Conexão de Banco de Dados**, digite _MyDbConnection_. Esse nome deve corresponder à cadeia de conexão que está referenciada em _Models/MyDatabaseContext.cs_.

3. Insira o nome de usuário e a senha de administrador, que você usou em [Criar um servidor](#create-a-server) na etapa 3, nos campos de nome de usuário e senha do Banco de Dados, respectivamente.

    ![Configurar uma cadeia de conexão de banco de dados](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-connection.png)

4. Selecione **Concluir**.

Depois que o assistente terminar de criar os recursos do Azure, clique em **Publicar** para implantar seu aplicativo ASP.NET no Azure. Seu navegador padrão é iniciado com a URL para o aplicativo implantado.

Adicione alguns itens de tarefas.

![Aplicativo ASP.NET publicado no aplicativo do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Parabéns! Seu aplicativo ASP.NET controlado por dados está em execução em tempo real no Serviço de Aplicativo do Azure.

## <a name="access-the-database-locally"></a>Acessar o banco de dados localmente

O Visual Studio permite que você explore e gerencie seu novo banco de dados com facilidade no **Pesquisador de Objetos do SQL Server**.

### <a name="create-a-database-connection"></a>Criar uma conexão de banco de dados

No menu **Visualizar**, selecione **Pesquisador de objetos do SQL Server**.

Na parte superior do **Pesquisador de Objetos do SQL Server**, clique no botão **Adicionar SQL Server**.

### <a name="configure-the-database-connection"></a>Configurar a conexão de banco de dados

Na caixa de diálogo **Conectar**, expanda o nó **Azure**. Todas as suas instâncias de Banco de Dados SQL no Azure são listadas aqui.

Selecione o banco de dados criado anteriormente. A conexão criada antes é automaticamente preenchida na parte inferior.

Digite a senha de administrador de banco de dados que você criou anteriormente e clique em **Conectar**.

![Configurar a conexão de Banco de Dados do Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>Permitir conexão do cliente a partir de seu computador

A caixa de diálogo **Crie uma nova regra de firewall** é aberta. Por padrão, um servidor só permite conexões com os respectivos bancos de dados nos serviços do Azure, como o aplicativo do Azure. Para se conectar ao banco de dados fora do Azure, crie uma regra de firewall no nível do servidor. A regra de firewall permite o endereço IP público do seu computador local.

A caixa de diálogo já está preenchida com o endereço IP público do seu computador.

Certifique-se de que **Adicionar meu IP do cliente** está selecionado e clique em **OK**.

![Criar uma regra de firewall](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Quando o Visual Studio terminar de criar a configuração de firewall para a instância do Banco de dados SQL, sua conexão aparecerá no **Pesquisador de Objetos do SQL Server**.

Aqui, você pode executar as operações de banco de dados mais comuns, como executar consultas, criar exibições, procedimentos armazenados e, muito mais.

Expanda sua conexão > **Bancos de dados** >  **&lt;seu banco de dados>**  > **Tabelas**. Clique com o botão direito do mouse na tabela `Todoes` e selecione **Exibir Dados**.

![Explorar objetos do Banco de Dados SQL](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Atualizar aplicativo com Migrações do Code First

Use as ferramentas conhecidas no Visual Studio para atualizar o banco de dados e o aplicativo no Azure. Nesta etapa, você usará Migrações do Code First no Entity Framework para fazer uma alteração no seu esquema de banco de dados e publicá-lo no Azure.

Para obter mais informações sobre como usar as Migrações do Entity Framework Code First, consulte [Introdução ao Entity Framework 6 Code First usando MVC 5](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="update-your-data-model"></a>Atualizar seu modelo de dados

Abra _Models\Todo.cs_ no editor de códigos. Adicione a seguinte propriedade à classe `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Executar Migrações do Code First localmente

Execute alguns comandos para fazer as atualizações para seu banco de dados local.

A partir do menu **Ferramentas** clique em **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.

Na janela do Console do Gerenciador de Pacotes, habilite as Migrações do Code First:

```powershell
Enable-Migrations
```

Adicione uma migração:

```powershell
Add-Migration AddProperty
```

Atualize o banco de dados local:

```powershell
Update-Database
```

Digite `Ctrl+F5` para executar o aplicativo. Teste os links editar, detalhes e criar.

Se o aplicativo for carregado sem erros, as Migrações do Code First tiveram êxito. No entanto, sua página ainda tem a mesma aparência porque a lógica de aplicativo ainda não está usando essa nova propriedade.

### <a name="use-the-new-property"></a>Usar a nova propriedade

Faça algumas alterações em seu código para usar a propriedade `Done`. Para simplificar este tutorial, somente as exibições `Index` e `Create` serão alteradas para observar a propriedade em ação.

Abra _Controllers\TodosController.cs_.

Localize o `Create()` método na linha 52 e adicione `Done` à lista de propriedades no atributo `Bind`. Quando terminar, a assinatura do método `Create()` deverá ter o seguinte código:

```csharp
public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
```

Abra _Views\Todos\Create.cshtml_.

No código do Razor, você deve ver um elemento `<div class="form-group">` que usa `model.Description` e outro elemento `<div class="form-group">` que usa `model.CreatedDate`. Imediatamente após esses dois elementos, adicione outro elemento `<div class="form-group">` que usa `model.Done`:

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

Abra _Views\Todos\Index.cshtml_.

Procure o elemento vazio `<th></th>`. Logo acima desse elemento, adicione o seguinte código do Razor:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Localize o elemento `<td>` que contém os métodos auxiliares `Html.ActionLink()`. _Acima_ disto `<td>`, adicione outro elemento `<td>` com o seguinte código Razor:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Isso é tudo o que você precisa para ver as alterações nas exibições `Index` e `Create`.

Digite `Ctrl+F5` para executar o aplicativo.

Agora você pode adicionar um item de tarefas e marcar **Concluído**. Em seguida, ele deverá aparecer na sua página inicial como um item concluído. Lembre-se de que a exibição `Edit` não mostra o campo `Done`, porque você não alterou a exibição `Edit`.

### <a name="enable-code-first-migrations-in-azure"></a>Habilitar Migrações do Code First no Azure

Agora que a alteração de código funciona, incluindo a migração de banco de dados, publique-a no aplicativo do Azure e atualize o Banco de Dados SQL com as Migrações do Code First também.

Exatamente como antes, clique com o botão direito do mouse no projeto e selecione **Publicar**.

Clique em **Configurar** para abrir as configurações de publicação.

![Abrir as configurações de publicação](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

No assistente, clique em **Próximo**.

Certifique-se de que a cadeia de conexão do banco de dados SQL está preenchida em **MyDatabaseContext (MyDbConnection)** . Talvez seja necessário selecionar o banco de dados **myToDoAppDb** da lista suspensa.

Selecione **Executar o Migrations do Code First (executado na inicialização do aplicativo)** e, em seguida, clique em **Salvar**.

![Habilitar as Migrações do Code First no aplicativo do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>Publicar suas alterações

Agora que você habilitou as Migrações do Code First no aplicativo do Azure, publique as alterações de código.

Na página de publicação, clique em **Publicar**.

Tente adicionar os itens pendentes outra vez, selecione **Concluído** e os itens deverão aparecer na sua página inicial como um item concluído.

![Aplicativo do Azure após Migração do Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Observe que todos os itens de tarefas existentes ainda são exibidos. Quando você republicar seu aplicativo ASP.NET, os dados existentes no Banco de Dados SQL não serão perdidos. Além disso, as Migrações do Code First apenas alteram o esquema de dados e deixam os dados existentes intactos.

## <a name="stream-application-logs"></a>Transmitir logs de aplicativos

Transmita mensagens de rastreamento diretamente do aplicativo do Azure para o Visual Studio.

Abra _Controllers\TodosController.cs_.

Cada ação inicia com um método `Trace.WriteLine()`. Esse código é adicionado para mostrar como adicionar mensagens de rastreamento ao aplicativo do Azure.

### <a name="open-server-explorer"></a>Abra o Gerenciador de Servidores

No menu **Visualizar**, selecione **Gerenciador de Servidores**. Configure o log para o aplicativo do Azure no **Gerenciador de Servidores**.

### <a name="enable-log-streaming"></a>Habilitar streaming de log

No **Gerenciador de Servidores**, expanda **Azure** > **Serviço de Aplicativo**.

Expanda o grupo de recursos **myResourceGroup** criado quando você criou o aplicativo do Azure pela primeira vez.

Clique com o botão direito do mouse no aplicativo do Azure e selecione **Exibir Logs de Streaming**.

![Habilitar streaming de log](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

Os logs agora são transmitidos na janela **Saída**.

![Streaming de log na janela Saída](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

No entanto, ainda não é possível ver nenhuma das mensagens de rastreamento. Isso ocorre porque quando você seleciona **Exibir Logs de Streaming** pela primeira vez, o aplicativo do Azure define o nível de rastreamento como `Error`, o que registra em log apenas eventos de erro (com o método `Trace.TraceError()`).

### <a name="change-trace-levels"></a>Alterar níveis de rastreamento

Para alterar os níveis de rastreamento e exibir outras mensagens de rastreamento, retorne para o **Gerenciador de Servidores**.

Clique com o botão direito do mouse no aplicativo do Azure novamente e selecione **Exibir Configurações**.

Na lista suspensa **Log de Aplicativo (Sistema de Arquivos)** , selecione **detalhado**. Clique em **Save** (Salvar).

![Alterar nível de rastreamento para Detalhado](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> Você pode testar diferentes níveis de rastreamento para ver quais tipos de mensagens são exibidos para cada nível. Por exemplo, o nível **Informações** inclui todos os logs criados por `Trace.TraceInformation()`, `Trace.TraceWarning()` e `Trace.TraceError()`, mas não os logs criados por `Trace.WriteLine()`.

No navegador, navegue para o aplicativo novamente em *http://&lt;nome do aplicativo>.azurewebsites.net* e, em seguida, tente clicar em torno do aplicativo de lista de tarefas pendentes no Azure. As mensagens de rastreamento agora são transmitidas para a janela **Saída** no Visual Studio.

```console
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```

### <a name="stop-log-streaming"></a>Parar o streaming de log

Para interromper o serviço de streaming de log, clique no botão **Parar Monitoramento** na janela **Saída**.

![Parar o streaming de log](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Gerenciar o aplicativo do Azure

Acesse o [portal do Azure](https://portal.azure.com) para gerenciar o aplicativo Web. Pesquise e selecione **Serviços de Aplicativos**.

![Pesquise por Serviços de Aplicativos do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

Selecione o nome do seu aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Você aterrissou na página do aplicativo.

Por padrão, o portal mostra a página **Visão geral**. Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Página Serviço de Aplicativo no portal do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> * Criar um banco de dados no Banco de Dados SQL do Azure
> * Conectar um aplicativo ASP.NET ao Banco de Dados SQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs do Azure para seu terminal
> * Gerenciar o aplicativo no portal do Azure

Avance para o próximo tutorial para aprender a aprimorar a segurança da sua conexão com o Banco de Dados SQL do Azure.

> [!div class="nextstepaction"]
> [Acessar o Banco de Dados SQL com segurança usando identidades gerenciadas para recursos do Azure](app-service-web-tutorial-connect-msi.md)

Mais recursos:

> [!div class="nextstepaction"]
> [Configurar o aplicativo ASP.NET](configure-language-dotnet-framework.md)

Deseja otimizar e reduzir seus gastos com a nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar os custos com o Gerenciamento de Custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)