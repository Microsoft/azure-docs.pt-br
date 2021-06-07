---
title: 'Tutorial: ASP.NET Core com o Banco de Dados SQL do Azure'
description: Saiba como executar um aplicativo .NET Core no Serviço de Aplicativo do Azure, com uma conexão com um Banco de Dados SQL do Azure.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-csharp, mvc, cli-validate, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 52a5b127312ef979791d17b27ca67b21a779e310
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765709"
---
# <a name="tutorial-build-an-aspnet-core-and-azure-sql-database-app-in-azure-app-service"></a>Tutorial: Criar um aplicativo ASP.NET Core e do Banco de Dados SQL do Azure no Serviço de Aplicativo do Azure

::: zone pivot="platform-windows"  

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches no Azure. Este tutorial mostra como criar um aplicativo do .NET Core e conectá-lo a um Banco de Dados SQL. Quando terminar, você terá um aplicativo MVC .NET Core em execução no Serviço de Aplicativo no Windows.

::: zone-end

::: zone pivot="platform-linux"

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches usando o sistema operacional Linux. Este tutorial mostra como criar um aplicativo .NET Core e conectá-lo a um Banco de Dados SQL. Quando terminar, você terá um aplicativo MVC .NET Core em execução no Serviço de Aplicativo no Linux.

::: zone-end

