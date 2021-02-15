---
title: Dicas de desempenho para o SDK do Java v4 do Azure Cosmos DB
description: Saiba mais sobre as opções de configuração do cliente para melhorar o desempenho de banco de dados do Azure Cosmos para o SDK do Java v4
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 10/13/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: 8aad9df4720c833a74659b5cd36b7f5aafdf9b60
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631832"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Dicas de desempenho para o SDK do Java v4 do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK do Java v4](performance-tips-java-sdk-v4-sql.md)
> * [SDK do Java Assíncrono v2](performance-tips-async-java.md)
> * [SDK do Java Síncrono v2](performance-tips-java.md)
> * [SDK v3 do .NET](performance-tips-dotnet-sdk-v3-sql.md)
> * [SDK do .NET v2](performance-tips.md)
> 

> [!IMPORTANT]  
> As dicas de desempenho neste artigo são apenas para o SDK do Java v4 do Azure Cosmos DB. Confira as [Notas sobre a versão](sql-api-sdk-java-v4.md) do SDK do Java v4 do Azure Cosmos DB, o [repositório do Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), o [Guia de solução de problemas](troubleshoot-java-sdk-v4-sql.md) do SDK do Java v4 do Azure Cosmos DB para saber mais. Se você estiver usando uma versão mais antiga do que a v4, confira o guia [Migrar para o SDK do Java v4 do Azure Cosmos DB](migrate-java-v4-sdk.md) para obter ajuda na atualização para a v4.

O Azure Cosmos DB é um banco de dados distribuído rápido e flexível que pode ser dimensionado perfeitamente com garantia de latência e produtividade. Você não precisa fazer alterações importantes de arquitetura nem escrever um código complexo para dimensionar seu banco de dados com o Azure Cosmos DB. Aumentar e reduzir é tão fácil quanto fazer uma única chamada à API ou uma chamada ao método do SDK. No entanto, como o Azure Cosmos DB é acessado por meio de chamadas de rede, há otimizações do lado do cliente que você pode fazer para obter o desempenho máximo ao usar o SDK do Java v4 do Azure Cosmos DB.

Assim, se você estiver se perguntando "Como posso melhorar o desempenho do meu banco de dados?" considere as seguintes opções:

## <a name="networking"></a>Rede

* **Modo de Conexão: Usar o modo Direto**
<a id="direct-connection"></a>
    
O modo de conexão padrão do SDK do Java é direto. Você pode configurar o modo de conexão no construtor de cliente usando os métodos *directmode ()* ou *gatewaymode ()* , conforme mostrado abaixo. Para configurar qualquer modo com as configurações padrão, chame um dos métodos sem argumentos. Caso contrário, passe uma instância de classe de definições de configuração como o argumento (*DirectConnectionConfig* para *directmode ()*,  *GatewayConnectionConfig* para *gatewaymode ()*.). Para saber mais sobre as diferentes opções de conectividade, consulte o artigo [modos de conectividade](sql-sdk-connection-modes.md) .
    
### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> SDK do Java v4

# <a name="async"></a>[Async](#tab/api-async)

API assíncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientConnectionModeAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

API síncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientConnectionModeSync)]

--- 

O método *directmode ()* tem uma substituição adicional, pelo seguinte motivo. Operações de plano de controle como banco de dados e contêiner CRUD *sempre* utilizam o modo de gateway; Quando o usuário configurou o modo direto para operações do plano de dados, as operações do plano de controle usam as configurações padrão do modo de gateway. Isso é mais adequado para a maioria dos usuários. No entanto, os usuários que desejam o modo direto para operações de plano de dados, bem como tunability de parâmetros do modo de gateway de plano de controle, podem usar a seguinte substituição de *directmode ()* :

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> SDK do Java v4

# <a name="async"></a>[Async](#tab/api-async)

API assíncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientDirectOverrideAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

API síncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientDirectOverrideSync)]

--- 

<a name="collocate-clients"></a>
* **Colocar clientes na mesma região do Azure para desempenho**
<a id="same-region"></a>

