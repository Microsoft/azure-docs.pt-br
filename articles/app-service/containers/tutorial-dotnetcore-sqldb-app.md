---
title: ASP.NET Core com o Banco de Dados SQL no Linux – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como executar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure no Linux, com uma conexão com um Banco de Dados SQL.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 4837867188721b13b3f4cb64245ae85a1e32fe50
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656633"
---
# <a name="build-an-aspnet-core-and-sql-database-app-in-azure-app-service-on-linux"></a>Criar um aplicativo ASP.NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure no Linux

> [!NOTE]
> Este artigo implanta um aplicativo no Serviço de Aplicativo no Linux. Para implantar no Serviço de Aplicativo no _Windows_, confira [Criar um aplicativo .NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure](../app-service-web-tutorial-dotnetcore-sqldb.md).
>

O [Serviço de Aplicativo no Linux](app-service-linux-intro.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches usando o sistema operacional Linux. Este tutorial mostra como criar um aplicativo .NET Core e conectá-lo a um Banco de Dados SQL. Quando terminar, você terá um aplicativo MVC .NET Core em execução no Serviço de Aplicativo no Linux.

![aplicativo em execução no Serviço de Aplicativo no Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um Banco de Dados SQL no Azure
> * Conectar um aplicativo .NET Core ao Banco de Dados SQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Instalar o Git](https://git-scm.com/)
* [Instalar o .NET Core](https://www.microsoft.com/net/core/)

## <a name="create-local-net-core-app"></a>Criar um aplicativo .NET Core local

Nesta etapa, você configura o projeto .NET Core local.

### <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Na janela do terminal, `cd` para um diretório de trabalho.

Execute os comandos a seguir para clonar o repositório de exemplo e alterar sua raiz.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Esse projeto de exemplo contém um aplicativo CRUD (create-read-update-delete) básico usando o [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Executar o aplicativo

Execute os comandos a seguir para instalar os pacotes necessários, executar as migrações de banco de dados e iniciar o aplicativo.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

Navegue até `http://localhost:5000` em um navegador. Selecione o link **Criar novo** e crie alguns itens _de tarefas_.

![conecta-se com êxito ao Banco de Dados SQL](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Para parar o .NET Core a qualquer momento, pressione `Ctrl+C` no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Criar um Banco de Dados SQL de produção

Nesta etapa, você cria um Banco de Dados SQL no Azure. Quando seu aplicativo é implantado no Azure, ele usa esse banco de dados na nuvem.

Para o Banco de Dados SQL, este tutorial usa o [Banco de Dados SQL do Azure](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Criar um servidor lógico do Banco de Dados SQL

No Cloud Shell, crie um servidor lógico do Banco de Dados SQL com o comando [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create).

Substitua o espaço reservado *\<server-name>* por um nome exclusivo de Banco de Dados SQL. Esse nome é usado como parte do ponto de extremidade do Banco de Dados SQL, `<server-name>.database.windows.net` e, portanto, o nome precisa ser exclusivo em todos os servidores lógicos no Azure. O nome deve conter somente letras minúsculas, números e o caractere hífen (-), e deve ter entre 3 e 50 caracteres. Além disso, substitua *\<db-username>* e *\<db-password>* por um nome de usuário e uma senha de sua escolha. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

Quando o servidor lógico do Banco de Dados SQL é criado, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server-name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server-name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall de servidor

Crie uma [regra de firewall no nível de servidor do Banco de Dados SQL do Azure](../../sql-database/sql-database-firewall-configure.md) usando o comando [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Quando o IP inicial e o IP final estiverem definidos como 0.0.0.0, o firewall estará aberto somente para outros recursos do Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Criar um banco de dados

Crie um banco de dados com um [nível de desempenho S0](../../sql-database/sql-database-service-tiers-dtu.md) no servidor usando o comando [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Criar uma cadeia de conexão

Na cadeia de caracteres a seguir, substitua *\<server-name>* pelo nome do servidor, *\<db-username>* pelo nome de usuário do banco de dados e *\<db-password>* pela senha do banco de dados usados anteriormente.

```
Server=tcp:<server-name>.database.windows.net,1433;Database=coreDB;User ID=<db-username>;Password=<db-password>;Encrypt=true;Connection Timeout=30;
```

Esta é a cadeia de conexão do aplicativo .NET Core. Copie-a para uso posterior.

## <a name="deploy-app-to-azure"></a>Implantar o aplicativo no Azure

Nesta etapa, você implantará seu aplicativo .NET Core conectado ao Banco de Dados SQL no Serviço de Aplicativo no Linux.

### <a name="configure-local-git-deployment"></a>Configurar a implantação do git local

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar um aplicativo Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Configurar um variável de ambiente

Para definir as cadeias de conexão para o aplicativo do Azure, use o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell. No comando a seguir, substitua *\<app-name>* pelo nome do aplicativo, bem como o parâmetro *\<connection-string>* pela cadeia de conexão criada anteriormente.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection-string>' --connection-string-type SQLServer
```

Em seguida, defina a configuração de aplicativo `ASPNETCORE_ENVIRONMENT` como _Produção_. Essa configuração permite saber se o aplicativo está em execução no Azure, porque você usa o SQLite para o ambiente de desenvolvimento local e o Banco de Dados SQL para o ambiente do Azure.

O exemplo a seguir define uma configuração de aplicativo `ASPNETCORE_ENVIRONMENT` no aplicativo do Azure. Substitua o espaço reservado *\<app-name>* .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>Conectar-se ao Banco de Dados SQL em produção

No repositório local, abra Startup.cs e encontre o seguinte código:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Substitua-o pelo código a seguir, que usa as variáveis de ambiente configuradas anteriormente.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Se esse código detectar que ele está sendo executado em produção (que indica o ambiente do Azure), ele usará a cadeia de conexão configurada para se conectar ao Banco de Dados SQL. Para obter informações sobre como as configurações de aplicativo são acessadas no Serviço de Aplicativo, confira [Acessar as variáveis de ambiente](configure-language-dotnetcore.md#access-environment-variables).

A chamada `Database.Migrate()` ajuda você quando ele está em execução no Azure, porque ela cria automaticamente os bancos de dados necessários para o aplicativo .NET Core, de acordo com sua configuração de migração.

Salve suas alterações e depois confirme-as no repositório do Git.

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Navegar até o aplicativo do Azure

Navegue até o aplicativo implantado usando o navegador da Web.

```bash
http://<app-name>.azurewebsites.net
```

Adicione alguns itens de tarefas.

![aplicativo em execução no Serviço de Aplicativo no Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Parabéns!** Você está executando um aplicativo .NET Core controlado por dados no Serviço de Aplicativo no Linux.

## <a name="update-locally-and-redeploy"></a>Atualizar localmente e reimplantar

Nesta etapa, você faz uma alteração no esquema de banco de dados e publica-o no Azure.

### <a name="update-your-data-model"></a>Atualizar seu modelo de dados

Abra _Models\Todo.cs_ no editor de códigos. Adicione a seguinte propriedade à classe `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Executar Migrações do Code First localmente

Execute alguns comandos para fazer as atualizações para seu banco de dados local.

```bash
dotnet ef migrations add AddProperty
```

Atualize o banco de dados local:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Usar a nova propriedade

Faça algumas alterações em seu código para usar a propriedade `Done`. Para simplificar este tutorial, somente as exibições `Index` e `Create` serão alteradas para observar a propriedade em ação.

Abra _Controllers\TodosController.cs_.

Localize o `Create()` método e adicione `Done` à lista de propriedades no atributo `Bind`. Quando terminar, a assinatura do método `Create()` deverá ter o seguinte código:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Abra _Views\Todos\Create.cshtml_.

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

Abra _Views\Todos\Index.cshtml_.

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

No navegador, navegue até `http://localhost:5000/`. Agora você pode adicionar um item de tarefas e marcar **Concluído**. Em seguida, ele deverá aparecer na sua página inicial como um item concluído. Lembre-se de que a exibição `Edit` não mostra o campo `Done`, porque você não alterou a exibição `Edit`.

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

Quando o `git push` estiver concluído, navegue até o aplicativo do Azure e experimente a nova funcionalidade.

![Aplicativo do Azure após Migração do Code First](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Observe que todos os itens de tarefas existentes ainda são exibidos. Quando você republicar o aplicativo .NET Core, os dados existentes no Banco de Dados SQL não serão perdidos. Além disso, as Migrações do Entity Framework Core apenas alteram o esquema de dados e deixam os dados existentes intactos.

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

O projeto de exemplo já segue as diretrizes em [Registro do ASP.NET Core no Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) com duas alterações de configuração:

- Inclui uma referência a `Microsoft.Extensions.Logging.AzureAppServices` em *DotNetCoreSqlDb.csproj*.
- Chamadas `loggerFactory.AddAzureWebAppDiagnostics()` em *Startup.cs*.

> [!NOTE]
> O nível de log do projeto é definido como `Information` em *appsettings.json*.
>

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Para obter mais informações sobre como personalizar os logs do ASP.NET Core, veja [Efetuar logon no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Gerenciar o aplicativo do Azure

Acesse o [portal do Azure](https://portal.azure.com) para ver o aplicativo que você criou.

No menu à esquerda, clique em **Serviços de Aplicativos** e, em seguida, clique no nome do seu aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Por padrão, o portal mostra a página **Visão Geral** do aplicativo. Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Página Serviço de Aplicativo no portal do Azure](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

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
> [Tutorial: Mapear o nome DNS personalizado para seu aplicativo](../app-service-web-tutorial-custom-domain.md)

Se preferir, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar o aplicativo ASP.NET Core](configure-language-dotnetcore.md)
