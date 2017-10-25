---
title: Compilar um aplicativo Node.js do Azure Cosmos DB usando a API do Graph | Microsoft Docs
description: "Apresenta um exemplo de código Node.js que pode ser usado para se conectar ao BD Cosmos do Azure e consultá-lo"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/29/2017
ms.author: denlee
ms.openlocfilehash: 228d739ac4505d9f16c43bb484dd8050631f084e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api"></a>Azure Cosmos DB: Compilar um aplicativo Node.js usando a API do Graph

O Azure Cosmos DB é o serviço de banco de dados multi-modelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB. 

Este artigo de Início rápido demonstra como criar uma conta do Azure Cosmos DB para a API do Graph (versão prévia), um banco de dados e um gráfico usando o portal do Azure. Em seguida, você compila e executa um aplicativo de console usando o driver [Gremlin Node.js](https://www.npmjs.com/package/gremlin) de software livre.

## <a name="prerequisites"></a>Pré-requisitos

Antes que possa executar esta amostra, você deverá ter os seguintes pré-requisitos:
* [Node.js](https://nodejs.org/en/) versão v0.10.29 ou posterior
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Crie uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um gráfico

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, clonaremos um aplicativo de API do Graph do GitHub, definiremos a cadeia de conexão e o executaremos. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra uma janela de terminal do Git, tal como um Git Bash e altere (por meio do comando `cd`) para um diretório de trabalho.

2. Execute o comando a seguir para clonar o repositório de exemplo: 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Abra o arquivo da solução no Visual Studio. 

## <a name="review-the-code"></a>Examine o código

Façamos uma rápida revisão do que está acontecendo no aplicativo. Abra o arquivo `app.js` e você verá as linhas de código a seguir. 

* O cliente Gremlin é criado.

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

  Todas as configurações estão em `config.js` e podem ser editadas na seção Editar a seguir.

* Uma série de etapas do Gremlin são executadas usando o método `client.execute`.

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

1. Abra o arquivo config.js. 

2. Em config.js, preencha a chave config.endpoint com o valor **Gremlin URI** na página do portal do Azure **Visão geral**. 

    `config.endpoint = "GRAPHENDPOINT";`

    ![Exibir e copiar uma chave de acesso no Portal do Azure, folha Chaves](./media/create-graph-nodejs/gremlin-uri.png)

   Se o valor do **URI do Gremlin** estiver em branco, você pode gerar o valor a partir da página **Chaves** no portal. Use o valor do **URI**, remova https:// e altere os documentos para gráficos.

   O ponto de extremidade do Gremlin deve ser apenas o nome de host sem número de porta do protocolo como `mygraphdb.graphs.azure.com` (não `https://mygraphdb.graphs.azure.com` ou `mygraphdb.graphs.azure.com:433`).

3. Em config.js, preencha o valor config.primaryKey com o valor **Chave primária** na página do portal do Azure **Chaves**. 

    `config.primaryKey = "PRIMARYKEY";`

   ![Folha “Chaves” do portal do Azure](./media/create-graph-nodejs/keys.png)

4. Insira o nome do banco de dados e o nome do gráfico (contêiner) para o valor de config.database e config.collection. 

Aqui está um exemplo da aparência do seu arquivo config.js concluído:

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "testgraphacct.graphs.azure.com";
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>Execute o aplicativo de console

1. Abra uma janela de terminal e mude (por meio do comando `cd`) para um diretório de instalação para o arquivo package.json incluído no projeto.

2. Executar `npm install` para instalar os módulos npm necessários, incluindo `gremlin`.

3. Execute `node app.js` em um terminal para iniciar o aplicativo de nó.

## <a name="browse-with-data-explorer"></a>Procurar com o Data Explorer

Agora você pode voltar ao Data Explorer no Portal do Azure e exibir, consultar, modificar e trabalhar com seus novos dados gráficos.

No Data Explorer, o novo banco de dados aparece no painel **Gráficos**. Expanda o banco de dados, seguido pela coleção e, em seguida, selecione **Gráfico**.

Os dados gerados pelo aplicativo de exemplo são exibidos no próximo painel dentro da guia **Gráfico** quando você seleciona **Aplicar filtro**.

Tente preencher `g.V()` com `.has('firstName', 'Thomas')` para testar o filtro. Observe que o valor diferencia maiúsculas de minúsculas.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Limpar seus recursos

Se você não planeja continuar usando este aplicativo, exclua todos os recursos criados neste artigo fazendo o seguinte: 

1. No portal do Azure, no menu de navegação esquerdo, selecione **Grupos de recursos**. Em seguida, selecione o nome do recurso que você criou. 

2. Na página do grupo de recursos, selecione **Excluir**. Digite o nome do recurso a ser excluída e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como criar uma conta do Azure Cosmos DB, como criar um gráfico usando o Data Explorer e como executar um aplicativo. Agora, você pode criar consultas mais complexas e implementar uma lógica de passagem de gráfico avançada usando o Gremlin. 

> [!div class="nextstepaction"]
> [Consultar usando o Gremlin](tutorial-query-graph.md)
