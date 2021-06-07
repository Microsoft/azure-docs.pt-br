---
title: Recursos e notas de versão do Spring data Azure Cosmos DB V3 para a API do SQL
description: Saiba mais sobre o Spring data Azure Cosmos DB V3 para a API do SQL, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do Java assíncrono do SQL Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/15/2021
ms.author: kuthapar
ms.custom: devx-track-java
ms.openlocfilehash: 536c0ab78a4b7e08abb1532635aff722df9e15cd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563099"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Spring data Azure Cosmos DB V3 para a API de núcleo (SQL): notas de versão e recursos
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

O Spring data Azure Cosmos DB versão 3 for Core (SQL) permite que os desenvolvedores usem Azure Cosmos DB em aplicativos Spring. O Spring data Azure Cosmos DB expõe a interface Spring data para manipular bancos de dados e coleções, trabalhar com documentos e emitir consultas. As APIs de sincronização e assíncrona (reativa) têm suporte no mesmo artefato Maven. 

> [!IMPORTANT]
> O Spring data Azure Cosmos DB tem uma dependência da estrutura Spring Data.
> 
> as versões do Azure-Spring-data-Cosmos do 3.0.0 para o 3.4.0 dão suporte às versões de Spring data 2,2 e 2,3.
> 
> Azure-Spring-data-Cosmos versões 3.5.0 e posteriores dão suporte às versões de Spring data 2.4.3 e posteriores.
>

O [Spring Framework](https://spring.io/projects/spring-framework) é um modelo de programação e configuração que simplifica o desenvolvimento de aplicativos Java. O Spring simplifica o "encanamento" de aplicativos usando a injeção de dependência. Muitos desenvolvedores gostam do Spring, pois tornam a criação e o teste de aplicativos mais simples. O [Spring boot](https://spring.io/projects/spring-boot) estende essa manipulação da estrutura com um olho voltado para o desenvolvimento de aplicativos e microserviços da Web. [Spring data](https://spring.io/projects/spring-data) é um modelo de programação e estrutura para acessar armazenamentos de dados como Azure Cosmos DB do contexto de um aplicativo Spring ou Spring boot. 

Você pode usar o Spring data Azure Cosmos DB em seus aplicativos de [nuvem Spring do Azure](https://azure.microsoft.com/services/spring-cloud/) .

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

## <a name="get-started-fast"></a>Introdução rápida

  Comece a executar o Spring data Azure Cosmos DB seguindo nosso guia de [início do Spring boot](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). A abordagem do iniciante do Spring boot é a maneira recomendada de começar a usar o conector de Azure Cosmos DB de dados Spring.

  Como alternativa, você pode adicionar a dependência de Azure Cosmos DB Spring data ao seu `pom.xml` arquivo, conforme mostrado abaixo:

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>Conteúdo útil

| Conteúdo | Link |
|---|---|
|**Baixe o SDK**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**Documentação da API** | [Documentação de referência de API Java](/java/api/com.azure.spring.data.cosmos) |
|**Contribuir para o SDK** | [SDK do Azure para repositório central do Java no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Introdução** | [Início rápido: criar um aplicativo de Azure Cosmos DB de dados Spring para gerenciar Azure Cosmos DB dados da API do SQL](./create-sql-api-spring-data.md) <br> [Repositório GitHub com código de início rápido](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Amostras de código básico** | [Azure Cosmos DB: exemplos de Spring data Azure Cosmos DB para a API do SQL](sql-api-spring-data-sdk-samples.md) <br> [Repositório GitHub com código de exemplo](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Dicas de desempenho**| [Dicas de desempenho para Java SDK V4 (aplicável a Spring Data)](performance-tips-java-sdk-v4-sql.md)| 
| **Solução de problemas** | [Solucionar problemas do SDK do Java V4 (aplicável a Spring Data)](troubleshoot-java-sdk-v4-sql.md) | 
| **Workshops e laboratórios do Azure Cosmos DB** |[Home page dos workshops do Cosmos DB](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>Observações adicionais

* O Spring data Azure Cosmos DB dá suporte a Java JDK 8 e Java JDK 11.
* No momento, o Spring data 2,3 tem suporte, o Spring data 2,4 não tem suporte no momento.

## <a name="faq"></a>Perguntas frequentes

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Saiba mais sobre o [Spring Framework](https://spring.io/projects/spring-framework).

Saiba mais sobre o [Spring boot](https://spring.io/projects/spring-boot).

Saiba mais sobre o [Spring data](https://spring.io/projects/spring-data).