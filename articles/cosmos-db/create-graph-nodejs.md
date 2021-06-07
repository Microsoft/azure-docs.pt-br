---
title: Criar um aplicativo Node.js do Azure Cosmos DB usando a API do Gremlin
description: Apresenta um exemplo de código Node.js que pode ser usado para se conectar ao BD Cosmos do Azure e consultá-lo
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/05/2019
ms.author: chrande
ms.custom: devx-track-js
ms.openlocfilehash: 0b88456bd245f79c38d7829dca76de702f768c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360132"
---
# <a name="quickstart-build-a-nodejs-application-by-using-azure-cosmos-db-gremlin-api-account"></a>Início Rápido: Criar um aplicativo Node.js usando a conta de API do Gremlin do Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Console do Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Neste início rápido, você criará e gerenciará uma conta da API do Gremlin para Azure Cosmos DB (grafo) no portal do Azure e adicionará dados usando um aplicativo Node.js clonado do GitHub. O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global.

## <a name="prerequisites"></a>Pré-requisitos
- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Node.js 0.10.29 e posterior](https://nodejs.org/).
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora vamos clonar um aplicativo de API do Gremlin do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra um prompt de comando, crie uma nova pasta chamada exemplos de git e feche o prompt de comando.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Abra o arquivo da solução no Visual Studio. 

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os snippets de código a seguir. Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-string). 

Todos os snippets de código a seguir são retirados do arquivo *app.js*.

Este aplicativo de console usa o driver [Gremlin Node.js](https://www.npmjs.com/package/gremlin) open-source.

* O cliente Gremlin é criado.

    ```javascript
    const authenticator = new Gremlin.driver.auth.PlainTextSaslAuthenticator(
        `/dbs/${config.database}/colls/${config.collection}`, 
        config.primaryKey
    )


    const client = new Gremlin.driver.Client(
        config.endpoint, 
        { 
            authenticator,
            traversalsource : "g",
            rejectUnauthorized : true,
            mimeType : "application/vnd.gremlin-v2.0+json"
        }
    );

    ```

  Todas as configurações estão em *config.js*, que podemos editar na [seção a seguir](#update-your-connection-string).

* Uma série de funções são definidas para executar operações de Gremlin diferentes. Este é um deles:

    ```javascript
    function addVertex1()
    {
        console.log('Running Add Vertex1'); 
        return client.submit("g.addV(label).property('id', id).property('firstName', firstName).property('age', age).property('userid', userid).property('pk', 'pk')", {
                label:"person",
                id:"thomas",
                firstName:"Thomas",
                age:44, userid: 1
            }).then(function (result) {
                    console.log("Result: %s\n", JSON.stringify(result));
            });
    }
    ```

* Cada função executa um `client.execute` método com um parâmetro de cadeia de caracteres de consulta Gremlin. Aqui está um exemplo de como `g.V().count()` é executado:

    ```javascript
    function countVertices()
    {
        console.log('Running Count');
        return client.submit("g.V().count()", { }).then(function (result) {
            console.log("Result: %s\n", JSON.stringify(result));
        });
    }
    ```

* No final do arquivo, todos os métodos são então invocados. Isso irá executá-los um após o outro:

    ```javascript
    client.open()
    .then(dropGraph)
    .then(addVertex1)
    .then(addVertex2)
    .then(addEdge)
    .then(countVertices)
    .catch((err) => {
        console.error("Error running query...");
        console.error(err)
    }).then((res) => {
        client.close();
        finish();
    }).catch((err) => 
        console.error("Fatal error:", err)
    );
    ```


## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

1. Abra o arquivo *config.js*. 

2. Em *config.js*, preencha a chave `config.endpoint` com o valor do **Ponto de Extremidade do Gremlin** da página **Visão geral** da sua conta do Cosmos DB no portal do Azure. 

    `config.endpoint = "https://<your_Gremlin_account_name>.gremlin.cosmosdb.azure.com:443/";`

    :::image type="content" source="./media/create-graph-nodejs/gremlin-uri.png" alt-text="Exibir e copiar uma chave de acesso no portal do Azure, página Visão geral":::

3. Em *config.js*, preencha o valor config.primaryKey com o valor da **Chave Primária** da página **Chaves** da sua conta do Cosmos DB no portal do Azure. 

    `config.primaryKey = "PRIMARYKEY";`

   :::image type="content" source="./media/create-graph-nodejs/keys.png" alt-text="Folha de chaves do portal do Azure":::

4. Insira o nome do banco de dados e o nome do grafo (contêiner) para o valor de config.database e config.collection. 

Este é um exemplo de como o arquivo *config.js* concluído deverá ser:

```javascript
var config = {}

// Note that this must include the protocol (HTTPS:// for .NET SDK URI or wss:// for Gremlin Endpoint) and the port number
config.endpoint = "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"; 
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>Execute o aplicativo de console

1. Abra uma janela de terminal e mude (por meio do comando `cd`) para um diretório de instalação do arquivo *package.json* incluído no projeto.

2. Executar `npm install` para instalar os módulos npm necessários, incluindo `gremlin`.

3. Execute `node app.js` em um terminal para iniciar o aplicativo de nó.

## <a name="browse-with-data-explorer"></a>Procurar com o Data Explorer

Agora você pode voltar ao Data Explorer no Portal do Azure e exibir, consultar, modificar e trabalhar com seus novos dados dos grafos.

No Data Explorer, o novo banco de dados aparece no painel **Grafos**. Expanda o banco de dados, seguido pelo contêiner e, em seguida, selecione **Grafo**.

Os dados gerados pelo aplicativo de exemplo são exibidos no próximo painel dentro da guia **Grafo** quando você seleciona **Aplicar filtro**.

Tente preencher `g.V()` com `.has('firstName', 'Thomas')` para testar o filtro. Observe que o valor diferencia maiúsculas de minúsculas.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Limpar seus recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar uma conta do Azure Cosmos DB, criar um grafo usando o Data Explorer e executar um aplicativo Node.js para adicionar dados ao grafo. Agora, você pode criar consultas mais complexas e implementar uma lógica de passagem de grafo avançada usando o Gremlin. 

> [!div class="nextstepaction"]
> [Consultar usando o Gremlin](tutorial-query-graph.md)
