---
title: Tutorial de MongoDB, Angular e Node para Azure - parte 5 | Microsoft Docs
description: "Parte 5 da série de tutoriais sobre como criar um aplicativo do MongoDB com Angular e Node no Azure Cosmos DB usando exatamente as mesmas APIs usadas para MongoDB"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.openlocfilehash: e752e18f6d579633c0cf553224ae7617b774ad0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-5-use-mongoose-to-connect-to-azure-cosmos-db"></a>Criar um aplicativo do MongoDB com Angular e Azure Cosmos DB - parte 5: usar o Mongoose para se conectar ao Azure Cosmos DB

Este tutorial com várias partes demonstra como criar uma novo aplicativo [API do MongoDB](mongodb-introduction.md) escrito em Node.js com Express, Angular e seu banco de dados do Azure Cosmos DB.

A Parte 5 do tutorial se baseia na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) e inclui as seguintes tarefas:

> [!div class="checklist"]
> * Usar o Mongoose para se conectar ao Azure Cosmos DB
> * Obter informações de cadeia de conexão do Azure Cosmos DB
> * Criar o modelo hero
> * Criar o serviço hero para obter dados do hero
> * Executar o aplicativo localmente

## <a name="video-walkthrough"></a>Passo a passo em vídeo

> [!VIDEO https://www.youtube.com/embed/sI5hw6KPPXI]


## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, verifique se você concluiu as etapas na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) do tutorial.

> [!TIP]
> Este tutorial percorre passo a passo com você as etapas para compilar o aplicativo. Se você deseja baixar o projeto concluído, pode obter o aplicativo concluído no [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) do GitHub.

## <a name="use-mongoose-to-connect-to-azure-cosmos-db"></a>Usar o Mongoose para se conectar ao Azure Cosmos DB

1. Instale o módulo npm do Mongoose, que é uma API normalmente usada para se comunicar com o MongoDB.

    ```bash
    npm i mongoose --save
    ```

2. Agora, crie um novo arquivo na pasta de **servidor** chamada **mongo.js**. Neste arquivo, adicione todas as suas informações de conexão para o banco de dados do Azure Cosmos DB.

3. Copie o código a seguir em **mongo.js**. Esse código:
    * Requer Mongoose.
    * Substitui a premissa do Mongo para usar a premissa básica compilada no ES6/ES2015 e versões posteriores.
    * Chama um arquivo env que lhe permite configurar certas coisas com base no estágio em que você está, preparação, produção ou desenvolvimento. Vamos criar esse arquivo em breve.
    * Inclui a nossa cadeia de conexão do MongoDB, que será definida no arquivo env.
    * Cria uma função de conexão que chama o Mongoose.

    ```javascript
    const mongoose = require('mongoose');
    /**
     * Set to Node.js native promises
     * Per http://mongoosejs.com/docs/promises.html
     */
    mongoose.Promise = global.Promise;

    const env = require('./env/environment');

    // eslint-disable-next-line max-len
    const mongoUri = `mongodb://${env.dbName}:${env.key}@${env.dbName}.documents.azure.com:${env.cosmosPort}/?ssl=true`; //&replicaSet=globaldb`;

    function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
    }

    module.exports = {
      connect,
      mongoose
    };
    ```
    
4. No painel do Explorer, crie uma pasta em **servidor** chamada **ambiente** e, na pasta **ambiente**, crie um novo arquivo chamado **environment.js** .

5. Do arquivo mongo.js, sabemos que precisamos incluir o `dbName`, a `key`e a `cosmosPort` e, portanto, copie o código a seguir em **environment.js**.

    ```javascript
    const cosmosPort = 1234; // replace with your port
    const dbName = 'your-cosmos-db-name-goes-here';
    const key = 'your-key-goes-here';

    module.exports = {
      dbName,
      key,
      cosmosPort
    };
    ```

## <a name="get-the-connection-string-information"></a>Obter informações da cadeia de ligação

1. Em **environment.js**, altere o valor de `cosmosPort` para 10255. (Você pode encontrar a porta do Azure Cosmos DB no Portal do Azure)

    ```javascript
    const cosmosPort = 10255;
    ```

2. Em **environment.js**, altere o valor de `dbName` para o nome da conta do Azure Cosmos DB criada na [Etapa 4](tutorial-develop-mongodb-nodejs-part4.md). 

3. Recupere a chave primária para a conta do Azure Cosmos DB usando o seguinte comando de CLI na janela de terminal: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    * `<cosmosdb-name>`é o nome da conta do Azure Cosmos DB criada na [Etapa 4](tutorial-develop-mongodb-nodejs-part4.md).

4. Copie a chave primária no arquivo environment.js como o valor de `key`.

    Seu aplicativo agora tem todas as informações necessárias para se conectar ao Azure Cosmos DB. Essas informações também podem ser recuperadas no portal. Para saber mais, confira [Obter a cadeia de conexão do MongoDB para personalizar](connect-mongodb-account.md#GetCustomConnection). O nome de usuário no portal é igual ao dbName em environments.js. 

## <a name="create-a-hero-model"></a>Criar um modelo Hero

1.  No painel do Explorer, crie o arquivo **hero.model.js** na pasta **servidor**.

2. Copie o código a seguir em **hero.model.js**. Esse código:
   * Requer Mongoose.
   * Cria um novo esquema com uma ID, um nome e uma fala.
   * Cria um modelo usando o esquema.
   * Exporta o modelo. 
   * Nomeie a coleção Heroes (em vez de Heros, que seria o nome padrão da coleção com base nas regras de nomenclatura no plural do Mongoose).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Criar um serviço Hero

1.  No painel do Explorer, crie o arquivo **hero.service.js** na pasta **servidor**.

2. Copie o código a seguir em **hero.service.js**. Esse código:
   * Obtém o modelo que você acabou de criar
   * Conecta-se ao banco de dados
   * Cria uma variável docquery que usa o método hero.find para definir uma consulta que retorna todos os heroes.
   * Executa uma consulta com o docquery.exec para obter uma lista de todos os heroes, em que o status da resposta é 200. 
   * Se o status é 500, envia a mensagem de erro
   * Como estamos usando módulos, ele obtém os heroes. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="add-the-hero-service-to-routesjs"></a>Adicionar o serviço hero a routes.js

1. No Visual Studio Code, em **routes.js**, comente a função `res.send` que envia os dados de exemplo do Hero e adicione uma linha para chamar a função `heroService.getHeroes` em vez disso.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

2. Em **routes.js**, exija o serviço hero:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

3. Em **hero.service.js**, atualize a função getHeroes para usar os parâmetros `req` e `res` da seguinte maneira:

    ```javascript
    function getHeroes(req, res) {
    ```

    Vamos examinar e percorrer o encadeamento de chamadas. Primeiro, vemos o `index.js`, que define o servidor de nó e observamos que ele está configurando e definindo nossas rotas. Nosso arquivo routes.js se comunica com o serviço hero e o manda obter nossas funções como getHeroes e transmitir a solicitação e a resposta. Aqui, hero.service.js vai obter o modelo, conectar-se ao Mongo e, em seguida, executar getHeroes quando o chamarmos, e retornará uma resposta de 200. Em seguida, ele volta pela cadeia. 

## <a name="run-the-app"></a>Execute o aplicativo

1. Agora, vamos executar o aplicativo novamente. No Visual Studio Code, salve todas as suas alterações, clique no botão **Depurar** ![Ícone de depuração no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png) no lado esquerdo e clique no botão **Iniciar depuração** botão ![Ícone de depuração no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png).

3. Agora, vamos voltar para o navegador, abrir as ferramentas de desenvolvedor e a guia Rede e navegar até http://localhost:3000, onde está nosso aplicativo.

    ![Nova conta do Azure Cosmos DB no portal do Azure](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

   Não há nenhum hero armazenado no aplicativo, mas na próxima etapa do tutorial vamos adicionar as funções put, push e delete para podermos adicionar, atualizar e excluir heroes da interface do usuário usando conexões Mongoose para nosso banco de dados do Azure Cosmos DB. 

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Usou APIs do Mongoose para conectar seu aplicativo heroes ao Azure Cosmos DB 
> * Adicionou a funcionalidade get heroes ao aplicativo

Você pode prosseguir para a próxima parte do tutorial a fim de adicionar funções Post, Put e Delete ao aplicativo.

> [!div class="nextstepaction"]
> [Adicionar funções Post, Put e Delete ao aplicativo](tutorial-develop-mongodb-nodejs-part6.md)
