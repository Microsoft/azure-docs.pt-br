---
title: Tutorial do Node.js para a API do SQL do Azure Cosmos DB
description: Um tutorial do Node.js que demonstra como se conectar ao Azure Cosmos DB ou consultá-lo usando a API do SQL
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: 1115d7bb0d6857aa39f246743df54a6cf3fd3676
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93098647"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Tutorial: Compilar um aplicativo de console do Node.js com o SDK do JavaScript para gerenciar dados da API de SQL do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Async Java](./create-sql-api-java.md)
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

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>Configurar o aplicativo Node.js

Antes de começar a escrever código para compilar o aplicativo, você pode criar a estrutura do aplicativo. Execute as seguintes etapas para configurar seu aplicativo Node.js que tem o código do framework:

1. Abra seu terminal favorito.
2. Localize a pasta ou o diretório em que você deseja salvar o aplicativo do Node.js.
3. Crie arquivos JavaScript vazios com os seguintes comandos:

   * Windows:
     * `fsutil file createnew app.js 0`
     * `fsutil file createnew config.js 0`
     * `md data`
     * `fsutil file createnew data\databaseContext.js 0`

   * Linux/OS X:
     * `touch app.js`
     * `touch config.js`
     * `mkdir data`
     * `touch data/databaseContext.js`

4. Crie e inicialize um arquivo `package.json`. Use o seguinte comando:
   * ```npm init -y```

5. Instalar o @azure/cosmos módulo por meio do npm. Use o seguinte comando:
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>Definir as configurações do aplicativo

Agora que seu aplicativo existe, você precisa verificar se ele pode se comunicar como Azure Cosmos DB. Atualizando algumas definições de configuração, conforme mostrado nas etapas a seguir, você pode definir seu aplicativo para se comunicar com o Azure Cosmos DB:

1. Abra o arquivo *config.js* no seu editor de texto favorito.