Quando possível, coloque aplicativos que chamam o Azure Cosmos DB na mesma região do banco de dados do Azure Cosmos. Para obter uma comparação aproximada, as chamadas para o Azure Cosmos DB na mesma região são concluídas de 1 a 2 ms, mas a latência entre a Costa Leste e a Oeste dos EUA é maior que 50 ms. Provavelmente, essa latência pode variar entre as solicitações dependendo da rota seguida pela solicitação conforme ela passa do cliente para o limite de datacenter do Azure. A menor latência possível é alcançada garantindo que o aplicativo de chamada está localizado na mesma região do Azure do ponto de extremidade do Azure Cosmos DB provisionado. Para obter uma lista de regiões disponíveis, consulte [Regiões do Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Ilustração da política de conexão do Azure Cosmos DB" border="false":::

Um aplicativo que interage com uma conta do Azure Cosmos DB de várias regiões precisa configurar [locais preferenciais](tutorial-global-distribution-sql-api.md#preferred-locations) para garantir que as solicitações sejam feitas para uma região posicionada.

* **Habilite a rede acelerada na sua VM do Azure para uma latência mais baixa.**

É recomendável que você siga as instruções para habilitar a Rede Acelerada na VM do Azure do [Windows (clique para obter instruções)](../virtual-network/create-vm-accelerated-networking-powershell.md) ou [Linux (clique para obter instruções)](../virtual-network/create-vm-accelerated-networking-cli.md) para maximizar o desempenho.

Sem a rede acelerada, a E/S que transita entre a VM do Azure e outros recursos do Azure pode ser desnecessariamente encaminhada por meio de um host e um comutador virtual situados entre a VM e sua placa de rede. Ter o host e o comutador virtual embutidos no caminho de dados não apenas aumenta a latência e a tremulação no canal de comunicação, ele também rouba ciclos de CPU da VM. Com a rede acelerada, a VM interage diretamente com a NIC sem intermediários; todos os detalhes da diretiva de rede que estavam sendo manipulados pelo host e pelo comutador virtual agora são manipulados no hardware na NIC; o host e o comutador virtual são ignorados. Geralmente, você pode esperar uma latência mais baixa e uma taxa de transferência mais alta, bem como uma latência mais *consistente* e menor utilização da CPU quando você habilita a rede acelerada.

Limitações: a rede acelerada deve ter suporte no sistema operacional da VM e só pode ser habilitada quando a VM é interrompida e desalocada. A VM não pode ser implantada com o Azure Resource Manager.

Confira as instruções do [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) e do [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) para obter mais detalhes.

## <a name="sdk-usage"></a>Uso do SDK
* **Instalar o SDK mais recente**

Os SDKs do Azure Cosmos DB estão constantemente sendo aprimorados para fornecer o melhor desempenho. Consulte as páginas do [SDK do Azure Cosmos DB](sql-api-sdk-async-java.md) para determinar o SDK mais recente e examinar as melhorias.

* **Usar um cliente do Azure Cosmos DB singleton para obter o tempo de vida do aplicativo**

Cada instância do cliente do Azure Cosmos DB tem um thread-safe e realiza um gerenciamento de conexão eficiente e o cache de endereço. Para permitir o gerenciamento de conexões eficiente e o melhor desempenho pelo cliente do Azure Cosmos DB, é recomendável usar uma única instância do cliente do Azure Cosmos DB por AppDomain durante a vida útil do aplicativo.

<a id="max-connection"></a>

* **Use o nível de consistência mais baixo necessário para seu aplicativo**

Quando você cria um *CosmosClient*, a consistência padrão usada se não for definido explicitamente é *Sessão*. Se a consistência de *Sessão* não for exigida pela lógica do aplicativo, defina a *Consistência* como *Eventual*. Observação: é recomendável usar pelo menos a consistência de *Sessão* em aplicativos que empregam o processador do feed de alterações do Azure Cosmos DB.

* **Usar a API assíncrona para obter a taxa de transferência máxima provisionada**

O SDK do Java v4 do Azure Cosmos DB agrupa duas APIs, síncrona e assíncrona. Em termos gerais, a API assíncrona implementa a funcionalidade do SDK, enquanto que a API síncrona é um wrapper fino que faz chamadas de bloqueio para a API assíncrona. Isso significa que, em contraste com o SDK do Java v2 do Azure Cosmos DB antigo, que era somente assíncrono e para o SDK do Java v2 síncrono mais antigo do Azure Cosmos DB, que era somente síncrono e tinha uma implementação completamente separada. 
    
A escolha da API é determinada durante a inicialização do cliente; um *CosmosAsyncClient* dá suporte à API assíncrona enquanto um *CosmosClient* dá suporte à API síncrona. 
    
A API assíncrona implementa E/S sem bloqueio e é a melhor opção se sua meta for obter a taxa de transferência máxima ao emitir solicitações para o Azure Cosmos DB. 
    
O uso da API síncrona pode ser a escolha certa se você quiser ou precisar de uma API que bloqueie a resposta a cada solicitação, ou se a operação síncrona for o paradigma dominante em seu aplicativo. Por exemplo, você pode querer a API síncrona quando estiver mantendo dados para o Azure Cosmos DB em um aplicativo de microsserviço, desde que a taxa de transferência fornecida não seja crítica.  
    
Apenas lembre-se de que a taxa de transferência da API síncrona é reduzida com tempo de resposta de solicitação crescente, enquanto a API assíncrona pode saturar os recursos completos de largura de banda do seu hardware. 
    
A colocação geográfica pode fornecer uma taxa de transferência maior e mais consistente ao usar a API de sincronização (consulte [Posicionar clientes na mesma região do Azure para melhorar o desempenho](#collocate-clients)), mas ainda não deve exceder a taxa de transferência obtida da API assíncrona.

Alguns usuários também podem não estar familiarizados com o [Project Reactor](https://projectreactor.io/), a estrutura da Reactive Streams usada para implementar a API assíncrona do SDK do Java v4 do Azure Cosmos DB. Se isso for uma preocupação, recomendamos que você leia nosso [Guia de introdução de padrão do Reactor](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) e, em seguida, dê uma olhada em [Introdução à programação da Reactive](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro) para se familiarizar. Se você já tiver usado Azure Cosmos DB com uma interface assíncrona e o SDK usado foi o SDK de Java v2 do Azure Cosmos DB, talvez esteja familiarizado com [ReactiveX](http://reactivex.io/)/[RxJava](https://github.com/ReactiveX/RxJava), mas não sabe o que mudou no Project Reactor. Nesse caso, dê uma olhada em nosso [Guia Reactor versus RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) para se familiarizar.

Os trechos de código a seguir mostram como inicializar seu cliente do Azure Cosmos DB para a API assíncrona ou a operação de API de sincronização, respectivamente:

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> SDK do Java v4

# <a name="async"></a>[Async](#tab/api-async)

API assíncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

API síncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientSync)]

 --- 

* **Ajustar ConnectionPolicy**

Por padrão, as solicitações do Cosmos DB de modo direto são feitas por TCP ao usar o SDK do Java v4 do Azure Cosmos DB. O modo direto interno usa uma arquitetura especial para gerenciar dinamicamente os recursos de rede e obter o melhor desempenho.

No SDK do Java v4 do Azure Cosmos DB, o modo direto é a melhor opção para melhorar o desempenho do banco de dados com a maioria das cargas de trabalho. 

* ***Visão geral do modo direto** _

:::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Ilustração da arquitetura do modo direto" border="false":::

A arquitetura do lado do cliente empregada no modo direto permite a utilização de rede previsível e o acesso multiplexado a réplicas do Azure Cosmos DB. O diagrama acima mostra como o modo direto encaminha solicitações de cliente para réplicas no back-end do Cosmos DB. A arquitetura do modo direto aloca até 10 _ *canais** no lado do cliente por réplica de BD. Um canal é uma conexão TCP precedida por um buffer de solicitação, que é de 30 solicitações de profundidade. Os canais que pertencem a uma réplica são alocados dinamicamente conforme o necessário pelo **Ponto de Extremidade de Serviço** da réplica. Quando o usuário emite uma solicitação no modo direto, o **TransportClient** roteia a solicitação para o ponto de extremidade de serviço apropriado com base na chave de partição. A **Fila de Solicitação** armazena em buffer as solicitações antes do Ponto de Extremidade de Serviço.

* ***Opções de configuração para o modo direto** _

Se o comportamento de modo direto não padrão for desejado, crie uma instância _DirectConnectionConfig * e personalize suas propriedades e, em seguida, passe a instância de propriedade personalizada para o método *directmode ()* no construtor de cliente Azure Cosmos DB.

Essas definições de configuração controlam o comportamento da arquitetura de modo direto subjacente discutida acima.

Como uma primeira etapa, use as definições de configuração recomendadas abaixo. Essas opções de *DirectConnectionConfig* são definições avançadas de configuração que podem afetar o desempenho do SDK de maneiras inesperadas; Recomendamos que os usuários evitem modificá-los, a menos que se sintam muito à vontade em entender as compensações e é absolutamente necessário. Entre em contato com a equipe do [Azure Cosmos DB](mailto:CosmosDBPerformanceSupport@service.microsoft.com) se você encontrar problemas neste tópico específico.

| Opções de configuração       | Padrão   |
| :------------------:       | :-----:   |
| idleConnectionTimeout      | "PT0"     |
| maxConnectionsPerEndpoint  | "130"     |
| connectTimeout             | "PT5S"    |
| idleEndpointTimeout        | PT1H    |
| maxRequestsPerConnection   | máximo      |

* **Ajustar consultas paralelas para coleções particionadas**

Azure Cosmos DB Java SDK v4 dá suporte a consultas paralelas, que permitem a consulta uma coleção particionada em paralelo. Para saber mais, confira [exemplos de código](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) relacionados ao trabalho com o SDK do Java v4 do Azure Cosmos DB. Consultas paralelas são projetadas para melhorar a latência da consulta e a produtividade em relação à contraparte serial.

* ***Ajustando \: setMaxDegreeOfParallelism** _
    
As consultas paralelas funcionam consultando várias partições em paralelo. No entanto, os dados de uma coleção particionada individual são buscados em série com relação à consulta. Por isso, use setMaxDegreeOfParallelism para definir o número de partições que representa o máximo de chance de conseguir uma consulta com o melhor desempenho, desde que todas as outras condições do sistema permaneçam as mesmas. Se você não souber o número de partições, poderá usar setMaxDegreeOfParallelism para definir um número alto, e o sistema escolherá o mínimo (número de partições, entrada fornecida pelo usuário) como o grau máximo de paralelismo.

É importante observar que as consultas paralelas produzirão os melhores benefícios se os dados forem distribuídos uniformemente em todas as partições com relação à consulta. Se a coleção particionada for particionada de uma forma que todos ou a maioria dos dados retornados por uma consulta ficarem concentrados em algumas partições (uma partição, na pior das hipóteses), o desempenho da consulta seria um gargalo dessas partições.

_ ***Ajustando \: setMaxBufferedItemCount** _
    
A consulta paralela destina-se a buscar previamente resultados enquanto o lote atual de resultados está sendo processado pelo cliente. A busca prévia ajuda a melhorar a latência geral de uma consulta. setMaxBufferedItemCount limita o número de resultados pré-buscados. Definir setMaxBufferedItemCount para o número esperado de resultados retornados (ou um número mais alto) permite que a consulta receba o benefício máximo da busca prévia.

A busca prévia funciona da mesma forma independentemente do MaxDegreeOfParallelism, e há um único buffer para os dados de todas as partições.

_ **Escalar horizontalmente sua carga de trabalho do cliente**

Se você estiver testando em altos níveis da taxa de transferência, o aplicativo cliente poderá tornar-se o gargalo devido à limitação do computador na utilização da CPU ou da rede. Se você chegar a este ponto, poderá continuar aumentando a conta do Azure Cosmos DB ainda mais distribuindo seus aplicativos cliente entre vários servidores.

Uma boa regra geral é não exceder 50% de utilização de CPU em qualquer servidor determinado, para manter a latência baixa.

<a id="tune-page-size"></a>

* **Ajustar o tamanho da página para os feeds de leitura/consultas para ter o melhor desempenho**

Ao executar uma leitura em massa dos documentos usando a funcionalidade do feed de leitura (por exemplo, *readItems*) ou ao enviar uma consulta SQL (*queryItems*), os resultados serão retornados de uma maneira segmentada se o conjunto de resultados for muito grande. Por padrão, os resultados são retornados em blocos de 100 itens ou 1 MB, o limite que for atingido primeiro.

Suponha que seu aplicativo emita uma consulta para o Azure Cosmos DB e suponha que seu aplicativo exija o conjunto completo de resultados da consulta para concluir sua tarefa. Para reduzir o número idas e vindas na rede necessárias para recuperar todos os resultados aplicáveis, você pode aumentar o tamanho da página ajustando o campo do cabeçalho de solicitação [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers). 

* Para consultas de partição única, ajustar o valor do campo [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para -1 (sem limite no tamanho da página) maximiza a latência minimizando o número de páginas de resposta de consulta: o conjunto de resultados completo retornará em uma única página ou, se a consulta demorar mais do que o intervalo de tempo limite, o conjunto de resultados completo será retornado no número mínimo de páginas possível. Isso poupa tempo de ida e volta da solicitação.
    
* Para consultas entre partições, definir o campo [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) como -1 e remover os riscos de limite de tamanho de página sobrecarregando o SDK com tamanhos de página não gerenciáveis. No caso entre partições, é recomendável aumentar o limite de tamanho de página para um valor grande, mas finito, talvez 1000, para reduzir a latência. 
    
Em alguns aplicativos, talvez você não precise do conjunto completo de resultados da consulta. Nos casos em que você precisa exibir apenas alguns resultados, por exemplo, se a interface do usuário ou a API do aplicativo retornar apenas 10 resultados de uma vez, também será possível diminuir o tamanho da página para 10 para reduzir a taxa de transferência consumida pelas leituras e consultas.

Você também pode definir o argumento de tamanho de página preferencial do método *byPage*, em vez de modificar diretamente o campo de cabeçalho REST. Lembre-se de que [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) ou o argumento de tamanho de página preferencial de *byPage* estão definindo apenas um limite superior no tamanho da página, não um requisito absoluto; portanto, por vários motivos, você pode ver o Azure Cosmos DB retornar páginas que são menores do que o tamanho preferencial de sua página. 

* **Usar o Agendador Apropriado (Evitar roubo de threads Netty de E/S Eventloop)**

A funcionalidade assíncrona do SDK do Java do Azure Cosmos DB baseia-se na E/S sem bloqueio de [netty](https://netty.io/). O SDK usa um número fixo de threads de eventloop netty de E/S (como muitos núcleos de CPU que seu computador possui) para executar operações de E/S. O Fluxo retornado pela API emite o resultado em um dos threads de loop event loop netty de E/S compartilhados. Portanto, é importante não bloquear os threads de netty eventloop de E/S compartilhados. Fazer o trabalho intensivo de CPU ou bloquear operação no thread de netty eventloop de E/S pode causar deadlock ou reduzir significativamente a taxa de transferência de SDK.

Por exemplo, o código a seguir executa um trabalho intensivo de CPU no thread de netty eventloop de E/S:
### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>API assíncrona do SDK v4 do Java (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNeedsSchedulerAsync)]

Quando um resultado é recebido, se desejar fazer trabalho intensivo de CPU no resultado, você deve evitar fazer isso em thread de netty eventloop de E/S. Em vez disso, você pode fornecer seu próprio Agendador para fornecer seu próprio thread para executar seu trabalho, conforme mostrado abaixo (requer `import reactor.core.scheduler.Schedulers` ).

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>API assíncrona do SDK v4 do Java (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddSchedulerAsync)]

Com base no tipo de seu trabalho, você deve usar o Agendador Reactor existente apropriado para seu trabalho. Leia aqui [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html).

Para saber mais sobre o SDK do Java v4 do Azure Cosmos DB, confira o [Diretório do Cosmos DB do SDK do Azure para o monorrepositório de Java no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos).

* **Otimizar as configurações de log em seu aplicativo**

Por vários motivos, talvez você queira ou precise adicionar o registro em log em uma thread que está gerando alta taxa de transferência de solicitação. Se seu objetivo for saturar completamente a taxa de transferência provisionada de um contêiner com solicitações geradas por essa thread, as otimizações de log poderão melhorar significativamente o desempenho.

* ***Configurar um agente assíncrono** _

A latência de um agente síncrono é necessariamente um dos fatores do cálculo de latência geral da sua thread de geração de solicitação. Um agente assíncrono, como [log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0), é recomendado para desacoplar a sobrecarga de log de suas threads de aplicativos de alto desempenho.

_ ***Desabilitar registro em log da sub-rede** _

O registro em log da biblioteca Netty é verborrágico e precisa ser desativado (suprimir o log na configuração talvez não seja suficiente) para evitar custos adicionais de CPU. Se você não estiver no modo de depuração, desabilite o registro em log do netty completamente. Portanto, se estiver usando log4j para remover os custos adicionais de CPU devidos por ``org.apache.log4j.Category.callAppenders()`` do netty, adicione a seguinte linha à sua base de código:

```java
org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
```

 _ **Limite de recursos de arquivos abertos do so**
 
Alguns sistemas Linux (como Red Hat) têm um limite superior no número de arquivos abertos e, portanto no número total de conexões. Execute o seguinte para exibir os limites atuais:

```bash
ulimit -a
```

O número de arquivos abertos (nofile) deve ser grande o suficiente para ter espaço suficiente para o tamanho do pool de conexão configurada e outros arquivos abertos pelo SO. Isso pode ser modificado para permitir um maior tamanho de pool de conexão.

Abra o arquivo limits.conf:

```bash
vim /etc/security/limits.conf
```
    
Adicione/modifique as linhas a seguir:

```
* - nofile 100000
```

* **Especificar chave de partição em gravações de ponto**

Para melhorar o desempenho das gravações de ponto, especifique a chave de partição do item na chamada à API de gravação de ponto, conforme mostrado abaixo:

# <a name="async"></a>[Async](#tab/api-async)

API assíncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNoPKAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

API síncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceNoPKSync)]

--- 

em vez de fornecer apenas a instância do item, conforme mostrado abaixo:

# <a name="async"></a>[Async](#tab/api-async)

API assíncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddPKAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

API síncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceAddPKSync)]

