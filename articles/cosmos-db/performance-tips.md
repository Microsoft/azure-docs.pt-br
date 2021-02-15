---
title: Azure Cosmos DB dicas de desempenho para o SDK do .NET v2
description: Aprenda as opções de configuração do cliente para melhorar o desempenho do SDK do .NET v2 Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: sngun
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: 47e20e89c8eaef59b9acd6cf7e31244afd4bcf60
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359040"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Dicas de desempenho para o Azure Cosmos DB e .NET SDK v2
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK v3 do .NET](performance-tips-dotnet-sdk-v3-sql.md)
> * [SDK do .NET v2](performance-tips.md)
> * [SDK do Java v4](performance-tips-java-sdk-v4-sql.md)
> * [SDK do Java Assíncrono v2](performance-tips-async-java.md)
> * [SDK do Java Síncrono v2](performance-tips-java.md)

O Azure Cosmos DB é um banco de dados distribuído rápido e flexível que pode ser dimensionado perfeitamente com garantia de latência e produtividade. Você não precisa fazer grandes alterações na arquitetura nem escrever código complexo para dimensionar seu banco de dados com Azure Cosmos DB. Aumentar e diminuir a escala é tão fácil quanto fazer uma única chamada de API. Para saber mais, veja [como provisionar a taxa de transferência do contêiner](how-to-provision-container-throughput.md) ou [como provisionar a taxa de transferência do banco de dados](how-to-provision-database-throughput.md). Mas como Azure Cosmos DB é acessado por meio de chamadas de rede, há otimizações do lado do cliente que você pode fazer para obter o máximo de desempenho ao usar o [SDK do .net do SQL](sql-api-sdk-dotnet-standard.md).

Portanto, se você estiver tentando melhorar o desempenho do banco de dados, considere estas opções:

## <a name="upgrade-to-the-net-v3-sdk"></a>Atualizar para o SDK do .NET v3

