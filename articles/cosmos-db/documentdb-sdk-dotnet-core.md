---
title: API do .NET Core, SDK e recursos do Azure Cosmos DB | Microsoft Docs
description: "Saiba tudo sobre o SDK e a API do .NET Core, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do .NET Core para Azure Cosmos DB."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/2/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7615074ce3f5098d5155dfc28ada567ea6361878
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-net-core-sdk-release-notes-and-resources"></a>SDK do .NET Core do Azure Cosmos DB: notas de versão e recursos
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [Feed de alterações do .NET](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Provedor de recursos REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Baixe o SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência de API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Exemplos**</td><td>[Exemplos de código .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Introdução**</td><td>[Introdução ao SDK do .NET Core do Azure Cosmos DB](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**Tutorial do aplicativo Web**</td><td>[Desenvolvimento de aplicativos Web com o Azure Cosmos DB](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Framework atualmente com suporte**</td><td>[.NET Standard 1.6 e .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão

O SDK do .NET Core do Azure Cosmos DB tem paridade de recurso com a versão mais recente do [SDK do .NET do Azure Cosmos DB](documentdb-sdk-dotnet.md).

> [!NOTE] 
> O SDK do .NET Core do Azure Cosmos DB não é compatível com aplicativos UWP (Plataforma Universal do Windows). Se você estiver interessado no SDK do .NET Core que dê suporte a aplicativos UWP, envie um email para [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Alterações internas relacionadas ao suporte a [Microsoft.Azure.Graphs](https://docs.microsoft.com/en-us/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Adição de suporte a PartitionKeyRangeId como FeedOption de modo a definir o escopo dos resultados da consulta para um valor específico do intervalo de chaves de partição. 
* Adição de suporte a StartTime como ChangeFeedOption para começar a procurar as alterações após esse horário. 

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Foi corrigido um problema na classe JsonSerializable que podia gerar uma exceção de excedente de pilha.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Adicionado suporte para a especificação de JsonSerializerSettings personalizadas ao instanciar uma instância [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Suporte ao .NET Standard 1.5 como uma das estruturas de destino.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Corrigido um problema que afetava computadores x64 que não davam suporte à instrução SSE4 e que geravam SEHException ao executarem consultas do Azure Cosmos DB.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Adição de suporte a um novo nível de consistência chamado ConsistentPrefix.
*   Adição de suporte a métricas de consulta em partições individuais.
*   Adição de suporte para limitar o tamanho do token de continuação em consultas.
*   Adição de suporte para um rastreamento mais detalhado das solicitações com falha.
*   Melhorias de desempenho no SDK.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Correção de um problema no qual o valor de PartitionKey fornecido em FeedOptions para consultas de agregação é ignorado.
* Correção de um problema na manipulação transparente do gerenciamento de partição durante a metade da execução da consulta Order By entre partições.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Correção de um problema que causou deadlocks em algumas APIs assíncronas quando usado dentro do contexto do ASP.NET.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Correções para tornar o SDK mais resilientes ao failover automático em determinadas condições.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Correção de um problema que eventualmente causa uma WebException: O nome remoto não pôde ser resolvido.
* Adição do suporte para ler um documento digitado diretamente com a adição de novas sobrecargas à API ReadDocumentAsync.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Adicionado suporte ao LINQ para consultas de agregação (CONT.NÚM, MÍNIMO, MÁXIMO, SOMA e MÉDIA).
* Correção de um problema de vazamento de memória do objeto ConnectionPolicy causado pelo uso do manipulador de eventos.
* Correção de um problema no qual UpsertAttachmentAsync não estava funcionando quando ETag era usada.
* Correção de um problema no qual a continuação da consulta order-by entre partições não estava funcionando ao classificar no campo de cadeia de caracteres.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Suporte adicionado para consultas de agregação (COUNT, MIN, MAX, SUM e AVG). Veja [Suporte de agregação](documentdb-sql-query.md#Aggregates).
* Taxa de transferência mínima reduzida em coleções particionadas de 10.100 RU/s a 2500 RU/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

O SDK do .NET Core para Azure Cosmos DB permite criar aplicativos [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) rápidos de plataforma cruzada para execução em Windows, Mac e Linux. A versão mais recente do SDK do .NET Core do Azure Cosmos DB é totalmente compatível com o [Xamarin](https://www.xamarin.com) e pode ser usada para criar aplicativos destinados a iOS, Android e Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

O SDK de Versão Prévia do .NET Core para Azure Cosmos DB permite criar aplicativos [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) rápidos de plataforma cruzada para execução em Windows, Mac e Linux.

O SDK de Versão Prévia do .NET Core do Azure Cosmos DB tem paridade de recurso com a versão mais recente do [SDK do .NET do Azure Cosmos DB](documentdb-sdk-dotnet.md) e dá suporte para o seguinte:
* Todos os [modos de conexão](performance-tips.md#networking): Modo de gateway, TCP Direto e HTTPs Direto. 
* Todos os [níveis de consistência](consistency-levels.md): Forte, Sessão, Desatualização Limitada e Eventual.
* [Coleções particionadas](partition-data.md). 
* [Contas e replicação geográfica de banco de dados de várias regiões](distribute-data-globally.md).

Se você tiver dúvidas relacionadas a esse SDK, poste no [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb) ou registre um problema no [repositório do github](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Datas de lançamento e desativação

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [1.5.1](#1.5.1) |2 de outubro de 2017 |--- |
| [1.5.0](#1.5.0) |10 de agosto de 2017 |--- | 
| [1.4.1](#1.4.1) |7 de agosto de 2017 |--- |
| [1.4.0](#1.4.0) |2 de agosto de 2017 |--- |
| [1.3.2](#1.3.2) |12 de junho de 2017 |--- |
| [1.3.1](#1.3.1) |23 de maio de 2017 |--- |
| [1.3.0](#1.3.0) |10 de maio de 2017 |--- |
| [1.2.2](#1.2.2) |19 de abril de 2017 |--- |
| [1.2.1](#1.2.1) |29 de março de 2017 |--- |
| [1.2.0](#1.2.0) |25 de março de 2017 |--- |
| [1.1.2](#1.1.2) |20 de março de 2017 |--- |
| [1.1.1](#1.1.1) |14 de março de 2017 |--- |
| [1.1.0](#1.1.0) |16 de fevereiro de 2017 |--- |
| [1.0.0](#1.0.0) |21 de dezembro de 2016 |--- |
| [0.1.0-preview](#0.1.0-preview) |15 de novembro de 2016 |31 de dezembro de 2016 |

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