--- 

Há suporte para o último, mas isso adicionará latência ao seu aplicativo; o SDK deve analisar o item e extrair a chave de partição.

## <a name="indexing-policy"></a>Política de indexação
 
* **Excluir caminhos não utilizados da indexação para ter gravações mais rápidas**

A política de indexação do Azure Cosmos DB permite que você especifique quais caminhos de documento serão incluídos ou excluídos da indexação, aproveitando os Caminhos de Indexação (setIncludedPaths e setExcludedPaths). O uso dos caminhos de indexação pode oferecer um melhor desempenho de gravação e menor armazenamento de índices para os cenários nos quais os padrões da consulta são conhecidos com antecedência, pois os custos da indexação estão correlacionados diretamente com o número de caminhos exclusivos indexados. Por exemplo, o código a seguir mostra como incluir e excluir seções inteiras dos documentos (também conhecido como subárvore) da indexação usando o curinga "*".

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>SDK do Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

Para obter mais informações, consulte [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Produtividade
<a id="measure-rus"></a>

* **Medir e ajustar para o uso mais baixo de unidades/segundo da solicitação**

O Azure Cosmos DB oferece um conjunto avançado de operações do banco de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos – todos operando nos documentos em uma coleção de banco de dados. O custo associado a cada uma dessas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar em e gerenciar recursos de hardware, você pode pensar em uma RU (unidade de solicitação) como uma medida única para os recursos necessários para realizar várias operações de bancos de dados e atender a uma solicitação do aplicativo.

A taxa de transferência é provisionada com base no número de [unidades de solicitação](request-units.md) definidas para cada contêiner. O consumo da unidade de solicitação é avaliado em termos de taxa por segundo. Os aplicativos que excedem a taxa das unidades de solicitação provisionada para seu contêiner serão limitados até que a taxa fique abaixo do nível reservado para o contêiner. Caso o aplicativo exija um nível mais alto de taxa de transferência, é possível aumentar a taxa de transferência provisionando unidades de solicitação adicionais.

A complexidade de uma consulta afeta a quantidade de unidades de solicitação consumida para uma operação. O número de predicados, natureza dos predicados, número de UDFs e tamanho do conjunto de dados de origem influenciam o custo das operações de consulta.

Para medir a sobrecarga de qualquer operação (criar, atualizar ou excluir), examine o cabeçalho [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para medir o número de unidades de solicitação consumidas por essas operações. Você também pode examinar a propriedade RequestCharge equivalente em ResourceResponse\<T> ou FeedResponse\<T>.

# <a name="async"></a>[Async](#tab/api-async)

API assíncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceRequestChargeAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

API síncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceRequestChargeSync)]

