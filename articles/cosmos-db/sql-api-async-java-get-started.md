---
title: 'Tutorial: Criar um aplicativo Java com o SDK Java Assíncrono para gerenciar uma conta de API do SQL no Azure Cosmos DB'
description: Este tutorial mostra como usar armazenar e acessar dados dentro da conta da API do SQL no Azure Cosmos DB usando um aplicativo Java Assíncrono.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 12/15/2018
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: c466c11cc51ca8d8efcf9ff4e86a9dbd2fd4930c
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985647"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Tutorial: Criar um aplicativo Java com o SDK Java Assíncrono para gerenciar dados armazenados em uma conta de API do SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Como desenvolvedor, você pode ter aplicativos que usam dados de documentos NoSQL. Você pode usar a conta de API do SQL no Azure Cosmos DB para armazenar e acessar esses dados de documento. Este tutorial mostra como criar um aplicativo Java com o SDK do Java Assíncrono para armazenar e gerenciar dados de documentos. 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Criar e conectar-se a uma conta do Azure Cosmos
> * Configurando sua solução
> * Criar uma coleção
> * Criando documentos JSON
> * Consultar a coleção

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem os seguintes recursos:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/). 

* [Git](https://git-scm.com/downloads).

* [Java Development Kit (JDK) 8+](https://aka.ms/azure-jdks).

* [Maven](https://maven.apache.org/download.cgi).

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Crie uma conta do Azure Cosmos usando as seguintes etapas:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Clonar o repositório do GitHub

Clonar o repositório do GitHub para [Começar a usar Azure Cosmos DB e Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Por exemplo, de um diretório local, execute o seguinte para recuperar o projeto de exemplo localmente.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

O diretório contém um arquivo `pom.xml` e uma pasta `src/main/java/com/microsoft/azure/cosmosdb/sample` que contém o código-fonte em Java, incluindo `Main.java`. O projeto contém o código necessário para executar operações com o Azure Cosmos DB, como criar documentos e consultar dados em uma coleção. O arquivo `pom.xml` inclui uma dependência no [SDK do Java do Azure Cosmos DB no Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Conectar-se a uma conta do Azure Cosmos

Em seguida, retorne ao [portal do Azure](https://portal.azure.com) para recuperar o ponto de extremidade e a chave mestra primária. O ponto de extremidade e a chave primária do Azure Cosmos DB são necessários para que seu aplicativo reconheça onde deve se conectar e para que o Azure Cosmos DB confie na conexão do seu aplicativo. O arquivo `AccountSettings.java` contém a chave primária e os valores de URI. 

No portal do Azure, vá para sua conta do Azure Cosmos e, em seguida, clique em **Chaves**. Copie o URI e a CHAVE PRIMÁRIA do portal e cole-a no arquivo `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Obter chaves da captura de tela do portal][keys]

## <a name="initialize-the-client-object"></a>Inicializar o objeto de cliente

Inicializar o objeto cliente usando os valores de URI e de chave primária do host definidos no arquivo "AccountSettings.java".

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Criar um banco de dados

Crie seu banco de dados do Azure Cosmos DB usando o método `createDatabaseIfNotExists()` da classe DocumentClient. Um banco de dados é o contêiner lógico de armazenamento de documentos JSON particionado em coleções.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>Criar uma coleção

Você pode criar uma coleção usando o método `createDocumentCollectionIfNotExists()` da classe DocumentClient. Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada.

> [!WARNING]
> **createCollection** criará uma nova coleção com uma taxa de transferência reservada, que tem implicações de preço. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>Criar documentos JSON

Crie um documento usando o método createDocument da classe DocumentClient. Os documentos são conteúdo JSON (arbitrário) definido pelo usuário. Agora podemos inserir um ou mais documentos. O arquivo "src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java" define os documentos da família JSON. 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB dá suporte a consultas avançadas de documentos JSON armazenados em cada coleção. O código de exemplo a seguir mostra como consultar documentos no Azure Cosmos DB usando a sintaxe SQL com o método `queryDocuments`.

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>Executar o aplicativo de console Java

Para executar o aplicativo de console, vá até a pasta de projeto e compile usando o Maven:

```bash
mvn package
```

A execução de `mvn package` baixa a biblioteca mais recente do Azure Cosmos DB do Maven e produz `GetStarted-0.0.1-SNAPSHOT.jar`. Em seguida, execute o aplicativo por meio de:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Você agora concluiu este tutorial do NoSQL e tem um aplicativo de console em Java funcional.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, você pode excluir o grupo de recursos, a conta do Azure Cosmos e todos os recursos relacionados. Para fazer isso, escolha o grupo de recursos da máquina virtual, escolha **Excluir** e, em seguida, confirme o nome do grupo de recursos que será excluído.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um aplicativo Java com o SDK do Java Assíncrono para gerenciar dados da API do SQL no Azure Cosmos DB. Prossiga agora para o próximo artigo:

> [!div class="nextstepaction"]
> [Compilar um aplicativo de console do Node.js com o SDK JavaScript e o Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