O [SDK do .net v3](https://github.com/Azure/azure-cosmos-dotnet-v3) é lançado. Se você usar o SDK do .NET v3, consulte o [Guia de desempenho do .net v3](performance-tips-dotnet-sdk-v3-sql.md) para obter as seguintes informações:

- O padrão é direcionar o modo TCP
- Suporte à API do Stream
- Suporte ao serializador personalizado para permitir o System.Text.JSno uso
- Lote integrado e suporte em massa

## <a name="hosting-recommendations"></a>Recomendações de hospedagem

**Para cargas de trabalho com uso intensivo de consulta, use o Windows 64-bit em vez do processamento de host Linux ou Windows de 32 bits**

Recomendamos o processamento de host do Windows de 64 bits para melhorar o desempenho. O SDK do SQL inclui um ServiceInterop.dll nativo para analisar e otimizar as consultas localmente. O ServiceInterop.dll tem suporte apenas na plataforma Windows x64. Para Linux e outras plataformas sem suporte em que o ServiceInterop.dll não está disponível, é feita uma chamada de rede adicional ao gateway para obter a consulta otimizada. Os seguintes tipos de aplicativos usam o processamento de host de 32 bits por padrão. Para alterar o processamento de host para o processamento de 64 bits, siga estas etapas, com base no tipo de seu aplicativo:

- Para aplicativos executáveis, você pode alterar o processamento do host definindo o [destino da plataforma](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) para **x64**  na janela de **Propriedades do projeto** , na guia **Compilar** .

- Para projetos de teste baseados em VSTest, você pode alterar o processamento de host selecionando **testar**  >  **configurações**  >  **de teste arquitetura de processador padrão como x64** no menu de **teste** do Visual Studio.

- Para aplicativos Web ASP.net implantados localmente, você pode alterar o processamento de host selecionando **usar a versão de 64 bits do IIS Express para sites e projetos** em **ferramentas**  >  **Opções**  >  **projetos e soluções**  >  **projetos da Web**.

- Para aplicativos Web ASP.NET implantados no Azure, você pode alterar o processamento de host selecionando a plataforma de **64 bits** nas **configurações do aplicativo** na portal do Azure.

> [!NOTE] 
> Por padrão, novos projetos do Visual Studio são definidos para **qualquer CPU**. Recomendamos que você defina seu projeto como **x64** para que ele não mude para **x86**. Um projeto definido para **qualquer CPU** pode mudar facilmente para **x86** se uma dependência somente x86 for adicionada.<br/>
> ServiceInterop.dll precisa estar na pasta da qual a DLL do SDK está sendo executada. Isso deve ser uma preocupação apenas se você copiar DLLs manualmente ou se tiver sistemas de compilação/implantação personalizados.
    
**Ativar coleta de lixo do lado do servidor (GC)**

Reduzir a frequência de coleta de lixo pode ajudar em alguns casos. No .NET, defina [gcServer](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element) como `true` .

**Escalar horizontalmente a carga de trabalho do cliente**

Se você estiver testando em níveis altos de taxa de transferência (mais de 50.000 RU/s), o aplicativo cliente poderá se tornar o afunilamento devido à máquina que está capping a utilização da CPU ou da rede. Se você chegar a este ponto, poderá continuar aumentando a conta do Azure Cosmos DB ainda mais distribuindo seus aplicativos cliente entre vários servidores.

> [!NOTE] 
> Alto uso da CPU pode causar maior latência e exceções de tempo limite de solicitação.

## <a name="networking"></a>Rede do <a id="networking"></a>

**Política de conexão: usar o modo de conexão direta**

O modo de conexão padrão do SDK do .NET V2 é gateway. Você configura o modo de conexão durante a construção da `DocumentClient` instância usando o `ConnectionPolicy` parâmetro. Se você usar o modo direto, também precisará definir o usando `Protocol` o `ConnectionPolicy` parâmetro. Para saber mais sobre as diferentes opções de conectividade, consulte o artigo [modos de conectividade](sql-sdk-connection-modes.md) .

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

**Esgotamento de porta efêmera**

Se você vir um alto volume de conexão ou uso de porta alta em suas instâncias, primeiro verifique se suas instâncias de cliente são singletons. Em outras palavras, as instâncias de cliente devem ser exclusivas durante o tempo de vida do aplicativo.

Ao executar no protocolo TCP, o cliente otimiza a latência usando as conexões de vida longa, em oposição ao protocolo HTTPS, que encerra as conexões após 2 minutos de inatividade.

Em cenários em que você tem acesso esparso e se você notar uma contagem de conexões maior quando comparado ao acesso do modo de gateway, você pode:

* Configure a propriedade [ConnectionPolicy. PortReuseMode](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) para `PrivatePortPool` (em vigor com a versão do Framework>= 4.6.1 e a versão do .net core >= 2,0): essa propriedade permite que o SDK use um pequeno pool de portas efêmeras para diferentes pontos de extremidade de destino Azure Cosmos DB.
* Configure a propriedade [ConnectionPolicy. IdleConnectionTimeout](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) deve ser maior ou igual a 10 minutos. Os valores recomendados são entre 20 minutos e 24 horas.

**Chamar OpenAsync para evitar a latência de inicialização na primeira solicitação**

Por padrão, a primeira solicitação tem maior latência porque ela precisa buscar a tabela de roteamento de endereços. Ao usar o [SDK v2](sql-api-sdk-dotnet.md), chame `OpenAsync()` uma vez durante a inicialização para evitar essa latência de inicialização na primeira solicitação. A chamada é semelhante a: `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` o irá gerar solicitações para obter a tabela de roteamento de endereços para todos os contêineres na conta. Para contas que têm muitos contêineres, mas cujo aplicativo acessa um subconjunto deles, o `OpenAsync` geraria uma quantidade desnecessária de tráfego, o que tornaria a inicialização lenta. Portanto, `OpenAsync` o uso do pode não ser útil nesse cenário porque torna a inicialização do aplicativo mais lenta.

**Para desempenho, colocar clientes na mesma região do Azure**

Quando possível, coloque todos os aplicativos que chamam Azure Cosmos DB na mesma região que o banco de dados Azure Cosmos DB. Veja uma comparação aproximada: as chamadas para Azure Cosmos DB na mesma região são concluídas dentro de 1 ms a 2 ms, mas a latência entre a costa oeste e a leste dos EUA é mais de 50 ms. Essa latência pode variar de solicitação a solicitação, dependendo da rota realizada pela solicitação à medida que ela passa do cliente para o limite de datacenter do Azure. Você pode obter a menor latência possível garantindo que o aplicativo de chamada esteja localizado na mesma região do Azure que o ponto de extremidade de Azure Cosmos DB provisionado. Para obter uma lista de regiões disponíveis, consulte [Regiões do Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="A política de conexão Azure Cosmos DB" border="false":::

**Aumentar o número de threads/tarefas**
<a id="increase-threads"></a>

Como as chamadas para Azure Cosmos DB são feitas pela rede, talvez seja necessário variar o grau de paralelismo de suas solicitações para que o aplicativo cliente aguarde o tempo mínimo de espera entre as solicitações. Por exemplo, se você estiver usando a [biblioteca paralela de tarefas](/dotnet/standard/parallel-programming/task-parallel-library-tpl)do .net, crie na ordem de centenas de tarefas que lêem ou gravam em Azure Cosmos DB.

**Habilitar rede acelerada**
 
Para reduzir a latência e a tremulação da CPU, recomendamos que você habilite a rede acelerada nas máquinas virtuais do cliente. Consulte [criar uma máquina virtual do Windows com rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) ou [criar uma máquina virtual Linux com rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>Uso do SDK

**Instalar o SDK mais recente**

Os SDKs do Azure Cosmos DB estão constantemente sendo aprimorados para fornecer o melhor desempenho. Consulte as páginas do [SDK do Azure Cosmos DB](sql-api-sdk-dotnet-standard.md) para determinar o SDK mais recente e examinar as melhorias.

**Usar um cliente do Azure Cosmos DB singleton para obter o tempo de vida do aplicativo**

Cada `DocumentClient` instância é thread-safe e executa o gerenciamento de conexão e o cache de endereços eficientes ao operar no modo direto. Para permitir o gerenciamento de conexão eficiente e melhor desempenho do cliente SDK, recomendamos que você use uma única instância por `AppDomain` tempo de vida do aplicativo.

**Aumentar o System.Net MaxConnections por host ao usar o modo de gateway**

Azure Cosmos DB solicitações são feitas por HTTPS/REST quando você usa o modo de gateway. Eles estão sujeitos ao limite de conexão padrão por nome de host ou endereço IP. Talvez seja necessário definir `MaxConnections` como um valor mais alto (100 a 1.000) para que a biblioteca de cliente possa usar várias conexões simultâneas para Azure Cosmos DB. No SDK do .NET 1.8.0 e posterior, o valor padrão de [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) é 50. Para alterar o valor, você pode definir [Documents. Client. ConnectionPolicy. MaxConnectionLimit](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) para um valor mais alto.

**Ajustar consultas paralelas para coleções particionadas**

O SDK do SQL .NET 1.9.0 e versões posteriores dão suporte a consultas paralelas, que permitem consultar uma coleção particionada em paralelo. Para obter mais informações, consulte [exemplos de código](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs) relacionados ao trabalho com os SDKs. As consultas paralelas são projetadas para fornecer melhor latência de consulta e taxa de transferência do que o equivalente em série. As consultas paralelas fornecem dois parâmetros que você pode ajustar para atender às suas necessidades: 
- `MaxDegreeOfParallelism` controla o número máximo de partições que podem ser consultadas em paralelo. 
- `MaxBufferedItemCount` controla o número de resultados previamente buscados.

**_Grau de ajuste do paralelismo_* _

A consulta paralela funciona consultando várias partições em paralelo. Mas os dados de uma partição individual são buscados em série em relação à consulta. A configuração `MaxDegreeOfParallelism` no [SDK v2](sql-api-sdk-dotnet.md) para o número de partições tem a melhor chance de obter a consulta de melhor desempenho, desde que todas as outras condições do sistema permaneçam as mesmas. Se você não souber o número de partições, poderá definir o grau de paralelismo como um número alto. O sistema escolherá o mínimo (número de partições, entrada fornecida pelo usuário) como o grau de paralelismo.

As consultas paralelas produzem mais benefícios se os dados forem distribuídos uniformemente entre todas as partições em relação à consulta. Se a coleção particionada for particionada para que todos ou a maioria dos dados retornados por uma consulta se concentrasse em algumas partições (uma partição é o pior caso), essas partições causarão um afunilamento do desempenho da consulta.

_*_Ajustando MaxBufferedItemCount_*_
    
A consulta paralela destina-se a buscar previamente resultados enquanto o lote atual de resultados está sendo processado pelo cliente. Essa pré-busca ajuda a melhorar a latência geral de uma consulta. O `MaxBufferedItemCount` parâmetro limita o número de resultados previamente buscados. Defina `MaxBufferedItemCount` como o número esperado de resultados retornados (ou um número mais alto) para permitir que a consulta receba o benefício máximo da busca prévia.

A busca prévia funciona da mesma maneira, independentemente do grau de paralelismo, e há um único buffer para os dados de todas as partições.  

_ *Implementar retirada em intervalos de RetryAfter**

Durante o teste de desempenho, você deve aumentar a carga até que uma pequena taxa de solicitações seja limitada. Se as solicitações forem limitadas, o aplicativo cliente deverá fazer o logoff no limite para o intervalo de repetição especificado pelo servidor. Respeitar a retirada garante que você gaste uma quantidade mínima de tempo esperando entre repetições. 

O suporte à política de repetição está incluído nesses SDKs:
- Versão 1.8.0 e posterior do [SDK do .net para SQL](sql-api-sdk-dotnet.md) e o [SDK do Java para SQL](sql-api-sdk-java.md)
- Versão 1.9.0 e posterior do [ SDK doNode.js para SQL](sql-api-sdk-node.md) e o [SDK do Python para SQL](sql-api-sdk-python.md)
- Todas as versões com suporte dos SDKs do [.NET Core](sql-api-sdk-dotnet-core.md) 

Para obter mais informações, consulte [RetryAfter](/dotnet/api/microsoft.azure.documents.documentclientexception.retryafter).
    
Na versão 1,19 e posterior do SDK do .NET, há um mecanismo para registrar informações de diagnóstico adicionais e solucionar problemas de latência, conforme mostrado no exemplo a seguir. Você pode registrar a cadeia de caracteres de diagnóstico para solicitações que tenham uma latência de leitura mais alta. A cadeia de caracteres de diagnóstico capturada ajudará você a entender quantas vezes você recebeu erros 429 para uma determinada solicitação.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Armazenar em cache os URIs do documento para uma menor latência de leitura**

Armazene em cache os URIs do documento sempre que possível para ter o melhor desempenho de leitura. Você precisa definir a lógica para armazenar em cache a ID do recurso ao criar um recurso. As pesquisas baseadas em IDs de recurso são mais rápidas do que as pesquisas baseadas em nome, portanto, armazenar esses valores em cache melhora o desempenho.

**Ajustar o tamanho da página para os feeds de leitura/consultas para ter o melhor desempenho**

Quando você faz uma leitura em massa de documentos usando a funcionalidade de feed de leitura (por exemplo, `ReadDocumentFeedAsync` ) ou quando você emite uma consulta SQL, os resultados são retornados de uma maneira segmentada se o conjunto de resultados for muito grande. Por padrão, os resultados são retornados em blocos de 100 itens ou 1 MB, o limite que for atingido primeiro.

Para reduzir o número de viagens de ida e volta da rede necessárias para recuperar todos os resultados aplicáveis, você pode aumentar o tamanho da página usando [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para solicitar até 1.000 cabeçalhos. Quando você precisar exibir apenas alguns resultados, por exemplo, se a interface do usuário ou a API do aplicativo retornar apenas 10 resultados por vez, você também poderá diminuir o tamanho da página para 10 para reduzir a taxa de transferência consumida para leituras e consultas.

> [!NOTE] 
> A `maxItemCount` propriedade não deve ser usada apenas para paginação. Seu principal uso é melhorar o desempenho das consultas, reduzindo o número máximo de itens retornados em uma única página.  

Você também pode definir o tamanho da página usando os SDKs de Azure Cosmos DB disponíveis. A propriedade [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet&preserve-view=true) no `FeedOptions` permite que você defina o número máximo de itens a serem retornados na operação de enumeração. Quando `maxItemCount` é definido como-1, o SDK localiza automaticamente o valor ideal, dependendo do tamanho do documento. Por exemplo:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Quando uma consulta é executada, os dados resultantes são enviados em um pacote TCP. Se você especificar um valor muito baixo para `maxItemCount` , o número de viagens necessárias para enviar os dados no pacote TCP será alto, o que afetará o desempenho. Portanto, se você não tiver certeza de qual valor definir para a `maxItemCount` propriedade, é melhor defini-lo como-1 e permitir que o SDK escolha o valor padrão.

**Aumentar o número de threads/tarefas**

Consulte [aumentar o número de threads/tarefas](#increase-threads) na seção de rede deste artigo.

## <a name="indexing-policy"></a>Política de indexação
 
**Excluir caminhos não utilizados da indexação para ter gravações mais rápidas**

A política de indexação de Azure Cosmos DB também permite especificar quais caminhos de documento incluir ou excluir da indexação usando caminhos de indexação (IndexingPolicy. IncludedPaths e IndexingPolicy. ExcludedPaths). Os caminhos de indexação podem melhorar o desempenho de gravação e reduzir o armazenamento de índice para cenários nos quais os padrões de consulta são conhecidos com antecedência. Isso ocorre porque os custos de indexação se correlacionam diretamente com o número de caminhos exclusivos indexados. Por exemplo, este código mostra como excluir uma seção inteira dos documentos (uma subárvore) da indexação usando o curinga "*":

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

Para obter mais informações, consulte [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a><a id="measure-rus"></a> Taxa

**Medir e ajustar para unidades de solicitação menores/segundo uso**

O Azure Cosmos DB oferece um conjunto avançado de operações de banco de dados. Essas operações incluem consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos, todos operando nos documentos em uma coleção de banco de dados. O custo associado a cada uma dessas operações varia dependendo da CPU, da e/s e da memória necessária para concluir a operação. Em vez de pensar e gerenciar recursos de hardware, você pode pensar em uma RU (unidade de solicitação) como uma medida única para os recursos necessários para executar várias operações de banco de dados e atender a uma solicitação de aplicativo.

A taxa de transferência é provisionada com base no número de [unidades de solicitação](request-units.md) definidas para cada contêiner. O consumo de unidade de solicitação é avaliado como uma taxa por segundo. Os aplicativos que excedem a taxa de unidade de solicitação provisionada para seu contêiner são limitados até que a taxa fique abaixo do nível provisionado para o contêiner. Se seu aplicativo exigir um nível mais alto de taxa de transferência, você poderá aumentar sua taxa de transferência Provisionando unidades de solicitação adicionais.

A complexidade de uma consulta afeta quantas unidades de solicitação são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho do conjunto de código de origem influenciam o custo das operações de consulta.

Para medir a sobrecarga de qualquer operação (criar, atualizar ou excluir), inspecione o cabeçalho [x-MS-Request-encharge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (ou a `RequestCharge` Propriedade equivalente no `ResourceResponse\<T>` ou `FeedResponse\<T>` no SDK do .net) para medir o número de unidades de solicitação consumidas pelas operações:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

O encargo de solicitação retornado neste cabeçalho é uma fração de sua taxa de transferência provisionada (ou seja, 2.000 RUs/segundo). Por exemplo, se a consulta anterior retornar documentos de 1.000 1 KB, o custo da operação será 1.000. Portanto, dentro de um segundo, o servidor honra apenas duas solicitações desse tipo antes de limitar a taxa de solicitações posteriores. Para obter mais informações, consulte [unidades de solicitação](request-units.md) e a calculadora de unidade de [solicitação](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Lidar com uma limitação da taxa/taxa de solicitação muito grande**

Quando um cliente tenta exceder a taxa de transferência reservada para uma conta, não há degradação de desempenho no servidor e nenhum uso da capacidade de taxa de transferência além do nível reservado. O servidor encerrará de forma preventiva a solicitação com RequestRateTooLarge (código de status HTTP 429). Ele retornará um cabeçalho [x-MS-Retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) que indica a quantidade de tempo, em milissegundos, que o usuário deve aguardar antes de tentar a solicitação novamente.

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

Os SDKs irão capturar implicitamente essa resposta, respeitarão o cabeçalho server-specified retry-after e repetirão a solicitação. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa será bem-sucedida.

Se você tiver mais de um cliente operando de forma cumulativa consistentemente acima da taxa de solicitação, a contagem de repetição padrão definida atualmente como 9 internamente pelo cliente pode não ser suficiente. Nesse caso, o cliente gera um DocumentClientException com o código de status 429 para o aplicativo. 

Você pode alterar a contagem de repetição padrão definindo o `RetryOptions` na `ConnectionPolicy` instância. Por padrão, a DocumentClientException com o código de status 429 será retornada após uma espera cumulativa de 30 segundos se a solicitação continuar a operar acima da taxa de solicitação. Esse erro retorna mesmo quando a contagem de repetições atual é menor que a contagem máxima de repetições, se o valor atual é o padrão de 9 ou um valor definido pelo usuário.

O comportamento de repetição automatizado ajuda a melhorar a resiliência e a usabilidade para a maioria dos aplicativos. Mas pode não ser o melhor comportamento quando você estiver fazendo benchmarks de desempenho, especialmente quando estiver medindo a latência. A latência observada pelo cliente terá um pico se o teste atingir a limitação do servidor e fizer com que o SDK do cliente repita silenciosamente. Para evitar picos de latência durante os testes de desempenho, meça o custo retornado por cada operação e verifique se as solicitações estão operando abaixo da taxa de solicitação reservada. Para obter mais informações, consulte [unidades de solicitação](request-units.md).

**Para maior taxa de transferência, design para documentos menores**

O encargo da solicitação (ou seja, o custo de processamento da solicitação) de uma determinada operação se correlaciona diretamente com o tamanho do documento. As operações em documentos grandes custam mais do que as operações em documentos pequenos.

## <a name="next-steps"></a>Próximas etapas

Para um aplicativo de exemplo que é usado para avaliar Azure Cosmos DB para cenários de alto desempenho em alguns computadores cliente, consulte [teste de desempenho e escala com Azure Cosmos DB](performance-testing.md).

Para saber mais sobre como projetar seu aplicativo para escala e alto desempenho, consulte [Particionamento e escala no Azure Cosmos DB](partitioning-overview.md).