--- 

A carga de solicitação retornada nesse cabeçalho é uma fração de sua taxa de transferência provisionada. Por exemplo, se você tem 2000 RUs/s provisionados e se a consulta anterior retornar 1000 documentos de 1 KB, o custo da operação será 1000. Assim, em um segundo, o servidor mantém apenas duas dessas solicitações antes de limitar as solicitações subsequentes. Para saber mais, consulte [Unidades de solicitação](request-units.md) e a [calculadora das unidades de solicitação](https://www.documentdb.com/capacityplanner).

<a id="429"></a>
* **Lidar com uma limitação da taxa/taxa de solicitação muito grande**

Quando um cliente tentar exceder a taxa de transferência reservada para uma conta, não haverá nenhuma degradação de desempenho no servidor e nenhum uso da capacidade da taxa além do nível reservado. O servidor encerrará antecipadamente a solicitação com RequestRateTooLarge (código de status HTTP 429) e retornará o cabeçalho [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) indicando a quantidade de tempo, em milissegundos, que o usuário deve aguardar antes de tentar novamente a solicitação.

```xml
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

Os SDKs irão capturar implicitamente essa resposta, respeitarão o cabeçalho server-specified retry-after e repetirão a solicitação. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa será bem-sucedida.

Se você tiver mais de um cliente operando cumulativamente de como consistente acima da taxa de solicitação, a contagem de repetição padrão atualmente definida para 9 internamente pelo cliente poderá não ser suficiente. Nesse caso, o cliente irá gerar um *CosmosClientException* com o código de status 429 para o aplicativo. A contagem de repetição padrão pode ser alterada usando setRetryOptions na instância ConnectionPolicy. Por padrão, o *CosmosClientException* com o código de status 429 será retornada após uma espera cumulativa de 30 segundos se a solicitação continuar a operar acima da taxa de solicitação. Isso ocorre mesmo quando a contagem de repetição atual é menor que a contagem de repetição máxima, seja o padrão 9 seja um valor definido pelo usuário.

Embora o comportamento de repetição automática ajude a melhorar a resiliência e a utilidade da maioria dos aplicativos, ela pode entrar em conflito ao fazer comparações de desempenho, especialmente ao medir a latência. A latência observada pelo cliente terá um pico se o teste atingir a limitação do servidor e fizer com que o SDK do cliente repita silenciosamente. Para evitar picos de latência durante os testes de desempenho, meça o custo retornado por cada operação e verifique se as solicitações estão operando abaixo da taxa de solicitação reservada. Para saber mais, consulte [Unidades de solicitação](request-units.md).

* **Design de documentos menores para uma maior taxa de transferência**

O custo da solicitação (o custo de processamento da solicitação) de uma determinada operação está correlacionado diretamente com o tamanho do documento. As operações em documentos grandes custam mais que as operações de documentos pequenos. O ideal é arquitetar seu aplicativo e os fluxos de trabalho para que o tamanho do item seja aproximadamente 1 KB ou uma ordem de magnitude semelhante. Para aplicativos com detecção de latência, itens grandes devem ser evitados: documentos com vários MB tornarão o seu aplicativo mais lento.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como projetar seu aplicativo para escala e alto desempenho, consulte [Particionamento e escala no Azure Cosmos DB](partitioning-overview.md).
