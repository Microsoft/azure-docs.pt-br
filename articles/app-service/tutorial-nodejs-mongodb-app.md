---
title: 'Tutorial: Aplicativo Node.js com o MongoDB'
description: Saiba como executar um aplicativo Node.js no Azure, com uma conexão com um banco de dados MongoDB no Azure (Cosmos DB). O MEAN.js é usado no tutorial.
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/16/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-js, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: b1dcd413f301f25460cb29f1bb20e67a37ac6ebb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767305"
---
# <a name="tutorial-build-a-nodejs-and-mongodb-app-in-azure"></a>Tutorial: Criar um aplicativo Node.js e MongoDB no Azure

::: zone pivot="platform-windows"  

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este tutorial mostra como criar um aplicativo Node.js no Serviço de Aplicativo no Windows e conectá-lo a um banco de dados MongoDB. Quando terminar, você terá um aplicativo MEAN (MongoDB, Express, AngularJS e Node.js) executando em [Serviço de Aplicativo do Azure](overview.md). Para simplificar, o aplicativo de exemplo usa a [estrutura Web MEAN.js](https://meanjs.org/).

::: zone-end

::: zone pivot="platform-linux"


O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches usando o sistema operacional Linux. Este tutorial mostra como criar um aplicativo Node.js no Serviço de Aplicativo no Linux, conectá-lo localmente a um banco de dados MongoDB e, depois, implantá-lo em um banco de dados na API do Azure Cosmos DB para MongoDB. Quando terminar, você terá um aplicativo MEAN (MongoDB, Expresso, AngularJS e Node.js) em execução no Serviço de Aplicativo no Linux. Para simplificar, o aplicativo de exemplo usa a [estrutura Web MEAN.js](https://meanjs.org/).

::: zone-end

![Aplicativo MEAN.js em execução no Serviço de Aplicativo do Azure](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

O que você aprenderá:

> [!div class="checklist"]
> * Criar um banco de dados MongoDB no Azure
> * Conectar um aplicativo Node.js ao MongoDB
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Instalar o Git](https://git-scm.com/)
- [Instalar o Node.js e o NPM](https://nodejs.org/)
- [Instale o Bower](https://bower.io/) (exigido pelo [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
- [Instale o Gulp.js](https://gulpjs.com/) (exigido pelo [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
- [Instalar e executar o MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

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

Ignore o aviso de config.domain. Quando o aplicativo estiver totalmente carregado, você verá algo semelhante à seguinte mensagem:

<pre>
--
MEAN.JS – ambiente de desenvolvimento

Ambiente:     Development Server:          http://0.0.0.0:3000 Banco de dados:        mongodb://localhost/mean-dev Versão do aplicativo:     0.5.0 Versão do MEAN.JS: 0.5.0 --
</pre>

Navegue até `http://localhost:3000` em um navegador. Clique em **Criar Conta** no menu superior e crie um usuário de teste. 

O aplicativo de exemplo MEAN.js armazena dados do usuário no banco de dados. Se tiver êxito na criação de um usuário e ao entrar, seu aplicativo estará gravando dados no banco de dados local MongoDB.

![O MEAN.js conecta-se com êxito ao MongoDB](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Selecione **Admin > Gerenciar Artigos** para adicionar alguns artigos.

Para parar o Node.js a qualquer momento, pressione `Ctrl+C` no terminal. 

## <a name="create-production-mongodb"></a>Criar MongoDB de produção

Ness etapa, você cria um banco de dados MongoDB no Azure. Quando seu aplicativo é implantado no Azure, ele usa esse banco de dados na nuvem.

Para o MongoDB, este tutorial usa o [BD Cosmos do Azure](/azure/documentdb/). O BD Cosmos dá suporte a conexões de cliente do MongoDB.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>Criar uma conta do BD Cosmos

> [!NOTE]
> Há um custo para criar os bancos de dados do Azure Cosmos DB deste tutorial em sua própria assinatura do Azure. Para usar uma conta gratuita do Azure Cosmos DB por sete dias, você pode usar a experiência [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) experiência. Basta clicar no botão **Criar** no bloco do MongoDB para criar um banco de dados gratuito do MongoDB no Azure. Quando o banco de dados é criado, navegue até **Cadeia de Conexão** no portal e recupere a cadeia de conexão do Azure Cosmos DB para usá-la posteriormente no tutorial.
>

No Cloud Shell, crie uma conta do Cosmos DB com o comando [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create).

No comando a seguir, substitua o espaço reservado *\<cosmosdb-name>* por um nome exclusivo do Cosmos DB. Esse nome exclusivo é usado como parte do seu ponto de extremidade do Cosmos DB, `https://<cosmosdb-name>.documents.azure.com/`, portanto, o nome precisa ser exclusivo em todas as contas do Cosmos DB no Azure. O nome deve conter somente letras minúsculas, números e o caractere hífen (-), e deve ter entre 3 e 50 caracteres.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

O parâmetro *--kind MongoDB* habilita conexões do cliente MongoDB.

Quando a conta do BD Cosmos é criada, a CLI do Azure mostra informações semelhantes ao exemplo a seguir:

<pre>
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://&lt;cosmosdb-name&gt;.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  &lt; Output truncated for readability &gt;
}
</pre>

## <a name="connect-app-to-production-mongodb"></a>Conectar o aplicativo ao MongoDB de produção

Nesta etapa, você conecta o aplicativo de exemplo MEAN.js ao BD Cosmos que acabou de ser criado usando uma cadeia de conexão do MongoDB. 

### <a name="retrieve-the-database-key"></a>Recuperar a chave do banco de dados

Para se conectar ao BD Cosmos, você precisa da chave do banco de dados. No Cloud Shell, use o comando [`az cosmosdb list-keys`](/cli/azure/cosmosdb#az_cosmosdb_list_keys) para recuperar a chave primária.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup
```

A CLI do Azure apresenta as informações semelhantes ao seguinte exemplo:

<pre>
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
</pre>

Copie o valor de `primaryMasterKey`. Essas informações serão necessárias na próxima etapa.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurar a cadeia de conexão em seu aplicativo Node.js

No repositório local do MEAN.js, na pasta _config/env/_ , crie um arquivo chamado _local-production.js_. _.gitignore_ já está configurado para manter esse arquivo fora do repositório. 

Copie o seguinte código dentro dele. Substitua os dois espaços reservados *\<cosmosdb-name>* pelo nome do banco de dados Cosmos DB e substitua o espaço reservado *\<primary-master-key>* pela chave copiada na etapa anterior.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

A opção `ssl=true` é necessária porque o [Cosmos DB requer SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 

Salve suas alterações.

### <a name="test-the-application-in-production-mode"></a>Testar o aplicativo em modo de produção 

Em uma janela de terminal local, execute o comando a seguir para reduzir e agrupar scripts para o ambiente de produção. Esse processo gera os arquivos necessários para o ambiente de produção.

```bash
gulp prod
```

Em uma janela de terminal local, execute o comando a seguir para usar a cadeia de conexão configurada em _config/env/local-production.js_. Ignore o erro de certificado e o aviso de config.domain.

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

`NODE_ENV=production` define a variável de ambiente que informa o Node. js a ser executado no ambiente de produção.  `node server.js` inicia o servidor Node.js com `server.js` na raiz do repositório. É dessa forma que o aplicativo Node.js é carregado no Azure. 

Quando o aplicativo for carregado, verifique se ele está sendo executado no ambiente de produção:

<pre>
--
MEAN.JS

Ambiente:     produção Servidor:          http://0.0.0.0:8443 Banco de dados:        mongodb://&lt; cosmosdb-name&gt;:&lt; primary-master-key&gt;@&lt; cosmosdb-name&gt;.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false Versão do aplicativo:     0.5.0 Versão do MEAN.JS: 0.5.0
</pre>

Navegue até `http://localhost:8443` em um navegador. Clique em **Criar Conta** no menu superior e crie um usuário de teste. Se tiver êxito na criação de um usuário e ao entrar, seu aplicativo estará gravando dados no banco de dados do Cosmos DB no Azure. 

No terminal, pare o Node.js digitando `Ctrl+C`. 

## <a name="deploy-app-to-azure"></a>Implantar o aplicativo no Azure

Nessa etapa, você implanta seu aplicativo Node.js conectado ao MongoDB no Serviço de Aplicativo do Azure.

### <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>Criar um aplicativo Web

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

::: zone-end

### <a name="configure-an-environment-variable"></a>Configurar um variável de ambiente

Por padrão, o projeto MEAN.js mantém _config/env/local-production.js_ fora do repositório Git. Portanto, para seu aplicativo do Azure, use as configurações de aplicativo para definir a cadeia de conexão do MongoDB.

Para definir as configurações do aplicativo, use o [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) no Cloud Shell. 

O exemplo a seguir define uma configuração de aplicativo `MONGODB_URI` no aplicativo do Azure. Substitua os espaços reservados *\<app-name>* , *\<cosmosdb-name>* e *\<primary-master-key>* .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true"
```

No código Node.js, você [acessa essa configuração de aplicativo](configure-language-nodejs.md#access-environment-variables) com `process.env.MONGODB_URI`, assim como você teria acesso a qualquer variável de ambiente. 

No repositório local do MEAN.js, abra _config/env/production.js_ (e não _config/env/local-production.js_), que tem a configuração específica ao ambiente de produção. O aplicativo MEAN.js padrão já está configurado para usar a variável de ambiente `MONGODB_URI` que você criou.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

É possível notar que o processo de implantação executa [Gulp](https://gulpjs.com/) após `npm install`. O Serviço de Aplicativo não executa tarefas Gulp ou Grunt durante a implantação, portanto, esse repositório de exemplo possui dois arquivos adicionais em seu diretório raiz para habilitá-lo: 

- _.deployment_ – este arquivo instrui o Serviço de Aplicativo a executar `bash deploy.sh` como o script de implantação personalizado.
- _deploy.sh_ – o script de implantação personalizado. Se você revisar o arquivo, verá que ele executa `gulp prod` após `npm install` e `bower install`. 

É possível usar essa abordagem para adicionar qualquer etapa à implantação com base em Git. Se você reiniciar o aplicativo do Azure em qualquer ponto, o Serviço de Aplicativo não executará novamente essas tarefas de automação. Para obter mais informações, confira [Executar o Grunt/Bower/Gulp](configure-language-nodejs.md#run-gruntbowergulp).

### <a name="browse-to-the-azure-app"></a>Navegar até o aplicativo do Azure 

Navegue até o aplicativo implantado usando o navegador da Web. 

```bash 
http://<app-name>.azurewebsites.net 
``` 

Clique em **Criar Conta** no menu superior e crie um usuário fictício. 

Se você tiver êxito e o aplicativo entrar automaticamente no usuário criado, isso significa que seu aplicativo MEAN.js no Azure tem conectividade com o banco de dados MongoDB (Cosmos DB). 

![Aplicativo MEAN.js em execução no Serviço de Aplicativo do Azure](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Selecione **Admin > Gerenciar Artigos** para adicionar alguns artigos. 

**Parabéns!** Você está executando um aplicativo Node.js controlado por dados no Serviço de Aplicativo do Azure.

## <a name="update-data-model-and-redeploy"></a>Atualizar o modelo de dados e reimplantar

Nessa etapa, você altera o modelo de dados `article` e publica suas alterações no Azure.

### <a name="update-the-data-model"></a>Atualizar o modelo de dados

No repositório local do MEAN.js, abra _modules/articles/server/models/article.server.model.js_.

Em `ArticleSchema`, adicione um tipo `String` chamado `comment`. Quando terminar, seu código de esquema deverá ter essa aparência:

```javascript
const ArticleSchema = new Schema({
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
  let article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Abra _modules/articles/client/views/view-article.client.view.html_.

Logo acima da marca de fechamento `</section>`, adicione a seguinte linha para exibir `comment` juntamente com o resto dos dados do artigo:

```html
<p class="lead" ng-bind="vm.article.comment"></p>
```

Abra _modules/articles/client/views/list-articles.client.view.html_.

Logo acima da marca de fechamento `</a>`, adicione a seguinte linha para exibir `comment` juntamente com o resto dos dados do artigo:

```html
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Abra _modules/articles/client/views/admin/list-articles.client.view.html_.

Dentro do elemento `<div class="list-group">` e logo acima da marcação de fechamento `</a>`, adicione a seguinte linha para exibir `comment` juntamente com o resto dos dados do artigo:

```html
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Abra _modules/articles/client/views/admin/form-article.client.view.html_.

Localize o elemento `<div class="form-group">` que contém o botão enviar, que se parece com este:

```html
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Logo acima dessa marcação, adicione outro elemento `<div class="form-group">` que permite editar o campo `comment`. Seu novo elemento deve ter esta aparência:

```html
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Testar suas alterações localmente

Salve todas as alterações.

Na janela de terminal local, teste as alterações no modo de produção novamente.

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

Navegue até `http://localhost:8443` em um navegador e certifique-se de que está conectado.

Clique em **Admin > Gerenciar Artigos** e, em seguida, adicione um artigo selecionando o botão **+** .

Agora, você verá a nova caixa de texto `Comment`.

![Campo de comentário adicionado para Artigos](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

No terminal, pare o Node.js digitando `Ctrl+C`. 

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

Na janela do terminal local, confirme suas alterações no Git, em seguida, envie as alterações do código para o Azure.

```bash
git commit -am "added article comment"
git push azure main
```

Quando o `git push` estiver concluído, navegue até o aplicativo do Azure e experimente a nova funcionalidade.

![Alterações de banco de dados e modelos publicadas no Azure](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Se você adicionou artigos anteriores, ainda será possível vê-los. Dados existentes no BD Cosmos não são perdidos. Também, suas atualizações para o esquema de dados e deixa seus dados existentes intactos.

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo 

::: zone pivot="platform-windows"  

Enquanto o aplicativo Node.js é executado no Serviço de Aplicativo do Azure, você pode obter logs do console transferidos para o seu terminal. Dessa forma, é possível obter as mesmas mensagens de diagnóstico para ajudá-lo a depurar erros de aplicativo.

Para iniciar o streaming de log, use o comando [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) no Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
``` 

Após o início do streaming de log, atualize o aplicativo do Azure no navegador para obter o tráfego da Web. Agora, será possível ver os logs do console transferidos para o seu terminal.

Para interromper o streaming de log a qualquer momento, digite `Ctrl+C`. 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

## <a name="manage-your-azure-app"></a>Gerenciar o aplicativo do Azure

Acesse o [portal do Azure](https://portal.azure.com) para ver o aplicativo que você criou.

No menu à esquerda, clique em **Serviços de Aplicativos** e, em seguida, clique no nome do seu aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/tutorial-nodejs-mongodb-app/access-portal.png)

Por padrão, o portal mostra a página **Visão Geral** do aplicativo. Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Página Serviço de Aplicativo no portal do Azure](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

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

Vá para o próximo tutorial para aprender a mapear um nome DNS personalizado para o aplicativo.

> [!div class="nextstepaction"] 
> [Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-domain.md)

Se preferir, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar o aplicativo Node.js](configure-language-nodejs.md)
