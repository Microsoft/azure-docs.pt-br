---
title: PHP (Laravel) com MySQL – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como fazer com que um aplicativo PHP funcione no Azure com conexão a um banco de dados MySQL. O Laravel é usado no tutorial.
services: app-service\web
documentationcenter: php
author: cephalin
manager: erikre
editor: ''
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: tutorial
ms.date: 11/15/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: eddccc9897380e3ff47de49771a617bf6cacc407
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66138354"
---
# <a name="tutorial-build-a-php-and-mysql-app-in-azure"></a>Tutorial: Criar um aplicativo Web PHP e MySQL no Azure

> [!NOTE]
> Este artigo implanta um aplicativo no Serviço de Aplicativo no Windows. Para implantar no Serviço de Aplicativo no _Linux_, confira [Criar um aplicativo PHP e MySQL no Serviço de Aplicativo do Azure no Linux](./containers/tutorial-php-mysql-app.md).
>

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este tutorial mostra como criar um aplicativo PHP no Azure e conectá-lo a um banco de dados MySQL. Quando terminar, você terá um aplicativo [Laravel](https://laravel.com/) em execução no Serviço de Aplicativo do Azure.

![Aplicativo PHP em execução no Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um banco de dados MySQL no Azure
> * Conectar um aplicativo PHP ao MySQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Instalar o Git](https://git-scm.com/)
* [Instalar o PHP 5.6.4 ou posterior](https://php.net/downloads.php)
* [Instalar o Composer](https://getcomposer.org/doc/00-intro.md)
* Habilite as seguintes extensões de PHP de que o Laravel precisa: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
* [Instalar e iniciar o MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

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

Na janela do terminal, `cd` para um diretório de trabalho.

Execute o comando a seguir para clonar o repositório de exemplo.

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

Na raiz do repositório, crie um arquivo de texto chamado *.env*. Copie as variáveis a seguir para o arquivo *.env*. Substitua o espaço reservado _&lt;root_password>_ pela senha do usuário raiz do MySQL.

```
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

![O PHP se conecta com êxito ao MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Para parar o servidor PHP, digite `Ctrl + C` no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Criar o MySQL no Azure

Nesta etapa, você cria um banco de dados MySQL no [Banco de Dados do Azure para MySQL](/azure/mysql). Posteriormente, você configura o aplicativo PHP para se conectar a esse banco de dados.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Criar um servidor MySQL

No Cloud Shell, crie um servidor no Banco de Dados do Azure para MySQL com o comando [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create).

No comando a seguir, substitua o espaço reservado  *\<mysql_server_name >* por um nome de servidor exclusivo, *\<admin_user>* por um nome de usuário e *\<admin_password>* por uma senha. O nome do servidor é usado como parte de seu ponto de extremidade do MySQL (`https://<mysql_server_name>.mysql.database.azure.com`) e, portanto, precisa ser exclusivo entre todos os servidores no Azure.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name> --location "West Europe" --admin-user <admin_user> --admin-password <admin_password> --sku-name B_Gen5_1
```

> [!NOTE]
> Como há várias credenciais para considerar neste tutorial, para evitar confusão, `--admin-user` e `--admin-password` são definidos como valores fictícios. Em um ambiente de produção, siga as práticas recomendadas de segurança ao escolher um bom nome de usuário e uma senha para o servidor MySQL no Azure.
>
>

Quando o servidor MySQL for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "location": "westeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  ...   +  
  -  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Configurar o firewall do servidor

No Cloud Shell, crie uma regra de firewall para o servidor MySQL para permitir conexões de cliente usando o comando [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create). Quando o IP inicial e o IP final estiverem definidos como 0.0.0.0, o firewall estará aberto somente para outros recursos do Azure. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Você pode ser ainda mais restritivo na regra de firewall ao [usar somente os endereços de IP de saída que seu aplicativo usa](overview-inbound-outbound-ips.md#find-outbound-ips).
>

No Cloud Shell, execute o comando novamente para permitir acesso do seu computador local, substituindo  *\<you_ip_address>* por [seu endereço IP IPv4 local](https://www.whatsmyip.org/).

```azurecli-interactive
az mysql server firewall-rule create --name AllowLocalClient --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address>
```

### <a name="connect-to-production-mysql-server-locally"></a>Conectar-se ao servidor MySQL de produção localmente

Na janela do terminal local, conecte-se ao servidor MySQL no Azure. Use o valor especificado anteriormente para _&lt;mysql_server_name>_ . Quando for solicitada uma senha, use a que você especificou quando criou o banco de dados no Azure.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Criar um banco de dados de produção

No prompt `mysql`, crie um banco de dados.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Criar um usuário com permissões

Crie um usuário de banco de dados chamado _phpappuser_ e conceda a ele todos os privilégios no banco de dados `sampledb`. Novamente, para manter a simplicidade do tutorial, use _MySQLAzure2017_ como senha.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Saia da conexão do servidor digitando `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Conectar o aplicativo ao MySQL do Azure

Nesta etapa, você conecta o aplicativo PHP ao banco de dados MySQL criado no Banco de Dados do Azure para MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurar a conexão de banco de dados

Na raiz do repositório, crie um arquivo _.env.production_ e copie as variáveis a seguir para ele. Substitua o espaço reservado _&lt;mysql_server_name>_ em *DB_HOST* e em *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Salve as alterações.

> [!TIP]
> Para proteger as informações de conexão do MySQL, esse arquivo já foi excluído do repositório Git (consulte _.gitignore_ na raiz do repositório). Posteriormente, você aprende a configurar variáveis de ambiente no Serviço de Aplicativo para se conectar ao banco de dados no Banco de Dados do Azure para MySQL. Com variáveis de ambiente, você não precisa do arquivo *.env* no Serviço de Aplicativo.
>

### <a name="configure-ssl-certificate"></a>Configurar o certificado SSL

Por padrão, o Banco de Dados do Azure para MySQL impõe conexões SSL de clientes. Para se conectar ao seu banco de dados MySQL no Azure, você deve usar o certificado [ _.pem_ fornecido pelo Banco de Dados do Azure para MySQL](../mysql/howto-configure-ssl.md).

Abra _config/database.php_ e adicione os parâmetros `sslmode` e `options` a `connections.mysql`, conforme mostra o código a seguir.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

O certificado `BaltimoreCyberTrustRoot.crt.pem` é fornecido no repositório para facilitar este tutorial. 

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

![O PHP se conecta com êxito ao Banco de Dados do Azure para MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

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

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Criar um aplicativo Web

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

### <a name="configure-database-settings"></a>Definir configurações de banco de dados

Conforme mencionado anteriormente, é possível se conectar ao banco de dados MySQL do Azure usando variáveis de ambiente no Serviço de Aplicativo.

No Cloud Shell, defina as variáveis de ambiente como _configurações do aplicativo_ usando o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set).

O comando a seguir define as configurações do aplicativo `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Substitua os espaços reservados _&lt;appname>_ e _&lt;mysql_server_name>_ .

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
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

No Cloud Shell, defina a chave de aplicativo no aplicativo do Serviço de Aplicativo usando o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set). Substitua os espaços reservados _&lt;appname>_ e _&lt;outputofphpartisankey:generate>_ .

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` instrui o Laravel a retornar informações de depuração quando o aplicativo implantado encontra erros. Ao executar um aplicativo de produção, defina-o como `false`, que é mais seguro.

### <a name="set-the-virtual-application-path"></a>Definir o caminho do aplicativo virtual

Defina o caminho do aplicativo virtual do aplicativo. Esta etapa é necessária porque o [ciclo de vida do aplicativo do Laravel](https://laravel.com/docs/5.4/lifecycle) começa no diretório _public_, em vez de no diretório raiz do aplicativo. Outras estruturas PHP cujo ciclo de vida começa no diretório raiz podem funcionar sem a configuração manual do caminho do aplicativo virtual.

No Cloud Shell, defina o caminho do aplicativo virtual usando o comando [`az resource update`](/cli/azure/resource#az-resource-update). Substitua o espaço reservado _&lt;appname>_ .

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Por padrão, o Serviço de Aplicativo do Azure aponta o caminho do aplicativo virtual raiz ( _/_ ) para o diretório raiz dos arquivos de aplicativo implantados (_sites\wwwroot_).

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

> [!NOTE]
> Você observará que o processo de implantação instala pacotes do [Composer](https://getcomposer.org/) no final. O Serviço de Aplicativo não executa essas automações durante a implantação padrão, de modo que esse repositório de exemplo tem três arquivos adicionais em seu diretório raiz para habilitá-las:
>
> - `.deployment` - Esse arquivo informa o Serviços de Aplicativo para executar `bash deploy.sh` como o script de implantação personalizada.
> - `deploy.sh` - O script de implantação personalizada. Se examinar o arquivo, você verá que ele executa `php composer.phar install` após `npm install`.
> - `composer.phar` – O gerenciador de pacotes do Composer.
>
> É possível usar essa abordagem para adicionar qualquer etapa à implantação base em Git no Serviço de Aplicativo. Para obter mais informações, consulte [Script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

### <a name="browse-to-the-azure-app"></a>Navegar até o aplicativo do Azure

Navegue até `http://<app_name>.azurewebsites.net` e adicione algumas tarefas à lista.

![Aplicativo PHP em execução no Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

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

Abra o arquivo *resources/views/tasks.blade.php*. Procure a marcação de abertura `<tr>` e a substitua por:

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

![Caixa de seleção adicionada à tarefa](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

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
git push azure master
```

Quando `git push` for concluído, navegue até o aplicativo do Azure e teste a nova funcionalidade.

![Alterações de banco de dados e modelos publicadas no Azure](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Se você tiver adicionado tarefas, elas serão retidas no banco de dados. As atualizações no esquema de dados deixam os dados existentes intactos.

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

Embora o aplicativo PHP seja executado no Serviço de Aplicativo do Azure, é possível fazer com que os logs do console sejam redirecionados para o terminal. Dessa forma, é possível obter as mesmas mensagens de diagnóstico para ajudá-lo a depurar erros de aplicativo.

Para iniciar o streaming de log, use o comando [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) no Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Depois que o streaming de log for iniciado, atualize o aplicativo do Azure no navegador para obter parte do tráfego da Web. Agora é possível ver os logs do console redirecionados para o terminal. Se você não vir os logs do console imediatamente, verifique novamente após 30 segundos.

Para interromper o streaming de log a qualquer momento, digite `Ctrl`+`C`.

> [!TIP]
> Um aplicativo PHP pode usar o [error_log()](https://php.net/manual/function.error-log.php) padrão para gerar a saída para o console. O aplicativo de exemplo usa essa abordagem em _app/Http/routes.php_.
>
> Como uma estrutura da Web, o [Laravel usa o Monolog](https://laravel.com/docs/5.4/errors) como provedor de logs. Para saber como fazer com que o Monolog produza mensagens para o console, confira [PHP: como usar o Monolog para registrar no console (php://out)](https://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out).
>
>

## <a name="manage-the-azure-app"></a>Gerenciar o aplicativo do Azure

Acesse o [portal do Azure](https://portal.azure.com) para gerenciar o aplicativo que você criou.

No menu à esquerda, clique em **Serviços de Aplicativos** e, em seguida, clique no nome do aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Você verá a página Visão geral do aplicativo. Aqui, você pode executar tarefas básicas de gerenciamento, como parar, iniciar, reiniciar, procurar e excluir.

O menu à esquerda fornece páginas para configurar o aplicativo.

![Página Serviço de Aplicativo no portal do Azure](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um banco de dados MySQL no Azure
> * Conectar um aplicativo PHP ao MySQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

Vá para o próximo tutorial para aprender a mapear um nome DNS personalizado para o aplicativo.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-domain.md)
