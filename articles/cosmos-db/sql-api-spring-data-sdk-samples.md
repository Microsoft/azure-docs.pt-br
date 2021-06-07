---
title: 'API do SQL do Azure Cosmos DB: Exemplos do Spring Data v3'
description: Encontre exemplos de Spring Data v3 no GitHub para tarefas comuns usando a API do SQL do Azure Cosmos DB, incluindo operações CRUD.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 378bb891c8539a6cf3d61f6511a0f58377d2bfd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93091133"
---
# <a name="azure-cosmos-db-sql-api-spring-data-azure-cosmos-db-v3-examples"></a>API do SQL do Azure Cosmos DB: Exemplos do Spring Data Azure Cosmos DB v3
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Exemplos de SDK do .NET V2](sql-api-dotnet-samples.md)
> * [Exemplos de SDK do .NET V3](sql-api-dotnet-v3sdk-samples.md)
> * [Exemplos do SDK do Java V4](sql-api-java-sdk-samples.md)
> * [Exemplos do SDK do Spring Data V3](sql-api-spring-data-sdk-samples.md)
> * [Exemplos do Node.js](sql-api-nodejs-samples.md)
> * [Exemplos do Python](sql-api-python-samples.md)
> * [Galeria de Exemplos de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Estas notas sobre a versão são para a versão 3 do Spring Data Azure Cosmos DB. Você pode encontrar as [notas sobre a versão 2 aqui](sql-api-sdk-java-spring-v2.md). 
>
> O Spring Data Azure Cosmos DB é compatível apenas com a API do SQL.
>
> Confira estes artigos para obter informações sobre o Spring Data em outras APIs do Azure Cosmos DB:
> * [Spring Data para Apache Cassandra com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Você pode [ativar benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Todos os meses, sua Assinatura do Visual Studio lhe oferece créditos que podem ser usados para serviços pagos do Azure.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Os aplicativos de exemplo mais recentes que executam operações CRUD e outras operações comuns nos recursos do Azure Cosmos DB estão incluídos no repositório do GitHub [azure-spring-data-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples). Esse artigo fornece:

* Links para as tarefas em cada um dos arquivos de projeto de exemplo do Spring Data Azure Cosmos DB. 
* Links para o conteúdo de referência da API relacionada.

**Pré-requisitos**

Para executar esse aplicativo de exemplo, serão necessários:

* Java Development Kit 8
* Spring Data Azure Cosmos DB v3

Opcionalmente, você pode usar o Maven para obter os últimos binários do Spring Data Azure Cosmos DB v3 para uso no projeto. O Maven adiciona automaticamente todas as dependências necessárias. Caso contrário, você poderá baixar diretamente as dependências listadas no arquivo **pom.xml** e adicioná-las ao caminho de compilação.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Executar aplicativos de exemplo**

Clone o repositório do exemplo:
```bash
$ git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples

$ cd azure-spring-data-cosmos-java-sql-api-samples
```

Você pode executar os exemplos usando um IDE (Eclipse, IntelliJ ou VSCODE) ou na linha de comando usando o Maven.

Em **application.properties**, essas variáveis de ambiente devem ser definidas

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

para dar aos exemplos acesso de leitura/gravação à conta, aos bancos de dados e aos contêineres.

O IDE pode fornecer a capacidade de executar o código de exemplo do Spring Data. Caso contrário, você poderá usar o seguinte comando de terminal para executar o exemplo:

```bash
mvn spring-boot:run
```

## <a name="document-crud-examples"></a>Documentar exemplos CRUD
O arquivo de [exemplos](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) mostra como executar as tarefas a seguir. Para saber mais sobre os documentos do Azure Cosmos antes de executar os exemplos a seguir, veja o artigo conceitual [Trabalhando com bancos de dados, contêineres e itens](account-databases-containers-items.md).

| Tarefa | Referência de API |
| --- | --- |
| [Criar um documento](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L46-L47) | CosmosRepository.save |
| [Ler um documento por ID](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L56-L58) | CosmosRepository.derivedQueryMethod |
| [Excluir todos os documentos](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L39-L41) | CosmosRepository.deleteAll |

## <a name="derived-query-method-examples"></a>Exemplos de métodos de consulta derivada
O arquivo de [exemplos](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) mostra como executar as tarefas a seguir. Para saber mais sobre as consultas do Azure Cosmos DB antes de executar os exemplos a seguir, talvez seja útil ler o artigo [Métodos de Consulta Derivada no Spring, da Baeldung](https://www.baeldung.com/spring-data-derived-queries).

| [Consulta de documentos](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L73-L77) | CosmosRepository. derivedQueryMethod |

## <a name="custom-query-examples"></a>Exemplos de consultas personalizadas
O arquivo de [Exemplos](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) mostra como realizar as tarefas a seguir usando a gramática de consulta SQL. Para saber mais sobre a referência de consulta SQL no Azure Cosmos DB antes de executar as amostras a seguir, confira [Exemplos de consulta SQL do Azure Cosmos DB](./sql-query-getting-started.md). 


| Tarefa | Referência de API |
| --- | --- |
| [Consulta de todos os documentos](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L20-L22) | @Query anotação |
| [Consulta de igualdade usando = =](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L24-L26) | @Query anotação |
| [Consulta de desigualdade usando != e NOT](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L28-L38) | @Query anotação |
| [Consulta usando operadores de intervalo como >, <, >=, <=](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L40-L42) | @Query anotação |
| [Consulta usando operadores de intervalo em cadeias de caracteres](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L44-L46) | @Query anotação |
| [Consultar com ORDER BY](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L48-L50) | @Query anotação |
| [Consultar com DISTINCT](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L52-L54) | @Query anotação |
| [Consultar com funções de agregação](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L56-L62) | @Query anotação |
| [Trabalhar com subdocumentos](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L64-L66) | @Query anotação |
| [Consulta com junções dentro do documento](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L68-L85) | @Query anotação |
| [Consulta com cadeia de caracteres, operadores matemáticos e de matriz](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L87-L97) | @Query anotação |