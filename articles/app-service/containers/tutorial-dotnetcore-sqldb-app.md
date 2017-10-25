---
title: "Criar um aplicativo Web .NET Core e do Banco de Dados SQL no Aplicativo Web para Contêineres do Azure | Microsoft Docs"
description: "Saiba como executar um aplicativo .NET Core no Aplicativo Web para Contêineres do Azure, com uma conexão a um Banco de Dados SQL."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/31/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 365747f9f9c765e8db1ab86946ba578c321ec732
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-net-core-and-sql-database-web-app-in-azure-web-app-for-containers"></a>Criar um aplicativo Web .NET Core e do Banco de Dados SQL no Aplicativo Web para Contêineres do Azure

O [Aplicativo Web para Contêineres](app-service-linux-intro.md) fornecem um serviço de hospedagem na Web altamente escalonável e com aplicação de patch automática no sistema operacional Linux. Este tutorial mostra como criar um aplicativo Web .NET Core e conectá-lo a um Banco de Dados SQL. Quando terminar, você terá um aplicativo MVC .NET Core em execução no Aplicativo Web para Contêineres. 

![aplicativo em execução no Aplicativo Web para Contêineres](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

Você aprenderá a:

> [!div class="checklist"]
> * Criar um Banco de Dados SQL no Azure
> * Conectar um aplicativo .NET Core ao Banco de Dados SQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instalar o Git](https://git-scm.com/)
1. [Instalar o SDK 1.1.2 do .NET Core](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.1.2-download.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-local-net-core-app"></a>Criar um aplicativo .NET Core local

Nesta etapa, você configura o projeto .NET Core local.

### <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Na janela do terminal, `cd` para um diretório de trabalho.  

Execute os comandos a seguir para clonar o repositório de exemplo e alterar sua raiz.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Esse projeto de exemplo contém um aplicativo CRUD (create-read-update-delete) básico usando o [Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/).

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

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-sql-database-logical-server"></a>Criar um servidor lógico do Banco de Dados SQL

No Cloud Shell, crie um servidor lógico do Banco de Dados SQL com o comando [az sql server create](/cli/azure/sql/server#create).

Substitua o espaço reservado *\<server_name>* por um nome exclusivo do Banco de Dados SQL. Esse nome é usado como parte do ponto de extremidade do Banco de Dados SQL, `<server_name>.database.windows.net` e, portanto, o nome precisa ser exclusivo em todos os servidores lógicos no Azure. O nome deve conter somente letras minúsculas, números e o caractere hífen (-), e deve ter entre 3 e 50 caracteres. Além disso, substitua *\<db_username>* e *\<db_password>* por um nome de usuário e uma senha de sua escolha. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

Quando o servidor lógico do Banco de Dados SQL é criado, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall de servidor

Crie uma [ regra de firewall no nível de servidor de banco de dados SQL do Azure](../../sql-database/sql-database-firewall-configure.md) usando o comando [az sql server firewall create](/cli/azure/sql/server#create). Quando o IP inicial e o IP final estiverem definidos como 0.0.0.0, o firewall estará aberto somente para outros recursos do Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Criar um banco de dados

Crie um banco de dados com [nível de desempenho S0](../../sql-database/sql-database-service-tiers.md) no servidor usando o comando [az sql db create](/cli/azure/sql/db#create). 

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Criar uma cadeia de conexão

Substitua a cadeia de caracteres a seguir pelos *\<server_name>*, *\<db_username>* e *\<db_password>* usados anteriormente.

```
Server=tcp:<server_name>.database.windows.net,1433;Initial Catalog=coreDB;Persist Security Info=False;User ID=<db_username>;Password=<db_password>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Esta é a cadeia de conexão do aplicativo .NET Core. Copie-a para uso posterior.

## <a name="deploy-app-to-azure"></a>Implantar o aplicativo no Azure

Nesta etapa, você implanta seu aplicativo .NET Core conectado ao Banco de Dados SQL no Aplicativo Web para Contêineres.

### <a name="configure-local-git-deployment"></a>Configurar a implantação do git local 

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Criar um aplicativo Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-linux-dotnetcore-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Configurar um variável de ambiente

Para definir as cadeias de conexão para o aplicativo do Azure, use o comando [az webapp config appsettings update](/cli/azure/webapp/config/appsettings#update) no Cloud Shell. No comando a seguir, substitua *\<app name>*, bem como o parâmetro *\<connection_string>*, pela cadeia de conexão criada anteriormente.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection_string>' --connection-string-type SQLServer
```

Em seguida, defina a configuração de aplicativo `ASPNETCORE_ENVIRONMENT` como _Produção_. Essa configuração permite saber se o aplicativo está em execução no Azure, porque você usa o SQLLite para o ambiente de desenvolvimento local e o Banco de Dados SQL para o ambiente do Azure. 

O exemplo a seguir permite definir uma configuração de aplicativo `ASPNETCORE_ENVIRONMENT` no seu aplicativo Web do Azure. Substitua o espaço reservado *\<app_name>*.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>Conectar-se ao Banco de Dados SQL em produção

No repositório local, abra Startup.cs e encontre o seguinte código:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Substitua-o pelo código a seguir, que usa as variáveis de ambiente configuradas anteriormente.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<DotNetCoreSqlDbContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<DotNetCoreSqlDbContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<DotNetCoreSqlDbContext>().Database.Migrate();
```

Se esse código detectar que ele está sendo executado em produção (que indica o ambiente do Azure), ele usará a cadeia de conexão configurada para se conectar ao Banco de Dados SQL. 

A chamada `Database.Migration()` ajuda você quando ele está em execução no Azure, porque ela cria automaticamente os bancos de dados necessários para o aplicativo .NET Core, de acordo com sua configuração de migração. 

Salve suas alterações.

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
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

### <a name="browse-to-the-azure-web-app"></a>Navegar até o aplicativo Web do Azure 

Navegue até o aplicativo Web implantado usando o navegador da Web. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Adicione alguns itens de tarefas.

![aplicativo em execução no Aplicativo Web para Contêineres](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Parabéns!** Você está executando um aplicativo .NET Core controlado por dados no Aplicativo Web para Contêineres.

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
    @Html.DisplayFor(modelItem => item.CreatedDate)
</td>
```

Isso é tudo o que você precisa para ver as alterações nas exibições `Index` e `Create`. 

### <a name="test-your-changes-locally"></a>Testar suas alterações localmente

Execute o aplicativo localmente.

```
dotnet run
```

No navegador, navegue até `http://localhost:5000/`. Agora você pode adicionar um item de tarefas e marcar **Concluído**. Em seguida, ele deverá aparecer na sua página inicial como um item concluído. Lembre-se de que a exibição `Edit` não mostra o campo `Done`, porque você não alterou a exibição `Edit`.

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

```bash

git commit -am "added done field"
git push azure master
```

Quando `git push` estiver completo, navegue até seu aplicativo Web do Azure e tente a nova funcionalidade.

![Aplicativo Web do Azure após Migração do Code First Migration](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Observe que todos os itens de tarefas existentes ainda são exibidos. Quando você republicar o aplicativo .NET Core, os dados existentes no Banco de Dados SQL não serão perdidos. Além disso, as Migrações do Entity Framework Core apenas alteram o esquema de dados e deixam os dados existentes intactos.

## <a name="manage-your-azure-web-app"></a>Gerenciar seu aplicativo Web do Azure

Vá para o [portal do Azure](https://portal.azure.com) para ver o aplicativo Web que você criou.

No menu à esquerda, clique em **Serviço de Aplicativos** e, em seguida, clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Por padrão, o portal mostra a página **Visão geral** do seu aplicativo Web . Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

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

Vá para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo Web.

> [!div class="nextstepaction"] 
> [Mapear um nome DNS personalizado existente para aplicativos Web do Azure](../app-service-web-tutorial-custom-domain.md)
