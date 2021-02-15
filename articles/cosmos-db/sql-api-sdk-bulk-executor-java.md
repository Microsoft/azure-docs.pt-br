---
title: 'O Azure Cosmos DB: Recursos, SDK e API Java do executor em massa'
description: Saiba tudo sobre o SDK e a API Java de bulk executor, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK Java de bulk executor do Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6f5993826c7d8c39f4e062c0a84ffd95eaf9cf2d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098596"
---
# <a name="java-bulk-executor-library-download-information"></a>Biblioteca do executor em massa Java: Fazer o download das informações
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

| |  |
|---|---|
|**Descrição**|A biblioteca bulk executor permite que aplicativos cliente executem operações em massa em contas do Azure Cosmos DB. a biblioteca bulk executor fornece os namespaces BulkImport e BulkUpdate. O módulo BulkImport pode importar em massa documentos de forma otimizada, de modo que a taxa de transferência provisionada para uma coleção seja consumida até seu limite máximo. O módulo BulkUpdate pode atualizar em massa dados existentes nos contêineres do Azure Cosmos como patches.|
|**Baixe o SDK**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Biblioteca do executor em massa no GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Documentação da API**| [Documentação de referência de API Java](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Introdução**|[Introdução ao SDK Java da biblioteca bulk executor](bulk-executor-java.md)|
|**runtime mínimo com suporte**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)|

## <a name="release-notes"></a>Notas de versão

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Corrigir DocumentAnalyzer.java para extrair corretamente valores de chave de partição aninhados do JSON.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Adicionar funcionalidade em operações BulkDelete para tentar novamente em caso de falhas específicas e também retornar uma lista de falhas que podem ser recuperadas para o usuário.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Atualizar para o SDK do Cosmos versão 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Corrigir 'mergeAll' para continuar em 'id' e valor de chave de partição para que qualquer propriedade de documento com patch que seja colocada após 'id' e o valor de chave de partição seja adicionada à lista de itens atualizados.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Atualizar o grau inicial de simultaneidade para 1 e adicionar logs de depuração para minilote.