---
title: 'Tutorial: Criar um aplicativo Web do Node.js com o SDK JavaScript do Azure Cosmos DB para gerenciar dados da API do SQL'
description: Este tutorial do Node.js explora como usar o Microsoft Azure Cosmos DB para armazenar e acessar dados de um aplicativo Web do Node.js Express hospedado no recurso Aplicativos Web do Serviço de Aplicativo do Microsoft Azure.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.custom: devx-track-js
ms.openlocfilehash: 49cf54bda985f7d97b2db6a3ada7859aee829cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359533"
---
# <a name="tutorial-build-a-nodejs-web-app-using-the-javascript-sdk-to-manage-a-sql-api-account-in-azure-cosmos-db"></a>Tutorial: Compilar um aplicativo Web do Node.js usando o SDK do JavaScript para gerenciar uma conta de API do SQL no Azure Cosmos DB 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Como um desenvolvedor, você pode ter aplicativos que usam dados de documentos NoSQL. Você pode usar uma conta de API do SQL no Azure Cosmos DB para armazenar e acessar esses dados de documento. Este tutorial do Node.js mostra como armazenar e acessar dados de uma conta de API do SQL no Azure Cosmos DB usando um aplicativo do Node.js Express hospedado no recurso Aplicativos Web do Serviço de Aplicativo do Microsoft Azure. Neste tutorial, você criará um aplicativo baseado na Web (aplicativo Todo) que permite criar, recuperar e concluir tarefas. As tarefas são armazenadas como documentos JSON no Azure Cosmos DB. 

Este tutorial demonstra como criar uma conta da API do SQL no Azure Cosmos DB usando o portal do Azure. Em seguida, compile e execute um aplicativo Web que se baseia no SDK do Node.js para criar um banco de dados e um contêiner e adicionar itens ao contêiner. Este tutorial usa o SDK JavaScript versão 3.0.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB
> * Criar um novo aplicativo do Node.js
> * Conectar o aplicativo ao Azure Cosmos DB
> * Executar e implantar o aplicativo no Azure

