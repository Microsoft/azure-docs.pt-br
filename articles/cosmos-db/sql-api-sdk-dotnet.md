---
title: 'O Azure Cosmos DB: API, SDK e recursos .NET SQL'
description: Saiba tudo sobre o SDK e a API do .NET do SQL, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do .NET do Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: 83a866b20d2802b7d49363b7c6451356e938eac1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626835"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>SDK do .NET do Azure Cosmos DB para a API do SQL: Downloads e notas sobre a versão
> [!div class="op_single_selector"]
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
|**Baixe o SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**Documentação da API**|[Documentação de referência de API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Exemplos**|[Exemplos de código .NET](sql-api-dotnet-samples.md)|
|**Introdução**|[Introdução ao SDK do .NET do Azure Cosmos DB](sql-api-get-started.md)|
|**Tutorial do aplicativo Web**|[Desenvolvimento de aplicativos Web com o Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Framework atualmente com suporte**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Notas de versão

### <a name="a-name3001-preview3001-preview"></a><a name="3.0.0.1-preview"/>3.0.0.1-preview
* Versão prévia 1 da [Versão 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) do SDK .NET para visualização pública.
* O destino é .NET Standard, que dá suporte para .NET Framework 4.6.1+ e .NET Core 2.0+
* Novo modelo de objeto, com CosmosClient de nível superior e métodos divididos entre as classes CosmosDatabases, CosmosContainers e CosmosItems. 
* Suporte para fluxos. 
* Atualizado CosmosResponseMessage do servidor para retornar o código de status e somente gerar exceção quando nenhuma resposta for retornada. 

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

* Aprimoramentos de rastreamento de diagnóstico

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

### <a name="a-name12201220"></a><a name="1.22.0"/>1.22.0

* Propriedade ConsistencyLevel adicionada a FeedOptions.
* JsonSerializerSettings adicionado a RequestOptions e FeedOptions.
* EnableReadRequestsFallback adicionado a ConnectionPolicy.

### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* Corrigido KeyNotFoundException para consultas order by entre partições em situações extremas.
* Corrigido o bug onde o atributo JsonProperty na cláusula select para consultas LINQ não estava sendo respeitado.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Corrigido um bug que ocorre em determinadas condições de corrida, que resulta em erros "Microsoft.Azure.Documents.NotFoundException: A sessão de leitura não está disponível para o token de sessão de entrada" intermitentes ao usar o nível de consistência de sessão.

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* Regressão fixa em que FeedOptions.MaxItemCount = -1 gerou uma System.ArithmeticException: o tamanho da página é negativo.
* Uma nova função ToString() foi adicionada a QueryMetrics.
* Estatísticas de partição foram expostas em coleções de leitura.
* A propriedade PartitionKey foi adicionada a ChangeFeedOptions.
* Correções de bug secundárias.

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* Adiciona a capacidade de especificar índices exclusivos para os documentos usando a propriedade UniqueKeyPolicy na DocumentCollection.
* Correção de um bug no qual as configurações personalizadas JsonSerializer não estavam sendo cumpridas por algumas consultas e pela execução de procedimento armazenado.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Alteração da identidade visual do DocumentDB do Azure para o Azure Cosmos DB na referência de API, documentação, informações de metadados em assemblies e o pacote NuGet. 
* Expor informações de diagnóstico e a latência da resposta de solicitações enviadas com o modo de conectividade direta. Os nomes de propriedade são RequestDiagnosticsString e RequestLatency na classe ResourceResponse.
* Esta versão do SDK requer a versão mais recente do Emulador do Azure Cosmos DB disponível para fazer o download em https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Alterações internas para assemblies de amigos da Microsoft.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Foram adicionadas vários correções e melhorias de confiabilidade.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Adição de suporte a PartitionKeyRangeId como FeedOption de modo a definir o escopo dos resultados da consulta para um valor específico do intervalo de chaves de partição. 
* Adição de suporte a StartTime como ChangeFeedOption para começar a procurar as alterações após esse horário.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Foi corrigido um problema na classe JsonSerializable que podia gerar uma exceção de excedente de pilha.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Corrigido um problema que exigia a recompilação o aplicativo devido à introdução de JsonSerializerSettings como um parâmetro opcional no construtor DocumentClient.
* O construtor DocumentClient, que exigia JsonSerializerSettings como o último parâmetro para permitir valores padrão de parâmetros ConnectionPolicy e ConsistencyLevel ao passar no parâmetro JsonSerializerSettings, foi marcado como obsoleto.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Adicionado suporte para a especificação de JsonSerializerSettings personalizadas ao instanciar [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Corrigido um problema que afetava computadores x64 que não davam suporte à instrução SSE4 e que geravam SEHException ao executarem consultas de SQL do Azure Cosmos DB.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Adição de suporte a um novo nível de consistência chamado ConsistentPrefix.
*   Adição de suporte a métricas de consulta em partições individuais.
*   Adição de suporte para limitar o tamanho do token de continuação em consultas.
*   Adição de suporte para um rastreamento mais detalhado das solicitações com falha.
*   Melhorias de desempenho no SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Funcionalmente equivalente a 1.13.3. Algumas alterações internas.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Funcionalmente equivalente a 1.13.2. Algumas alterações internas.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Correção de um problema no qual o valor de PartitionKey fornecido em FeedOptions para consultas de agregação é ignorado.
* Correção de um problema na manipulação transparente do gerenciamento de partição durante a metade da execução da consulta Order By entre partições.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Correção de um problema que causou deadlocks em algumas APIs assíncronas quando usado dentro do contexto do ASP.NET.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Correções para tornar o SDK mais resilientes ao failover automático em determinadas condições.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Correção de um problema que eventualmente causa uma WebException: O nome remoto não pôde ser resolvido.
* Adição do suporte para ler um documento digitado diretamente com a adição de novas sobrecargas à API ReadDocumentAsync.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* Adicionado suporte ao LINQ para consultas de agregação (CONT.NÚM, MÍNIMO, MÁXIMO, SOMA e MÉDIA).
* Correção de um problema de vazamento de memória do objeto ConnectionPolicy causado pelo uso do manipulador de eventos.
* Correção de um problema no qual UpsertAttachmentAsync não estava funcionando quando ETag era usada.
* Correção de um problema no qual a continuação da consulta order-by entre partições não estava funcionando ao classificar no campo de cadeia de caracteres.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Suporte adicionado para consultas de agregação (COUNT, MIN, MAX, SUM e AVG). Veja [Suporte de agregação](how-to-sql-query.md#Aggregates).
* Taxa de transferência mínima reduzida em coleções particionadas de 10.100 RU/s a 2500 RU/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Correção para um problema no qual algumas das consultas entre partições com falha no processo de host de 32 bits.
* Correção para um problema no qual o contêiner de sessão não estava sendo atualizado com o token para solicitações com falha no modo de Gateway.
* Correção para um problema no qual uma consulta com chamadas a UDF na projeção estava falhando em alguns casos.
* Correções de desempenho do lado do cliente para aumentar a taxa de transferência de leitura e gravação das solicitações.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Correção para um problema no qual o contêiner de sessão não estava sendo atualizado com o token para solicitações com falha.
* Adicionado suporte para o SDK trabalhar em um processo de host de 32 bits. Observe que, se você usar consultas entre partições, processamento de host de 64 bits é recomendado para melhorar o desempenho.
* Desempenho aprimorado para cenários que envolvem consultas com um grande número de valores de chave de partição em uma expressão em.
* Preenchimento de várias estatísticas de cota de recursos no ResourceResponse para solicitações de leitura de conjuntos de documento quando a opção de solicitação PopulateQuotaInfo estiver definida.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Pequena correção de desempenho para a API CreateDocumentCollectionIfNotExistsAsync introduzida no 1.11.0.
* Correção de desempenho no SDK para cenários que envolvem o alto grau de solicitações simultâneas.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Suporte para novas classes e métodos para processar o [feed de alterações](change-feed.md) dos documentos dentro de uma coleção.
* Suporte para continuação de consulta entre partições e algumas melhorias de desempenho para consultas entre partições.
* Acréscimo dos métodos CreateDatabaseIfNotExistsAsync e CreateDocumentCollectionIfNotExistsAsync.
* Suporte para LINQ para funções do sistema: IsDefined, IsNull e IsPrimitive.
* Correção para binplacing automático dos assemblies Microsoft.Azure.Documents.ServiceInterop.dll e DocumentDB.Spatial.Sql.dll para a pasta de lixeira do aplicativo ao usar o pacote do Nuget com projetos que têm ferramentas project.json.
* Suporte para emitir rastreamentos ETW do lado cliente, que podem ser úteis em cenários de depuração.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Adicionado suporte a conectividade direta para coleções particionadas.
* Melhor desempenho para o nível de consistência Bounded Staleness.
* Adicionado Polygon e LineString DataTypes ao especificar a política de indexação de coleção para consultas espaciais de isolamento geográfico.
* Adicionado suporte ao LINQ para StringEnumConverter, IsoDateTimeConverter e UnixDateTimeConverter ao converter predicados.
* Várias correções de bugs do SDK.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Corrigido um problema que causava a seguinte NotFoundException: A sessão de leitura não está disponível para o token de sessão de entrada. Essa exceção ocorria em alguns casos ao consultar a região de leitura de uma conta distribuída geograficamente.
* Exposta a propriedade ResponseStream na classe ResourceResponse, que permite acesso direto ao fluxo subjacente de uma resposta.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Foram modificadas as classes ResourceResponse, FeedResponse, StoredProcedureResponse e MediaResponse para implementar a interface pública correspondente para que elas possam ser simuladas para a implantação orientada a testes (TDD).
* Foi corrigido um problema que causava um cabeçalho de chave de partição malformado ao usar um objeto JsonSerializerSettings personalizado para serialização de dados.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Corrigido um problema que causou falha em consultas de longa execução com o erro: Token de autorização não é válido no momento atual.
* Foi corrigido um problema que removia o SqlParameterCollection original de consultas superiores/ordenar por entre partições.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Adição de suporte a consultas paralelas de coleções particionadas.
* Adição de suporte a consultas ORDER BY e TOP entre partições para coleções particionadas.
* Correção das referências ausentes para DocumentDB.Spatial.Sql.dll e Microsoft.Azure.Documents.ServiceInterop.dll, que são obrigatórios ao fazer referência a um projeto do Azure Cosmos DB com uma referência para o pacote Nuget do Azure Cosmos DB.
* Correção da capacidade de usar parâmetros de diferentes tipos ao usar funções definidas pelo usuário no LINQ. 
* Correção de um bug para contas globalmente replicadas em que chamadas Upsert estavam sendo direcionadas para locais de leitura em vez de locais de gravação.
* Adição de métodos que estavam ausentes à interface IDocumentClient: 
  * Método UpsertAttachmentAsync que usa mediaStream e opções como parâmetros
  * Método CreateAttachmentAsync que usa opções como um parâmetro
  * Método CreateOfferQuery que usa querySpec como um parâmetro.
* Retirada do selo de classes públicas que são expostas na interface IDocumentClient.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Suporte adicionado para contas de banco de dados de várias regiões.
* Suporte adicionado para repetição de solicitações limitadas.  O usuário pode personalizar o número de repetições e o tempo máximo de espera configurando a propriedade ConnectionPolicy.RetryOptions.
* Nova interface IDocumentClient adicionada que define as assinaturas de todos os métodos e propriedades de DocumentClient.  Como parte dessa alteração, também foram alterados os métodos de extensão que criam IQueryable e IOrderedQueryable para métodos na própria classe DocumentClient.
* Opção de configuração adicionada para definir ServicePoint.ConnectionLimit de um determinado URI de ponto de extremidade do Azure Cosmos DB.  Use ConnectionPolicy.MaxConnectionLimit para alterar o valor padrão, que é definido como 50.
* IPartitionResolver e sua implementação foram preteridos.  O suporte para IPartitionResolver agora é obsoleto. Recomendamos que você use Coleções Particionadas para taxa de transferência e armazenamento superiores.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Adicionada uma sobrecarga ao Uri com base no método ExecuteStoredProcedureAsync que recebe RequestOptions como um parâmetro.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Adicionado suporte a TTL (tempo de vida) para documentos.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Correção de um bug no pacote Nuget do SDK do .NET para empacotamento como parte de uma solução do Serviço de Nuvem do Azure.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Implementação de [coleções particionadas](partition-data.md) e [níveis de desempenho definidos pelo usuário](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Corrigido]** A consulta do ponto de extremidade do Azure Cosmos DB gera: 'System.Net.Http.HttpRequestException: Erro ao copiar o conteúdo para um fluxo'.

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Expansão do suporte do LINQ, incluindo novos operadores para paginação, expressões condicionais e comparação de intervalo.
  * Operador Take para habilitar o comportamento de SELECT TOP no LINQ
  * Operador CompareTo para habilitar comparações de intervalo de cadeia de caracteres
  * Operadores condicionais (?) e de união (??)
* **[Corrigido]** ArgumentOutOfRangeException ao combinar a projeção do Modelo com o Where-In na consulta LINQ. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Corrigido]** Se Select não for a última expressão, o Provedor LINQ presumiu que não havia projeção e produziu SELECT * incorretamente.  [Nº 58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Implementação de Upsert, adição de métodos UpsertXXXAsync
* Melhorias de desempenho de todas as solicitações
* Suporte ao provedor LINQ para os métodos conditional, coalesce e CompareTo em cadeias de caracteres
* **[Corrigido]** Provedor LINQ --> Implementa o método Contains em List para gerar o mesmo SQL como em IEnumerable e Array
* **[Corrigido]** BackoffRetryUtility usa a mesma HttpRequestMessage novamente em vez de criar uma nova em uma nova tentativa
* **[Obsoleto]** UriFactory.CreateCollection--> agora deve usar UriFactory.CreateDocumentCollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Corrigido]** Problemas de localização ao usar informações de cultura em outro idioma que não o inglês, como nl-NL, etc. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Adicionado roteamento baseado em ID
  * Novo auxiliar UriFactory para ajudá-lo a construir links para recursos baseados em identificação
  * Novas sobrecargas em DocumentClient para receber URI
* Adicionado IsValid() e IsValidDetailed () em LINQ para geoespaciais
* Suporte ao provedor LINQ aprimorado:
  * **Matemática** - Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan e Truncate
  * **Cadeia de caracteres** - Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString e ToUpper
  * **Array** - Concat, Contains, Count
  * **IN** 

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Suporte adicionado para modificar políticas de indexação.
  * Novo método ReplaceDocumentCollectionAsync no DocumentClient
  * Nova propriedade IndexTransformationProgress em ResourceResponse<T> para acompanhar o progresso em percentual de alterações na política de índice
  * DocumentCollection.IndexingPolicy agora é mutável
* Suporte adicionado para consulta e indexação espacial.
  * Novo namespace Microsoft.Azure.Documents.Spatial para serializar/desserializar tipos espaciais, como Ponto e Polígono
  * Nova classe SpatialIndex para indexação de dados GeoJSON armazenados no Cosmos DB
* **[Corrigido]** : consulta SQL incorreta gerada usando a expressão LINQ [Nº 38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Adicionada dependência de Newtonsoft.Json v5.0.7.
* Alterações feitas para dar suporte a Order By:
  
  * Suporte ao provedor LINQ para OrderBy() ou OrderByDescending()
  * IndexingPolicy para dar suporte a Order By 
    
    **Possível alteração interruptiva** 
    
    Se você tem o código existente que provisiona as coleções com uma política personalizada de indexação, o seu código existente precisa ser atualizado para dar suporte à nova classe IndexingPolicy. Se você não tem uma política personalizada de indexação, essa alteração não afeta você.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Adicionado suporte a dados de particionamento usando as novas classes HashPartitionResolver e RangePartitionResolver e o IPartitionResolver.
* Adicionada serialização DataContract.
* Adicionado suporte a GUID no provedor LINQ.
* Adicionado suporte a UDF no LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK DO GA

## <a name="release--retirement-dates"></a>Datas de lançamento e desativação
A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente. 

Qualquer solicitação feita ao BD Cosmos do Azure usando um SDK obsoleto é rejeitada pelo serviço.

<br/>

| Version | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [2.2.3](#2.2.3) |11 de fevereiro de 2019 |--- |
| [2.2.2](#2.2.2) |06 de fevereiro de 2019 |--- |
| [2.2.1](#2.2.1) |24 de dezembro de 2018 |--- |
| [2.2.0](#2.2.0) |07 de dezembro de 2018 |--- |
| [2.1.3](#2.1.3) |15 de outubro de 2018 |--- |
| [2.1.2](#2.1.2) |04 de outubro de 2018 |--- |
| [2.1.1](#2.1.1) |27 de setembro de 2018 |--- |
| [2.1.0](#2.1.0) |21 de setembro de 2018 |--- |
| [2.0.0](#2.0.0) |07 de setembro de 2018 |--- |
| [1.22.0](#1.22.0) |19 de abril de 2018 |--- |
| [1.21.1](#1.20.1) |09 de março de 2018 |--- |
| [1.20.2](#1.20.1) |21 de fevereiro de 2018 |--- |
| [1.20.1](#1.20.1) |5 de fevereiro de 2018 |--- |
| [1.19.1](#1.19.1) |16 de novembro de 2017 |--- |
| [1.19.0](#1.19.0) |10 de novembro, 2017 |--- |
| [1.18.1](#1.18.1) |07 de novembro de 2017 |--- |
| [1.18.0](#1.18.0) |17 de outubro de 2017 |--- |
| [1.17.0](#1.17.0) |10 de agosto de 2017 |--- |
| [1.16.1](#1.16.1) |7 de agosto de 2017 |--- |
| [1.16.0](#1.16.0) |2 de agosto de 2017 |--- |
| [1.15.0](#1.15.0) |30 de junho de 2017 |--- |
| [1.14.1](#1.14.1) |23 de maio de 2017 |--- |
| [1.14.0](#1.14.0) |10 de maio de 2017 |--- |
| [1.13.4](#1.13.4) |9 de maio de 2017 |--- |
| [1.13.3](#1.13.3) |6 de maio de 2017 |--- |
| [1.13.2](#1.13.2) |19 de abril de 2017 |--- |
| [1.13.1](#1.13.1) |29 de março de 2017 |--- |
| [1.13.0](#1.13.0) |24 de março de 2017 |--- |
| [1.12.2](#1.12.2) |20 de março de 2017 |--- |
| [1.12.1](#1.12.1) |14 de março de 2017 |--- |
| [1.12.0](#1.12.0) |15 de fevereiro de 2017 |--- |
| [1.11.4](#1.11.4) |06 de fevereiro de 2017 |--- |
| [1.11.3](#1.11.3) |26 de janeiro de 2017 |--- |
| [1.11.1](#1.11.1) |21 de dezembro de 2016 |--- |
| [1.11.0](#1.11.0) |08 de dezembro de 2016 |--- |
| [1.10.0](#1.10.0) |27 de setembro de 2016 |--- |
| [1.9.5](#1.9.5) |1º de setembro de 2016 |--- |
| [1.9.4](#1.9.4) |24 de agosto de 2016 |--- |
| [1.9.3](#1.9.3) |15 de agosto de 2016 |--- |
| [1.9.2](#1.9.2) |23 de julho de 2016 |--- |
| [1.8.0](#1.8.0) |14 de junho de 2016 |--- |
| [1.7.1](#1.7.1) |6 de maio de 2016 |--- |
| [1.7.0](#1.7.0) |26 de abril de 2016 |--- |
| [1.6.3](#1.6.3) |08 de abril de 2016 |--- |
| [1.6.2](#1.6.2) |29 de março de 2016 |--- |
| [1.5.3](#1.5.3) |19 de fevereiro de 2016 |--- |
| [1.5.2](#1.5.2) |14 de dezembro de 2015 |--- |
| [1.5.1](#1.5.1) |23 de novembro de 2015 |--- |
| [1.5.0](#1.5.0) |5 de outubro de 2015 |--- |
| [1.4.1](#1.4.1) |25 de agosto de 2015 |--- |
| [1.4.0](#1.4.0) |13 de agosto de 2015 |--- |
| [1.3.0](#1.3.0) |5 de agosto de 2015 |--- |
| [1.2.0](#1.2.0) |6 de julho de 2015 |--- |
| [1.1.0](#1.1.0) |30 de abril de 2015 |--- |
| [1.0.0](#1.0.0) |8 de abril de 2015 |--- |


## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

