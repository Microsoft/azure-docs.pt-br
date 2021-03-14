---
title: Azure Cosmos DB dicas de desempenho para o SDK do .NET v3
description: Aprenda as opções de configuração do cliente para ajudar a melhorar o desempenho do SDK do .NET v3 Azure Cosmos DB.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: jawilley
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: 06fb087744ff4ecd96bee7a26e4a796e87866322
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433651"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Dicas de desempenho para o Azure Cosmos DB e .NET
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK v3 do .NET](performance-tips-dotnet-sdk-v3-sql.md)
> * [SDK do .NET v2](performance-tips.md)
> * [SDK do Java v4](performance-tips-java-sdk-v4-sql.md)
> * [SDK do Java Assíncrono v2](performance-tips-async-java.md)
> * [SDK do Java Síncrono v2](performance-tips-java.md)

O Azure Cosmos DB é um banco de dados distribuído rápido e flexível que é dimensionado de forma direta com a latência garantida e os níveis de taxa de transferência. Você não precisa fazer grandes alterações na arquitetura nem escrever código complexo para dimensionar seu banco de dados com Azure Cosmos DB. Aumentar e diminuir a escala é tão fácil quanto fazer uma única chamada de API. Para saber mais, confira [provisionar taxa de transferência de contêiner](how-to-provision-container-throughput.md) ou [provisionar taxa de transferência](how-to-provision-database-throughput.md) 

Como Azure Cosmos DB é acessado por meio de chamadas de rede, você pode fazer otimizações do lado do cliente para obter o máximo de desempenho ao usar o [SDK do .net do SQL](sql-api-sdk-dotnet-standard.md).

Se você estiver tentando melhorar o desempenho do banco de dados, considere as opções apresentadas nas seções a seguir.

## <a name="hosting-recommendations"></a>Recomendações de hospedagem

**Para cargas de trabalho com uso intensivo de consulta, use o Windows 64-bit em vez do processamento de host Linux ou Windows de 32 bits**

Recomendamos o processamento de host do Windows de 64 bits para melhorar o desempenho. O SDK do SQL inclui um ServiceInterop.dll nativo para analisar e otimizar as consultas localmente. O ServiceInterop.dll tem suporte apenas na plataforma Windows x64. 

Para Linux e outras plataformas sem suporte em que o ServiceInterop.dll não está disponível, é feita uma chamada de rede adicional ao gateway para obter a consulta otimizada. 

Os quatro tipos de aplicativos listados aqui usam o processamento de host de 32 bits por padrão. Para alterar o processamento de host para o processamento de 64 bits para o tipo de aplicativo, faça o seguinte:

- **Para aplicativos executáveis**: na janela de **Propriedades do projeto** , no painel de **compilação** , defina o [destino da plataforma](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) como **x64**.

- **Para projetos de teste baseados em VSTest**: no menu de **teste** do Visual Studio **, selecione**  >  **configurações de teste** de teste e, em seguida, defina a **arquitetura de processador padrão** como **x64**.

- **Para aplicativos Web ASP.net implantados localmente**: selecione **ferramentas**  >  **Opções**  >  **projetos e soluções**  >  **projetos da Web** e, em seguida, selecione **usar a versão de 64 bits do IIS Express para sites e projetos**.

- **Para aplicativos web ASP.net implantados no Azure**: no portal do Azure, em **configurações do aplicativo**, selecione a plataforma de **64 bits** .

> [!NOTE] 
> Por padrão, novos projetos do Visual Studio são definidos para **qualquer CPU**. Recomendamos que você defina seu projeto como **x64** para que ele não mude para **x86**. Um projeto que é definido como **qualquer CPU** pode mudar facilmente para **x86** se uma dependência somente x86 for adicionada.<br/>
> O arquivo de ServiceInterop.dll precisa estar na pasta da qual a DLL do SDK está sendo executada. Isso deve ser uma preocupação apenas se você copiar DLLs manualmente ou se tiver sistemas personalizados de compilação ou implantação.
    
**Ativar a coleta de lixo do lado do servidor**

Reduzir a frequência de coleta de lixo pode ajudar em alguns casos. No .NET, defina [gcServer](/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) como `true` .

