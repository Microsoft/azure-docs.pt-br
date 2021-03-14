---
title: 'O Azure Cosmos DB: API .NET, SDK e recursos do executor em massa'
description: Saiba tudo sobre o SDK e a API BulkExecutor .NET, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK BulkExecutor .NET do Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: 91fcfc614fed95c40aa3fed4368d17399255e8b5
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423838"
---
# <a name="net-bulk-executor-library-download-information"></a>Biblioteca do executor em massa .NET: Fazer o download das informações 
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
| **Descrição**| A biblioteca do executor em massa .NET permite que os aplicativos clientes executem operações em massa nas contas do Azure Cosmos DB. Essa biblioteca fornece namespaces BulkImport, BulkUpdate e BulkDelete. O módulo BulkImport pode importar em massa documentos de forma otimizada, de modo que a taxa de transferência provisionada para uma coleção seja consumida até seu limite máximo. O módulo BulkUpdate pode atualizar em massa dados existentes nos contêineres do Azure Cosmos como patches. O módulo BulkDelete pode excluir documentos em massa de forma otimizada, de modo que o rendimento provisionado para uma coleção seja consumido em sua extensão máxima.|
|**Baixe o SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Biblioteca do executor em massa no GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Documentação da API**|[Documentação de referência de API .NET](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor)|
|**Introdução**|[Introdução ao SDK .NET da biblioteca bulk executor](bulk-executor-dot-net.md)|
| **Framework atualmente com suporte**| Microsoft .NET Framework 4.5.2, 4.6.1 e o .NET Standard 2.0 |

> [!NOTE]
> Se você estiver usando o executor em massa, consulte a versão mais recente 3.x do [SDK .NET](tutorial-sql-api-dotnet-bulk-import.md), que tem o executor em massa incorporado ao SDK. 

## <a name="release-notes"></a>Notas de versão

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1-preview

* TotalElapsedTime corrigido na resposta de BulkDelete para medir corretamente o tempo total, incluindo repetições.

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0-preview

* Dependência do SDK alterada para >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* Adição de compatibilidade com o executor em massa de grafo para aceitar TTL em vértices e bordas

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-preview2

* Correção de um problema, que causou exceções durante o dimensionamento elástico do Azure Cosmos DB ao ser executado no modo Gateway. Essa correção torna funcionalmente equivalente à versão 1.4.1.

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-preview2

* Adição de compatibilidade com BulkDelete para contas da API do SQL para aceitar chave de partição, tuplas de ID de documento a serem excluídas. Essa mudança torna funcionalmente equivalente à versão 1.4.0.

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-preview2

* Incluindo MongoBulkExecutor para suporte ao .NET Standard 2.0. Esse recurso torna funcionalmente equivalente à versão 1.3.0, com o acréscimo de suporte a .NET Standard 2.0 como a estrutura de destino.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-preview

* Adição de .NET Standard 2.0 como uma das estruturas de destino compatíveis para fazer com que a biblioteca do executor em massa funcione com aplicativos .NET Core.

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* Foi corrigido um problema com BulkDeleteAsync quando os valores com aspas de escape foram passados como parâmetros de entrada.

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* Correção de um problema no MongoBulkExecutor que estava aumentando o tamanho do documento inesperadamente, adicionando o preenchimento e, em alguns casos, ultrapassando o limite de tamanho máximo do documento.

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* Foi corrigido um problema com BulkDeleteAsync que ocorre quando a coleção tem caminhos de chave de partição aninhados.

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* O MongoBulkExecutor agora implementa IDisposable, e espera-se que seja descartado depois de usado.

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* Bloqueio removido na versão do SDK. O pacote agora depende do SDK >= 2.5.1.

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* Processamento fixo de identificadores ao chamar BulkImport com uma lista de objetos POCO com valores numéricos.

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* TotalElapsedTime corrigido na resposta de BulkDelete para medir corretamente o tempo total, incluindo repetições.

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* Corrigido o alto consumo de CPU em determinados cenários.
* O rastreamento agora usa TraceSource. Os usuários podem definir ouvintes para a fonte de `BulkExecutorTrace`.
* Correção de um cenário raro que poderia causar um bloqueio ao enviar documentos com quase 2 MB.

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* Atualizado o executor em massa para agora usar a versão mais recente do SDK do .NET Azure Cosmos DB (2.4.0)

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* Adição de compatibilidade com o executor em massa de grafo para aceitar TTL em vértices e bordas

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* Correção de um problema, que causou exceções durante o dimensionamento elástico do Azure Cosmos DB ao ser executado no modo Gateway.

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* Adição de compatibilidade com BulkDelete para contas da API do SQL para aceitar chave de partição, tuplas de ID de documento a serem excluídas.

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* Correção de um problema que causou um erro de formatação no agente de usuário usado pelo executor em massa.

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* Aprimoramento para importar do executor em massa e atualizar as APIs de forma transparente para adaptar a escala elástica de contêiner do Cosmos quando o armazenamento excede a capacidade atual sem gerar exceções.

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* E aumentado a dependência do SDK .NET do DocumentDB para a versão 2.1.3.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Correção de um problema que fez com que o executor em massa gerasse o erro JSRT ao importar para coleções corrigidas.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Adicionado suporte para a operação de BulkDelete para contas de API de SQL do Azure Cosmos DB.
* Adicionado suporte para a operação de BulkImport para API do Azure Cosmos DB para MongoDB.
* E aumentado a dependência do SDK .NET do DocumentDB para a versão 2.0.0. 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* Adicionado suporte para a operação de BulkImport para contas de API do Gremlin do Azure Cosmos DB.

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Pequena correção de bug para a operação BulkImport das contas da API SQL do Azure Cosmos DB.

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Adicionado suporte para operações BulkImport e BulkUpdate para contas da API SQL do Azure Cosmos DB.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a biblioteca Java de Executor em massa, consulte o artigo a seguir:

[SDK de biblioteca do executor em massa Java e informações da versão](sql-api-sdk-bulk-executor-java.md)