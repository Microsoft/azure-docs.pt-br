---
title: Aplicativo Java com a API do Cassandra para Azure Cosmos DB usando o SDK do Java 4.0
description: Este guia de início rápido mostra como usar a API do Cassandra do Azure Cosmos DB para criar um aplicativo de perfil com o portal do Azure e o Java 4.0 SDK.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: ee30af3f07c8b350393822f01833feec6e3b52e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93099803"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data-v4-driver"></a>Criar um aplicativo Java para gerenciar os dados da API do Cassandra do Azure Cosmos DB (Driver v4)
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> 

Neste início rápido, você cria uma conta de API do Cassandra do Azure Cosmos DB e usa um aplicativo Java do Cassandra clonado no GitHub para criar um banco de dados e um contêiner do Cassandra usando os [drivers Apache Cassandra v4.x](https://github.com/datastax/java-driver/tree/4.x) para Java. O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ou então [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure.
- [JDK (Java Development Kit) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Aponte a variável de ambiente `JAVA_HOME` para a pasta em que o JDK está instalado.
- Um [arquivo binário do Maven](https://maven.apache.org/download.cgi). No Ubuntu, execute `apt-get install maven` para instalar o Maven.
- [Git](https://www.git-scm.com/downloads). No Ubuntu, execute `sudo apt-get install git` para instalar o Git.

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

Antes de criar um banco de dados de documentos, você precisa criar uma conta Cassandra com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos trabalhar com o código. Agora vamos clonar um aplicativo do Cassandra do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra um prompt de comando. Crie uma pasta chamada `git-samples`. Em seguida, feche o prompt de comando.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started-v4.git
    ```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como o código cria os recursos de banco de dados,examine os snippets a seguir. Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-string). Esses snippets são extraídos do arquivo *src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java*.  

* O `CqlSession` se conecta à API do Cassandra do Azure Cosmos DB e retorna uma sessão para acessar (o objeto `Cluster` do driver v3 agora está obsoleto). O host, a porta e a senha e o nome de usuário do Cassandra são definidos usando a página de cadeia de conexão no portal do Azure.

    ```java
        this.session = CqlSession.builder().withSslContext(sc)
                .addContactPoint(new InetSocketAddress(cassandraHost, cassandraPort)).withLocalDatacenter(region)
                .withAuthCredentials(cassandraUsername, cassandraPassword).build();
    ```


Os snippets de código a seguir são do arquivo *src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java*.

* Remova o keyspace se ele já existir em uma execução anterior.

    ```java
    public void dropKeyspace() {
        String query = "DROP KEYSPACE IF EXISTS "+keyspace+"";
        session.execute(query);
        LOGGER.info("dropped keyspace '"+keyspace+"'");
    } 
    ```
* Um novo keyspace é criado.

    ```java
    public void createKeyspace() {
        String query = "CREATE KEYSPACE "+keyspace+" WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }";
        session.execute(query);
        LOGGER.info("Created keyspace '"+keyspace+"'");
    }
    ```

* Uma nova tabela é criada.

   ```java
    public void createTable() {
        String query = "CREATE TABLE "+keyspace+"."+table+" (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table '"+table+"'");
    }
   ```

* Entidades de usuário são inseridas usando um objeto de instrução preparado.

    ```java
    public String prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  "+keyspace+"."+table+" (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return insertStatement;
    }

    public void insertUser(String preparedStatement, int id, String name, String city) {
        PreparedStatement prepared = session.prepare(preparedStatement);
        BoundStatement bound = prepared.bind(id, city, name).setIdempotent(true);
        session.execute(bound);
    }
    ```

* Consulte para obter as informações de todos os usuários.

    ```java
    public void selectAllUsers() {
        final String query = "SELECT * FROM "+keyspace+"."+table+"";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

 * Consulte para obter informações de um usuário único.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM "+keyspace+"."+table+" where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo. Os detalhes da cadeia de conexão permitem que o aplicativo se comunique com o banco de dados hospedado.

1. Em sua conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com/), selecione **Cadeia de Conexão**. 

    :::image type="content" source="./media/create-cassandra-java/copy-username-connection-string-azure-portal.png" alt-text="Exibir e copiar um nome de usuário do portal do Azure, página Cadeia de Conexão":::

2. Use o botão :::image type="icon" source="./media/create-cassandra-java/copy-button-azure-portal.png"::: no lado direito da tela para copiar o valor do PONTO DE CONTATO.

3. Abra o arquivo *config.properties* da pasta *C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources*. 

3. Cole o valor do PONTO DE CONTATO do portal sobre `<Cassandra endpoint host>` na linha 2.

    A linha 2 do *config.properties* agora deve ser semelhante a 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Volte ao portal e copie o valor do NOME DE USUÁRIO. Cole o valor do NOME DE USUÁRIO do portal sobre `<cassandra endpoint username>` na linha 4.

    A linha 4 do *config.properties* agora deve ser semelhante a 

    `cassandra_username=cosmos-db-quickstart`

4. Volte ao portal e copie o valor da SENHA. Cole o valor da SENHA do portal sobre `<cassandra endpoint password>` na linha 5.

    A linha 5 do *config.properties* agora deve ser semelhante a 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Na linha 6, se você quiser usar um certificado TLS/SSL específico, substitua `<SSL key store file location>` pela localização do certificado TLS/SSL. Se um valor não for fornecido, será usado o certificado JDK instalado em <JAVA_HOME>/jre/lib/security/cacerts. 

6. Se você alterou a linha 6 para usar um certificado TLS/SSL específico, atualize a linha 7 para usar a senha desse certificado. 

7. Observe que será necessário adicionar a região padrão (por exemplo, `West US`) para o ponto de contato, por exemplo,

    `region=West US`

    Isso ocorre porque o driver v.4x permite que apenas um DC local seja emparelhado com o ponto de contato. Se você desejar adicionar uma região diferente do padrão (que é a região que foi fornecida quando a conta de Cosmos DB foi criada pela primeira vez), será necessário usar o sufixo regional ao adicionar um ponto de contato, por exemplo, `host-westus.cassandra.cosmos.azure.com`.

8. Salve o arquivo *config.properties*.

## <a name="run-the-java-app"></a>Executar o aplicativo Java

1. Na janela do terminal do Git, execute `cd` na pasta `azure-cosmosdb-cassandra-java-getting-started-v4`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started-v4"
    ```

2. Na janela do terminal do Git, use o seguinte comando para gerar o arquivo `cosmosdb-cassandra-examples.jar`.

    ```git
    mvn clean install
    ```

3. Na janela do terminal git, execute os comandos a seguir para iniciar o aplicativo Java.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    A janela do terminal exibe notificações de que o keyspace e a tabela foram criados. Em seguida, todos os usuários na tabela são selecionados e retornados, e é exibida a saída. Depois, é selecionada uma linha por ID, e o valor é exibido.  

    Pressione Ctrl+C para interromper a execução do programa e feche a janela do console.

4. No portal do Azure, abra **Data Explorer** para consultar, modificar e trabalhar com esses novos dados. 

    :::image type="content" source="./media/create-cassandra-java/view-data-explorer-java-app.png" alt-text="Exibir os dados no Data Explorer – Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do Azure Cosmos DB com a API do Cassandra e executar um aplicativo Cassandra Java que cria um banco de dados e contêiner Cassandra. Agora, é possível importar outros dados para sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)
