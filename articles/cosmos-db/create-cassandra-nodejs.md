---
title: 'Início Rápido: API do Cassandra com .Node.js – Azure Cosmos DB'
description: Este guia de início rápido mostra como usar a API Cassandra do Azure Cosmos DB para criar um aplicativo de perfil com Node.js
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/10/2021
ms.custom: devx-track-js
ms.openlocfilehash: 126ece1327fa92c9b92c587922f1b8d9335d1a01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559284"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Início Rápido: compilar um aplicativo do Cassandra com o SDK do Node.js e o Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Neste início rápido, você cria uma conta de API do Cassandra do Azure Cosmos DB e usa um aplicativo Cassandra Node.js clonado no GitHub para criar um banco de dados e contêiner Cassandra. O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Como alternativa, você pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure, sem ônus e sem compromisso.

Além disso, você precisa de:
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) versão v0.10.29 ou superior
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

Antes de criar um banco de dados de documentos, você precisa criar uma conta Cassandra com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos clonar um aplicativo da API do Cassandra do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra um prompt de comando. Crie uma pasta chamada `git-samples`. Em seguida, feche o prompt de comando.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do Git, como git bash. Use o comando `cd` para mudar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

1. Instale as dependências de Node.js com npm.

    ```bash
    npm install
    ```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como o código cria os recursos de banco de dados,examine os snippets a seguir. Os snippets são todos obtidos do arquivo `uprofile.js` na pasta `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-string). 

* Os valores de nome de usuário e senha são definidos usando a página de cadeia de conexão no portal do Microsoft Azure. 

   ```javascript
    let authProvider = new cassandra.auth.PlainTextAuthProvider(
        config.username,
        config.password
    );
   ```

* O `client` é inicializado com informações do contactPoint. O contactPoint é recuperado do Portal do Azure.

    ```javascript
    let client = new cassandra.Client({
        contactPoints: [`${config.contactPoint}:10350`],
        authProvider: authProvider,
        localDataCenter: config.localDataCenter,
        sslOptions: {
            secureProtocol: "TLSv1_2_method"
        },
    });
    ```

* O `client` se conecta à API Cassandra do Azure Cosmos DB.

    ```javascript
    client.connect();
    ```

* Um novo keyspace é criado.

    ```javascript
    var query =
        `CREATE KEYSPACE IF NOT EXISTS ${config.keySpace} WITH replication = {'class': 'NetworkTopologyStrategy', 'datacenter' : '1' }`;
    await client.execute(query);
  }
    ```

* Uma nova tabela é criada.

   ```javascript
    query =
        `CREATE TABLE IF NOT EXISTS ${config.keySpace}.user (user_id int PRIMARY KEY, user_name text, user_bcity text)`;
    await client.execute(query);
   },
   ```

* Entidades de chave/valor são inseridas.

    ```javascript
    const arr = [
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (1, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (2, 'JiriK', 'Toronto')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (3, 'IvanH', 'Mumbai')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (4, 'IvanH', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (5, 'IvanaV', 'Belgaum')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (6, 'LiliyaB', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (7, 'JindrichH', 'Buenos Aires')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (8, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (9, 'JozefM', 'Seattle')`,
    ];
    for (const element of arr) {
        await client.execute(element);
    }
    ```

* Consulta para obter todos os pares chave-valor.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user`;
    const resultSelect = await client.execute(query);

    for (const row of resultSelect.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* Consulta para obter uma chave-valor.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user where user_id=1`;
    const resultSelectWhere = await client.execute(query);

    for (const row of resultSelectWhere.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* Feche a conexão. 

    ```javascript
    client.shutdown();
    ```  

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo. A cadeia de conexão permite que seu aplicativo se comunique com o banco de dados hospedado.

1. Em sua conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com/), selecione **Cadeia de Conexão**. 

1. Use o botão :::image type="icon" source="./media/create-cassandra-nodejs/copy.png"::: no lado direito da tela para copiar o valor superior, o PONTO DE CONTATO.

    :::image type="content" source="./media/create-cassandra-nodejs/keys.png" alt-text="Exibir e copiar o PONTO DE CONTATO, o NOME DE USUÁRIO e a SENHA da página de cadeia de conexão do Portal do Azure":::

1. Abra o arquivo `config.js` . 

1. Cole o valor PONTO DE CONTATO do portal sobre `'CONTACT-POINT` na linha 9.

    Agora, a linha 9 deve ser semelhante a 

    `contactPoint: "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com",`

1. Copie o valor NOME DE USUÁRIO do portal e cole-o sobre `<FillMEIN>` na linha 2.

    Agora a linha 2 deve ser semelhante a 

    `username: 'cosmos-db-quickstart',`

1. Copie o valor SENHA do portal e cole-o sobre `USERNAME` na linha 8.

    Agora a linha 8 deve ser semelhante a

    `password: '2Ggkr662ifxz2Mg==',`

1. Substitua REGIÃO pela região do Azure em que você criou este recurso.

1. Salve o arquivo `config.js`.


## <a name="run-the-nodejs-app"></a>Executar o aplicativo do Node.js

1. Na janela do terminal, verifique se você está no diretório de exemplo clonado anteriormente:

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

1. Execute o aplicativo Node:

    ```bash
    npm start
    ```

4. Verifique se os resultados estão conforme o esperado na linha de comando.

    :::image type="content" source="./media/create-cassandra-nodejs/output.png" alt-text="Exibir e verificar a saída":::

    Pressione CTRL+C para interromper a execução do programa e feche a janela do console. 

5. No portal do Azure, abra **Data Explorer** para consultar, modificar e trabalhar com esses novos dados. 

    :::image type="content" source="./media/create-cassandra-nodejs/data-explorer.png" alt-text="Exibir os dados no Data Explorer"::: 

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do Azure Cosmos DB com a API do Cassandra e executar um aplicativo Cassandra Node.js que cria um banco de dados e contêiner Cassandra. Agora, é possível importar outros dados para sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)