**Escalar horizontalmente a carga de trabalho do cliente**

Se você estiver testando em níveis de taxa de transferência alta ou em taxas maiores que 50.000 unidades de solicitação por segundo (RU/s), o aplicativo cliente poderá se tornar um afunilamento de carga de trabalho. Isso ocorre porque o computador pode se limitar à utilização da CPU ou da rede. Se você chegar a este ponto, poderá continuar aumentando a conta do Azure Cosmos DB ainda mais distribuindo seus aplicativos cliente entre vários servidores.

> [!NOTE] 
> Alto uso da CPU pode causar maior latência e exceções de tempo limite de solicitação.

## <a name="networking"></a>Rede
<a id="direct-connection"></a>

**Política de conexão: usar o modo de conexão direta**

O modo de conexão padrão do SDK do .NET v3 é direto. Você configura o modo de conexão ao criar a `CosmosClient` instância no `CosmosClientOptions` .  Para saber mais sobre as diferentes opções de conectividade, consulte o artigo [modos de conectividade](sql-sdk-connection-modes.md) .

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

**Esgotamento de porta efêmera**

Se você vir um alto volume de conexão ou uso de porta alta em suas instâncias, primeiro verifique se suas instâncias de cliente são singletons. Em outras palavras, as instâncias de cliente devem ser exclusivas durante o tempo de vida do aplicativo.

Quando ele está em execução no protocolo TCP, o cliente otimiza a latência usando as conexões de vida longa. Isso está em contraste com o protocolo HTTPS, que encerra as conexões após dois minutos de inatividade.

Em cenários em que você tem acesso esparso, e se você notar uma contagem de conexões maior quando comparado ao acesso do modo de gateway, você pode:

* Configure a propriedade [CosmosClientOptions. PortReuseMode](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) para `PrivatePortPool` (em vigor com as versões do Framework 4.6.1 e posteriores e as versões 2,0 e posteriores do .NET Core). Essa propriedade permite que o SDK use um pequeno pool de portas efêmeras para vários pontos de extremidade de destino Azure Cosmos DB.
* Configure a propriedade [CosmosClientOptions. IdleConnectionTimeout](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) como maior ou igual a 10 minutos. Os valores recomendados são de 20 minutos a 24 horas.

<a id="same-region"></a>

**Para desempenho, colocar clientes na mesma região do Azure**

Quando possível, coloque todos os aplicativos que chamam Azure Cosmos DB na mesma região que o banco de dados Azure Cosmos DB. Aqui está uma comparação aproximada: chamadas para Azure Cosmos DB na mesma região terminam dentro de 1 milissegundo (MS) a 2 ms, mas a latência entre a costa oeste e a leste dos EUA é mais de 50 ms. Essa latência pode variar de solicitação a solicitação, dependendo da rota realizada pela solicitação à medida que ela passa do cliente para o limite de datacenter do Azure. 

