---
title: Dicas de desempenho do Microsoft Azure Cosmos DB para .NET
description: Saiba mais sobre as opções de configuração do cliente para melhorar o desempenho de banco de dados do Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sngun
ms.openlocfilehash: 21886c11bea6ff09cf97362e06c6d304aaa0d8cc
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250053"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Dicas de desempenho para o Azure Cosmos DB e .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

O Azure Cosmos DB é um banco de dados distribuído rápido e flexível que pode ser dimensionado perfeitamente com garantia de latência e produtividade. Você não precisa fazer alterações importantes de arquitetura nem escrever um código complexo para dimensionar seu banco de dados com o Azure Cosmos DB. Aumentar e diminuir a escala é tão fácil quanto fazer uma única chamada de API. Para saber mais, veja [como provisionar a taxa de transferência do contêiner](how-to-provision-container-throughput.md) ou [como provisionar a taxa de transferência do banco de dados](how-to-provision-database-throughput.md). No entanto, como o Azure Cosmos DB é acessado por meio de chamadas de rede, há otimizações do lado do cliente que você pode fazer para obter o melhor desempenho ao usar o [SDK do SQL .NET](documentdb-sdk-dotnet.md).

Assim, se você estiver se perguntando "Como posso melhorar o desempenho do meu banco de dados?" considere as seguintes opções:

## <a name="networking"></a>Rede
<a id="direct-connection"></a>