1. Copie e cole o snippet de código a seguir no arquivo *config.js* e defina as propriedades `endpoint` e `key` para o URI do ponto de extremidade do Azure Cosmos DB e a chave primária. O banco de dados, os nomes de contêiner são definidos como **Tarefas** e **Itens**. A chave de partição que você usará para esse aplicativo será **/category**.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   Você pode encontrar os detalhes do ponto de extremidade e da chave no painel **Chaves** do [portal do Azure](https://portal.azure.com).

   :::image type="content" source="media/sql-api-nodejs-get-started/node-js-tutorial-keys.png" alt-text="Captura de tela de Obter chaves do portal do Azure":::

O SDK do JavaScript usa os termos genéricos *contêiner* e *item*. Um contêiner pode ser uma coleção, um gráfico ou uma tabela. Um item pode ser um documento, borda/vértice ou linha e descreve o conteúdo do contêiner. No snippet de código anterior, o código de `module.exports = config;` é usado para exportar o objeto de configuração para que você possa referenciá-lo dentro do arquivo *app.js*.

## <a name="create-a-database-and-a-container"></a>Criar um banco de dados e um contêiner

1. Abra o arquivo do *databaseContext.js* em seu editor de texto favorito.

1. Copie e cole o código a seguir no arquivo *databaseContext.js*. Esse código define uma função que criará o banco de dados "Tarefas", "Itens" e o contêiner se eles ainda não existirem na sua conta do Azure Cosmos:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   Um banco de dados é o contêiner lógico de itens particionados em contêineres. Você pode criar um banco de dados usando a `createIfNotExists` ou a função create da classe **Databases**. Um contêiner consiste em itens que, no caso da API do SQL, são documentos JSON. Você pode criar um contêiner usando a `createIfNotExists` ou a função create da classe **Containers**. Depois de criar um contêiner, você pode armazenar e consultar os dados.

   > [!WARNING]
   > Criar um contêiner tem implicações de preço. Acesse nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para saber o que esperar.

## <a name="import-the-configuration"></a>Importar a configuração

1. Abra o arquivo *app.js* no seu editor de texto favorito.

1. Copie e cole o código a seguir para importar o módulo `@azure/cosmos`, a configuração e o databaseContext que você definiu nas etapas anteriores. 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Conectar-se a uma conta do Azure Cosmos

No arquivo *app.js*, copie e cole o código a seguir para usar o ponto de extremidade e a chave salvos anteriormente para criar um objeto CosmosClient.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> Caso esteja se conectando ao **Emulador do Cosmos DB**, desabilite a verificação de TLS para o processo do nó:
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Agora que você tem o código para inicializar o cliente do Azure Cosmos DB, veremos como trabalhar com os recursos do Azure Cosmos DB.

## <a name="query-items"></a><a id="QueryItem"></a>Itens de consulta

O Azure Cosmos DB é compatível com consultas avançadas de itens JSON armazenados em cada contêiner. O código de exemplo a seguir mostra uma consulta que você pode executar nos itens em seu contêiner. Você pode consultar os itens usando a função de consulta da classe `Items`. Adicione o código a seguir ao arquivo *app.js* para consultar os itens da sua conta do Azure Cosmos:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>Criar um item

Um item pode ser criado usando a função create da classe `Items`. Se você estiver usando a API do SQL, os itens serão projetados como documentos, que são conteúdo JSON definido pelo usuário (arbitrário). Neste tutorial, você cria um novo item dentro do banco de dados de tarefas.

1. No arquivo app.js, estabeleça a definição de item:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. Adicione o seguinte código para criar o item definido anteriormente:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>Atualizar um item

O Azure Cosmos DB é compatível com a substituição de conteúdo dos itens. Copie e cole o código a seguir no arquivo *app.js*. Esse código obtém um item do contêiner e atualiza o campo *isComplete* como true.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>Excluir um item

O Azure Cosmos DB é compatível com a exclusão de itens JSON. O código a seguir mostra como obter um item por sua ID e excluí-lo. Copie e cole o código a seguir no arquivo *app.js*:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DeleteItem":::

## <a name="run-your-nodejs-application"></a><a id="Run"></a>Execute o aplicativo Node.js

De modo geral, o código final deverá ter esta aparência:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

No terminal, localize o arquivo ```app.js``` e execute o comando:

```bash 
node app.js
```

Você deverá ver a saída do aplicativo iniciado. A saída deve corresponder ao texto de exemplo abaixo.

```
Created database:
Tasks

Created container:
Items

Querying container: Items
1 - Pick up apples and strawberries.

Created new item: 3 - Complete Cosmos DB Node.js Quickstart ⚡

Updated item: 3 - Complete Cosmos DB Node.js Quickstart ⚡
Updated isComplete to true

Deleted item with id: 3
```

## <a name="get-the-complete-nodejs-tutorial-solution"></a><a id="GetSolution"></a>Obter a solução completa do tutorial do Node.js

Se você não teve tempo para concluir as etapas neste tutorial ou se apenas deseja baixar o código, poderá obtê-lo no [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

Para executar a solução de introdução que contém todos os códigos deste artigo, você precisará disto:

* Uma [conta do Azure Cosmos DB][create-account].
* A solução [Getting Started](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) disponível no GitHub.

Instale as dependências do projeto por meio do npm. Use o seguinte comando:

* ```npm install``` 

Em seguida, no arquivo ```config.js```, atualize os valores config.endpoint e config.key, conforme descrito na [Etapa 3: Definir as configurações do aplicativo](#Config).  

No terminal, localize o arquivo ```app.js``` e execute o comando:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando esses recursos já não forem necessários, você poderá excluir o grupo de recursos, a conta do Azure Cosmos DB e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos que você usou para a conta do Azure Cosmos DB, selecione **Excluir** e, em seguida, confirme o nome do grupo de recursos a ser excluído.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Monitorar uma conta do Azure Cosmos DB](./monitor-cosmos-db.md)

[create-account]: create-sql-api-dotnet.md#create-account