![aplicativo em execução no Serviço de Aplicativo](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um Banco de Dados SQL no Azure
> * Conectar um aplicativo .NET Core ao Banco de Dados SQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>
- <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Instalar o SDK do .NET Core 3.1 mais recente</a>

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-net-core-app"></a>Criar um aplicativo .NET Core local

Nesta etapa, você configura o projeto .NET Core local.

### <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Na janela do terminal, `cd` para um diretório de trabalho.

Execute os comandos a seguir para clonar o repositório de exemplo e alterar sua raiz.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Esse projeto de exemplo contém um aplicativo CRUD (create-read-update-delete) básico usando o [Entity Framework Core](/ef/core/).

### <a name="run-the-application"></a>Executar o aplicativo

Execute os comandos a seguir para instalar os pacotes necessários, executar as migrações de banco de dados e iniciar o aplicativo.

```bash
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

Navegue até `http://localhost:5000` em um navegador. Selecione o link **Criar novo** e crie alguns itens _de tarefas_.

![conecta-se com êxito ao Banco de Dados SQL](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Para parar o .NET Core a qualquer momento, pressione `Ctrl+C` no terminal.

## <a name="create-production-sql-database"></a>Criar um Banco de Dados SQL de produção

Nesta etapa, você cria um Banco de Dados SQL no Azure. Quando seu aplicativo é implantado no Azure, ele usa esse banco de dados na nuvem.

Para o Banco de Dados SQL, este tutorial usa o [Banco de Dados SQL do Azure](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Criar um servidor lógico do Banco de Dados SQL

No Cloud Shell, crie um servidor lógico do Banco de Dados SQL com o comando [`az sql server create`](/cli/azure/sql/server#az_sql_server_create).

Substitua o espaço reservado *\<server-name>* por um nome *exclusivo* do Banco de Dados SQL. Esse nome é usado como parte do ponto de extremidade do Banco de Dados SQL globalmente exclusivo, `<server-name>.database.windows.net`. Os caracteres válidos são `a`-`z`, `0`-`9`, `-`. Além disso, substitua *\<db-username>* e *\<db-password>* por um nome de usuário e uma senha de sua escolha. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

Quando o servidor lógico do Banco de Dados SQL é criado, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall de servidor

Crie uma [regra de firewall no nível de servidor do Banco de Dados SQL do Azure](../azure-sql/database/firewall-configure.md) usando o comando [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create). Quando o IP inicial e o IP final estiverem definidos como 0.0.0.0, o firewall estará aberto somente para outros recursos do Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Você pode ser ainda mais restritivo na regra de firewall ao [usar somente os endereços de IP de saída que seu aplicativo usa](overview-inbound-outbound-ips.md#find-outbound-ips).
>

No Cloud Shell, execute o comando novamente para permitir acesso no computador local, substituindo *\<your-ip-address>* pelo [endereço IP IPv4 local](https://www.whatsmyip.org/).

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database"></a>Criar um banco de dados

Crie um banco de dados com um [nível de desempenho S0](../azure-sql/database/service-tiers-dtu.md) no servidor usando o comando [`az sql db create`](/cli/azure/sql/db#az_sql_db_create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Criar uma cadeia de conexão

Obtenha a cadeia de conexão usando o comando [`az sql db show-connection-string`](/cli/azure/sql/db#az_sql_db_show_connection_string).

```azurecli-interactive
az sql db show-connection-string --client ado.net --server <server-name> --name coreDB
```

Na saída do comando, substitua *\<username>* e *\<password>* pelas credenciais de administrador de banco de dados usadas anteriormente.

Esta é a cadeia de conexão do aplicativo .NET Core. Copie-a para uso posterior.

### <a name="configure-app-to-connect-to-production-database"></a>Configurar o aplicativo para se conectar ao banco de dados de produção

No repositório local, abra Startup.cs e encontre o seguinte código:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Substitua-o pelo código a seguir.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> Para aplicativos de produção que precisam ser expandidos horizontalmente, siga as práticas recomendadas em [Aplicar migrações em produção](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production).
> 

### <a name="run-database-migrations-to-the-production-database"></a>Executar migrações de banco de dados para o banco de dados de produção

Seu aplicativo conecta-se atualmente a um banco de dados SQLite local. Agora que você configurou um Banco de Dados SQL do Azure, recrie a migração inicial para defini-lo como destino. 

Na raiz do repositório, execute os comandos a seguir. Substitua *\<connection-string>* pela cadeia de conexão criada anteriormente.

```
# Delete old migrations
rm -r Migrations
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>Executar o aplicativo com nova configuração

Agora que as migrações de banco de dados são executadas no banco de dados de produção, teste seu aplicativo executando:

```
dotnet run
```

Navegue até `http://localhost:5000` em um navegador. Selecione o link **Criar novo** e crie alguns itens _de tarefas_. Agora, seu aplicativo está lendo e gravando dados no banco de dados de produção.

Confirme suas alterações locais e então confirme-as no repositório do Git. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

Agora, você está pronto para implantar seu código.

## <a name="deploy-app-to-azure"></a>Implantar o aplicativo no Azure

Nesta etapa, você implantará seu aplicativo .NET Core conectado ao Banco de Dados SQL no Serviço de Aplicativo.

### <a name="configure-local-git-deployment"></a>Configurar a implantação do git local

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

### <a name="create-a-web-app"></a>Criar um aplicativo Web

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

::: zone-end

### <a name="configure-connection-string"></a>Configurar a cadeia de conexão

Para definir as cadeias de conexão para o aplicativo do Azure, use o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) no Cloud Shell. No comando a seguir, substitua *\<app-name>* pelo nome do aplicativo, bem como o parâmetro *\<connection-string>* pela cadeia de conexão criada anteriormente.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

No ASP.NET Core, você poderá usar essa cadeia de conexão nomeada (`MyDbConnection`) empregando o modelo padrão, como qualquer cadeia de conexão especificada em *appsettings.json*. Nesse caso, `MyDbConnection` também é definido em seu *appsettings.json*. Durante a execução no Serviço de Aplicativo, a cadeia de conexão definida no Serviço de Aplicativo tem precedência sobre a cadeia de conexão definida no seu *appsettings.json*. O código usa o valor *appsettings.json* durante o desenvolvimento local e o mesmo código usa o valor do Serviço de Aplicativo quando implantado.

Para ver como a cadeia de conexão é referenciada em seu código, confira [Configurar o aplicativo para conectar-se ao banco de dados de produção](#configure-app-to-connect-to-production-database).

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

::: zone pivot="platform-windows"  

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 273, done.
Counting objects: 100% (273/273), done.
Delta compression using up to 4 threads
Compressing objects: 100% (175/175), done.
Writing objects: 100% (273/273), 1.19 MiB | 1.85 MiB/s, done.
Total 273 (delta 96), reused 259 (delta 88)
remote: Resolving deltas: 100% (96/96), done.
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cccecf86c5'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote: .
remote: .
remote: .
remote: Done.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/cccecf86c56493ffa594e76ea1deb3abb3702d89/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

::: zone-end

### <a name="browse-to-the-azure-app"></a>Navegar até o aplicativo do Azure

Navegue até o aplicativo implantado usando o navegador da Web.

```bash
http://<app-name>.azurewebsites.net
```

Adicione alguns itens de tarefas.

![aplicativo em execução no Serviço de Aplicativo](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Parabéns!** Você está executando um aplicativo .NET Core controlado por dados no Serviço de Aplicativo.

## <a name="update-locally-and-redeploy"></a>Atualizar localmente e reimplantar

Nesta etapa, você faz uma alteração no esquema de banco de dados e publica-o no Azure.

### <a name="update-your-data-model"></a>Atualizar seu modelo de dados

Abra _Models/Todo.cs_ no editor de códigos. Adicione a seguinte propriedade à classe `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>Executar migrações de banco de dados novamente

Execute alguns comandos para fazer atualizações ao banco de dados de produção.

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

> [!NOTE]
> Se você abrir uma nova janela de terminal, precisará definir a cadeia de conexão para o banco de dados de produção no terminal, como fez em [Executar migrações de banco de dados para o banco de dados de produção](#run-database-migrations-to-the-production-database).
>

### <a name="use-the-new-property"></a>Usar a nova propriedade

Faça algumas alterações em seu código para usar a propriedade `Done`. Para simplificar este tutorial, somente as exibições `Index` e `Create` serão alteradas para observar a propriedade em ação.

Abra _Controllers/TodosController.cs_.

Localize o `Create([Bind("ID,Description,CreatedDate")] Todo todo)` método e adicione `Done` à lista de propriedades no atributo `Bind`. Quando terminar, a assinatura do método `Create()` deverá ter o seguinte código:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Abra _Views/Todos/Create.cshtml_.

No código do Razor, você deve ver um elemento `<div class="form-group">` para `Description` e, em seguida, outro elemento `<div class="form-group">` para `CreatedDate`. Imediatamente após esses dois elementos, adicione outro elemento `<div class="form-group">` para `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Abra _Views/Todos/Index.cshtml_.

Procure o elemento vazio `<th></th>`. Logo acima desse elemento, adicione o seguinte código do Razor:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Localize o elemento `<td>` que contém os auxiliares de marcação `asp-action`. Logo acima desse elemento, adicione o seguinte código do Razor:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Isso é tudo o que você precisa para ver as alterações nas exibições `Index` e `Create`.

### <a name="test-your-changes-locally"></a>Testar suas alterações localmente

Execute o aplicativo localmente.

```bash
dotnet run
```

> [!NOTE]
> Se você abrir uma nova janela de terminal, precisará definir a cadeia de conexão para o banco de dados de produção no terminal, como fez em [Executar migrações de banco de dados para o banco de dados de produção](#run-database-migrations-to-the-production-database).
>

No navegador, navegue até `http://localhost:5000/`. Agora você pode adicionar um item de tarefas e marcar **Concluído**. Em seguida, ele deverá aparecer na sua página inicial como um item concluído. Lembre-se de que a exibição `Edit` não mostra o campo `Done`, porque você não alterou a exibição `Edit`.

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

```bash
git add .
git commit -m "added done field"
git push azure main
```

Quando `git push` estiver concluído, navegue até seu aplicativo do Serviço de Aplicativo e tente adicionar um item de tarefa pendente e marcar **Concluído**.

![Aplicativo do Azure após Migração do Code First](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Observe que todos os itens de tarefas existentes ainda são exibidos. Quando você republicar o aplicativo ASP.NET Core, os dados existentes no Banco de Dados SQL não serão perdidos. Além disso, as Migrações do Entity Framework Core apenas alteram o esquema de dados e deixam os dados existentes intactos.

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

Enquanto o aplicativo ASP.NET Core é executado no serviço de aplicativo do Azure, você pode transferir os logs do console para o Cloud Shell. Dessa forma, é possível obter as mesmas mensagens de diagnóstico para ajudá-lo a depurar erros de aplicativo.

O projeto de exemplo já segue as diretrizes em [Registro do ASP.NET Core no Azure](/aspnet/core/fundamentals/logging#azure-app-service-provider) com duas alterações de configuração:

- Inclui uma referência a `Microsoft.Extensions.Logging.AzureAppServices` em *DotNetCoreSqlDb.csproj*.
- Chama `loggerFactory.AddAzureWebAppDiagnostics()` em *Program.cs*.

Para definir o [nível de log](/aspnet/core/fundamentals/logging#log-level) do ASP.NET Core no Serviço de Aplicativo para `Information` do nível padrão `Error`, use o comando [`az webapp log config`](/cli/azure/webapp/log#az_webapp_log_config) no Cloud Shell.

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging filesystem --level information
```

> [!NOTE]
> O nível de log do projeto já está definido como `Information` em *appsettings.json*.

Para iniciar o streaming de log, use o comando [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) no Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Depois que o streaming de log for iniciado, atualize o aplicativo do Azure no navegador para obter parte do tráfego da Web. Agora é possível ver os logs do console redirecionados para o terminal. Se você não vir os logs do console imediatamente, verifique novamente após 30 segundos.

Para interromper o streaming de log a qualquer momento, digite `Ctrl`+`C`.

Para obter mais informações sobre como personalizar os logs do ASP.NET Core, veja [Efetuar logon no ASP.NET Core](/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Gerenciar o aplicativo do Azure

Para ver o aplicativo que você criou, no [portal do Azure](https://portal.azure.com), procure **Serviços de Aplicativos** e selecione essa opção.

![Selecionar Serviços de Aplicativos no portal do Azure](./media/tutorial-dotnetcore-sqldb-app/app-services.png)

Na página **Serviços de Aplicativos**, selecione o nome do seu aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Por padrão, o portal mostra a página **Visão Geral** do aplicativo. Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Página Serviço de Aplicativo no portal do Azure](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Próximas etapas

O que você aprendeu:

> [!div class="checklist"]
> * Criar um Banco de Dados SQL no Azure
> * Conectar um aplicativo .NET Core ao Banco de Dados SQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs do Azure para seu terminal
> * Gerenciar o aplicativo no portal do Azure

Prossiga para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo.

> [!div class="nextstepaction"]
> [Tutorial: Mapear o nome DNS personalizado para seu aplicativo](app-service-web-tutorial-custom-domain.md)

Se preferir, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar o aplicativo ASP.NET Core](configure-language-dotnetcore.md)