1. **Política de Conexão: Use o modo de conexão direta**

    Como um cliente se conecta ao Azure Cosmos DB tem implicações importantes sobre o desempenho, especialmente em termos da latência observada do lado do cliente. Há duas definições principais da configuração disponíveis para configurar a Política de conexão do cliente – o *modo* da conexão e o *protocolo* da conexão.  Os dois modos disponíveis são:

   * Modo Gateway (padrão)
      
     O Modo Gateway é suportado em todas as plataformas SDK e é o padrão configurado. Se seu aplicativo for executado em uma rede corporativa com restrições de firewall rígidas, o Modo Gateway será a melhor opção, já que ele usa a porta HTTPS padrão e um único ponto de extremidade. A compensação de desempenho, no entanto, é que o Modo Gateway envolve um salto de rede adicional sempre que os dados são lidos ou gravados no Azure Cosmos DB. Por isso, o Modo Direto oferece um melhor desempenho devido a menos saltos de rede. Também é recomendável o modo de conexão de gateway ao executar aplicativos em ambientes com número limitado de conexões de soquete, por exemplo, ao usar o Azure Functions ou se estiver em um plano de consumo. 

   * Modo Direto

     O modo direto oferece suporte à conectividade por meio de protocolos TCP e HTTPS. Se você estiver usando a versão mais recente do SDK do .NET, o modo de conectividade direta terá suporte no .NET Standard 2,0 e no .NET Framework. Ao usar o Modo Direto, há duas opções de protocolo disponíveis:

     * TCP
     * HTTPS

     Ao usar o modo de Gateway, o Cosmos DB usa a porta 443 e as portas 10250, 10255 e 10256 ao usar a API do Azure Cosmos DB para MongoDB. A porta 10250 mapeia para a uma instância do MongoDB padrão sem replicação geográfica, e as portas 10255/10256 mapeiam para a instância do MongoDB com funcionalidade de replicação geográfica. Ao usar o TCP no Modo Direto, além das portas do Gateway, você precisa garantir que o intervalo de portas de 10000 a 20000 está aberto, pois o Azure Cosmos DB usa portas TCP dinâmicas. Se essas portas não estiverem abertas e você tentar usar TCP, você receberá o erro 503 Serviço indisponível. A tabela a seguir mostra os modos de conectividade disponíveis para APIs diferentes e o usuário de portas de serviço para cada API:

     |Modo da conexão  |Protocolo com Suporte  |SDKs com suporte  |Porta/serviço de API  |
     |---------|---------|---------|---------|
     |Gateway  |   HTTPS    |  Todos os SDKS    |   SQL (443), Mongo (10250, 10255, 10256), tabela (443), Cassandra (10350), grafo (443)    |
     |Direta    |    HTTPS     |  SDK do .NET e do Java    |   Portas dentro do intervalo de 10.000-20.000    |
     |Direta    |     TCP    |  SDK .NET    | Portas dentro do intervalo de 10.000-20.000 |

     O Cosmos DB oferece um modelo de programação RESTful simples e aberto via HTTPS. Além disso, ele oferece um protocolo TCP eficiente que também é RESTful em seu modelo de comunicação e está disponível por meio do SDK do cliente .NET. Tanto TCP direto quanto HTTPS usam SSL para criptografar tráfego e autenticação inicial. Para ter um melhor desempenho, use o protocolo TCP quando possível.

     O Modo Conectividade é configurado durante a construção da instância do DocumentClient com o parâmetro ConnectionPolicy. Se o Modo Direto for usado, o Protocolo também poderá ser definido no parâmetro ConnectionPolicy.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
     new ConnectionPolicy
     {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
     });
     ```

     Como o TCP tem suporte apenas no Modo Direto, se o Modo Gateway for usado, o protocolo HTTPS será sempre utilizado para se comunicar com o Gateway e o valor do Protocolo na ConnectionPolicy será ignorado.

     ![Ilustração da política de conexão do Azure Cosmos DB](./media/performance-tips/connection-policy.png)

2. **Chamar OpenAsync para evitar a latência de inicialização na primeira solicitação**

    Por padrão, a primeira solicitação tem uma latência maior porque tem que buscar a tabela de roteamento de endereço. Para evitar essa latência de inicialização na primeira solicitação, você deve chamar OpenAsync() uma vez durante a inicialização da seguinte maneira.

        await client.OpenAsync();
   <a id="same-region"></a>
3. **Colocar os clientes na mesma região do Azure para o desempenho**

    Quando possível, coloque aplicativos que chamam o Azure Cosmos DB na mesma região do banco de dados do Azure Cosmos DB. Para obter uma comparação aproximada, as chamadas para o Azure Cosmos DB na mesma região são concluídas de 1 a 2 ms, mas a latência entre a Costa Leste e a Oeste dos EUA é maior que 50 ms. Provavelmente, essa latência pode variar entre as solicitações dependendo da rota seguida pela solicitação conforme ela passa do cliente para o limite de datacenter do Azure. A menor latência possível é alcançada garantindo que o aplicativo de chamada está localizado na mesma região do Azure do ponto de extremidade do Azure Cosmos DB provisionado. Para obter uma lista de regiões disponíveis, consulte [Regiões do Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustração da política de conexão do Azure Cosmos DB](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **Aumentar o número de threads/tarefas**

    Como as chamadas ao Azure Cosmos DB são feitas pela rede, talvez seja necessário variar o grau de paralelismo das solicitações, de forma que o aplicativo cliente aguarde um tempo mínimo entre as solicitações. Por exemplo, se você estiver usando a [Biblioteca de Paralelismo de 100s de Tarefas](https://msdn.microsoft.com//library/dd460717.aspx) do .NET, crie centenas de tarefas de leitura ou gravação no Azure Cosmos DB.

5. **Habilitar rede acelerada**

   Para reduzir a latência e a tremulação da CPU, recomendamos que as máquinas virtuais do cliente sejam habilitadas para rede acelerada. Consulte a artigos [criar uma máquina virtual do Windows com rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) ou [criar uma máquina virtual Linux com rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md) para habilitar a rede acelerada.


## <a name="sdk-usage"></a>Uso do SDK
1. **Instalar o SDK mais recente**

    Os SDKs do Azure Cosmos DB estão constantemente sendo aprimorados para fornecer o melhor desempenho. Consulte as páginas do [SDK do Azure Cosmos DB](documentdb-sdk-dotnet.md) para determinar o SDK mais recente e examinar as melhorias.
2. **Usar um cliente do Azure Cosmos DB singleton para obter o tempo de vida do aplicativo**

    Cada instância do DocumentClient tem um thread-safe e realiza um gerenciamento de conexão eficiente e o cache de endereço ao operar no Modo Direto. Para permitir o gerenciamento de conexão eficiente e o melhor desempenho por DocumentClient, é recomendável usar uma única instância de DocumentClient por AppDomain durante a vida útil do aplicativo.

   <a id="max-connection"></a>
3. **Aumentar System.Net MaxConnections por host usando o modo de Gateway**

    As solicitações do Azure Cosmos DB são feitas por HTTPS/REST durante o uso do modo Gateway e estão sujeitas ao limite de conexão padrão por nome do host ou endereço IP. Talvez seja necessário que você defina o MaxConnections para um valor mais alto (100-1000) para que a biblioteca de cliente possa utilizar várias conexões simultâneas ao banco de dados do Azure Cosmos DB. No SDK do .NET 1.8.0 e posterior, o valor padrão para [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) é 50 e para alterar o valor, você pode definir o [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) para um valor mais alto.   
4. **Ajustar consultas paralelas para coleções particionadas**

     A versão 1.9.0 e superiores do SDK do SQL .NET oferecem suporte a consultas paralelas, o que permite a consulta a uma coleção particionada em paralelo . Para obter mais informações, consulte [exemplos de código](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) relacionados ao trabalho com os SDKs. Consultas paralelas são projetadas para melhorar a latência da consulta e a produtividade em relação à contraparte serial. Consultas paralelas fornecem dois parâmetros que os usuários podem ajustar para atender aos próprios requisitos, (a) MaxDegreeOfParallelism: para controlar o número máximo de partições que podem ser consultadas em paralelo e (b) MaxBufferedItemCount: para controlar o número de resultados de pré-obtidos.

    (a) ***Ajuste de MaxDegreeOfParallelism\:*** a consulta paralela funciona consultando várias partições em paralelo. No entanto, os dados de uma coleta particionada individual são buscados em série com relação à consulta. Então, definir MaxDegreeOfParallelism como o número de partições representa o máximo de chance de conseguir uma consulta com o melhor desempenho, desde que todas as outras condições do sistema permaneçam as mesmas. Se você não souber o número de partições, defina MaxDegreeOfParallelism como um número alto, e o sistema escolherá o mínimo (número de partições, entrada fornecida pelo usuário) como o MaxDegreeOfParallelism.

    É importante observar que as consultas paralelas produzirão os melhores benefícios se os dados forem distribuídos uniformemente em todas as partições com relação à consulta. Se a coleção particionada for particionada de uma forma que todos ou a maioria dos dados retornados por uma consulta ficarem concentrados em algumas partições (uma partição, na pior das hipóteses), o desempenho da consulta seria um gargalo dessas partições.

    (b) ***Ajuste MaxBufferedItemCount\:*** a consulta paralela destina-se a buscar previamente resultados enquanto o lote atual de resultados está sendo processado pelo cliente. A busca prévia ajuda a melhorar a latência geral de uma consulta. MaxBufferedItemCount é o parâmetro para limitar o número de resultados pré-obtidos. Configurar MaxBufferedItemCount para o número esperado de resultados retornados (ou um número mais alto) permite que a consulta receba o benefício máximo da busca prévia.

    A busca prévia funciona da mesma forma independentemente do MaxDegreeOfParallelism, e há um único buffer para os dados de todas as partições.  
5. **Ativar o GC no lado do servidor**

    A redução da frequência da coleta de lixo pode ajudar em alguns casos. No .NET, defina [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) como true.
6. **Implementar a retirada em intervalos de RetryAfter**

    Durante o teste de desempenho, você deve aumentar a carga até que uma pequena taxa de solicitações seja restringida. Se restringida, o aplicativo cliente deve retirar a limitação no intervalo de nova tentativa do servidor especificado. Respeitar a retirada garante que você perca o mínimo de tempo de espera entre as tentativas. O suporte da política de repetição está incluído na Versão 1.8.0 e posteriores do SQL [.NET](sql-api-sdk-dotnet.md) e [Java](sql-api-sdk-java.md), na versão 1.9.0 e posteriores do [Node.js](sql-api-sdk-node.md) e [Python](sql-api-sdk-python.md) e em todas as versões com suporte dos SDKs do [.NET Core](sql-api-sdk-dotnet-core.md). Para obter mais informações, [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    Com a versão 1.19 e posterior do SDK do .NET, há um mecanismo para registrar informações de diagnóstico adicionais e solucionar problemas de latência, conforme mostrado no exemplo a seguir. Você pode registrar a cadeia de caracteres de diagnóstico para solicitações que tenham uma latência de leitura mais alta. A cadeia de caracteres de diagnóstico capturada ajudará você a entender o número de vezes que você observou 429s para uma determinada solicitação.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
7. **Escalar horizontalmente sua carga de trabalho do cliente**

    Se você estiver testando em altos níveis da taxa de transferência (&gt;50.000 RU/s), o aplicativo cliente poderá fazer um gargalo devido à limitação do computador na utilização da CPU ou da Rede. Se você chegar a este ponto, poderá continuar aumentando a conta do Azure Cosmos DB ainda mais distribuindo seus aplicativos cliente entre vários servidores.
8. **Armazenar em cache os URIs do documento para uma menor latência de leitura**

    Armazene em cache os URIs do documento sempre que possível para ter o melhor desempenho de leitura. Você precisa definir a lógica para armazenar em cache o resourceid quando você cria o recurso. ResourceID com base em pesquisas são mais rápido que as pesquisas de nome com base, para que esses valores de cache melhorem o desempenho. 

   <a id="tune-page-size"></a>
1. **Ajustar o tamanho da página para os feeds de leitura/consultas para ter o melhor desempenho**

   Ao executar uma grande quantidade de leitura dos documentos utilizando a funcionalidade do feed de leitura (por exemplo, ReadDocumentFeedAsync) ou ao enviar uma consulta SQL do DocumentDB, os resultados são retornados de uma maneira segmentada se o conjunto de resultados for muito grande. Por padrão, os resultados são retornados em blocos de 100 itens ou 1 MB, o limite que for atingido primeiro.

   Para reduzir o número de idas e vindas na rede necessárias para recuperar todos os resultados aplicáveis, você pode aumentar o tamanho da página para até 1000 usando o cabeçalho de solicitação [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers). Nos casos em que você precisa exibir apenas alguns resultados, por exemplo, se a interface do usuário ou a API do aplicativo retornar apenas 10 resultados de uma vez, também será possível diminuir o tamanho da página para 10 para reduzir a taxa de transferência consumida pelas leituras e consultas.

   > [!NOTE] 
   > A propriedade maxItemCount não deve ser usada apenas para fins de paginação. É o principal uso para melhorar o desempenho das consultas, reduzindo o número máximo de itens retornados em uma única página.  

   Você também pode definir o tamanho da página usando os SDKs de Azure Cosmos DB disponíveis. A propriedade [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) no feedoptions permite que você defina o número máximo de itens a serem retornados na operação enmuration. Quando `maxItemCount` é definido como-1, o SDK localiza automaticamente o valor ideal dependendo do tamanho do documento. Por exemplo:
    
   ```csharp
    IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
   ```
    
   Quando uma consulta é executada, os dados resultantes são enviados em um pacote TCP. Se você especificar um valor muito baixo `maxItemCount`para, o número de corridas necessárias para enviar os dados no pacote TCP será alto, o que afetará o desempenho. Portanto, se você não tiver certeza de qual valor definir `maxItemCount` para propriedade, é melhor defini-lo como-1 e permitir que o SDK escolha o valor padrão. 

10. **Aumentar o número de threads/tarefas**

    Consulte [Aumentar o número de threads/tarefas](#increase-threads) na seção Rede.

11. **Usar o processamento do host de 64 bits**

    O SDK do SQL funciona em um processo de host de 32 bits quando você usa a versão 1.11.4 ou posterior do SDK do SQL .NET. No entanto, se você usa consultas entre partições, recomenda-se o processamento de host de 64 bits para melhorar o desempenho. Os seguintes tipos de aplicativos têm o processo de host de 32 bits como o padrão, portanto para alterá-los para 64 bits, siga estas etapas com base no tipo de seu aplicativo:

    - Para aplicativos executáveis, isso pode ser feito desmarcando a opção **Preferir 32 bits** na janela **Propriedades do Projeto**, na guia **Compilar**.

    - Para projetos de teste com base em VSTest, isso pode ser feito selecionando **teste**->**configurações de teste**->**padrão arquitetura de processador X64**, do **Visual Studio Test** opção de menu.

    - Para aplicativos Web ASP.NET implantados localmente, isso pode ser feito verificando o **usar a versão de 64 bits do IIS Express para sites da web e projetos**, em **ferramentas**->**opções**->**projetos e soluções**->**projetos da Web**.

    - Para aplicativos Web ASP.NET implantados no Azure, isso pode ser feito escolhendo o **plataforma de 64 bits** no **configurações do aplicativo** no portal do Azure.

## <a name="indexing-policy"></a>Política de indexação
 
1. **Excluir caminhos não utilizados da indexação para ter gravações mais rápidas**

    A política de indexação do Cosmos DB também permite que você especifique quais caminhos de documento serão incluídos ou excluídos da indexação, aproveitando os Caminhos de Indexação (IndexingPolicy.IncludedPaths e IndexingPolicy.ExcludedPaths). O uso dos caminhos de indexação pode oferecer um melhor desempenho de gravação e menor armazenamento de índices para os cenários nos quais os padrões da consulta são conhecidos com antecedência, pois os custos da indexação estão correlacionados diretamente com o número de caminhos exclusivos indexados.  Por exemplo, o código a seguir mostra como excluir uma seção inteira dos documentos (uma subárvore) da indexação usando o curinga "*".

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Para obter mais informações, consulte [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Produtividade
<a id="measure-rus"></a>

1. **Medir e ajustar para o uso mais baixo de unidades/segundo da solicitação**

    O Azure Cosmos DB oferece um conjunto avançado de operações do banco de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos – todos operando nos documentos em uma coleção de banco de dados. O custo associado a cada uma dessas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar em e gerenciar recursos de hardware, você pode pensar em uma RU (unidade de solicitação) como uma medida única para os recursos necessários para realizar várias operações de bancos de dados e atender a uma solicitação do aplicativo.

    A taxa de transferência é provisionada com base no número de [unidades de solicitação](request-units.md) definidas para cada contêiner. O consumo da unidade de solicitação é avaliado em termos de taxa por segundo. Os aplicativos que excedem a taxa das unidades de solicitação provisionada para seu contêiner serão limitados até que a taxa fique abaixo do nível reservado para o contêiner. Caso o aplicativo exija um nível mais alto de taxa de transferência, é possível aumentar a taxa de transferência provisionando unidades de solicitação adicionais. 

    A complexidade de uma consulta afeta a quantidade de Unidades de Solicitação que são consumidas para uma operação. O número de predicados, natureza dos predicados, número de UDFs e tamanho do conjunto de dados de origem influenciam o custo das operações de consulta.

    Para medir a sobrecarga de qualquer operação (criar, atualizar ou excluir), inspecione o cabeçalho [x-MS-Request-encharge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (ou a propriedade RequestCharge equivalente em ResourceResponse\<T > ou FeedResponse\<t > no SDK do .net) para Meça o número de unidades de solicitação consumidas por essas operações.

    ```csharp
    // Measure the performance (request units) of writes
    ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
    Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
    // Measure the performance (request units) of queries
    IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
    while (queryable.HasMoreResults)
         {
              FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
              Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
         }
    ```             

    A carga de solicitação retornada nesse cabeçalho é uma fração de sua taxa de transferência provisionada (ou seja, 2000 RUs/segundo). Por exemplo, se a consulta anterior retornar 1.000 documentos de 1 KB, o custo da operação será 1.000. Assim, em um segundo, o servidor mantém apenas duas dessas solicitações antes de limitar as solicitações subsequentes. Para saber mais, consulte [Unidades de solicitação](request-units.md) e a [calculadora das unidades de solicitação](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Lidar com uma limitação da taxa/taxa de solicitação muito grande**

    Quando um cliente tentar exceder a taxa de transferência reservada para uma conta, não haverá nenhuma degradação de desempenho no servidor e nenhum uso da capacidade da taxa além do nível reservado. O servidor encerrará antecipadamente a solicitação com RequestRateTooLarge (código de status HTTP 429) e retornará o cabeçalho [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) indicando a quantidade de tempo, em milissegundos, que o usuário deve aguardar antes de tentar novamente a solicitação.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Os SDKs irão capturar implicitamente essa resposta, respeitarão o cabeçalho server-specified retry-after e repetirão a solicitação. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa será bem-sucedida.

    Se você tiver mais de um cliente operando cumulativamente de como consistente acima da taxa de solicitação, a contagem de repetição padrão atualmente definida para 9 internamente pelo cliente poderá não ser suficiente. Nesse caso, o cliente irá gerar uma DocumentClientException com o código de status 429 para o aplicativo. A contagem de repetição padrão pode ser alterada definindo RetryOptions na instância de ConnectionPolicy. Por padrão, a DocumentClientException com o código de status 429 será retornada após uma espera cumulativa de 30 segundos se a solicitação continuar a operar acima da taxa de solicitação. Isso ocorre mesmo quando a contagem de repetição atual é menor que a contagem de repetição máxima, seja o padrão 9 seja um valor definido pelo usuário.

    Embora o comportamento de repetição automática ajude a melhorar a resiliência e a utilidade da maioria dos aplicativos, ela pode entrar em conflito ao fazer comparações de desempenho, especialmente ao medir a latência.  A latência observada pelo cliente terá um pico se o teste atingir a limitação do servidor e fizer com que o SDK do cliente repita silenciosamente. Para evitar picos de latência durante os testes de desempenho, meça o custo retornado por cada operação e verifique se as solicitações estão operando abaixo da taxa de solicitação reservada. Para saber mais, consulte [Unidades de solicitação](request-units.md).
3. **Design de documentos menores para uma maior taxa de transferência**

    O custo da solicitação (ou seja, o custo de processamento da solicitação) de uma determinada operação está correlacionado diretamente com o tamanho do documento. As operações em documentos grandes custam mais que as operações de documentos pequenos.

## <a name="next-steps"></a>Próximas etapas
Para obter um aplicativo de exemplo usado para avaliar o Azure Cosmos DB para cenários de alto desempenho em alguns computadores cliente, consulte [Teste de desempenho e escala com o Azure Cosmos DB](performance-testing.md).

Além disso, para saber mais sobre como projetar seu aplicativo para escala e alto desempenho, consulte [Particionamento e escala no Azure Cosmos DB](partition-data.md).
