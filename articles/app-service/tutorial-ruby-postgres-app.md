---
title: 'Tutorial: Aplicativo Ruby do Linux com o Postgres'
description: Saiba como executar um aplicativo Ruby do Linux no Serviço de Aplicativo do Azure, com uma conexão com um banco de dados PostgreSQL no Azure. O Rails é usado no tutorial.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/18/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: 63194ab87e0f2228b8585e962394aa1ebfff48d6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767216"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Criar um aplicativo Ruby e Postgres no Serviço de Aplicativo do Azure no Linux

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este tutorial mostra como criar um aplicativo Ruby e conectá-lo a um banco de dados PostgreSQL. Quando terminar, você terá um aplicativo [Ruby on Rails](https://rubyonrails.org/) sendo executado no Serviço de Aplicativo no Linux.

:::image type="content" source="./media/tutorial-ruby-postgres-app/complete-checkbox-published.png" alt-text="Captura de tela de um exemplo de aplicativo Ruby on Rails intitulado Lista de Tarefas.":::

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um servidor de banco de dados PostgreSQL no Azure
> * Conectar um aplicativo Ruby on Rails para PostgreSQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Instalar o Git](https://git-scm.com/)
- [Instale o Ruby 2.6](https://www.ruby-lang.org/en/documentation/installation/)
- [Instalar o Ruby on Rails 5.1](https://guides.rubyonrails.org/v5.1/getting_started.html)
- [Instale e execute o PostgreSQL](https://www.postgresql.org/download/)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="prepare-local-postgres"></a>Preparar o Postgres local

Nesta etapa, você cria um banco de dados em seu servidor Postgres local para uso neste tutorial.

### <a name="connect-to-local-postgres-server"></a>Conectar ao servidor Postgres local

Abra a janela do terminal e execute `psql` para se conectar ao servidor Postgres local.

```bash
sudo -u postgres psql
```

Se sua conexão tiver êxito, o banco de dados Postgres já estará em execução. Caso contrário, verifique se o banco de dados Postgres local está iniciado seguindo as etapas em [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Digite `\q` para sair do cliente do Postgres. 

Crie um usuário do Postgres que pode criar bancos de dados executando o seguinte comando, usando seu nome de usuário do Linux conectado.

```bash
sudo -u postgres createuser -d <signed-in-user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Criar um aplicativo Ruby on Rails localmente
Nesta etapa, você obtém um aplicativo de exemplo Ruby on Rails, configura sua conexão de banco de dados e o executa localmente. 

### <a name="clone-the-sample"></a>Clonar o exemplo

Na janela do terminal, `cd` para um diretório de trabalho.

Execute o comando a seguir para clonar o repositório de exemplo.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd` para o diretório clonado. Instale os pacotes necessários.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>Executar o exemplo localmente

Execute as [migrações do Rails](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations) para criar as tabelas necessárias ao aplicativo. Para ver quais tabelas são criadas nas migrações, examine o diretório _db/migrate_ no repositório Git.

```bash
rake db:create
rake db:migrate
```

Executar o aplicativo.

```bash
rails server
```

Navegue até `http://localhost:3000` em um navegador. Adicione algumas tarefas à página.

![O Ruby on Rails se conecta com êxito ao Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

Para parar o servidor Ruby on Rails, digite `Ctrl + C` no terminal.

## <a name="create-postgres-in-azure"></a>Criar Postgres no Azure

Nesta etapa, você cria um banco de dados Postgres no [Banco de Dados do Azure para PostgreSQL](../postgresql/index.yml). Posteriormente, você configura o aplicativo Ruby on Rails para se conectar a esse banco de dados.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

## <a name="create-postgres-database-in-azure"></a>Criar um banco de dados Postgres no Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

Nesta seção, você criará um servidor e um Banco de Dados do Azure para PostgreSQL. Para começar, instale a extensão `db-up` com o seguinte comando:

```azurecli
az extension add --name db-up
```

Crie o banco de dados Postgres no Azure com o comando [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up), conforme mostrado no exemplo a seguir. Substitua *\<postgresql-name>* por um nome *exclusivo* (o ponto de extremidade do servidor é *https://\<postgresql-name>.postgres.database.azure.com*). Em *\<admin-username>* e *\<admin-password>* , especifique as credenciais para criar um usuário administrador para esse servidor Postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westeurope --server-name <postgresql-name> --database-name sampledb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Esse comando pode levar algum tempo porque está fazendo o seguinte:

- Criando um [grupo de recursos](../azure-resource-manager/management/overview.md#terminology) chamado `myResourceGroup` se ele não existir. Todo recurso do Azure precisa estar em um deles. `--resource-group` é opcional.
- Criando um servidor Postgres com o usuário administrativo.
- Criando um banco de dados `sampledb`.
- Permitindo o acesso do seu endereço IP local.
- Permitindo o acesso dos serviços do Azure.
- Criando um usuário de banco de dados com acesso a um banco de dados `sampledb`.

É possível executar todas as etapas separadamente com outros comandos `az postgres` e com `psql`, mas `az postgres up` executa todas elas em uma etapa para você.

Quando o comando for concluído, localize as linhas de saída com `Ran Database Query:`. Elas mostram o usuário do banco de dados criado para você, com o nome de usuário `root` e a senha `Sampledb1`. Você os usará posteriormente para conectar seu aplicativo ao banco de dados.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`, pode ser definido como qualquer uma das [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). É possível obter as regiões disponíveis para sua assinatura com o comando [`az account list-locations`](/cli/azure/account#az_account_list_locations). Para aplicativos de produção, coloque seu banco de dados e seu aplicativo na mesma localização.

## <a name="connect-app-to-azure-postgres"></a>Conectar o aplicativo ao Azure Postgres

Nesta etapa, você conecta o aplicativo Ruby on Rails ao banco de dados Postgres criado no Banco de Dados do Azure para PostgreSQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurar a conexão de banco de dados

No repositório, abra _config/database.yml_. Na parte inferior do arquivo, substitua as variáveis de produção pelo código a seguir. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Salve as alterações.

### <a name="test-the-application-locally"></a>Testar o aplicativo localmente

No terminal local, defina as seguintes variáveis de ambiente:

```bash
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=root@<postgres-server-name>
export DB_PASSWORD=Sampledb1
```

Execute migrações de banco de dados do Rails com os valores de produção que você acabou de configurar para criar as tabelas em seu banco de dados Postgres no Banco de Dados do Azure para PostgreSQL.

```bash
rake db:migrate RAILS_ENV=production
```

Ao executar no ambiente de produção, o aplicativo Rails precisa de ativos pré-compilados. Gere os ativos necessários com o seguinte comando:

```bash
rake assets:precompile
```

O ambiente de produção Rails também usa segredos para gerenciar a segurança. Gerar uma chave secreta.

```bash
rails secret
```

Salve a chave secreta para as respectivas variáveis usadas pelo ambiente de produção do Rails. Para sua conveniência, use a mesma chave para as duas variáveis.

```bash
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
```

Habilite o ambiente de produção do Rails para atender aos arquivos JavaScript e CSS.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Execute o aplicativo de exemplo no ambiente de produção.

```bash
rails server -e production
```

Navegue até `http://localhost:3000`. Se a página for carregada sem erros, o aplicativo Ruby on Rails estará se conectando ao banco de dados Postgres no Azure.

Adicione algumas tarefas à página.

![O Ruby on Rails se conecta com êxito ao Banco de Dados do Azure para PostgreSQL](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

Para parar o servidor Ruby on Rails, digite `Ctrl + C` no terminal.

### <a name="commit-your-changes"></a>Confirmar as alterações

Execute os seguintes comandos do Git para confirmar as alterações:

```bash
git add .
git commit -m "database.yml updates"
```

O aplicativo está pronto para ser implantado.

## <a name="deploy-to-azure"></a>Implantar no Azure

Nesta etapa, você implanta o aplicativo Rails conectado ao Postgres no Serviço de Aplicativo do Azure.

### <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar um aplicativo Web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Definir configurações de banco de dados

No Serviço de Aplicativo, defina as variáveis de ambiente como _configurações do aplicativo_ usando o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) no Cloud Shell.

O comando do Cloud Shell a seguir define as configurações do aplicativo `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Substitua os espaços reservados _&lt;appname>_ e _&lt;postgres-server-name>_ pelo nome do aplicativo e do servidor do Postgres.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="root@<postgres-server-name>" DB_PASSWORD="Sampledb1"
```

### <a name="configure-rails-environment-variables"></a>Configurar as variáveis de ambiente do Rails

No terminal local, [gere um novo segredo](configure-language-ruby.md#set-secret_key_base-manually) para o ambiente de produção do Rails no Azure.

```bash
rails secret
```

Configure as variáveis exigidas pelo ambiente de produção do Rails.

No comando do Cloud Shell a seguir, substitua os dois espaços reservados _&lt;output-of-rails-secret>_ pela nova chave secreta gerada no terminal local.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` pede ao contêiner Ruby padrão que pré-compile os ativos em cada implantação do Git. Para obter mais informações, confira [Pré-compilar ativos](configure-language-ruby.md#precompile-assets) e [Fornecer ativos estáticos](configure-language-ruby.md#serve-static-assets).

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

No terminal local, adicione um remoto do Azure ao repositório Git local.

```bash
git remote add azure <paste-copied-url-here>
```

Envie por push para o Azure remoto para implantar o Ruby no aplicativo Rails. Você deverá inserir a senha fornecida anteriormente como parte da criação do usuário de implantação.

```bash
git push azure main
```

Durante a implantação, o Serviço de Aplicativo do Azure comunica seu andamento com o Git.

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

:::image type="content" source="./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png" alt-text="Captura de tela do exemplo de aplicativo do Azure intitulado Tarefas mostrando tarefas adicionadas à lista.":::

Parabéns! Você está executando um aplicativo Ruby on Rails controlado por dados no Serviço de Aplicativo do Azure.

## <a name="update-model-locally-and-redeploy"></a>Atualizar o modelo localmente e reimplantar

Nesta etapa, você faz uma alteração simples no modelo de dados `task` e no aplicativo Web e, em seguida, publica a atualização no Azure.

Para o cenário de tarefas, modifique o aplicativo para que você possa marcar uma tarefa como concluída.

### <a name="add-a-column"></a>Adicionar uma coluna

No terminal, navegue para a raiz do repositório Git.

Gere uma nova migração que adiciona uma coluna booliana chamada `Done` à tabela `Tasks`:

```bash
rails generate migration AddDoneToTasks Done:boolean
```

Este comando gera um novo arquivo de migração no diretório _db/migrate_.


No terminal, execute migrações de banco de dados do Rails para fazer a alteração no banco de dados local.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Atualizar a lógica do aplicativo

Abra o arquivo *app/controllers/tasks_controller.rb*. Encontre a seguinte linha no final do arquivo:

```rb
params.require(:task).permit(:Description)
```

Modifique essa linha para incluir o novo parâmetro `Done`.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Atualizar os modos de exibição

Abra o arquivo *app/views/tasks/_form.html.erb*, que é o Formulário de edição.

Localize a linha `<%=f.error_span(:Description) %>` e insira o código a seguir diretamente abaixo dela:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Abra o arquivo *app/views/tasks/show.html.erb*, que é a página de Exibição de registro único. 

Localize a linha `<dd><%= @task.Description %></dd>` e insira o código a seguir diretamente abaixo dela:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Abra o arquivo *app/views/tasks/index.html.erb*, que é a página de Índice para todos os registros.

Localize a linha `<th><%= model_class.human_attribute_name(:Description) %></th>` e insira o código a seguir diretamente abaixo dela:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

No mesmo arquivo, localize a linha `<td><%= task.Description %></td>` e insira o código a seguir diretamente abaixo dela:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Testar as alterações localmente

No terminal local, execute o servidor Rails.

```bash
rails server
```

Para ver a alteração de status da tarefa, navegue até `http://localhost:3000` e adicione ou edite itens.

![Caixa de seleção adicionada à tarefa](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

Para parar o servidor Ruby on Rails, digite `Ctrl + C` no terminal.

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

No terminal, execute migrações de banco de dados do Rails para o ambiente de produção para fazer a alteração no banco de dados do Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Confirme todas as alterações no Git e, em seguida, envie as alterações de código por push para o Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure main
```

Quando `git push` for concluído, navegue até o aplicativo do Azure e teste a nova funcionalidade.

![Alterações de banco de dados e modelos publicadas no Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Se você tiver adicionado tarefas, elas serão retidas no banco de dados. As atualizações no esquema de dados deixam os dados existentes intactos.

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Gerenciar o aplicativo do Azure

Acesse o [portal do Azure](https://portal.azure.com) para gerenciar o aplicativo que você criou.

No menu à esquerda, clique em **Serviços de Aplicativos** e, em seguida, clique no nome do aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/tutorial-php-mysql-app/access-portal.png)

Você verá a página Visão geral do aplicativo. Aqui, você pode executar tarefas básicas de gerenciamento, como parar, iniciar, reiniciar, procurar e excluir.

O menu à esquerda fornece páginas para configurar o aplicativo.

![Página Serviço de Aplicativo no portal do Azure](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um banco de dados Postgres no Azure
> * Conectar um aplicativo Ruby on Rails para Postgres
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

Prossiga para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo.

> [!div class="nextstepaction"]
> [Tutorial: Mapear o nome DNS personalizado para seu aplicativo](app-service-web-tutorial-custom-domain.md)

Se preferir, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar o aplicativo Ruby](configure-language-ruby.md)
