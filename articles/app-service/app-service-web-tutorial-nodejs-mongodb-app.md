---
title: Compilar um aplicativo Web Node.js e MongoDB no Azure | Microsoft Docs
description: "Saiba como fazer com que um aplicativo Node.js funcione no Azure, com conexão a um BD Cosmos com uma cadeia de conexão MongoDB."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: d5b41faa28d9181b957ce346e25804e16d9303d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>Compilar um aplicativo Web Node.js e MongoDB no Azure

Os aplicativos Web do Azure fornecem um serviço de hospedagem na Web altamente escalonável, com aplicação automática de patches. Este tutorial mostra como criar um aplicativo Web Node.js no Azure e conectá-lo a um banco de dados MongoDB. Quando terminar, você terá um aplicativo MEAN (MongoDB, Express, AngularJS e Node.js) executando em [Serviço de Aplicativo do Azure](app-service-web-overview.md). Para simplificar, o aplicativo de exemplo usa a [estrutura Web MEAN.js](http://meanjs.org/).

![Aplicativo MEAN.js em execução no Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

O que você aprenderá:

> [!div class="checklist"]
> * Criar um banco de dados MongoDB no Azure
> * Conectar um aplicativo Node.js ao MongoDB
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instalar o Git](https://git-scm.com/)
1. [Instalar o Node.js e o NPM](https://nodejs.org/)
1. [Instale o Gulp.js](http://gulpjs.com/) (exigido pelo [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started))
1. [Instale e execute o MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb"></a>Testar o MongoDB local

Abra a janela do terminal e `cd` para o `bin` diretório da instalação do MongoDB. Você pode usar essa janela do terminal para executar todos os comandos deste tutorial.

Execute `mongo` no terminal para se conectar ao servidor do MongoDB local.

```bash
mongo
```

Se sua conexão tiver êxito, então seu banco de dados MongoDB já está sendo executado. Caso contrário, certifique-se de que seu banco de dados MongoDB local foi iniciado seguindo as etapas em [Instale o MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/). Com frequência, o MongoDB está instalado, mas você ainda precisa iniciá-lo executando `mongod`. 

Ao terminar o teste do seu banco de dados MongoDB, digite `Ctrl+C` no terminal. 

## <a name="create-local-nodejs-app"></a>Criar aplicativo Node.js local

Nessa etapa, você configura o projeto Node.js local.

### <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Na janela do terminal, `cd` para um diretório de trabalho.  

Execute o comando a seguir para clonar o repositório de exemplo. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Esse repositório de exemplo contém uma cópia do [repositório do MEAN.js](https://github.com/meanjs/mean). Ele é modificado para ser executado no Serviço de Aplicativo (para obter mais informações, consulte o [arquivo LEIAME](https://github.com/Azure-Samples/meanjs/blob/master/README.md) do repositório do MEAN.js).

### <a name="run-the-application"></a>Executar o aplicativo

Execute os seguintes comandos para instalar os pacotes necessários e iniciar o aplicativo.

```bash
cd meanjs
npm install
npm start
```

Quando o aplicativo estiver totalmente carregado, você verá algo semelhante à seguinte mensagem:

```
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Navegue até `http://localhost:3000` em um navegador. Clique em **Criar Conta** no menu superior e crie um usuário de teste. 

O aplicativo de exemplo MEAN.js armazena dados do usuário no banco de dados. Se tiver êxito na criação de um usuário e ao entrar, seu aplicativo estará gravando dados no banco de dados local MongoDB.

![O MEAN.js conecta-se com êxito ao MongoDB](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Selecione **Admin > Gerenciar Artigos** para adicionar alguns artigos.

Para parar o Node.js a qualquer momento, pressione `Ctrl+C` no terminal. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Criar MongoDB de produção

Ness etapa, você cria um banco de dados MongoDB no Azure. Quando seu aplicativo é implantado no Azure, ele usa esse banco de dados na nuvem.

Para o MongoDB, este tutorial usa o [BD Cosmos do Azure](/azure/documentdb/). O BD Cosmos dá suporte a conexões de cliente do MongoDB.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create).

[!INCLUDE [Resource group intro](../../includes/resource-group.md)]

O exemplo a seguir cria um grupo de recursos na região da Europa Ocidental.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Use o comando CLI do Azure [az appservice list-locations](/cli/azure/appservice#list-locations) para listar os locais disponíveis. 

### <a name="create-a-cosmos-db-account"></a>Criar uma conta do BD Cosmos

No Cloud Shell, crie uma conta do Cosmos DB com o comando [az cosmosdb create](/cli/azure/cosmosdb#create).

No comando a seguir, substitua um nome do Cosmos DB exclusivo quando vir o espaço reservado *\<cosmosdb_name>*. Esse nome exclusivo é usado como parte do seu ponto de extremidade do Cosmos DB, `https://<cosmosdb_name>.documents.azure.com/`, portanto, o nome precisa ser exclusivo em todas as contas do Cosmos DB no Azure. O nome deve conter somente letras minúsculas, números e o caractere hífen (-), e deve ter entre 3 e 50 caracteres.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

O parâmetro *--kind MongoDB* habilita conexões do cliente MongoDB.

Quando a conta do BD Cosmos é criada, a CLI do Azure mostra informações semelhantes ao exemplo a seguir:

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Conectar o aplicativo ao MongoDB de produção

Nesta etapa, você conecta o aplicativo de exemplo MEAN.js ao BD Cosmos que acabou de ser criado usando uma cadeia de conexão do MongoDB. 

### <a name="retrieve-the-database-key"></a>Recuperar a chave do banco de dados

Para se conectar ao BD Cosmos, você precisa da chave do banco de dados. No Cloud Shell, use o comando [az cosmosdb list-keys](/cli/azure/cosmosdb#list-keys) para recuperar a chave primária.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

A CLI do Azure apresenta as informações semelhantes ao seguinte exemplo:

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Copie o valor de `primaryMasterKey`. Essas informações serão necessárias na próxima etapa.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurar a cadeia de conexão em seu aplicativo Node.js

Em seu repositório do MEAN.js, abra _config/env/production.js_.

No objeto `db`, atualize o valor de `uri`:

* Substitua os dois espaços reservados  *\<cosmosdb_name >* com seu nome do banco de dados do Cosmos.
* Substitua o espaço reservado  *\<primary_master_key >* pela chave que você copiou na etapa anterior.

O código a seguir mostra o objeto `db`:

```javascript
db: {
  uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

A opção `ssl=true` é necessária porque o [Cosmos DB requer SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 

Salve suas alterações.

### <a name="test-the-application-in-production-mode"></a>Testar o aplicativo em modo de produção 

Execute o seguinte comando para reduzir e agrupar scripts para o ambiente de produção. Esse processo gera os arquivos necessários para o ambiente de produção.

```bash
gulp prod
```

Execute o seguinte comando para usar a cadeia de conexão configurada em _config/env/production.js_.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` define a variável de ambiente que informa o Node. js a ser executado no ambiente de produção.  `node server.js` inicia o servidor Node.js com `server.js` na raiz do repositório. É dessa forma que o aplicativo Node.js é carregado no Azure. 

Quando o aplicativo for carregado, verifique se ele está sendo executado no ambiente de produção:

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Navegue até `http://localhost:8443` em um navegador. Clique em **Criar Conta** no menu superior e crie um usuário de teste. Se tiver êxito na criação de um usuário e ao entrar, seu aplicativo estará gravando dados no banco de dados do Cosmos DB no Azure. 

No terminal, pare o Node.js digitando `Ctrl+C`. 

## <a name="deploy-app-to-azure"></a>Implantar o aplicativo no Azure

Nessa etapa, você implanta seu aplicativo Node.js conectado ao MongoDB no Serviço de Aplicativo do Azure.

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

No Cloud Shell, crie um plano do Serviço de Aplicativo com o comando [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

O exemplo a seguir cria um Plano do Serviço de Aplicativo chamado _myAppServicePlan_ usando o tipo de preço **GRATUITO**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Quando o Plano do Serviço de Aplicativo é criado, a CLI do Azure mostra informações semelhantes ao exemplo a seguir:

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
```

### <a name="create-a-web-app"></a>Criar um aplicativo Web

No Cloud Shell, crie um aplicativo Web no plano do `myAppServicePlan`Serviço de Aplicativo do com o comando [az webapp list-runtimes](/cli/azure/webapp#create). 

O aplicativo Web fornece um espaço de hospedagem para implantar seu código e fornecer uma URL para exibir o aplicativo implantado. Use para criar o aplicativo Web. 

No seguinte comando, substitua o espaço reservado *\<app_name>* por um nome de aplicativo exclusivo. Esse nome é usado como parte da URL padrão para o aplicativo Web, portanto, o nome precisa ser exclusivo em todos os aplicativos no Serviço de Aplicativo do Azure. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Quando o aplicativo Web tiver sido criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-an-environment-variable"></a>Configurar um variável de ambiente

Anteriormente no tutorial, você codificou a cadeia de conexão de banco de dados em _config/env/production.js_. De acordo com as melhores práticas de segurança, você deseja manter esses dados confidenciais fora do seu repositório Git. Para seu aplicativo em execução no Azure, você usará uma variável de ambiente.

No Cloud Shell, defina as variáveis de ambiente como _configurações do aplicativo_ usando o comando [az webapp config appsettings update](/cli/azure/webapp/config/appsettings#update). 

O exemplo a seguir permite definir uma configuração de aplicativo `MONGODB_URI` no seu aplicativo Web do Azure. Substitua os espaços reservados  *\<app_name >*,  *\<cosmosdb_name >* e  *\<primary_master_key >*.

```azurecli-interactive
az webapp config appsettings update --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

No código Node.js, você acessa essa configuração de aplicativo com `process.env.MONGODB_URI`, assim como você teria acesso a qualquer variável de ambiente. 

Agora, desfaça as alterações em _config/env/production.js_ com o seguinte comando:

```bash
git checkout -- .
```

Abra _config/env/production.js_ novamente. Observe que o aplicativo MEAN.js padrão já está configurado para usar a variável de ambiente `MONGODB_URI` que você criou.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="configure-local-git-deployment"></a>Configurar a implantação do git local 

No Cloud Shell, use o comando [az webapp deployment user set](/cli/azure/webapp/deployment/user#set) para criar credenciais de implantação.

É possível implantar seu aplicativo no Serviço de Aplicativo do Azure de várias maneiras, incluindo FTP, Git local, GitHub, Visual Studio Team Services e BitBucket. Para o FTP e o Git local, é necessário ter um usuário de implantação configurado no servidor para autenticar sua implantação. Este usuário de implantação está no nível de conta e é diferente da sua conta de assinatura do Azure. É necessário configurar esse usuário de implantação somente uma vez.

No comando a seguir, substitua *\<nome-de-usuário>* e *\<senha >* por um novo nome de usuário e senha. O nome do usuário deve ser exclusivo. A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos. Se receber um erro ` 'Conflict'. Details: 409`, altere o nome de usuário. Se receber um erro ` 'Bad Request'. Details: 400`, use uma senha mais forte.

```azurecli-interactive
az appservice web deployment user set --user-name <username> --password <password>
```

Registre o nome de usuário e senha para uso em etapas posteriores quando você implantar o aplicativo.

Use o comando [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git) para configurar o acesso do Git local ao aplicativo Web do Azure. 

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup
```

Quando o usuário de implantação é configurado, a CLI do Azure mostra a URL de implantação ao seu aplicativo Web Azure no seguinte formato:

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

Copie a saída do terminal, pois ela será usada na próxima etapa. 

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

Na janela do terminal local, adicione um remoto do Azure ao repositório Git local. 

```bash
git remote add azure <paste_copied_url_here> 
```

Envie para o Azure remoto para implantar seu aplicativo Node.js. Será solicitada a senha que você forneceu anteriormente como parte da criação do usuário de implantação. 

```bash
git push azure master
```

Durante a implantação, o Serviço de Aplicativo do Azure comunica seu andamento com o Git.

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

É possível notar que o processo de implantação executa [Gulp](http://gulpjs.com/) após `npm install`. O Serviço de Aplicativo não executa tarefas Gulp ou Grunt durante a implantação, portanto, esse repositório de exemplo possui dois arquivos adicionais em seu diretório raiz para habilitá-lo: 

- _.deployment_ – este arquivo instrui o Serviço de Aplicativo a executar `bash deploy.sh` como o script de implantação personalizado.
- _deploy.sh_ – o script de implantação personalizado. Se você revisar o arquivo, verá que ele executa `gulp prod` após `npm install` e `bower install`. 

É possível usar essa abordagem para adicionar qualquer etapa à implantação com base em Git. Se você reiniciar seu aplicativo Web do Azure em qualquer ponto, o Serviço de Aplicativo não executará novamente essas tarefas de automação.

### <a name="browse-to-the-azure-web-app"></a>Navegar até o aplicativo Web do Azure 

Navegue até o aplicativo Web implantado usando o navegador da Web. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Clique em **Criar Conta** no menu superior e crie um usuário fictício. 

Se você tiver êxito e o aplicativo entrar automaticamente no usuário criado, isso significa que seu aplicativo MEAN.js no Azure tem conectividade com o banco de dados MongoDB (Cosmos DB). 

![Aplicativo MEAN.js em execução no Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Selecione **Admin > Gerenciar Artigos** para adicionar alguns artigos. 

**Parabéns!** Você está executando um aplicativo Node.js controlado por dados no Serviço de Aplicativo do Azure.

## <a name="update-data-model-and-redeploy"></a>Atualizar o modelo de dados e reimplantar

Nessa etapa, você altera o modelo de dados `article` e publica suas alterações no Azure.

### <a name="update-the-data-model"></a>Atualizar o modelo de dados

Abra _modules/articles/server/models/article.server.model.js_.

Em `ArticleSchema`, adicione um tipo `String` chamado `comment`. Quando terminar, seu código de esquema deverá ter essa aparência:

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>Atualizar o código dos artigos

Atualize o resto do seu código `articles` para usar `comment`.

Há cinco arquivos que você precisa modificar: o controlador de servidor e as quatro exibições do cliente. 

Abra _modules/articles/server/controllers/articles.server.controller.js_.

Na função `update` adicione uma atribuição para `article.comment`. O código a seguir mostra a função `update` completa:

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Abra _modules/articles/client/views/view-article.client.view.html_.

Logo acima da marca de fechamento `</section>`, adicione a seguinte linha para exibir `comment` juntamente com o resto dos dados do artigo:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Abra _modules/articles/client/views/list-articles.client.view.html_.

Logo acima da marca de fechamento `</a>`, adicione a seguinte linha para exibir `comment` juntamente com o resto dos dados do artigo:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Abra _modules/articles/client/views/admin/list-articles.client.view.html_.

Dentro do elemento `<div class="list-group">` e logo acima da marcação de fechamento `</a>`, adicione a seguinte linha para exibir `comment` juntamente com o resto dos dados do artigo:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Abra _modules/articles/client/views/admin/form-article.client.view.html_.

Localize o elemento `<div class="form-group">` que contém o botão enviar, que se parece com este:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Logo acima dessa marcação, adicione outro elemento `<div class="form-group">` que permite editar o campo `comment`. Seu novo elemento deve ter esta aparência:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Testar suas alterações localmente

Salve todas as alterações.

Na janela de terminal local, teste as alterações no modo de produção novamente.

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> Lembre-se de que _config/env/production.js_ foi revertido e a variável de ambiente `MONGODB_URI` está definida apenas no seu aplicativo Web do Azure e não no computador local. Se analisar o arquivo de configuração, você verá que a configuração de produção é definida por padrão para usar um banco de dados MongoDB local. Isso garante seus dados de produção não sejam tocados ao testar suas alterações de código localmente.

Navegue até `http://localhost:8443` em um navegador e certifique-se de que está conectado.

Clique em **Admin > Gerenciar Artigos** e, em seguida, adicione um artigo selecionando o botão **+**.

Agora, você verá a nova caixa de texto `Comment`.

![Campo de comentário adicionado para Artigos](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

No terminal, pare o Node.js digitando `Ctrl+C`. 

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

Na janela do terminal local, confirme suas alterações no Git, em seguida, envie as alterações do código para o Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Quando `git push` estiver completo, navegue até seu aplicativo Web do Azure e tente a nova funcionalidade.

![Alterações de banco de dados e modelos publicadas no Azure](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Se você adicionou artigos anteriores, ainda será possível vê-los. Dados existentes no BD Cosmos não são perdidos. Também, suas atualizações para o esquema de dados e deixa seus dados existentes intactos.

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo 

Enquanto o aplicativo Node.js é executado no Serviço de Aplicativo do Azure, você pode obter logs do console transferidos para o seu terminal. Dessa forma, é possível obter as mesmas mensagens de diagnóstico para ajudá-lo a depurar erros de aplicativo.

Para iniciar o streaming de log, use o comando [az webapp log tail](/cli/azure/webapp/log#tail) no Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
``` 

Uma vez iniciado o streaming de log, atualize seu aplicativo Web do Azure no navegador para obter algum tráfego da Web. Agora, será possível ver os logs do console transferidos para o seu terminal.

Para interromper o streaming de log a qualquer momento, digite `Ctrl+C`. 

## <a name="manage-your-azure-web-app"></a>Gerenciar seu aplicativo Web do Azure

Vá para o [portal do Azure](https://portal.azure.com) para ver o aplicativo Web que você criou.

No menu à esquerda, clique em **Serviço de Aplicativos** e, em seguida, clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

Por padrão, o portal mostra a página **Visão geral** do seu aplicativo Web . Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Página Serviço de Aplicativo no portal do Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Próximas etapas

O que você aprendeu:

> [!div class="checklist"]
> * Criar um banco de dados MongoDB no Azure
> * Conectar um aplicativo Node.js ao MongoDB
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs do Azure para seu terminal
> * Gerenciar o aplicativo no portal do Azure

Vá para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo Web.

> [!div class="nextstepaction"] 
> [Mapear um nome DNS personalizado existente para aplicativos Web do Azure](app-service-web-tutorial-custom-domain.md)