## <a name="prerequisites"></a><a name="_Toc395783176"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, verifique se você tem os seguintes recursos:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] versão 6.10 ou superior.
* [Gerador do Express](https://www.expressjs.com/starter/generator.html) (você pode instalar o Express usando `npm install express-generator -g`)
* Instale o [Git][Git] na estação de trabalho local.

## <a name="create-an-azure-cosmos-db-account"></a><a name="_Toc395637761"></a>Criar uma conta do Azure Cosmos DB
Vamos começar criando uma conta do Azure Cosmos DB. Se você já tiver uma conta ou se estiver usando o Emulador do Azure Cosmos DB para este tutorial, pule para a [Etapa 2: Criar um novo aplicativo do Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="create-a-new-nodejs-application"></a><a name="_Toc395783178"></a>Criar um novo aplicativo do Node.js
Agora vamos aprender a criar um projeto Olá, Mundo em Node.js básico usando a estrutura Express.

1. Abra seu terminal favorito, como o prompt de comando do Node.js.

1. Navegue até o diretório no qual você deseja armazenar o novo aplicativo.

1. Use o gerador expresso para gerar um novo aplicativo chamado **tarefas**.

   ```bash
   express todo
   ```

1. Abra o novo diretório **todo** e instale as dependências.

   ```bash
   cd todo
   npm install
   ```

1. Execute o novo aplicativo.

   ```bash
   npm start
   ```

1. No navegador, veja seu novo aplicativo acessando `http://localhost:3000`.
   
   :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-express.png" alt-text="Aprender Node.js – captura de tela do aplicativo Olá, Mundo em uma janela do navegador":::

   Interrompa o aplicativo usando CTRL+C na janela do terminal e marque **y** para finalizar o trabalho em lotes.

## <a name="install-the-required-modules"></a><a name="_Toc395783179"></a>Instalar os módulos necessários

O arquivo **package.json** é um dos arquivos criados na raiz do projeto. Esse arquivo contém uma lista dos módulos adicionais que são necessários para seu aplicativo do Node.js. Ao implantar esse aplicativo no Azure, esse arquivo será usado para determinar quais módulos precisam ser instalados no Azure para dar suporte ao seu aplicativo. Instale mais dois pacotes para este tutorial.

1. Instale o módulo **\@azure/cosmos** via npm. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="connect-the-nodejs-application-to-azure-cosmos-db"></a><a name="_Toc395783180"></a>Conectar o aplicativo do Node.js ao Azure Cosmos DB
Agora que você concluiu a instalação e a configuração iniciais, escreverá o código exigido pelo aplicativo de lista de tarefas pendentes para se comunicar com o Azure Cosmos DB.

### <a name="create-the-model"></a>Criar o modelo
1. Na raiz do diretório do projeto, crie um novo diretório chamado **modelos**.  

2. No diretório **models**, criar um novo arquivo chamado **taskDao.js**. Esse arquivo contém o código necessário para criar o banco de dados e o contêiner. Ele também define métodos para ler, atualizar, criar e localizar as tarefas no Azure Cosmos DB. 

3. Copie o código a seguir para o arquivo **taskDao.js**:

   ```javascript
    // @ts-check
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const debug = require('debug')('todo:taskDao')

    // For simplicity we'll set a constant partition key
    const partitionKey = undefined
    class TaskDao {
      /**
       * Manages reading, adding, and updating Tasks in Cosmos DB
       * @param {CosmosClient} cosmosClient
       * @param {string} databaseId
       * @param {string} containerId
       */
      constructor(cosmosClient, databaseId, containerId) {
        this.client = cosmosClient
        this.databaseId = databaseId
        this.collectionId = containerId

        this.database = null
        this.container = null
      }

      async init() {
        debug('Setting up the database...')
        const dbResponse = await this.client.databases.createIfNotExists({
          id: this.databaseId
        })
        this.database = dbResponse.database
        debug('Setting up the database...done!')
        debug('Setting up the container...')
        const coResponse = await this.database.containers.createIfNotExists({
          id: this.collectionId
        })
        this.container = coResponse.container
        debug('Setting up the container...done!')
      }

      async find(querySpec) {
        debug('Querying for items from the database')
        if (!this.container) {
          throw new Error('Collection is not initialized.')
        }
        const { resources } = await this.container.items.query(querySpec).fetchAll()
        return resources
      }

      async addItem(item) {
        debug('Adding an item to the database')
        item.date = Date.now()
        item.completed = false
        const { resource: doc } = await this.container.items.create(item)
        return doc
      }

      async updateItem(itemId) {
        debug('Update an item in the database')
        const doc = await this.getItem(itemId)
        doc.completed = true

        const { resource: replaced } = await this.container
          .item(itemId, partitionKey)
          .replace(doc)
        return replaced
      }

      async getItem(itemId) {
        debug('Getting an item from the database')
        const { resource } = await this.container.item(itemId, partitionKey).read()
        return resource
      }
    }

    module.exports = TaskDao
   ```
4. Salve e feche o arquivo **taskDao.js** .  

### <a name="create-the-controller"></a>Criar o controlador

1. No diretório **rotas** do projeto, crie um novo arquivo chamado **tasklist.js**.  

2. Adicione os seguintes códigos ao **tasklist.js**. Esse código carrega os módulos CosmosClient e assíncrono que são usados pelo **tasklist.js**. Ele também define a classe **TaskList** que é transmitida a uma instância do objeto **TaskDao** definido anteriormente:
   
   ```javascript
    const TaskDao = require("../models/TaskDao");
    
    class TaskList {
      /**
       * Handles the various APIs for displaying and managing tasks
       * @param {TaskDao} taskDao
       */
      constructor(taskDao) {
        this.taskDao = taskDao;
      }
      async showTasks(req, res) {
        const querySpec = {
          query: "SELECT * FROM root r WHERE r.completed=@completed",
          parameters: [
            {
              name: "@completed",
              value: false
            }
          ]
        };

        const items = await this.taskDao.find(querySpec);
        res.render("index", {
          title: "My ToDo List ",
          tasks: items
        });
      }

      async addTask(req, res) {
        const item = req.body;

        await this.taskDao.addItem(item);
        res.redirect("/");
      }

      async completeTask(req, res) {
        const completedTasks = Object.keys(req.body);
        const tasks = [];

        completedTasks.forEach(task => {
          tasks.push(this.taskDao.updateItem(task));
        });

        await Promise.all(tasks);

        res.redirect("/");
      }
    }

    module.exports = TaskList;
   ```

3. Salve e feche o arquivo **tasklist.js** .

### <a name="add-configjs"></a>Adicionar config.js

1. Na raiz do diretório do projeto, crie um novo arquivo chamado **config.js**. 

2. Adicione o código a seguir ao arquivo **config.js**. Esse código define as configurações e valores necessários para nosso aplicativo.
   
   ```javascript
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. No arquivo **config.js**, atualize os valores de HOST e de AUTH_KEY usando os valores encontrados na página Chaves de sua conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com). 

4. Salve e feche o arquivo **config.js** .

### <a name="modify-appjs"></a>Modificar app.js

1. No diretório do projeto, abra o arquivo **app.js** . Esse arquivo foi criado anteriormente, quando o aplicativo Web Express foi criado.  

2. Adicione o código a seguir ao arquivo **app.js**. Esse código define o arquivo de configuração a ser usado e carrega os valores para algumas variáveis que você usará nas próximas seções. 
   
   ```javascript
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const config = require('./config')
    const TaskList = require('./routes/tasklist')
    const TaskDao = require('./models/taskDao')

    const express = require('express')
    const path = require('path')
    const logger = require('morgan')
    const cookieParser = require('cookie-parser')
    const bodyParser = require('body-parser')

    const app = express()

    // view engine setup
    app.set('views', path.join(__dirname, 'views'))
    app.set('view engine', 'jade')

    // uncomment after placing your favicon in /public
    //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
    app.use(logger('dev'))
    app.use(bodyParser.json())
    app.use(bodyParser.urlencoded({ extended: false }))
    app.use(cookieParser())
    app.use(express.static(path.join(__dirname, 'public')))

    //Todo App:
    const cosmosClient = new CosmosClient({
      endpoint: config.host,
      key: config.authKey
    })
    const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId)
    const taskList = new TaskList(taskDao)
    taskDao
      .init(err => {
        console.error(err)
      })
      .catch(err => {
        console.error(err)
        console.error(
          'Shutting down because there was an error settinig up the database.'
        )
        process.exit(1)
      })

    app.get('/', (req, res, next) => taskList.showTasks(req, res).catch(next))
    app.post('/addtask', (req, res, next) => taskList.addTask(req, res).catch(next))
    app.post('/completetask', (req, res, next) =>
      taskList.completeTask(req, res).catch(next)
    )
    app.set('view engine', 'jade')

    // catch 404 and forward to error handler
    app.use(function(req, res, next) {
      const err = new Error('Not Found')
      err.status = 404
      next(err)
    })

    // error handler
    app.use(function(err, req, res, next) {
      // set locals, only providing error in development
      res.locals.message = err.message
      res.locals.error = req.app.get('env') === 'development' ? err : {}

      // render the error page
      res.status(err.status || 500)
      res.render('error')
    })

    module.exports = app
   ```

3. Por fim, salve e feche o arquivo **app.js**.

## <a name="build-a-user-interface"></a><a name="_Toc395783181"></a>Criar uma interface do usuário

Agora vamos criar a interface do usuário para que um usuário possa interagir com o aplicativo. O aplicativo Express que criamos na seção anterior usa **Jade** como o mecanismo de exibição.

1. O arquivo **layout.jade** no diretório **views** é usado como um modelo global para outros arquivos **.jade**. Nesta etapa você o modificará para usar Twitter Bootstrap, um kit de ferramentas usado para projetar sites da Web.  

2. Abra o arquivo **layout.jade** encontrado na pasta **views** e substitua o conteúdo pelo seguinte código:

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Esse código instrui o mecanismo **Jade** a renderizar um HTML para nosso aplicativo e cria um **bloco** chamado **content** em que podemos fornecer o layout para nossas páginas de conteúdo. Salve e feche o arquivo **layout.jade** .

3. Agora, abra o arquivo **index.jade**, o modo de exibição que será usado pelo nosso aplicativo, e substitua o conteúdo do arquivo pelo seguinte código:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Esse código estende o layout e fornece conteúdo para o espaço reservado **content** que vimos anteriormente no arquivo **layout.jade**. Nesse layout, criamos dois formulários HTML.

O primeiro formulário contém uma tabela para nossos dados e um botão que permite atualizar itens ao ser lançado o método **/completeTask** do controlador.
    
O segundo formulário contém dois campos de entrada e um botão que permite criar um novo item ao ser lançado o método **/addtask** do controlador. Isso é tudo o que é necessário para que o aplicativo funcione.

## <a name="run-your-application-locally"></a><a name="_Toc395783181"></a>Execute o seu aplicativo localmente

Agora que você criou o aplicativo, pode executá-lo localmente usando as seguintes etapas:  

1. Para testar o aplicativo no computador local, execute `npm start` no terminal para iniciar o aplicativo e atualize a página do navegador `http://localhost:3000`. A página agora deve se parecer com a seguinte captura de tela:
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png" alt-text="Captura de tela do aplicativo MyTodo List em uma janela do navegador":::

    > [!TIP]
    > Se receber um erro sobre o recuo no arquivo layout.jade ou o arquivo index.jade, verifique se as duas primeiras linhas em ambos os arquivos estão justificadas à esquerda, sem espaços. Se houver espaços antes das duas primeiras linhas, remova-os, salve os dois arquivos e atualize a janela do navegador. 

2. Use os campos Item, Nome do Item e Categoria para inserir uma nova tarefa e marque **Adicionar Item**. Isso cria um documento no Azure Cosmos DB com essas propriedades. 

3. A página deverá ser atualizada para exibir o item recém-criado na lista de Tarefas Pendentes.
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png" alt-text="Captura de tela do aplicativo com um novo item na lista de Tarefas pendentes":::

4. Para concluir uma tarefa, marque a caixa de seleção na coluna Concluir e escolha **Atualizar tarefas**. Isso atualiza o documento que você já criou e o remove da exibição.

5. Para interromper o aplicativo, pressione CTRL+C na janela do terminal e marque **Y** para finalizar o trabalho em lotes.

## <a name="deploy-your-application-to-web-apps"></a><a name="_Toc395783182"></a>Implantar seu aplicativo para aplicativos Web

Depois que seu aplicativo for bem-sucedido localmente, você poderá implantá-lo do Azure usando as seguintes etapas:

1. Se você ainda não fez isso, habilite um repositório git para seu aplicativo de Aplicativos Web.

2. Adicione seu aplicativo de Aplicativos Web como um git remoto.
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. Implante o aplicativo efetuando push para o computador remoto.
   
   ```bash
   git push azure main
   ```

4. Em poucos segundos, seu aplicativo Web será publicado e iniciado em um navegador.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando esses recursos já não forem necessários, você poderá excluir o grupo de recursos, a conta do Azure Cosmos DB e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos que você usou para a conta do Azure Cosmos DB, selecione **Excluir** e, em seguida, confirme o nome do grupo de recursos a ser excluído.

## <a name="next-steps"></a><a name="_Toc395637775"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar aplicativos móveis com o Xamarin e o Azure Cosmos DB](mobile-apps-with-xamarin.md)


[Node.js]: https://nodejs.org/
[Git]: https://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app