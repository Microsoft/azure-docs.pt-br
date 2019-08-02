---
title: Tutorial do Node.js para a API do SQL do Azure Cosmos DB
description: Um tutorial do Node.js que demonstra como se conectar ao Azure Cosmos DB ou consultá-lo usando a API do SQL
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/05/2019
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: ba1ec821bd25e3b9f4479c3d09fdf5ab981ab0a7
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305518"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Tutorial: Compilar um aplicativo de console do Node.js com o SDK do JavaScript para gerenciar dados da API de SQL do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Como um desenvolvedor, você pode ter aplicativos que usam dados de documentos NoSQL. Você pode usar uma conta de API do SQL no Azure Cosmos DB para armazenar e acessar esses dados de documento. Este tutorial mostra como compilar um aplicativo de console do Node.js para criar recursos do Azure Cosmos DB e consultá-los.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Criar e conectar-se a uma conta do Azure Cosmos DB.
> * Configurar seu aplicativo.
> * Criar um banco de dados.
> * Criar um contêiner.
> * Adicionar itens ao contêiner.
> * Execute operações básicas nos itens, no contêiner e no banco de dados.

## <a name="prerequisites"></a>Pré-requisitos 

Verifique se você tem os seguintes recursos:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 ou superior.

## <a name="create-azure-cosmos-db-account"></a>Criar conta do Azure Cosmos DB

