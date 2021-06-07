---
title: 'Tutorial: Compilar um aplicativo PHP (Laravel) com o Servidor Flexível do Banco de Dados do Azure para MySQL'
description: Este tutorial explica como criar um aplicativo PHP com um servidor flexível.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: tutorial
ms.devlang: php
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bb38b72af6e7c649c0904c41d3052b15a4c36955
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770049"
---
# <a name="tutorial-build-a-php-laravel-and-mysql-flexible-server-preview-app-in-azure-app-service"></a>Tutorial: Compilar um aplicativo PHP (Laravel) e Servidor Flexível MySQL (versão prévia) no Serviço de Aplicativo do Azure


:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Aplicativo Web PHP no Azure com Servidor Flexível":::

O [Serviço de Aplicativo do Azure](../../app-service/overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches usando o sistema operacional Linux. Este tutorial mostra como criar um aplicativo PHP no Azure e conectá-lo a um banco de dados MySQL. Quando terminar, você terá um aplicativo [Laravel](https://laravel.com/) em execução no Serviço de Aplicativo do Azure no Linux.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Configurar um aplicativo PHP (Laravel) com o MySQL local
> * Criar um Servidor Flexível do MySQL (versão prévia)
> * Conectar um aplicativo PHP ao Servidor Flexível do MySQL (versão prévia)
> * Implantar o aplicativo no Serviço de Aplicativo do Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Gerenciar o aplicativo no portal do Azure

Se você não tiver uma [assinatura do Azure](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar o PHP 5.6.4 ou posterior](https://php.net/downloads.php)
3. [Instalar o Composer](https://getcomposer.org/doc/00-intro.md)
4. Habilite as seguintes extensões de PHP de que o Laravel precisa: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
5. [Instalar e iniciar o MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html)

## <a name="prepare-local-mysql"></a>Preparar o MySQL local

Nesta etapa, você cria um banco de dados em seu servidor MySQL local para uso neste tutorial.

### <a name="connect-to-local-mysql-server"></a>Conectar ao servidor MySQL local

Em uma janela de terminal, conecte-se ao servidor MySQL local. Você pode usar essa janela do terminal para executar todos os comandos deste tutorial.

```bash
mysql -u root -p
```

Se for solicitada uma senha, insira a senha da conta `root`. Caso não se lembre da senha de sua conta raiz, confira [MySQL: como redefinir a senha raiz](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Se o comando for executado com êxito, o MySQL Server estará em execução. Caso contrário, veja se o servidor MySQL local foi iniciado seguindo as [Etapas pós-instalação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database-locally"></a>Criar um banco de dados localmente

No prompt `mysql`, crie um banco de dados.

```sql
CREATE DATABASE sampledb;
```

Saia da conexão do servidor digitando `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Criar um aplicativo PHP localmente
Nesta etapa, você obtém um aplicativo de exemplo Laravel, configura sua conexão de banco de dados e o executa localmente.

### <a name="clone-the-sample"></a>Clonar o exemplo

Na janela do terminal, navegue até um diretório vazio no qual você pode clonar o aplicativo de exemplo.  Execute o comando a seguir para clonar o repositório de exemplo.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` para o diretório clonado.
Instale os pacotes necessários.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Configurar a conexão do MySQL

Na raiz do repositório, crie um arquivo chamado *.env*. Copie as variáveis a seguir para o arquivo *.env*. Substitua o espaço reservado _&lt;root_password>_ pela senha do usuário raiz do MySQL.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Para obter informações sobre como o Laravel usa o arquivo _.env_, consulte [Configuração de ambiente do Laravel](https://laravel.com/docs/5.4/configuration#environment-configuration).

### <a name="run-the-sample-locally"></a>Executar o exemplo localmente

Execute [migrações de banco de dados do Laravel](https://laravel.com/docs/5.4/migrations) para criar as tabelas necessárias para o aplicativo. Para ver quais tabelas são criadas nas migrações, examine o diretório _database/migrations_ no repositório Git.

```bash
php artisan migrate
```

Gere uma nova chave de aplicativo do Laravel.

```bash
php artisan key:generate
```

Execute o aplicativo.

```bash
php artisan serve
```

Navegue até `http://localhost:8000` em um navegador. Adicione algumas tarefas à página.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="O PHP se conecta com êxito ao MySQL":::

Para interromper o PHP, digite `Ctrl + C` no terminal.

## <a name="create-a-mysql-flexible-server-preview"></a>Criar um Servidor Flexível do MySQL (versão prévia)
Nesta etapa, você cria um banco de dados MySQL no [Servidor Flexível do Banco de Dados do Azure para MySQL](../index.yml) que está em versão prévia pública. Posteriormente, você configura o aplicativo PHP para se conectar a esse banco de dados. No [Azure Cloud Shell](../../cloud-shell/overview.md), crie um servidor com o comando [`az flexible-server create`](/cli/azure/mysql/server#az_mysql_flexible_server_create).

```azurecli-interactive
az mysql flexible-server create  --resource-group myResourceGroup --public-access <IP-Address>
```

> [!IMPORTANT]
> - Tome nota do **servername** e da **cadeia de conexão** a usar na próxima etapa para conectar e executar a migração de dados laravel.
> - Para o argumento **IP-address**, forneça o IP do computador cliente. O servidor é bloqueado quando criado e você precisa permitir o acesso ao computador cliente para gerenciar o servidor localmente.

### <a name="configure-server-firewall-to-allow-web-app-to-connect-to-the-server"></a>Configurar o firewall do servidor para permitir que o aplicativo Web se conecte ao servidor

No Cloud Shell, crie uma regra de firewall para o servidor MySQL para permitir conexões de cliente usando o comando az mysql server firewall-rule create. Quando tanto o IP inicial quanto o IP final são definidos como ```0.0.0.0```, o firewall é aberto somente para outros serviços do Azure que não têm um IP estático para se conectar ao servidor.

```azurecli
az mysql flexible-server firewall-rule create --name allanyAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>Conectar-se ao servidor MySQL de produção localmente

Na janela do terminal local, conecte-se ao servidor MySQL no Azure. Use o valor especificado anteriormente para ```<admin-user>``` e ```<mysql-server-name>```. Quando for solicitada uma senha, use a que você especificou quando criou o banco de dados no Azure.

```bash
mysql -u <admin-user> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Criar um banco de dados de produção

No prompt `mysql`, crie um banco de dados.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Criar um usuário com permissões

Crie um usuário de banco de dados chamado _phpappuser_ e conceda a ele todos os privilégios no banco de dados `sampledb`. Para manter a simplicidade do tutorial, use _MySQLAzure2020_ como senha.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2020';
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Saia da conexão do servidor digitando `quit`.

```sql
quit
```

## <a name="connect-app-to-mysql-flexible-server"></a>Conectar o aplicativo ao servidor flexível do MySQL

Nesta etapa, você conecta o aplicativo PHP ao banco de dados MySQL criado no Banco de Dados do Azure para MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurar a conexão de banco de dados

Na raiz do repositório, crie um arquivo _.env.production_ e copie as variáveis a seguir para ele. Substitua o espaço reservado _&lt;mysql-server-name>_ em *DB_HOST* e em *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Salve as alterações.

> [!TIP]
> Para proteger as informações de conexão do MySQL, esse arquivo já foi excluído do repositório Git (consulte _.gitignore_ na raiz do repositório). Posteriormente, você aprende a configurar variáveis de ambiente no Serviço de Aplicativo para se conectar ao banco de dados no Banco de Dados do Azure para MySQL. Com variáveis de ambiente, você não precisa do arquivo *.env* no Serviço de Aplicativo.
>

### <a name="configure-tlsssl-certificate"></a>Configurar o certificado TLS/SSL

Por padrão, o servidor flexível do MySQL impõe conexões TLS de clientes. Para se conectar ao seu banco de dados MySQL no Azure, você deve usar o certificado [ _.pem_ fornecido pelo Servidor Flexível do Banco de Dados do Azure para MySQL](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem). Baixe [este certificado](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) e coloque-o na pasta **SSL** na cópia local do repositório de aplicativos de exemplo.

Abra _config/database.php_ e adicione os parâmetros `sslmode` e `options` a `connections.mysql`, conforme mostra o código a seguir.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/DigiCertGlobalRootCA.crt.pem',
    ] : []
],
```

### <a name="test-the-application-locally"></a>Testar o aplicativo localmente

Execute migrações de banco de dados do Laravel com _.env.production_ como arquivo de ambiente para criar as tabelas em seu banco de dados MySQL no Banco de Dados do Azure para MySQL. Lembre-se de que _.env.production_ tem as informações de conexão ao banco de dados MySQL no Azure.

```bash
php artisan migrate --env=production --force
```

_.env.production_ ainda não tem uma chave de aplicativo válida. Gere uma nova chave de aplicativo para ele no terminal.

```bash
php artisan key:generate --env=production --force
```

Execute o aplicativo de exemplo com _.env.production_ como arquivo de ambiente.

```bash
php artisan serve --env=production
```

Navegue até `http://localhost:8000`. Se a página for carregada sem erros, o aplicativo PHP estará se conectando ao banco de dados MySQL no Azure.

Adicione algumas tarefas à página.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="O PHP se conecta com êxito ao Banco de Dados do Azure para MySQL":::

Para interromper o PHP, digite `Ctrl + C` no terminal.

### <a name="commit-your-changes"></a>Confirmar as alterações

Execute os seguintes comandos do Git para confirmar as alterações:

```bash
git add .
git commit -m "database.php updates"
```

O aplicativo está pronto para ser implantado.

## <a name="deploy-to-azure"></a>Implantar no Azure

Nesta etapa, você implanta o aplicativo PHP conectado ao MySQL no Serviço de Aplicativo do Azure.

### <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

O FTP e o Git local podem implantar em um aplicativo Web do Azure usando um usuário de implantação. Após configurar o usuário de implantação, use-o em todas as implantações do Azure. O nome de usuário e a senha da implantação no nível da conta são diferentes das credenciais de assinatura do Azure.

Para configurar o usuário de implantação, execute o comando [az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) no Azure Cloud Shell. Substitua _&lt;username>_ e _&lt;password>_ pelo nome de usuário e a senha do usuário de implantação.

O nome de usuário deve ser exclusivo no Azure. Para envios por push do Git local, não deve conter o símbolo "\@".
A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos.

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

A saída JSON mostra a senha como nulo. Se você receber um "Conflito". Detalhes: Erro 409, altere o nome de usuário. Se você receber uma "Solicitação inválida". Detalhes: Erro 400, use uma senha mais forte. **Registre seu nome de usuário e senha para usá-los na implantação de aplicativos Web.**

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

No Cloud Shell, crie um plano do Serviço de Aplicativo no grupo de recursos com o comando [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create). O exemplo a seguir cria um plano do Serviço de Aplicativo chamado myAppServicePlan no tipo de preço Gratuito (--sku F1) e em um contêiner do Linux (--is-linux).

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux

<a name="create"></a>

### <a name="create-a-web-app"></a>Criar um aplicativo Web

Crie um [aplicativo Web](../../app-service/overview.md#app-service-on-linux) no plano do Serviço de Aplicativo myAppServicePlan.

No Cloud Shell, você pode usar o comando [az webapp create](/cli/azure/webapp#az_webapp_create). No exemplo a seguir, substitua _&lt;app-name>_ por um nome do aplicativo exclusivo globalmente (os caracteres válidos são `a-z`, `0-9` e `-`). A execução é predefinida para `PHP|7.0`. Para ver todos os tempos de execução com suporte, execute [az webapp list-runtimes --linux](/cli/azure/webapp#az_webapp_list_runtimes).

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

Quando o aplicativo Web for criado, a CLI do Azure mostrará um resultado semelhante ao seguinte exemplo:

```
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Você criou um novo aplicativo Web vazio com a implantação do Git habilitada.

> [!NOTE]
> A URL do Git remoto é mostrada na propriedade deploymentLocalGitUrl, com o formato https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git. Salve essa URL, pois você precisará dela mais tarde.

### <a name="configure-database-settings"></a>Definir configurações de banco de dados

No Serviço de Aplicativo, defina as variáveis de ambiente como _configurações do aplicativo_ usando o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set).

O comando a seguir define as configurações do aplicativo `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Substitua os espaços reservados _&lt;app-name>_ e _&lt;mysql-server-name>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

É possível usar o método [getenv](https://www.php.net/manual/en/function.getenv.php) do PHP para acessar as configurações. O código do Laravel usa um wrapper [env](https://laravel.com/docs/5.4/helpers#method-env) em torno do `getenv` do PHP. Por exemplo, a configuração do MySQL em _config/database.php_ é parecida com o seguinte código:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Configurar variáveis de ambiente do Laravel

O Laravel precisa de uma chave de aplicativo no Serviço de Aplicativo. É possível configurá-la com as configurações do aplicativo.

Na janela do terminal local, use `php artisan` para gerar uma nova chave de aplicativo sem salvá-la em _.env_.

```bash
php artisan key:generate --show
```

No Cloud Shell, defina a chave de aplicativo no aplicativo do Serviço de Aplicativo usando o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set). Substitua os espaços reservados _&lt;app-name>_ e _&lt;outputofphpartisankey:generate>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` instrui o Laravel a retornar informações de depuração quando o aplicativo implantado encontra erros. Ao executar um aplicativo de produção, defina-o como `false`, que é mais seguro.

### <a name="set-the-virtual-application-path"></a>Definir o caminho do aplicativo virtual

O [ciclo de vida do aplicativo Laravel](https://laravel.com/docs/5.4/lifecycle) começa no diretório _public_, e não no diretório raiz do aplicativo. A imagem do Docker do PHP padrão para o Serviço de Aplicativo usa Apache, e não permite a personalização do `DocumentRoot` para Laravel. No entanto, você pode usar `.htaccess` para reescrever todas as solicitações a fim de apontar para _/public_ em vez do diretório raiz. Na raiz do repositório, já há um `.htaccess` para essa finalidade. Com ele, seu aplicativo Laravel está pronto para ser implantado.

Para obter mais informações, confira [Alterar raiz do site](../../app-service/configure-language-php.md?pivots=platform-linux#change-site-root).

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

De volta na janela do terminal local, adicione um remoto do Azure ao repositório Git local. Substitua _&lt;deploymentLocalGitUrl-from-create-step>_ pela URL do Git remoto que você salvou de [Criar um aplicativo Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Envie por push para o Azure remoto para implantar seu aplicativo com o comando a seguir. Quando solicitado a fornecer credenciais pelo Gerenciador de Credenciais do Git, insira as credenciais criadas em **Configurar um usuário de implantação**, não as credenciais usadas para entrar no portal do Azure.

```bash
git push azure main
```

Esse comando pode demorar um pouco para ser executado. Na execução, ele exibe informações semelhantes ao seguinte exemplo:

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Navegar até o aplicativo do Azure

Navegue até `http://<app-name>.azurewebsites.net` e adicione algumas tarefas à lista.

:::image type="content" source="media/tutorial-php-database-app/php-mysql-in-azure.png" alt-text="Aplicativo Web PHP no Azure":::

Parabéns! Você está executando um aplicativo PHP controlado por dados no Serviço de Aplicativo do Azure.

## <a name="update-model-locally-and-redeploy"></a>Atualizar o modelo localmente e reimplantar

Nesta etapa, você faz uma alteração simples no modelo de dados `task` e no aplicativo Web e, em seguida, publica a atualização no Azure.

Para o cenário de tarefas, modifique o aplicativo para que você possa marcar uma tarefa como concluída.

### <a name="add-a-column"></a>Adicionar uma coluna

Na janela do terminal local, navegue para a raiz do repositório Git.

Gere uma nova migração de banco de dados para a tabela `tasks`:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Esse comando exibe o nome do arquivo de migração gerado. Localize esse arquivo em _database/migrations_ e abra-o.

Substitua o método `up` pelo seguinte código:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

O código anterior adiciona uma coluna booliana à tabela `tasks` chamada `complete`.

Substitua o método `down` pelo seguinte código para a ação de reversão:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

Na janela do terminal local, execute migrações de banco de dados do Laravel para fazer a alteração no banco de dados local.

```bash
php artisan migrate
```

Com base na [convenção de nomenclatura do Laravel](https://laravel.com/docs/5.4/eloquent#defining-models), o modelo `Task` (consulte _app/Task.php_) é mapeado para a tabela `tasks` por padrão.

### <a name="update-application-logic"></a>Atualizar a lógica do aplicativo

Abra o arquivo *routes/web.php*. O aplicativo define suas rotas e sua lógica de negócios aqui.

No final do arquivo, adicione uma rota com o código a seguir:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

O código anterior faz uma atualização simples no modelo de dados ativando/desativando o valor de `complete`.

### <a name="update-the-view"></a>Atualizar a exibição

Abra o arquivo *resources/views/tasks.blade.php*. Encontre a marcação de abertura `<tr>` e a substitua por:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

O código anterior altera a cor da linha dependendo se a tarefa foi concluída.

Na linha seguinte, você tem o código a seguir:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Substitua a linha inteira pelo código a seguir:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

O código anterior adiciona o botão Enviar que referencia a rota definida anteriormente.

### <a name="test-the-changes-locally"></a>Testar as alterações localmente

Na janela do terminal local, execute o servidor de desenvolvimento pelo diretório raiz do repositório Git.

```bash
php artisan serve
```

Para ver a alteração de status da tarefa, navegue para `http://localhost:8000` e marque a caixa de seleção.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox.png" alt-text="Caixa de seleção adicionada à tarefa":::

Para interromper o PHP, digite `Ctrl + C` no terminal.

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

Na janela do terminal local, execute migrações de banco de dados do Laravel com a cadeia de conexão de produção para fazer a alteração no banco de dados do Azure.

```bash
php artisan migrate --env=production --force
```

Confirme todas as alterações no Git e, em seguida, envie as alterações de código por push para o Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure main
```

Quando `git push` for concluído, navegue até o aplicativo do Azure e teste a nova funcionalidade.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Alterações de banco de dados e modelos publicadas no Azure":::

Se você tiver adicionado tarefas, elas serão retidas no banco de dados. As atualizações no esquema de dados deixam os dados existentes intactos.

## <a name="clean-up-resources"></a>Limpar os recursos
Nas etapas anteriores, você criou os recursos do Azure em um grupo de recursos. Se você acha que não precisará desses recursos no futuro, exclua o grupo de recursos executando o seguinte comando no Cloud Shell:

```bash
az group delete --name myResourceGroup
```

<a name="next"></a>

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como gerenciar seus recursos no portal do Azure](../../azure-resource-manager/management/manage-resources-portal.md) <br/>
> [!div class="nextstepaction"]
> [Como gerenciar seu servidor](how-to-manage-server-cli.md)
