---
title: Início Rápido – Usar Java para criar um banco de dados de documentos usando o Azure Cosmos DB
description: Este início rápido apresenta um exemplo de código Java que pode ser usado para se conectar à API do SQL do Azure Cosmos DB e consultá-la
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 4b62b591c408f663fd28d5077af924f785ee66c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090402"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Início Rápido: Criar um aplicativo Java para gerenciar os dados de API de SQL do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [SDK do Java v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Neste início rápido, você criará e gerenciará uma conta da API do SQL para Azure Cosmos DB (grafo) no portal do Azure, usando um aplicativo Java clonado do GitHub. Primeiro, crie uma conta da API de SQL do Azure Cosmos DB usando o portal do Azure, crie um aplicativo Java usando o SDK do Java para SQL e, em seguida, adicione recursos à conta do Cosmos DB usando o aplicativo Java. O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global.

> [!IMPORTANT]  
> Este início rápido é somente para o SDK do Java v4 do Azure Cosmos DB. Confira as [Notas sobre a versão](sql-api-sdk-java-v4.md) do SDK do Java v4 do Azure Cosmos DB, o [repositório do Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), as [dicas de desempenho](performance-tips-java-sdk-v4-sql.md) do SDK do Java v4 do Azure Cosmos DB e o [Guia de solução de problemas](troubleshoot-java-sdk-v4-sql.md) do SDK do Java v4 do Azure Cosmos DB para obter mais informações. Se você estiver usando uma versão mais antiga do que a v4, confira o guia [Migrar para o SDK do Java v4 do Azure Cosmos DB](migrate-java-v4-sdk.md) para obter ajuda na atualização para a v4.
>

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ou então [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure. Você também pode usar o [Emulador do Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) com um URI de `https://localhost:8081` e a chave `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [JDK (Java Development Kit) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Aponte a variável de ambiente `JAVA_HOME` para a pasta em que o JDK está instalado.
- Um [arquivo binário do Maven](https://maven.apache.org/download.cgi). No Ubuntu, execute `apt-get install maven` para instalar o Maven.
- [Git](https://www.git-scm.com/downloads). No Ubuntu, execute `sudo apt-get install git` para instalar o Git.

## <a name="introductory-notes"></a>Notas introdutórias

*A estrutura de uma conta do Cosmos DB.* Independentemente da API ou da linguagem de programação, uma *conta* do Cosmos DB contém zero ou mais *bancos de dados*, um *DB* (banco de dados) contém zero ou mais *contêineres* e um *contêiner* contém zero ou mais itens, conforme mostrado no diagrama abaixo:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Entidades de conta do Azure Cosmos" border="false":::

Você pode ler mais sobre bancos de dados, contêineres e itens [aqui.](account-databases-containers-items.md) Algumas propriedades importantes são definidas no nível do contêiner, entre elas a *taxa de transferência provisionada* e a *chave de partição*. 

A taxa de transferência provisionada é medida em *RUs* (Unidades de Solicitação) que têm um preço monetário e são um fator determinante substancial no custo operacional da conta. A taxa de transferência provisionada pode ser selecionada na granularidade por contêiner ou na granularidade por banco de dados. No entanto, há preferência pela especificação da taxa de transferência no nível de contêiner normalmente. Você pode ler mais sobre o provisionamento da taxa de transferência [aqui.](set-throughput.md)

À medida que itens são inseridos em um contêiner do Cosmos DB, o banco de dados cresce horizontalmente adicionando mais armazenamento e computação para lidar com solicitações. As capacidades de armazenamento e computação são adicionadas em unidades discretas conhecidas como *partições* e você deve escolher um campo em seus documentos para ser a chave de partição que mapeia cada documento para uma partição. A maneira como as partições são gerenciadas é que cada participação recebe uma fatia aproximadamente igual do intervalo dos valores de chave de partição; portanto, é recomendável escolher uma chave de partição relativamente aleatória ou distribuída uniformemente. Caso contrário, algumas partições terão substancialmente mais solicitações (*partição a quente*), enquanto outras partições terão substancialmente menos solicitações (*partição a frio*), o que deve ser evitado. Você pode saber mais sobre particionamento [aqui](partitioning-overview.md).

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

Antes de criar um banco de dados de documentos, você precisa criar uma conta de API do SQL com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Adicionar um contêiner

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos trabalhar com o código. Vamos clonar um aplicativo de API do SQL do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática. 

Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os snippets de código a seguir. Caso contrário, você poderá seguir para [Executar o aplicativo](#run-the-app). 


# <a name="sync-api"></a>[API síncrona](#tab/sync)

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Gerenciar recursos de banco de dados usando a API síncrona

* Inicialização de `CosmosClient`. O `CosmosClient` fornece a representação lógica do lado do cliente para o serviço de banco de dados do Azure Cosmos. Esse cliente é usado para configurar e executar solicitações no serviço.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* Criação de `CosmosDatabase`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* Criação de `CosmosContainer`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Criação de item usando o método `createItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* As leituras de ponto são realizadas usando o método `readItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* As consultas SQL em JSON são executadas usando o método `queryItems`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

# <a name="async-api"></a>[API assíncrona](#tab/async)

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Gerenciar recursos de banco de dados usando a API assíncrona

* As chamadas à API assíncronas retornam imediatamente, sem aguardar uma resposta do servidor. Por conta disso, os snippets de código a seguir mostram os padrões de design adequados para realizar todas as tarefas de gerenciamento anteriores usando a API assíncrona.

* Inicialização de `CosmosAsyncClient`. O `CosmosAsyncClient` fornece a representação lógica do lado do cliente para o serviço de banco de dados do Azure Cosmos. Esse cliente é usado para configurar e executar solicitações assíncronas no serviço.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* Criação de `CosmosAsyncDatabase`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* Criação de `CosmosAsyncContainer`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Assim como acontece com a API de sincronização, a criação do item é realizada usando o método `createItem`. Este exemplo mostra como emitir com eficiência várias solicitações assíncronas de `createItem`, assinando um fluxo reativo que emite as solicitações e imprime as notificações. Como esse exemplo simples é executado até a conclusão e é encerrado, instâncias de `CountDownLatch` são usadas para garantir que o programa não seja encerrado durante a criação do item. **A prática adequada de programação assíncrona adequada não consiste em bloquear as chamadas assíncronas. Em casos de uso realistas, as solicitações são geradas de um loop main() que é executado indefinidamente, eliminando a necessidade de bloqueio de chamadas assíncronas.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Assim como acontece com a API síncrona, as leituras de ponto são executadas usando o método `readItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Assim como acontece com a API síncrona, as consultas SQL em JSON são executadas usando o método `queryItems`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

---

## <a name="run-the-app"></a>Executar o aplicativo

Agora volte ao portal do Azure para obter as informações de cadeia de conexão e iniciar o aplicativo com as informações de ponto de extremidade. Isso permite que seu aplicativo se comunique com o banco de dados hospedado.

1. Na janela do terminal do Git, execute `cd` na pasta do código de exemplo.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. Na janela do terminal git, use o seguinte comando para instalar os pacotes necessários do Java.

    ```bash
    mvn package
    ```

3. Na janela do terminal git, use o seguinte comando para iniciar o aplicativo Java (substitua SYNCASYNCMODE com `sync` ou `async` dependendo de qual código de exemplo você deseja executar, substitua YOUR_COSMOS_DB_HOSTNAME pelo valor entre aspas do URI do portal e substitua YOUR_COSMOS_DB_MASTER_KEY pela chave primária entre aspas do portal)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    A janela de terminal exibe uma notificação de que o banco de dados FamilyDB foi criado. 
    
4. O aplicativo cria um banco de dados com o nome `AzureSampleFamilyDB`
5. O aplicativo cria um contêiner com o nome `FamilyContainer`
6. O aplicativo realizará leituras de ponto usando IDs de objeto e valor de chave de partição (que é o lastName, no nosso exemplo). 
7. O aplicativo consultará os itens para recuperar todas as famílias cujo sobrenome esteja em ('Andersen', 'Wakefield', 'Johnson')

7. O aplicativo não exclui os recursos criados. Volte para o portal a fim de [limpar os recursos](#clean-up-resources).  da sua conta para que você não incorra em encargos.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta da API SQL do Azure Cosmos DB, criar um banco de dados e um contêiner de documentos usando o Data Explorer e executar um aplicativo Java para fazer a mesma coisa de forma programát. Agora, é possível importar outros dados para sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados no Azure Cosmos DB](import-data.md)
