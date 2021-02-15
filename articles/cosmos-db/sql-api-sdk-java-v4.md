---
title: 'SDK do Java v4 do Azure Cosmos DB para a API do SQL: notas de versão e recursos'
description: Saiba tudo sobre o SDK do Java v4 do Azure Cosmos DB para API e SDK do SQL, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do Java Assíncrono do Azure Cosmos DB SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: a70892a1900902cdf033434fe7edfed4c56f3b74
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097100"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>SDK do Java v4 do Azure Cosmos DB para API do Core (SQL): notas de versão e recursos
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

O SDK do Java v4 do Azure Cosmos DB para Core (SQL) combina uma API assíncrona e uma API síncrona em um artefato Maven. O SDK da v4 oferece desempenho aprimorado, novos recursos de API e suporte a Async com base no Project Reactor e na [biblioteca Netty](https://netty.io/). Os usuários podem esperar um desempenho aprimorado com o SKD do Java v4 do Azure Cosmos DB em comparação com o [SDK do Java Assíncrono v2 do Azure Cosmos DB](sql-api-sdk-async-java.md) e o [SDK do Java Síncrono v2 do Azure Cosmos DB](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Essas notas de versão são apenas para o SDK do Java v4 do Azure Cosmos DB. Se você estiver usando uma versão mais antiga do que a v4, confira o guia [Migrar para o SDK do Java v4 do Azure Cosmos DB](migrate-java-v4-sdk.md) para obter ajuda na atualização da versão 4.
>
> Aqui estão três etapas para começar rápido!
> 1. Instale o [runtime mínimo com suporte para Java, JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) para poder usar o SDK.
> 2. Trabalhe no [Guia de Início Rápido do SDK do Java v4 do Azure Cosmos DB](./create-sql-api-java.md), que fornece acesso ao artefato Maven, e percorre as solicitações básicas do Azure Cosmos DB.
> 3. Leia as [dicas de desempenho](performance-tips-java-sdk-v4-sql.md) e os guias de [solução de problemas](troubleshoot-java-sdk-v4-sql.md) do SDK do Java v4 do Azure Cosmos DB para otimizar o SDK para seu aplicativo.
>
> Os [workshops e laboratórios do Azure Cosmos DB](https://aka.ms/cosmosworkshop) são outro ótimo recurso para aprender como usar o SDK do Java v4 do Azure Cosmos DB!
>

## <a name="helpful-content"></a>Conteúdo útil

| Conteúdo | Link |
|---|---|
|**Baixe o SDK**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Documentação da API** | [Documentação de referência de API Java](/java/api/overview/azure/cosmosdb/client?preserve-view=true&view=azure-java-stable) |
|**Contribuir para o SDK** | [SDK do Azure para repositório central do Java no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Introdução** | [Início Rápido: Criar um aplicativo Java para gerenciar os dados de API de SQL do Azure Cosmos DB](./create-sql-api-java.md) <br> [Repositório GitHub com código de início rápido](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Amostras de código básico** | [Azure Cosmos DB: exemplos de Java para API do SQL](sql-api-java-sdk-samples.md) <br> [Repositório GitHub com código de exemplo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Aplicativo de console com feed de alterações**| [Feed de alterações-exemplo de Java SDK v4](create-sql-api-java-changefeed.md) <br> [Repositório GitHub com código de exemplo](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Exemplo de aplicativo Web**| [Compilar um aplicativo Web com o SDK do Java v4](sql-api-java-application.md) <br> [Repositório GitHub com código de exemplo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Dicas de desempenho**| [Dicas de desempenho para o SDK do Java v4](performance-tips-java-sdk-v4-sql.md)| 
| **Solução de problemas** | [Solucionar problemas do SDK do Java v4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrar para v4 de um SDK mais antigo** | [Migrar para o SDK do Java V4](migrate-java-v4-sdk.md) |
| **runtime mínimo com suporte**|[JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) | 
| **Workshops e laboratórios do Azure Cosmos DB** |[Home page dos workshops do Cosmos DB](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).