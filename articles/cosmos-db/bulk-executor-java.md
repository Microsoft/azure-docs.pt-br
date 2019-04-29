---
title: Usar a biblioteca de Java do executor em massa para executar a importação em massa e atualizar as operações no Azure Cosmos DB
description: Importação em massa e atualizar documentos do Azure Cosmos DB usando a biblioteca do Java de executor em massa.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: b6a5712c617ab1e16b5341d9727b840fe8ea2213
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894400"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Use a biblioteca bulk executor Java para executar operações em massa no Azure Cosmos DB

Este tutorial fornece instruções sobre como usar a biblioteca BulkExecutor Java do Azure Cosmos DB para importar e atualizar documentos do Azure Cosmos DB. Para saber mais sobre a biblioteca bulk executor e como ela ajuda a aproveitar armazenamento e taxa de transferência em massa, consulte o artigo [visão geral da Biblioteca bulk executor](bulk-executor-overview.md). Neste tutorial, você cria um aplicativo Java que gera documentos aleatórios a serem importados em massa para um contêiner do Azure Cosmos DB. Após a importação, você será em massa atualizar algumas propriedades de um documento. 

Atualmente, a biblioteca do executor em massa é suportada apenas pelas contas da API do Azure Cosmos DB SQL e da API Gremlin. Este artigo descreve como usar a biblioteca .NET do executor em massa com contas da API do SQL. Para saber mais sobre como usar a biblioteca do .NET de executor em massa com a API do Gremlin, consulte [executar operações em massa na API do Gremlin do Azure Cosmos DB](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.  

* Você pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure, gratuitamente e sem compromisso. Ou você pode usar o [Emulador do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) com o ponto de extremidade de `https://localhost:8081`. A Chave Primária é fornecida nas [Solicitações de autenticação](local-emulator.md#authenticating-requests).  

* [Java Development Kit (JDK) 1.7 +](https://aka.ms/azure-jdks)  
  - No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.  

  - Defina a variável de ambiente JAVA_HOME para apontar para a pasta onde o JDK está instalado.

* [Baixar](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um armazenamento binário [Maven](https://maven.apache.org/)  
  
  - No Ubuntu, você pode executar `apt-get install maven` para instalar o Maven.

* Crie uma conta de API SQL do Azure Cosmos DB usando as etapas descritas na seção [criar conta de banco de dados](create-sql-api-java.md#create-a-database-account) do artigo de início rápido do Java.

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos mudar para o trabalho com código baixando um aplicativo Java de exemplo do GitHub. Esse aplicativo executa operações em massa em dados do Azure Cosmos DB. Para clonar o aplicativo, abra um prompt de comando, navegue até o diretório onde você deseja copiar o aplicativo e execute o seguinte comando:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

O repositório clonado contém dois exemplos "bulkimport" e "bulkupdate" relativos à pasta "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor". O aplicativo "bulkimport" gera documentos aleatórios e importa-os no Azure Cosmos DB. O aplicativo "bulkupdate" atualiza alguns documentos no Azure Cosmos DB. Nas próximas seções, examinaremos o código em cada um desses aplicativos de exemplo. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Importar dados em massa no Azure Cosmos DB

1. As cadeias de caracteres de conexão do Azure Cosmos DB são lidas como argumentos e atribuídas a variáveis definidas no arquivo CmdLineConfiguration.java.  

2. Em seguida, o objeto de DocumentClient é inicializado usando as instruções a seguir:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. O objeto DocumentBulkExecutor é inicializado com valores de repetição alta para solicitações de tempo de espera e limitadas. E, em seguida, eles são definidos como 0 para passar o controle de congestionamento para DocumentBulkExecutor por toda a sua vida útil.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. Chame a API importAll que gera documentos aleatórios para importar em massa para um contêiner do Azure Cosmos DB. Você pode configurar as configurações de linha de comando no arquivo CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   A API de importação em massa aceita uma coleção de documentos serializado para JSON e tem a seguinte sintaxe, para obter mais detalhes, consulte a [documentação da API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   O método ImportAll aceita os seguintes parâmetros:
 
   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |isUpsert    |   Um sinalizador para habilitar o upsert de documentos. Se um documento com a ID fornecida já existir, ele será atualizado.  |
   |disableAutomaticIdGeneration     |   Um sinalizador para desabilitar a geração automática de ID. Por padrão, está definido como true.   |
   |maxConcurrencyPerPartitionRange    |  O grau máximo de simultaneidade por intervalo de chave de partição. O valor padrão é 20.  |

   **Definição de objeto de resposta de importação em massa** O resultado da chamada de API de importação em massa contém os seguintes métodos:

   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   O número total de documentos que foram importados com êxito sem os documentos fornecidos para a chamada de API de importação em massa.      |
   |double getTotalRequestUnitsConsumed()   |  O total de unidades de solicitação (RU) consumidas pela chamada de API de importação em massa.       |
   |Duration getTotalTimeTaken()   |    O tempo total gasto pela chamada de API de importação em massa para concluir a execução.     |
   |Lista\<exceção > getErrors() |  Obtém a lista de erros se alguns documentos fora do lote fornecido para a chamada à API de importação em massa que não falharam em ser inseridos.       |
   |Lista\<objeto > getBadInputDocuments()  |    A lista de documentos com formato inválido que não foram importados com êxito na chamada de API de importação em massa. O usuário deve corrigir os documentos retornados e repetir a importação. Os documentos com formato inválido incluem documentos cujo valor de ID não é uma cadeia de caracteres (null ou qualquer outro tipo de dados é considerado inválido).     |

5. Depois de ter o aplicativo de importação em massa pronto, compile a ferramenta de linha de comando da origem usando o comando 'mvn clean package'. Este comando gera um arquivo jar na pasta de destino:  

   ```java
   mvn clean package
   ```

6. Depois que as dependências de destino são geradas, você pode chamar o aplicativo importador em massa usando o seguinte comando:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   O importador em massa cria um novo banco de dados e uma coleção com o nome do banco de dados, nome da coleção e os valores de taxa de transferência especificados no arquivo App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Atualizar dados em massa no Azure Cosmos DB

Você pode atualizar os documentos existentes usando a API BulkUpdateAsync. Neste exemplo, você definirá o campo Nome para um novo valor e removerá o campo Descrição dos documentos existentes. Para o conjunto completo de operações suportadas de atualização de campo, consulte a [documentação da API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Defina os itens de atualização juntamente com as operações de atualização de campo correspondentes. Neste exemplo, você usará SetUpdateOperation para atualizar o campo Nome e UnsetUpdateOperation para remover o campo Descrição de todos os documentos. Você também pode executar outras operações, como incremento de um campo de documento por um valor específico, efetuar push de valores específicos para um campo de matriz ou remover um valor específico de um campo de matriz. Para saber mais sobre os diferentes métodos fornecidos pela API de atualização em massa, confira a [documentação da API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                      
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Chame a API updateAll que gera documentos aleatórios a serem importados em massa para um contêiner do Azure Cosmos DB. Você pode configurar as configurações de linha de comando a serem passadas no arquivo CmdLineConfiguration.java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   A API de atualização em massa aceita uma coleção de itens a serem atualizadas. Cada item de atualização especifica a lista de operações de atualização de campo a ser executada em um documento identificado por uma ID e um valor de chave de partição. Para obter mais detalhes, confira a [documentação da API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   O método updateAll aceita os seguintes parâmetros:

   |**Parâmetro** |**Descrição** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  O grau máximo de simultaneidade por intervalo de chave de partição. O valor padrão é 20.  |
 
   **Definição de objeto de resposta de importação em massa** O resultado da chamada de API de importação em massa contém os seguintes métodos:

   |**Parâmetro** |**Descrição**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   O número total de documentos que foram atualizados com êxito sem os documentos fornecidos para a chamada de API de atualização em massa.      |
   |double getTotalRequestUnitsConsumed() |  O total de unidades de solicitação (RU) consumidas pela chamada de API de atualização em massa.       |
   |Duration getTotalTimeTaken()  |   O tempo total gasto pela chamada de API de atualização em massa para concluir a execução.      |
   |Lista\<exceção > getErrors()   |    Obtém a lista de erros se alguns documentos fora do lote fornecido para a chamada à API de atualização em massa que não falharam em ser inseridos.      |

3. Depois de ter o aplicativo de atualização em massa pronto, compile a ferramenta de linha de comando da origem usando o comando 'mvn clean package'. Este comando gera um arquivo jar na pasta de destino:  

   ```
   mvn clean package
   ```

4. Depois que as dependências de destino são geradas, você pode chamar o aplicativo de atualização em massa usando o seguinte comando:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Dicas de desempenho 

Considere os seguintes pontos para melhor desempenho ao usar a biblioteca bulk executor:

* Para ter o melhor desempenho, execute o aplicativo de uma VM do Azure na mesma região da sua região de gravação da conta do Cosmos DB.  
* Para alcançar maior taxa de transferência:  

   * Defina o tamanho do heap da JVM para um número grande o suficiente para evitar qualquer problema de memória na manipulação de número grande de documentos. Tamanho do heap sugerido: max (3GB, 3 * tamanho de (todos os documentos passados para API de importação em massa em um lote)).  
   * Há um tempo de pré-processamento, devido ao qual você obterá maior taxa de transferência ao executar operações em massa com um grande número de documentos. Portanto, se você deseja importar 10.000.000 documentos, executar a importação em massa 10 vezes em 10 lotes de documentos, cada um de tamanho 1.000.000, é preferível do que a execução da importação em massa 100 vezes em 100 lotes de documentos, cada um de tamanho 100.000 documentos.  

* É recomendado criar uma instância de um único objeto DocumentBulkExecutor para o aplicativo inteiro em uma única máquina virtual que corresponda a um contêiner do Azure Cosmos DB específico.  

* Como uma execução de API de operação em massa única consome uma grande parte de ES de CPU e de rede do computador cliente. Isso acontece por geração de várias tarefas internamente, evite a geração de várias tarefas simultâneas no processo de aplicativo executando chamadas de API de operação em massa. Se uma única chamada à API de operação em massa em execução em uma única máquina virtual não puder consumir a produtividade inteira do contêiner (se a produtividade do contêiner for de mais de 1 milhão de RU/s), prefira criar máquinas virtuais separadas para executar as chamadas à API de operação em massa simultaneamente.

    
## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre os detalhes do pacote maven e as notas de versão da biblioteca bulk executor Java, consulte [Detalhes do SDK do bulk executor](sql-api-sdk-bulk-executor-java.md).


