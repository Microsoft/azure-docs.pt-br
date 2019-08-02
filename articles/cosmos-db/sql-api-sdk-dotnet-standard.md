---
title: 'O Azure Cosmos DB: API do SQL .NET Standard, recursos de & do SDK'
description: Saiba tudo sobre a API do SQL e o SDK do .NET, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do .NET Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 01645e66907e01b2e8fcb771976b50e5bbc8abf4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229018"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>SDK do .NET Standard do Azure Cosmos DB para a API do SQL: Downloads e notas sobre a versão
> [!div class="op_single_selector"]
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações do .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provedor de recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Baixe o SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**Documentação da API**|[Documentação de referência de API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Exemplos**|[Exemplos de código .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**Introdução**|[Introdução ao SDK do .NET do Azure Cosmos DB](sql-api-get-started.md)|
|**Tutorial do aplicativo Web**|[Desenvolvimento de aplicativos Web com o Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Framework atualmente com suporte**|[Microsoft .NET Standard 2.0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>Notas de versão

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* Disponibilidade geral da [versão 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) do SDK do .net
* Destinos .NET Standard 2,0, que oferece suporte ao .NET Framework 4.6.1 + e ao .NET Core 2.0 +
* Novo modelo de objeto, com CosmosClient de nível superior e métodos divididos em classes de contêiner e banco de dados relevantes
* Novas APIs de fluxo de alto desempenho
* Suporte interno para APIs do processador do feed de alterações
* APIs do Fluent Builder para CosmosClient, contêiner e processador do feed de alterações
* APIs de gerenciamento de taxa de transferência idiomática
* Requestoptions e ResponseTypes granulares para solicitações de banco de dados, contêiner, item, consulta e taxa de transferência
* Capacidade de dimensionar contêineres não particionados 
* Serializador extensível e personalizável
* Pipeline de solicitação extensível com suporte para manipuladores personalizados


## <a name="release--retirement-dates"></a>Datas de lançamento e desativação
A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente. 

Qualquer solicitação feita ao BD Cosmos do Azure usando um SDK obsoleto é rejeitada pelo serviço.

<br/>

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [3.0.0](#3.0.0) |15 de julho de 2019 |--- |

## <a name="faq"></a>Perguntas Frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

