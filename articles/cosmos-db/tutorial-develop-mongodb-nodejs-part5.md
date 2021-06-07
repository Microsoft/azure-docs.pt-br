---
title: Conectar o aplicativo Angular à API do Azure Cosmos DB para MongoDB usando o Mongoose
description: Este tutorial descreve como criar um aplicativo Node.js usando o Angular e o Express para gerenciar os dados armazenados no Cosmos DB. Nesta parte, você usa o Mongoose para se conectar ao Azure Cosmos DB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: a3097fa539f460ef5e8ffe73598fa5d55516717e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097797"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Criar um aplicativo Angular com a API do Azure Cosmos DB para MongoDB – usar o Mongoose para conectar-se ao Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este tutorial com várias partes demonstra como criar um aplicativo Node.js com o Express e o Angular e conectá-lo à sua [conta do Cosmos configurada com a API do Cosmos DB para MongoDB](mongodb-introduction.md). Este artigo descreve a Parte 5 do tutorial e se baseia na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md).

Nesta parte do tutorial, você:

> [!div class="checklist"]
> * Usará o Mongoose para se conectar ao Cosmos DB.
> * Obterá sua cadeia de conexão do Cosmos DB.
> * Criará o modelo Hero.
> * Criará o serviço Hero para obter dados do Hero.
> * Execute o aplicativo localmente.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Antes de iniciar este tutorial, conclua as etapas na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md).

* Este tutorial requer que você execute a CLI do Azure localmente. Você deve ter a CLI do Azure versão 2.0 ou posterior instalada. Execute `az --version` para encontrar a versão. Caso precise instalar ou atualizar a CLI do Azure, confira [Instalar a CLI do Azure 2.0](/cli/azure/install-azure-cli).

* Este tutorial percorre passo a passo com você as etapas para compilar o aplicativo. Se você deseja baixar o projeto concluído, pode obter o aplicativo concluído no [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) do GitHub.

## <a name="use-mongoose-to-connect"></a>Usar o Mongoose para se conectar

Mongoose é uma biblioteca de ODM (modelagem de dados de objeto) para o MongoDB e o Node.js. Você pode usar o Mongoose para se conectar à sua conta do Azure Cosmos DB. Use as etapas a seguir para instalar o Mongoose e conectar-se ao Azure Cosmos DB:

1. Instale o módulo npm do Mongoose, que é uma API usada para se comunicar com o MongoDB.

    ```bash
    npm i mongoose --save
    ```

1. Na pasta **server**, crie um arquivo chamado **mongo.js**. Você adicionará os detalhes de conexão da conta do Azure Cosmos DB a esse arquivo.

1. Copie o código a seguir para o arquivo **mongo.js**. O código oferece a seguinte funcionalidade:

   * Requer Mongoose.
   * Substitui a premissa do Mongo para usar a promessa básica compilada no ES6/ES2015 e em versões posteriores.
   * Chama um arquivo env que lhe permite configurar certas coisas com base no estágio em que você está, preparação, produção ou desenvolvimento. Você criará esse arquivo na próxima seção.
   * Inclui a cadeia de conexão do MongoDB, que é definida no arquivo env.
   * Cria uma função de conexão que chama o Mongoose.

     ```javascript
     const mongoose = require('mongoose');
     /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
     */
     mongoose.Promise = global.Promise;

     const env = require('./env/environment');

     // eslint-disable-next-line max-len
     const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

     function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
     }

     module.exports = {
     connect,
     mongoose
     };
     ```
    
1. No painel do Explorer, em **server**, crie uma pasta chamada **environment**. Na pasta **environment**, crie um arquivo chamado **environment.js**.

1. Do arquivo mongo.js, é preciso incluir valores para os parâmetros `dbName`, `key` e `cosmosPort`. Copie o código a seguir no arquivo **environment.js**:

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>Obtenha a cadeia de conexão

Para conectar seu aplicativo ao Azure Cosmos DB, você precisará atualizar as definições de configuração para o aplicativo. Use as etapas a seguir para atualizar as configurações: 

1. No portal do Azure, obtenha o número da porta, o nome da conta do Azure Cosmos DB e os valores de chave primária para sua conta do Azure Cosmos DB.

1. No arquivo **environment.js**, altere o valor de `port` para 10255. 

    ```javascript
    const port = 10255;
    ```

1. No arquivo **environment.js**, altere o valor de `accountName` para o nome da conta do Azure Cosmos DB que você criou na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) do tutorial. 

