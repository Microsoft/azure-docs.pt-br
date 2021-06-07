---
title: Recursos e notas de versão do Spring data Azure Cosmos DB v2 para API do SQL
description: Saiba mais sobre o Spring data Azure Cosmos DB v2 para a API do SQL, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do Java assíncrono do SQL Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 1f78d7185415b66445c78f3b4cdb54ed0d63cb0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216608"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>API do Spring data Azure Cosmos DB v2 para núcleo (SQL): notas de versão e recursos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK v3 do .NET](sql-api-sdk-dotnet-standard.md)
> * [SDK do .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 do .NET Core](sql-api-sdk-dotnet-core.md)
> * [SDK do Feed de Alterações do .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK do Java v4](sql-api-sdk-java-v4.md)
> * [SDK do Java Assíncrono v2](sql-api-sdk-async-java.md)
> * [SDK do Java Síncrono v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Conector do Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provedor de recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Executor em massa – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

 O Spring data Azure Cosmos DB versão 2 para núcleo (SQL) permite que os desenvolvedores usem Azure Cosmos DB em aplicativos Spring. O Spring data Azure Cosmos DB expõe a interface Spring data para manipular bancos de dados e coleções, trabalhar com documentos e emitir consultas. As APIs de sincronização e assíncrona (reativa) têm suporte no mesmo artefato Maven. 

O [Spring Framework](https://spring.io/projects/spring-framework) é um modelo de programação e configuração que simplifica o desenvolvimento de aplicativos Java. O Spring simplifica o "encanamento" de aplicativos usando a injeção de dependência. Muitos desenvolvedores gostam do Spring, pois tornam a criação e o teste de aplicativos mais simples. O [Spring boot](https://spring.io/projects/spring-boot) estende essa manipulação da estrutura com um olho voltado para o desenvolvimento de aplicativos e microserviços da Web. [Spring data](https://spring.io/projects/spring-data) é um modelo de programação para acessar armazenamentos de dados como Azure Cosmos DB do contexto de um aplicativo Spring ou Spring boot. 

Você pode usar o Spring data Azure Cosmos DB em seus aplicativos de [nuvem Spring do Azure](https://azure.microsoft.com/services/spring-cloud/) .

> [!IMPORTANT]  
> Estas notas de versão são para a versão 2 do Spring data Azure Cosmos DB. Você pode encontrar [notas de versão para a versão 3 aqui](sql-api-sdk-java-spring-v3.md). 
>
> O Spring Data Azure Cosmos DB é compatível apenas com a API do SQL.
>
> Consulte os seguintes artigos para obter informações sobre o Spring data em outras APIs de Azure Cosmos DB:
> * [Spring Data para Apache Cassandra com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Quer começar a usar rapidamente?
> 1. Instale o [tempo de execução Java mínimo com suporte, JDK 8](/java/azure/jdk/), para que você possa usar o SDK.
> 2. Crie um aplicativo de Azure Cosmos DB Spring data usando o [iniciador](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). É fácil!
> 3. Trabalhe no [Guia do desenvolvedor do Spring Data Azure Cosmos DB](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb), que percorre as solicitações de Azure Cosmos DB básicas.
>
> Você pode criar aplicativos de inicialização do Spring boot rapidamente usando o [Spring Initializr](https://start.spring.io/)!
>

## <a name="resources"></a>Recursos

| Recurso | Link |
|---|---|
| **Baixe o SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Documentação da API** | [Documentação de referência do Spring data Azure Cosmos DB]() |
|**Contribuir para o SDK** | [Repositório de Azure Cosmos DB de dados Spring no GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Iniciador do Spring boot**| [Biblioteca de cliente inicial do Spring boot do Azure Cosmos DB para Java](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Exemplo de aplicativo de TODO o Spring com Azure Cosmos DB**| [Experiência Java de ponta a ponta no serviço de aplicativo Linux (parte 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Guia do desenvolvedor** | [Guia do desenvolvedor do Azure Cosmos DB do Spring Data](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Usando o iniciador** | [Como usar o iniciador do Spring boot com a API do Azure Cosmos DB SQL](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Repositório GitHub para Azure Cosmos DB iniciador do Spring boot](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Exemplo com o serviço de Azure App** | [Como usar o Spring e o Cosmos DB com o Serviço de Aplicativo no Linux](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Exemplo de aplicativo TODO](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Histórico de versões

### <a name="230-may-21-2020"></a>2.3.0 (21 de maio de 2020)
#### <a name="new-features"></a>Novos recursos
* Atualiza a versão do Spring boot para 2.3.0.


### <a name="225-may-19-2020"></a>2.2.5 (19 de maio de 2020)
#### <a name="new-features"></a>Novos recursos
* Atualiza Azure Cosmos DB versão para 3.7.3.
#### <a name="key-bug-fixes"></a>Principais correções de bug
* Contém correções de vazamento de memória e atualizações de versão de sub-rede do Azure Cosmos DB SDK 3.7.3.

### <a name="224-april-6-2020"></a>2.2.4 (6 de abril de 2020)
#### <a name="key-bug-fixes"></a>Principais correções de bug
* Corrige o `allowTelemetry` sinalizador a ser levado em conta de `CosmosDbConfig` .
* Corrige a `TTL` propriedade no contêiner.

### <a name="223-february-25-2020"></a>2.2.3 (25 de fevereiro de 2020)
#### <a name="new-features"></a>Novos recursos
* Adiciona nova `findAll` pela API de chave de partição.
* Atualiza Azure Cosmos DB versão para 3.7.0.
#### <a name="key-bug-fixes"></a>Principais correções de bug
* Correções `collectionName`  ->  `containerName` .
* Correções `entityClass` e `domainClass`  ->  `domainType` .
* Corrige a "coleção de entidades de retorno salva pelo repositório em vez da entidade de entrada".

### <a name="2110-february-25-2020"></a>2.1.10 (25 de fevereiro de 2020)
#### <a name="key-bug-fixes"></a>Principais correções de bug
* A correção de backports para a "coleção de entidades de retorno salva pelo repositório em vez da entidade de entrada".

### <a name="222-january-15-2020"></a>2.2.2 (15 de janeiro de 2020)
#### <a name="new-features"></a>Novos recursos
* Atualiza a versão Azure Cosmos DB para o 3.6.0.
#### <a name="key-bug-fixes"></a>Principais correções de bug

### <a name="221-december-31-2019"></a>2.2.1 (31 de dezembro de 2019)
#### <a name="new-features"></a>Novos recursos
* Atualiza Azure Cosmos DB versão do SDK para o 3.5.0.
* Adiciona o campo de anotação para habilitar ou desabilitar a criação de coleção automática.
* Melhora o tratamento de exceção. Expõe `CosmosClientException` por meio de `CosmosDBAccessException` .
* Expõe `requestCharge` e `activityId` até `ResponseDiagnostics` .
#### <a name="key-bug-fixes"></a>Principais correções de bug
* A atualização do SDK 3.5.0 corrige "exceção quando Cosmos DB cabeçalho de resposta HTTP é maior que 8192 bytes," "ConsistencyPolicy. defaultConsistencyLevel () falha na desatualização limitada e no prefixo consistente".
* Corrige o `findById` comportamento do método. Anteriormente, esse método retornou vazio se a entidade não fosse encontrada em vez de lançar uma exceção.
* Corrige um bug no qual a classificação não foi aplicada na próxima página quando `CosmosPageRequest` foi usada.

### <a name="219-december-26-2019"></a>2.1.9 (26 de dezembro de 2019)
#### <a name="new-features"></a>Novos recursos
* Adiciona o campo de anotação para habilitar ou desabilitar a criação de coleção automática.
#### <a name="key-bug-fixes"></a>Principais correções de bug
*  Corrige o `findById` comportamento do método. Anteriormente, esse método retornou vazio se a entidade não fosse encontrada em vez de lançar uma exceção.

### <a name="220-october-21-2019"></a>2.2.0 (21 de outubro de 2019)
#### <a name="new-features"></a>Novos recursos
* Conclua o suporte ao repositório Cosmos reativo.
* Azure Cosmos DB solicitar a cadeia de caracteres de diagnóstico e suporte a métricas de consulta.
* Azure Cosmos DB atualização da versão do SDK para 3.3.1.
* Atualização da versão do Spring Framework para 5.2.0. RELEASE.
* Atualização da versão do Spring data Commons para o 2.2.0. RELEASE.
* Adiciona `findByIdAndPartitionKey` e `deleteByIdAndPartitionKey` APIs.
* Remove a dependência do Azure-documentdb.
* Remarca o DocumentDB para Azure Cosmos DB.
#### <a name="key-bug-fixes"></a>Principais correções de bug
* Corrige "a classificação gera uma exceção quando pageSize é menor que o total de itens no repositório".

### <a name="218-october-18-2019"></a>2.1.8 (18 de outubro de 2019)
#### <a name="new-features"></a>Novos recursos
* Substitui as APIs do DocumentDB.
* Adiciona `findByIdAndPartitionKey` e `deleteByIdAndPartitionKey` APIs.
* Adiciona bloqueio otimista com base em `_etag` .
* Habilita a expressão SpEL para o nome da coleção de documentos.
* Adiciona `ObjectMapper` melhorias.

### <a name="217-october-18-2019"></a>2.1.7 (18 de outubro de 2019)
#### <a name="new-features"></a>Novos recursos
* Adiciona a dependência Azure Cosmos DB SDK versão 3.
* Adiciona o repositório Cosmos reativo.
* Atualiza a implementação do `DocumentDbTemplate` para usar Azure Cosmos DB SDK versão 3.
* Adiciona outras alterações de configuração para o suporte ao repositório Cosmos reativo.

### <a name="212-march-19-2019"></a>2.1.2 (19 de março de 2019)
#### <a name="key-bug-fixes"></a>Principais correções de bug
* Remove `applicationInsights` a dependência para:
    * Risco potencial de dependências que poluam.
    * Incompatibilidade do Java 11.
    * Evitar o impacto potencial no desempenho na CPU e/ou na memória.

### <a name="207-march-20-2019"></a>2.0.7 (20 de março de 2019)
#### <a name="key-bug-fixes"></a>Principais correções de bug
* Backport remove `applicationInsights` a dependência para:
    * Risco potencial de dependências que poluam.
    * Incompatibilidade do Java 11.
    * Evitar o impacto potencial no desempenho na CPU e/ou na memória.

### <a name="211-march-7-2019"></a>2.1.1 (7 de março de 2019)
#### <a name="new-features"></a>Novos recursos
* Atualiza a versão principal para 2.1.1.

### <a name="206-march-7-2019"></a>2.0.6 (7 de março de 2019)
#### <a name="new-features"></a>Novos recursos
* Ignorar todas as exceções da telemetria.

### <a name="210-december-17-2018"></a>2.1.0 (17 de dezembro de 2018)
#### <a name="new-features"></a>Novos recursos
* Atualiza a versão para 2.1.0 para resolver o problema.

### <a name="205-september-13-2018"></a>2.0.5 (13 de setembro de 2018)
#### <a name="new-features"></a>Novos recursos
* Adiciona palavras-chave `exists` e `startsWith` .
* Atualizações Leiame.
#### <a name="key-bug-fixes"></a>Principais correções de bug
* Corrige "não é possível chamar o autohref diretamente para a entidade".
* Correções de "findAll falhará se a coleção não for criada".

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (pré-lançamento) (23 de agosto de 2018)
#### <a name="new-features"></a>Novos recursos
* Renomeia o pacote do documentdb para cosmosdb.
* Adiciona o novo recurso da palavra-chave do método de consulta. Agora há suporte para 16 palavras-chave da API do SQL.
* Adiciona um novo recurso de consulta com paginação e classificação.
* Simplifica a configuração do Spring-data-cosmosdb.
* Adiciona `deleteCollection` e `deleteAll` APIs.

#### <a name="key-bug-fixes"></a>Principais correções de bug
* Correção de bug e mitigação de defeito.

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Saiba mais sobre o [Spring Framework](https://spring.io/projects/spring-framework).

Saiba mais sobre o [Spring boot](https://spring.io/projects/spring-boot).

Saiba mais sobre o [Spring data](https://spring.io/projects/spring-data).