Você pode obter a menor latência possível garantindo que o aplicativo de chamada esteja localizado na mesma região do Azure que o ponto de extremidade de Azure Cosmos DB provisionado. Para obter uma lista de regiões disponíveis, consulte [Regiões do Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Colocar clientes na mesma região." border="false":::

   <a id="increase-threads"></a>

**Aumentar o número de threads/tarefas**

Como as chamadas para Azure Cosmos DB são feitas pela rede, talvez seja necessário variar o grau de simultaneidade de suas solicitações para que o aplicativo cliente aguarde o tempo mínimo de espera entre as solicitações. Por exemplo, se você estiver usando a [biblioteca paralela de tarefas](/dotnet/standard/parallel-programming/task-parallel-library-tpl)do .net, crie na ordem de centenas de tarefas que lêem ou gravam em Azure Cosmos DB.

**Habilitar rede acelerada**
 
Para reduzir a latência e a tremulação da CPU, recomendamos que você habilite a rede acelerada nas máquinas virtuais do cliente. Para obter mais informações, consulte [criar uma máquina virtual do Windows com rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) ou [criar uma máquina virtual Linux com rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>Uso do SDK

**Instalar o SDK mais recente**

Os SDKs do Azure Cosmos DB estão constantemente sendo aprimorados para fornecer o melhor desempenho. Para determinar o SDK mais recente e revisar as melhorias, consulte [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md).

**Usar APIs de fluxo**

O [SDK do .net v3](https://github.com/Azure/azure-cosmos-dotnet-v3) contém APIs de fluxo que podem receber e retornar dados sem serialização. 

Os aplicativos de camada intermediária que não consomem respostas diretamente do SDK, mas retransmitem-los para outras camadas de aplicativo podem se beneficiar das APIs de fluxo. Para obter exemplos de manipulação de fluxo, consulte os exemplos de [Gerenciamento de item](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) .

**Usar um cliente do Azure Cosmos DB singleton para obter o tempo de vida do aplicativo**

Cada `CosmosClient` instância é thread-safe e executa o gerenciamento de conexão e o cache de endereços eficientes quando opera no modo direto. Para permitir o gerenciamento de conexão eficiente e melhor desempenho do cliente SDK, recomendamos que você use uma única instância por `AppDomain` tempo de vida do aplicativo.

Quando você estiver trabalhando no Azure Functions, as instâncias também devem seguir as [diretrizes](../azure-functions/manage-connections.md#static-clients) existentes e manter uma única instância.

<a id="max-connection"></a>

**Desabilitar a resposta de conteúdo em operações de gravação**

Para cargas de trabalho que têm cargas de criação pesadas, defina a `EnableContentResponseOnWrite` opção de solicitação como `false` . O serviço não retornará mais o recurso criado ou atualizado para o SDK. Normalmente, como o aplicativo tem o objeto que está sendo criado, ele não precisa que o serviço o retorne. Os valores de cabeçalho ainda são acessíveis, como um encargo de solicitação. Desabilitar a resposta de conteúdo pode ajudar a melhorar o desempenho, pois o SDK não precisa mais alocar memória ou serializar o corpo da resposta. Ele também reduz o uso de largura de banda da rede para ajudar ainda mais o desempenho.  

```csharp
ItemRequestOptions requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**Habilitar Bulk para otimizar para taxa de transferência em vez de latência**

Habilite o *Bulk* para cenários em que a carga de trabalho requer uma grande quantidade de taxa de transferência e a latência não é tão importante. Para obter mais informações sobre como habilitar o recurso em massa e saber em quais cenários ele deve ser usado, consulte [introdução ao suporte em massa](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk).

**Aumentar o System.Net MaxConnections por host ao usar o modo de gateway**

Azure Cosmos DB solicitações são feitas por HTTPS/REST quando você usa o modo de gateway. Eles estão sujeitos ao limite de conexão padrão por nome de host ou endereço IP. Talvez seja necessário definir `MaxConnections` como um valor mais alto (de 100 a 1.000) para que a biblioteca de cliente possa usar várias conexões simultâneas para Azure Cosmos DB. No SDK do .NET 1.8.0 e posterior, o valor padrão de [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) é 50. Para alterar o valor, você pode definir [`Documents.Client.ConnectionPolicy.MaxConnectionLimit`](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) como um valor mais alto.

**Ajustar consultas paralelas para coleções particionadas**

O SDK do SQL .NET dá suporte a consultas paralelas, que permitem consultar um contêiner particionado em paralelo. Para obter mais informações, consulte [exemplos de código](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs) relacionados ao trabalho com os SDKs. As consultas paralelas são projetadas para fornecer melhor latência de consulta e taxa de transferência do que o equivalente em série. 

As consultas paralelas fornecem dois parâmetros que você pode ajustar para atender às suas necessidades: 

- **MaxConcurrency**: controla o número máximo de partições que podem ser consultadas em paralelo.

   A consulta paralela funciona consultando várias partições em paralelo. Mas os dados de uma partição individual são buscados em série em relação à consulta. A configuração `MaxConcurrency` no [SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3) para o número de partições tem a melhor chance de obter a consulta de melhor desempenho, desde que todas as outras condições do sistema permaneçam as mesmas. Se você não souber o número de partições, poderá definir o grau de paralelismo como um número alto. O sistema escolherá o mínimo (número de partições, entrada fornecida pelo usuário) como o grau de paralelismo.

    As consultas paralelas produzem mais benefícios se os dados forem distribuídos uniformemente entre todas as partições em relação à consulta. Se a coleção particionada for particionada para que todos ou a maioria dos dados retornados por uma consulta se concentrasse em algumas partições (uma partição é o pior caso), essas partições causarão um afunilamento do desempenho da consulta.
   
- **MaxBufferedItemCount**: controla o número de resultados previamente buscados.

   A consulta paralela destina-se a buscar previamente resultados enquanto o lote atual de resultados está sendo processado pelo cliente. Essa pré-busca ajuda a melhorar a latência geral de uma consulta. O `MaxBufferedItemCount` parâmetro limita o número de resultados previamente buscados. Defina `MaxBufferedItemCount` como o número esperado de resultados retornados (ou um número mais alto) para permitir que a consulta receba o benefício máximo da busca prévia.

   A busca prévia funciona da mesma maneira, independentemente do grau de paralelismo, e há um único buffer para os dados de todas as partições.  

**Implementar a retirada em intervalos de RetryAfter**

Durante o teste de desempenho, você deve aumentar a carga até que uma pequena taxa de solicitações seja limitada. Se as solicitações forem limitadas, o aplicativo cliente deverá retirar a limitação do intervalo de repetição especificado pelo servidor. Respeitar a retirada ajuda a garantir que você gaste um período mínimo de tempo esperando entre as tentativas. 

Para obter mais informações, consulte [RetryAfter](/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter#Microsoft_Azure_Cosmos_CosmosException_RetryAfter).
    
Há um mecanismo para registrar em log informações adicionais de diagnóstico e solucionar problemas de latência, conforme mostrado no exemplo a seguir. Você pode registrar a cadeia de caracteres de diagnóstico para solicitações que têm uma latência de leitura mais alta. A cadeia de caracteres capturada de diagnóstico ajudará você a entender quantas vezes você recebeu um erro *429* para uma determinada solicitação.

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**Aumentar o número de threads/tarefas**

Consulte [aumentar o número de threads/tarefas](#increase-threads) na seção de rede deste artigo.

## <a name="indexing-policy"></a>Política de indexação
 
**Excluir caminhos não utilizados da indexação para ter gravações mais rápidas**

A política de indexação de Azure Cosmos DB também permite que você especifique quais caminhos de documento incluir ou excluir da indexação usando caminhos de indexação (IndexingPolicy. IncludedPaths e IndexingPolicy. ExcludedPaths). 

A indexação somente dos caminhos de que você precisa pode melhorar o desempenho de gravação, reduzir os encargos de RU em operações de gravação e reduzir o armazenamento de índice para cenários nos quais os padrões de consulta são conhecidos com antecedência. Isso ocorre porque os custos de indexação se correlacionam diretamente com o número de caminhos exclusivos indexados. Por exemplo, o código a seguir mostra como excluir uma seção inteira dos documentos (uma subárvore) da indexação usando o curinga "*":

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

Para obter mais informações, consulte [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Produtividade
<a id="measure-rus"></a>

**Medir e ajustar para uso inferior de RU/s**

O Azure Cosmos DB oferece um conjunto avançado de operações de banco de dados. Essas operações incluem consultas relacionais e hierárquicas com arquivos UDF (formato de disco universal), procedimentos armazenados e gatilhos, todos operando nos documentos em uma coleção de banco de dados. 

Os custos associados a cada uma dessas operações variam dependendo da CPU, da e/s e da memória necessários para concluir a operação. Em vez de pensar e gerenciar recursos de hardware, você pode pensar em uma unidade de solicitação como uma medida única para os recursos necessários para executar várias operações de banco de dados e atender a uma solicitação de aplicativo.

A taxa de transferência é provisionada com base no número de [unidades de solicitação](request-units.md) definidas para cada contêiner. O consumo de unidade de solicitação é avaliado como uma taxa de unidades por segundo. Os aplicativos que excedem a taxa de unidade de solicitação provisionada para seu contêiner são limitados até que a taxa fique abaixo do nível provisionado para o contêiner. Se seu aplicativo exigir um nível mais alto de taxa de transferência, você poderá aumentar sua taxa de transferência Provisionando unidades de solicitação adicionais.

A complexidade de uma consulta afeta quantas unidades de solicitação são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de arquivos UDF e o tamanho do conjunto de código de origem influenciam o custo das operações de consulta.

Para medir a sobrecarga de qualquer operação (criar, atualizar ou excluir), inspecione o cabeçalho [x-MS-Request-encharge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (ou a `RequestCharge` Propriedade equivalente no `ResourceResponse\<T>` ou `FeedResponse\<T>` no SDK do .net) para medir o número de unidades de solicitação consumidas pelas operações:

```csharp
// Measure the performance (Request Units) of writes
ItemResponse<Book> response = await container.CreateItemAsync<Book>(myBook, new PartitionKey(myBook.PkValue));
Console.WriteLine("Insert of item consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
FeedIterator<Book> queryable = container.GetItemQueryIterator<ToDoActivity>(queryString);
while (queryable.HasMoreResults)
    {
        FeedResponse<Book> queryResponse = await queryable.ExecuteNextAsync<Book>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

O encargo de solicitação retornado nesse cabeçalho é uma fração de sua taxa de transferência provisionada (ou seja, 2.000 RU/s). Por exemplo, se a consulta anterior retornar documentos de 1.000 1 KB, o custo da operação será 1.000. Portanto, dentro de um segundo, o servidor honra apenas duas solicitações desse tipo antes de limitar a taxa de solicitações posteriores. Para obter mais informações, consulte [unidades de solicitação](request-units.md) e a calculadora de unidade de [solicitação](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Lidar com uma limitação da taxa/taxa de solicitação muito grande**

Quando um cliente tenta exceder a taxa de transferência reservada para uma conta, não há degradação de desempenho no servidor e nenhum uso da capacidade de taxa de transferência além do nível reservado. O servidor encerra preventivamente a solicitação com RequestRateTooLarge (código de status HTTP 429). Ele retorna um cabeçalho [x-MS-Retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) que indica a quantidade de tempo, em milissegundos, que o usuário deve aguardar antes de tentar a solicitação novamente.

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

Os SDKs irão capturar implicitamente essa resposta, respeitarão o cabeçalho server-specified retry-after e repetirão a solicitação. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa será bem-sucedida.

Se você tiver mais de um cliente operando de forma cumulativa consistentemente acima da taxa de solicitação, a contagem de repetição padrão atualmente definida como 9 internamente pelo cliente pode não ser suficiente. Nesse caso, o cliente gera um CosmosException com o código de status 429 para o aplicativo. 

Você pode alterar a contagem de repetição padrão definindo o `RetryOptions` na `CosmosClientOptions` instância. Por padrão, o CosmosException com o código de status 429 é retornado após um tempo de espera cumulativo de 30 segundos se a solicitação continuar a operar acima da taxa de solicitação. Esse erro é retornado mesmo quando a contagem de repetições atual é menor que a contagem máxima de repetições, se o valor atual é o padrão de 9 ou um valor definido pelo usuário.

O comportamento de repetição automatizado ajuda a melhorar a resiliência e a usabilidade para a maioria dos aplicativos. Mas pode não ser o melhor comportamento quando você estiver fazendo benchmarks de desempenho, especialmente quando estiver medindo a latência. A latência observada pelo cliente terá um pico se o teste atingir a limitação do servidor e fizer com que o SDK do cliente repita silenciosamente. Para evitar picos de latência durante os experimentos de desempenho, meça a cobrança retornada por cada operação e verifique se as solicitações estão operando abaixo da taxa de solicitação reservada. 

Para obter mais informações, consulte [unidades de solicitação](request-units.md).

**Para maior taxa de transferência, design para documentos menores**

O encargo da solicitação (ou seja, o custo de processamento da solicitação) de uma operação especificada se correlaciona diretamente com o tamanho do documento. As operações em documentos grandes custam mais do que as operações em documentos pequenos.

## <a name="next-steps"></a>Próximas etapas
Para um aplicativo de exemplo que é usado para avaliar Azure Cosmos DB para cenários de alto desempenho em alguns computadores cliente, consulte [teste de desempenho e escala com Azure Cosmos DB](performance-testing.md).

Para saber mais sobre como projetar seu aplicativo para escala e alto desempenho, consulte [Particionamento e escala no Azure Cosmos DB](partitioning-overview.md).