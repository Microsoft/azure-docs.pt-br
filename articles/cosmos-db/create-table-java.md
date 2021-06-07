---
title: Usar o API de Tabela e o Java para criar um aplicativo – Azure Cosmos DB
description: Este guia de início rápido mostra como usar a API de Tabela do Azure Cosmos DB para criar um aplicativo com o Portal do Azure e o Java
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: java
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 48335e577ed248a42914bdaa1b1e662daf2d26cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090160"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-table-api-data"></a>Início Rápido: Criar um aplicativo Java para gerenciar os dados de API de Tabela do Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
> 

Neste início rápido, você criará uma conta da API de Tabela do Azure Cosmos DB e usará o Data Explorer e um aplicativo Java clonado do GitHub para criar tabelas e entidades. O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ou então [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure. Você também pode usar o [Emulador do Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) com um URI de `https://localhost:8081` e a chave `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [JDK (Java Development Kit) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Aponte a variável de ambiente `JAVA_HOME` para a pasta em que o JDK está instalado.
- Um [arquivo binário do Maven](https://maven.apache.org/download.cgi). 
- [Git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

> [!IMPORTANT] 
> Você precisa criar uma nova conta de API de tabela para trabalhar com os SDKs de API de tabela disponíveis. Não há suporte para contas de API de tabela criadas durante a versão prévia pelos SDKs disponíveis.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Adicionar uma tabela

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, clonaremos um aplicativo de Tabela do GitHub, definiremos a cadeia de conexão e o executaremos. Você verá como é fácil trabalhar usando dados de forma programática.

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
    git clone https://github.com/Azure-Samples/storage-table-java-getting-started.git 
    ```

> [!TIP]
> Para ver uma explicação mais detalhada de um código semelhante, confira o artigo [Exemplo da API de Tabela do Cosmos DB](table-storage-how-to-use-java.md). 

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os snippets de código a seguir. Caso contrário, pule para a seção [Atualizar a cadeia de conexão](#update-your-connection-string) deste documento.

* O código a seguir mostra como criar uma tabela dentro do Armazenamento do Azure:

  ```java
  private static CloudTable createTable(CloudTableClient tableClient, String tableName) throws StorageException, RuntimeException, IOException, InvalidKeyException,   IllegalArgumentException, URISyntaxException, IllegalStateException {
  
    // Create a new table
    CloudTable table = tableClient.getTableReference(tableName);
    try {
        if (table.createIfNotExists() == false) {
            throw new IllegalStateException(String.format("Table with name \"%s\" already exists.", tableName));
        }
    }
    catch (StorageException s) {
        if (s.getCause() instanceof java.net.ConnectException) {
            System.out.println("Caught connection exception from the client. If running with the default configuration please make sure you have started the storage emulator.");
        }
        throw s;
    }

    return table;
  }
  ```

* O código a seguir mostra como inserir dados na tabela:

  ```javascript
  private static void batchInsertOfCustomerEntities(CloudTable table) throws StorageException {
  
  // Create the batch operation
  TableBatchOperation batchOperation1 = new TableBatchOperation();
  for (int i = 1; i <= 50; i++) {
      CustomerEntity entity = new CustomerEntity("Smith", String.format("%04d", i));
      entity.setEmail(String.format("smith%04d@contoso.com", i));
      entity.setHomePhoneNumber(String.format("425-555-%04d", i));
      entity.setWorkPhoneNumber(String.format("425-556-%04d", i));
      batchOperation1.insertOrMerge(entity);
  }
  
  // Execute the batch operation
  table.execute(batchOperation1);
  }
  ```

* O código a seguir mostra como consultar dados da tabela:

  ```java
  private static void partitionScan(CloudTable table, String partitionKey) throws StorageException {
  
      // Create the partition scan query
      TableQuery<CustomerEntity> partitionScanQuery = TableQuery.from(CustomerEntity.class).where(
          (TableQuery.generateFilterCondition("PartitionKey", QueryComparisons.EQUAL, partitionKey)));
  
      // Iterate through the results
      for (CustomerEntity entity : table.execute(partitionScanQuery)) {
          System.out.println(String.format("\tCustomer: %s,%s\t%s\t%s\t%s", entity.getPartitionKey(), entity.getRowKey(), entity.getEmail(), entity.getHomePhoneNumber(), entity.  getWorkPhoneNumber()));
      }
  }
  ```

* O código a seguir mostra como excluir dados da tabela:

  ```java
  
  System.out.print("\nDelete any tables that were created.");
  
  if (table1 != null && table1.deleteIfExists() == true) {
      System.out.println(String.format("\tSuccessfully deleted the table: %s", table1.getName()));
  }
  
  if (table2 != null && table2.deleteIfExists() == true) {
      System.out.println(String.format("\tSuccessfully deleted the table: %s", table2.getName()));
  }
  ```

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo. Isso permite que seu aplicativo se comunique com o banco de dados hospedado. 

1. Em sua conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com/), selecione **Cadeia de Conexão**. 

   :::image type="content" source="./media/create-table-java/cosmos-db-quickstart-connection-string.png" alt-text="Exibir as informações de cadeia de conexão no painel da Cadeia de Conexão":::

2. Copie a CADEIA DE CONEXÃO PRIMÁRIA usando o botão de cópia do lado direito.

3. Abra *config.properties* na pasta *C:\git-samples\storage-table-java-getting-started\src\main\resources*. 

5. Comente a linha um e remova os comentários da linha dois. As primeiras duas linhas agora devem ter esta aparência.

    ```xml
    #StorageConnectionString = UseDevelopmentStorage=true
    StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=[ACCOUNTNAME];AccountKey=[ACCOUNTKEY]
    ```

6. Cole o valor de CADEIA DE CONEXÃO PRIMÁRIA do portal no valor da StorageConnectionString na linha 2. 

    > [!IMPORTANT]
    > Se o ponto de extremidade usa documents.azure.com, isso significa que você tem uma conta de versão prévia, e você precisa criar um [nova conta de API de tabela](#create-a-database-account) para trabalhar com o SDK de API de tabela geralmente disponível.
    >

7. Salve o arquivo *config.properties*.

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB. 

## <a name="run-the-app"></a>Executar o aplicativo

1. Na janela do terminal git, `cd` para a pasta storage-table-java-getting-started.

    ```git
    cd "C:\git-samples\storage-table-java-getting-started"
    ```

2. Na janela do terminal do Git, execute os comandos a seguir para executar o aplicativo Java.

    ```git
    mvn compile exec:java 
    ```

    A janela de console exibe os dados da tabela sendo adicionados ao novo banco de dados de tabela no Azure Cosmos DB.

    Agora, é possível voltar ao Data Explorer e ver, consultar, modificar e trabalhar com esses novos dados. 

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta do Azure Cosmos DB, criar uma tabela usando o Data Explorer e executar um aplicativo Java para adicionar dados de tabela.  Agora, você pode consultar os dados usando a API de Tabela.  

> [!div class="nextstepaction"]
> [Importar dados de tabela para a API de Tabela](table-import.md)