1. Recupere a chave primária para a conta do Azure Cosmos DB usando o seguinte comando de CLI na janela de terminal: 

    ```azurecli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name> é o nome da conta do Azure Cosmos DB que você criou na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) do tutorial.

1. Copie a chave primária no arquivo **environment.js** como o valor de `key`.

Agora seu aplicativo tem todas as informações necessárias para se conectar ao Azure Cosmos DB. 

## <a name="create-a-hero-model"></a>Criar um modelo Hero

Em seguida, você precisará definir o esquema dos dados a armazenar no Azure Cosmos DB, definindo um arquivo de modelo. Use as etapas a seguir para criar um _modelo do Hero_ que defina o esquema dos dados:

1. No painel do Explorer, na pasta **server**, crie um arquivo chamado **hero.model.js**.

1. Copie o código a seguir no arquivo **hero.model.js**. O código oferece a seguinte funcionalidade:

   * Requer Mongoose.
   * Cria um novo esquema com uma ID, um nome e uma fala.
   * Cria um modelo usando o esquema.
   * Exporta o modelo. 
   * Nomeie a coleção **Heroes** (em vez de **Heros**, que é o nome padrão da coleção com base nas regras de nomenclatura no plural do Mongoose).

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

Depois de criar o modelo de Hero, você precisa definir um serviço para ler os dados e realizar operações de listagem, criação, exclusão e atualização. Use as etapas a seguir para criar um _serviço Hero_ que consulta os dados do Azure Cosmos DB:

1. No painel do Explorer, na pasta **server**, crie um arquivo chamado **hero.service.js**.

1. Copie o código a seguir no arquivo **hero.service.js**. O código oferece a seguinte funcionalidade:

   * Obtém o modelo que você criou.
   * Conecta-se ao banco de dados.
   * Cria uma variável `docquery` que usa o método `hero.find` para definir uma consulta que retorna todos os heroes.
   * Executa uma consulta com a função `docquery.exec` para obter uma lista de todos os heroes, em que o status da resposta é 200. 
   * Se o status é 500, envia de volta a mensagem de erro.
   * Já que estamos usando módulos, ele obtém os heroes. 

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

## <a name="configure-routes"></a>Configurar rotas

Em seguida, você precisa configurar rotas para lidar com as URLs para solicitações de obtenção, criação, leitura e exclusão. Os métodos de roteamento especificam funções de retorno de chamada (também chamadas de _funções de manipulador_). Essas funções são chamadas quando o aplicativo recebe uma solicitação para o ponto de extremidade especificado e o método HTTP. Use as etapas a seguir para adicionar o serviço Hero e define suas rotas:

1. No Visual Studio Code, no arquivo **routes.js**, comente a função `res.send` que envia os dados de exemplo do Hero. Em vez disso, adicione uma linha para chamar a função `heroService.getHeroes`.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. No arquivo **routes.js**, `require` o serviço Hero:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. No arquivo **hero.service.js**, atualize a função `getHeroes` para usar os parâmetros `req` e `res` da seguinte maneira:

    ```javascript
    function getHeroes(req, res) {
    ```

Levaremos um momento para analisar e vasculhar o código anterior. Primeiro, chegamos ao arquivo index.js, que configura o servidor de nó. Observe que ele configura e define suas rotas. Em seguida, seu arquivo routes.js se comunica com o serviço Hero e o manda obter funções como **getHeroes**, além de transmitir a solicitação e a resposta. O arquivo hero.service.js obtém o modelo e conecta-se ao Mongo. Em seguida, ele executa o **getHeroes** quando o chamamos e retorna uma resposta de 200. 

## <a name="run-the-app"></a>Executar o aplicativo

Em seguida, execute o aplicativo usando as etapas a seguir:

1. No Visual Studio Code, salve todas as suas alterações. À esquerda, selecione o botão **Depurar** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png"::: e o botão **Iniciar Depuração** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png":::.

1. Agora, alterne para o navegador. Abra as **Ferramentas para desenvolvedores** e a **guia Rede**. Vá para `http://localhost:3000` e lá você verá nosso aplicativo.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png" alt-text="Nova conta do Azure Cosmos DB no portal do Azure":::

Ainda não há nenhum Hero armazenado no aplicativo. Na próxima parte deste tutorial, adicionaremos funcionalidades de inserção, de push e de exclusão. Poderemos então adicionar, atualizar e excluir heroes da interface do usuário usando conexões Mongoose para nosso banco de dados do Azure Cosmos. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando esses recursos já não forem necessários, você poderá excluir o grupo de recursos, a conta do Azure Cosmos DB e todos os recursos relacionados. Use as etapas a seguir para excluir o grupo de recursos:

 1. Vá para o grupo de recursos em que você criou a conta do Azure Cosmos DB.
 1. Selecione **Excluir grupo de recursos**.
 1. Confirme o nome do grupo de recursos a excluir e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Continue para a Parte 6 do tutorial a fim de adicionar funções Post, Put e Delete ao aplicativo:

> [!div class="nextstepaction"]
> [Parte 6: Adicionar funções Post, Put e Delete ao aplicativo](tutorial-develop-mongodb-nodejs-part6.md)