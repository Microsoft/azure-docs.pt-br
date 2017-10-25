---
title: Recursos e SDK do .NET do Azure Cosmos DB | Microsoft Docs
description: "Saiba tudo sobre o SDK e a API do .NET, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do .NET do Azure Cosmos DB."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 8e239217-9085-49f5-b0a7-58d6e6b61949
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/11/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 317792e04244a96cf8e47bc7e4a7f633f7a6d8c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-net-sdk-download-and-release-notes"></a>SDK do .NET do Azure Cosmos DB: download e notas de versão
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

<tr><td>**Baixe o SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência de API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Exemplos**</td><td>[Exemplos de código .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Introdução**</td><td>[Introdução ao SDK do .NET do Azure Cosmos DB](documentdb-get-started.md)</td></tr>

<tr><td>**Tutorial do aplicativo Web**</td><td>[Desenvolvimento de aplicativos Web com o Azure Cosmos DB](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Framework atualmente com suporte**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão

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
*   Correção de um problema que afetava computadores x64 que não tinham suporte à instrução SSE4 e geravam uma SEHException ao executarem consultas da API DocumentDB do Azure Cosmos DB.

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
* Suporte adicionado para consultas de agregação (COUNT, MIN, MAX, SUM e AVG). Veja [Suporte de agregação](documentdb-sql-query.md#Aggregates).
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
* Foi corrigido um problema que fazia com que consultas de execução longa falhassem com o erro: o token de autorização não é válido no momento.
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
* Nova interface IDocumentClient adicionada que define as assinaturas de todos os métodos e propriedades de DocumenClient.  Como parte dessa alteração, também foram alterados os métodos de extensão que criam IQueryable e IOrderedQueryable para métodos na própria classe DocumentClient.
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
* **[Corrigido]** Consultar o ponto de extremidade do Azure Cosmos DB gera: “System.Net.Http.HttpRequestException: erro ao copiar o conteúdo para um fluxo”.

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

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
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

