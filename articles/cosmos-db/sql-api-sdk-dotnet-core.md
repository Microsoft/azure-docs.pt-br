---
title: 'O Azure Cosmos DB: SDK, API do .NET Core do SQL e recursos'
description: Saiba tudo sobre o SDK e a API do .NET Core do SQL, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do .NET Core para Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: de712d63fa2315a85c6c0eaf392d9c17758e4999
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226173"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>SDK do .NET Core do Azure Cosmos DB para API do SQL: Notas sobre a versão e recursos
> [!div class="op_single_selector"]
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações do .NET](sql-api-sdk-dotnet-changefeed.md)
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
|**Baixe o SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**Documentação da API**|[Documentação de referência de API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Exemplos**|[Exemplos de código .NET](sql-api-dotnet-samples.md)|
|**Introdução**|[Introdução ao Azure Cosmos DB .NET](sql-api-sdk-dotnet.md)|
|**Tutorial do aplicativo Web**|[Desenvolvimento de aplicativos Web com o Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Framework atualmente com suporte**|[.NET Standard 1.6 e .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Notas de versão

> [!NOTE]
> Se você estiver usando o .NET Core, consulte a versão mais recente 3. x do [SDK do .net](sql-api-sdk-dotnet-standard.md), que tem como destino .net Standard. 

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* Corrige a condição de corrida de rastreamento para consultas que causaram páginas vazias

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* A versão do System .net. http do SDK corresponde ao que está definido no pacote NuGet
* Tamanho de precisão decimal aumentado para consultas LINQ.
* Adicionadas novas classes CompositePath, CompositePathSortOrder, SpatialSpec, Spatialtype e PartitionKeyDefinitionVersion
* Adicionado TimeToLivePropertyPath a Documentcollection
* Adicionado CompositeIndexes e SpatialIndexes ao IndexPolicy
* Versão adicionada ao PartitionKeyDefinition
* Adicionado nenhum ao PartitionKey

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * Adicionado IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection e MaxTcpConnectionsPerEndpoint a ConnectionPolicy.
 
### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Aprimoramentos de diagnóstico

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* A configuração de variável de ambiente "POCOSerializationOnly" foi adicionada.

* DocumentDB.Spatial.Sql.dll foi excluído e, agora, foi incluído em Microsoft.Azure.Documents.ServiceInterop.dll

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Melhoria na lógica de repetição durante o failover para chamadas de execução de StoredProcedure.

* DocumentClientEventSource foi transformado em singleton. 

* O tempo limite de GatewayAddressCache que não estava respeitando o RequestTimeout de ConnectionPolicy foi corrigido.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* Para diagnósticos de transporte TCP/direto, foi incluído TransportException, um tipo de exceção interno do SDK. Quando presente nas mensagens de exceção, esse tipo imprime informações adicionais para solução de problemas de conectividade do cliente.

* Adicionada nova sobrecarga de construtor que usa um HttpMessageHandler, uma pilha de manipulador HTTP a ser usada para enviar solicitações HttpClient (por exemplo, HttpClientHandler).

* Correção de bug em que cabeçalhos com valores nulos não eram tratados corretamente.

* Aprimorada a validação de cache da coleção.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* Atualizado System.Net.Security para 4.3.2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Aprimoramentos de rastreamento de diagnóstico.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* Adição de maior resiliência a falhas transitórias de solicitação de várias regiões.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Adição de compatibilidade à gravação em várias regiões.
* Melhorias de desempenho de consultar entre partições com TOP e MaxBufferedItemCount.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Adicionado suporte para cancelamento de solicitações.
* Adicionado SetCurrentLocation para ConnectionPolicy, que preenche automaticamente as localizações preferenciais com base na região.
* Corrigido o Bug em Consultas de Partição Cruzada com Mín/Máx e um filtro que não corresponde a nenhum documento em uma partição individual.
* Os métodos DocumentClient agora têm paridade com IDocumentClient.
* Pilha de transporte de TCP direto atualizada para reduzir o número de conexões estabelecidas.
* Adicionado suporte para TCP de modo direto para clientes não Windows.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Adicionado suporte para cancelamento de solicitações.
* Adicionado SetCurrentLocation para ConnectionPolicy, que preenche automaticamente as localizações preferenciais com base na região.
* Corrigido o Bug em Consultas de Partição Cruzada com Mín/Máx e um filtro que não corresponde a nenhum documento em uma partição individual.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* Os métodos DocumentClient agora têm paridade com IDocumentClient.
* Pilha de transporte de TCP direto atualizada para reduzir o número de conexões estabelecidas.
* Adicionado suporte para TCP de modo direto para clientes não Windows.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0

* Propriedade ConsistencyLevel adicionada a FeedOptions.
* JsonSerializerSettings adicionado a RequestOptions e FeedOptions.
* EnableReadRequestsFallback adicionado a ConnectionPolicy.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* Corrigido KeyNotFoundException para consultas order by entre partições em situações extremas.
* Corrigido o bug onde o atributo JsonProperty na cláusula select para consultas LINQ não estava sendo respeitado.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Corrigido um bug que ocorre em determinadas condições de corrida, que resulta em erros "Microsoft.Azure.Documents.NotFoundException: A sessão de leitura não está disponível para os erros de token de sessão de entrada" ao usar o nível de consistência da Sessão.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Regressão fixa em que FeedOptions.MaxItemCount = -1 gerou uma System.ArithmeticException: o tamanho da página é negativo.
* Uma nova função ToString() foi adicionada a QueryMetrics.
* Estatísticas de partição foram expostas em coleções de leitura.
* A propriedade PartitionKey foi adicionada a ChangeFeedOptions.
* Correções de bug secundárias.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Adiciona a capacidade de especificar índices exclusivos para os documentos usando a propriedade UniqueKeyPolicy na DocumentCollection.
 * Correção de um bug no qual as configurações personalizadas JsonSerializer não estavam sendo cumpridas por algumas consultas e pela execução de procedimento armazenado.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Alteração da identidade visual do DocumentDB do Azure para o Azure Cosmos DB na referência de API, documentação, informações de metadados em assemblies e o pacote NuGet.
 * Expor informações de diagnóstico e a latência da resposta de solicitações enviadas com o modo de conectividade direta. Os nomes de propriedade são RequestDiagnosticsString e RequestLatency na classe ResourceResponse.
 * Esta versão do SDK requer a versão mais recente do Emulador do Azure Cosmos DB disponível para fazer o download em https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Foram adicionadas vários correções e melhorias de confiabilidade.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Alterações internas relacionadas ao suporte a [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

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

* Suporte adicionado para consultas de agregação (COUNT, MIN, MAX, SUM e AVG). Veja [Suporte de agregação](sql-query-aggregates.md).
* Taxa de transferência mínima reduzida em coleções particionadas de 10.100 RU/s a 2500 RU/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

O SDK do .NET Core para Azure Cosmos DB permite criar aplicativos [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) rápidos de plataforma cruzada para execução em Windows, Mac e Linux. A versão mais recente do SDK do .NET Core do Azure Cosmos DB é totalmente compatível com o [Xamarin](https://www.xamarin.com) e pode ser usada para criar aplicativos destinados a iOS, Android e Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

O SDK de Versão Prévia do .NET Core para Azure Cosmos DB permite criar aplicativos [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) rápidos de plataforma cruzada para execução em Windows, Mac e Linux.

O SDK de Versão Prévia do .NET Core do Azure Cosmos DB tem paridade de recurso com a versão mais recente do [SDK do .NET do Azure Cosmos DB](sql-api-sdk-dotnet.md) e dá suporte para o seguinte:
* Todos os [modos de conexão](performance-tips.md#networking): Modo Gateway, TCP Direto e HTTPs Diretos.
* Todos os [níveis de consistências](consistency-levels.md): Forte, Sessão, Desatualização Limitada e Eventual.
* [Coleções particionadas](partition-data.md).
* [Contas e replicação geográfica de banco de dados de várias regiões](distribute-data-globally.md).

Se você tiver dúvidas relacionadas a esse SDK, poste no [StackOverflow](https://stackoverflow.com/questions/tagged/azure-documentdb) ou registre um problema no [Repositório GitHub](https://github.com/Azure/azure-documentdb-dotnet/issues).

## <a name="release--retirement-dates"></a>Datas de lançamento e desativação

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [2.4.1](#2.4.1) |20 de junho de 2019 |--- |
| [2.4.0](#2.4.0) |05 de maio de 2019 |--- |
| [2.3.0](#2.3.0) |04 de abril de 2019 |--- |
| [2.2.3](#2.2.3) |11 de março de 2019 |--- |
| [2.2.2](#2.2.2) |06 de fevereiro de 2019 |--- |
| [2.2.1](#2.2.1) |24 de dezembro de 2018 |--- |
| [2.2.0](#2.2.0) |07 de dezembro de 2018 |--- |
| [2.1.3](#2.1.3) |15 de outubro de 2018 |--- |
| [2.1.2](#2.1.2) |04 de outubro de 2018 |--- |
| [2.1.1](#2.1.1) |27 de setembro de 2018 |--- |
| [2.1.0](#2.1.0) |21 de setembro de 2018 |--- |
| [2.0.0](#2.0.0) |07 de setembro de 2018 |--- |
| [1.9.1](#1.9.1) |09 de março de 2018 |--- |
| [1.8.2](#1.8.2) |21 de fevereiro de 2018 |--- |
| [1.8.1](#1.8.1) |5 de fevereiro de 2018 |--- |
| [1.7.1](#1.7.1) |16 de novembro de 2017 |--- |
| [1.7.0](#1.7.0) |10 de novembro, 2017 |--- |
| [1.6.0](#1.6.0) |17 de outubro de 2017 |--- |
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