Vamos criar uma conta do Azure Cosmos DB. Caso tenha uma conta que queira usar, você poderá pular para [Configurar seu aplicativo Node.js](#SetupNode). Se estiver usando o Emulador do Azure Cosmos DB, siga as etapas em [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e pule para [Configurar seu aplicativo Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Configurar o aplicativo Node.js

Antes de começar a escrever código para compilar o aplicativo, você pode criar a estrutura do aplicativo. Execute as seguintes etapas para configurar seu aplicativo Node.js que tem o código do framework:

1. Abra seu terminal favorito.
2. Localize a pasta ou o diretório em que você deseja salvar o aplicativo do Node.js.
3. Crie dois arquivos JavaScript vazios com os seguintes comandos:

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```

4. Crie e inicialize um arquivo `package.json`. Use o seguinte comando:
   * ```npm init -y```

5. Instalar o @azure/cosmos módulo por meio do npm. Use o seguinte comando:
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>Definir as configurações do aplicativo

Agora que seu aplicativo existe, você precisa verificar se ele pode se comunicar como Azure Cosmos DB. Atualizando algumas definições de configuração, conforme mostrado nas etapas a seguir, você pode definir seu aplicativo para se comunicar com o Azure Cosmos DB:

1. Abra ```config.js``` em seu editor de texto favorito.

1. Copie e cole o snippet de código a seguir e defina as propriedades ```config.endpoint``` e ```config.primaryKey``` como o URI do ponto de extremidade e a chave primária do Azure Cosmos DB. Ambas as configurações podem ser encontradas no [portal do Azure](https://portal.azure.com).

   ![Obter da de captura de tela do portal do Azure][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. Copie e cole os dados ```database```, ```container``` e ```items``` de seu objeto ```config``` abaixo de onde você configura suas propriedades ```config.endpoint``` e ```config.primaryKey```. Se já tiver dados que gostaria de armazenar em seu banco de dados, você poderá usar a ferramenta de Migração de Dados do Azure Cosmos DB, em vez de definir os dados aqui. O arquivo config.js deve ter o seguinte código:

   [!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/config.js)]

   O SDK do JavaScript usa os termos genéricos *contêiner* e *item*. Um contêiner pode ser uma coleção, um gráfico ou uma tabela. Um item pode ser um documento, borda/vértice ou linha e descreve o conteúdo do contêiner. 
   
   O código `module.exports = config;` é usado para exportar o objeto ```config```, de modo que você pode referenciá-lo no arquivo ```app.js```.

## <a id="Connect"></a>Conectar-se a uma conta do Azure Cosmos DB

1. Abra o arquivo ```app.js``` vazio no editor de texto. Copie e cole o código a seguir para importar o módulo ```@azure/cosmos``` e o módulo recém-criado ```config```.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. Copie e cole o código para usar ```config.endpoint``` e ```config.primaryKey``` salvos anteriormente para criar um novo CosmosClient.

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
   ```
   
> [!Note]
> Se estiver se conectando ao **Emulador do Cosmos DB**, desabilite a verificação SSL criando uma Política de conexão personalizada.
>   ```
>   const ConnectionPolicy = require('@azure/cosmos').ConnectionPolicy;
>   const connectionPolicy = new ConnectionPolicy();
>   connectionPolicy.DisableSSLVerification = true;
>
>   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey }, connectionPolicy });
>   ```

Agora que você tem o código para inicializar o cliente do Azure Cosmos DB, veremos como trabalhar com os recursos do Azure Cosmos DB.

## <a name="create-a-database"></a>Criar um banco de dados

1. Copie e cole o código a seguir para definir a ID do banco de dados e a ID do contêiner. É por meio dessas IDs que o cliente do Azure Cosmos DB encontrará o banco de dados e o contêiner corretos.

   ```javascript
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };
   ```

   Um banco de dados pode ser criado usando a função `createIfNotExists` ou create da classe **Databases**. Um banco de dados é o contêiner lógico de itens particionados em contêineres. 

2. Copie e cole os métodos **createDatabase** e **readDatabase** no arquivo app.js abaixo das definições de ```databaseId``` e ```containerId```. A função **createDatabase** criará um banco de dados com a ID ```FamilyDatabase```, especificada no objeto ```config```, se ele ainda não existir. A função **readDatabase** lerá a definição do banco de dados para garantir que o banco de dados existe.

   ```javascript
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { body: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. Copie e cole o código abaixo no local onde você configurou as funções **createDatabase** e **readDatabase** para adicionar a função auxiliar **exit** que imprimirá a mensagem de saída. 

   ```javascript
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. Copie e cole o código abaixo no local onde você configurou a função **exit** para chamar as funções **createDatabase** e **readDatabase**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   Aqui, seu código no ```app.js``` deverá estar mais ou menos assim:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. No terminal, localize o arquivo ```app.js``` e execute o comando: 

   ```bash 
   node app.js
   ```

## <a id="CreateContainer"></a>Criar um contêiner

Em seguida, crie um contêiner na conta do Azure Cosmos DB para que você possa armazenar e consultar os dados. 

> [!WARNING]
> Criar um contêiner tem implicações de preço. Acesse nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para saber o que esperar.

Um contêiner pode ser criado usando a função `createIfNotExists` ou create da classe **Containers**.  Um contêiner é formado por itens (que, no caso da API de SQL, são documentos JSON) e lógica de aplicativo JavaScript associada.

1. Copie e cole as funções **createContainer** e **readContainer** sob a função **readDatabase** no arquivo app.js. A função **createContainer** criará um novo contêiner com a ```containerId```, especificada no objeto ```config``` se ele ainda não existir. A função **readContainer** lerá a definição do contêiner para verificar a existência do contêiner.

   ```javascript
   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. Copie e cole o código abaixo da chamada para **readDatabase** para executar as funções **createContainer** e **readContainer**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   Neste ponto, seu código no ```app.js``` deverá ter essa aparência:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. No terminal, localize o arquivo ```app.js``` e execute o comando: 

   ```bash 
   node app.js
   ```

## <a id="CreateItem"></a>Criar um item

Um item pode ser criado usando a função create da classe **Itens**. Se você estiver usando a API do SQL, os itens serão projetados como documentos, que são conteúdo JSON definido pelo usuário (arbitrário). Agora você pode inserir um item no Azure Cosmos DB.

1. Copie e cole a função **createFamilyItem** sob a função **readContainer**. A função **createFamilyItem** cria os itens que contêm os dados JSON salvos no objeto ```config```. Antes da criação, vamos verificar para garantir que não haja um item com a mesma ID.

   ```javascript
   /**
   * Create family item
   */
   async function createFamilyItem(itemBody) {
      const { item } = await client.database(databaseId).container(containerId).items.upsert(itemBody);
      console.log(`Created family item with id:\n${itemBody.id}\n`);
   };
   ```

1. Copie e cole o código abaixo da chamada para **readContainer** para executar a função **createFamilyItem**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. No terminal, localize o arquivo ```app.js``` e execute o comando: 

   ```bash 
   node app.js
   ```


## <a id="Query"></a>Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB tem suporte para consultas avançadas de documentos JSON armazenados em cada contêiner. O código de exemplo a seguir mostra uma consulta que pode ser executada em documentos em seu contêiner.

1. Copie e cole a função **queryContainer** abaixo da função **createFamilyItem** no arquivo app.js. O Azure Cosmos DB dá suporte a consultas do tipo SQL, conforme mostrado abaixo.

   ```javascript
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec, {enableCrossPartitionQuery:true}).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. Copie e cole o código abaixo da chamada para **createFamilyItem** para executar a função **queryContainer**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. No terminal, localize o arquivo ```app.js``` e execute o comando:

   ```bash 
   node app.js
   ```


## <a id="ReplaceItem"></a>Substituir um item
O Azure Cosmos DB é compatível com a substituição de conteúdo dos itens.

1. Copie e cole a função **replaceFamilyItem** abaixo da função **queryContainer** no arquivo app.js. Observe que alteramos a propriedade 'grade' de um filho para um valor 6, que anteriormente era um 5.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
     const { item } = await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).replace(itemBody);
   };
   ```

1. Copie e cole o código abaixo da chamada para **queryContainer** para executar a função **replaceFamilyItem**. Além disso, adicione o código para chamar **queryContainer** novamente para verificar se o item foi alterado com êxito.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. No terminal, localize o arquivo ```app.js``` e execute o comando:

   ```bash 
   node app.js
   ```


## <a id="DeleteItem"></a>Excluir um item

O Azure Cosmos DB é compatível com a exclusão de itens JSON.

1. Copie e cole a função **deleteFamilyItem** sob a função **replaceFamilyItem**.

   ```javascript
   /**
   * Delete the item by ID.
   */
   async function deleteFamilyItem(itemBody) {
     await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. Copie e cole o código abaixo da chamada para a segunda **queryContainer** para executar a função **deleteFamilyItem**.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. No terminal, localize o arquivo ```app.js``` e execute o comando: 

   ```bash 
   node app.js
   ```


## <a id="DeleteDatabase"></a>Excluir o banco de dados

Excluir o banco de dados criado removerá o banco de dados e todos os recursos filho (contêineres, itens etc.).

1. Copie e cole a função **cleanup** sob a função **deleteFamilyItem** para remover o banco de dados e todos os recursos filho.

   ```javascript
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. Copie e cole o código abaixo da chamada para **deleteFamilyItem** para executar a função **cleanup**.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>Execute o aplicativo Node.js

De modo geral, o código final deverá ter esta aparência:

[!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/app.js)]

No terminal, localize o arquivo ```app.js``` e execute o comando: 

```bash 
node app.js
```

Você deverá ver a saída do aplicativo iniciado. A saída deve corresponder ao texto de exemplo abaixo.

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

## <a id="GetSolution"></a>Obter a solução completa do tutorial do Node.js 

Se você não teve tempo para concluir as etapas neste tutorial ou se apenas deseja baixar o código, poderá obtê-lo no [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). 

Para executar a solução de introdução que contém todos os códigos deste artigo, você precisará disto: 

* Uma [conta do Azure Cosmos DB][create-account]. 
* A solução [Getting Started](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) disponível no GitHub. 

Instale as dependências do projeto por meio do npm. Use o seguinte comando: 

* ```npm install``` 

Em seguida, no arquivo ```config.js```, atualize os valores config.endpoint e config.primaryKey, como descrito na [Etapa 3: Definir as configurações do aplicativo](#Config).  

No terminal, localize o arquivo ```app.js``` e execute o comando:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando esses recursos já não forem necessários, você poderá excluir o grupo de recursos, a conta do Azure Cosmos DB e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos que você usou para a conta do Azure Cosmos DB, selecione **Excluir** e, em seguida, confirme o nome do grupo de recursos a ser excluído.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Monitorar uma conta do Azure Cosmos DB](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
