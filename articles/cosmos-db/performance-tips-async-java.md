---
title: Dicas de desempenho do Azure Cosmos DB para Java Async
description: Saiba mais sobre as opções de configuração do cliente para melhorar o desempenho de banco de dados do Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 2c2d776012e702469be4fd3217fb89be0ad419bf
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261626"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Dicas de desempenho para o Azure Cosmos DB e Java Assíncrono

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

O Azure Cosmos DB é um banco de dados distribuído rápido e flexível que pode ser dimensionado perfeitamente com garantia de latência e produtividade. Você não precisa fazer alterações importantes de arquitetura nem escrever um código complexo para dimensionar seu banco de dados com o Azure Cosmos DB. Aumentar e reduzir é tão fácil quanto fazer uma única chamada à API ou uma chamada ao método do SDK. No entanto, como o Azure Cosmos DB é acessado por meio de chamadas de rede, há otimizações do lado do cliente que você pode fazer para obter o desempenho máximo ao usar o [SDK do SQL Java Assíncrono](sql-api-sdk-async-java.md).

Assim, se você estiver se perguntando "Como posso melhorar o desempenho do meu banco de dados?" considere as seguintes opções:

## <a name="networking"></a>Rede
   <a id="same-region"></a>
1. **Colocar os clientes na mesma região do Azure para o desempenho**

    Quando possível, coloque aplicativos que chamam o Azure Cosmos DB na mesma região do banco de dados do Azure Cosmos DB. Para obter uma comparação aproximada, as chamadas para o Azure Cosmos DB na mesma região são concluídas de 1 a 2 ms, mas a latência entre a Costa Leste e a Oeste dos EUA é maior que 50 ms. Provavelmente, essa latência pode variar entre as solicitações dependendo da rota seguida pela solicitação conforme ela passa do cliente para o limite de datacenter do Azure. A menor latência possível é alcançada garantindo que o aplicativo de chamada está localizado na mesma região do Azure do ponto de extremidade do Azure Cosmos DB provisionado. Para obter uma lista de regiões disponíveis, consulte [Regiões do Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustração da política de conexão do Azure Cosmos DB](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>Uso do SDK
1. **Instalar o SDK mais recente**

    Os SDKs do Azure Cosmos DB estão constantemente sendo aprimorados para fornecer o melhor desempenho. Consulte as páginas do [SDK do Azure Cosmos DB](sql-api-sdk-async-java.md) para determinar o SDK mais recente e examinar as melhorias.
2. **Usar um cliente do Azure Cosmos DB singleton para obter o tempo de vida do aplicativo**

    Cada instância do AsyncDocumentClient tem um thread-safe e realiza um gerenciamento de conexão eficiente e o cache de endereço. Para permitir o gerenciamento de conexão eficiente e o melhor desempenho por AsyncDocumentClient, é recomendável usar uma única instância de AsyncDocumentClient por AppDomain durante a vida útil do aplicativo.

   <a id="max-connection"></a>

3. **Ajustar ConnectionPolicy**

    As solicitações do Azure Cosmos DB são feitas por HTTPS/REST durante o uso do Async Java SDK e estão sujeitas ao tamanho máximo padrão do pool de conexão (1000). O valor padrão deve ser ideal para a maioria dos casos de uso. No entanto, caso tenha uma coleção grande com várias partições, você pode definir o tamanho máximo do pool de conexões para um número maior (digamos, 1500) usando setMaxPoolSize.

4. **Ajustar consultas paralelas para coleções particionadas**

    Azure Cosmos DB SQL Async Java SDK dá suporte a consultas paralelas, que permitem a consulta uma coleção particionada em paralelo. Para obter mais informações, consulte [exemplos de código](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) relacionados ao trabalho com os SDKs. Consultas paralelas são projetadas para melhorar a latência da consulta e a produtividade em relação à contraparte serial.

    (a) ***Ajustar setMaxDegreeOfParallelism\:*** consultas paralelas funcionam consultando várias partições em paralelo. No entanto, os dados de uma coleção particionada individual são buscados em série com relação à consulta. Por isso, use setMaxDegreeOfParallelism para definir o número de partições que representa o máximo de chance de conseguir uma consulta com o melhor desempenho, desde que todas as outras condições do sistema permaneçam as mesmas. Se você não souber o número de partições, poderá usar setMaxDegreeOfParallelism para definir um número alto, e o sistema escolherá o mínimo (número de partições, entrada fornecida pelo usuário) como o grau máximo de paralelismo.

    É importante observar que as consultas paralelas produzirão os melhores benefícios se os dados forem distribuídos uniformemente em todas as partições com relação à consulta. Se a coleção particionada for particionada de uma forma que todos ou a maioria dos dados retornados por uma consulta ficarem concentrados em algumas partições (uma partição, na pior das hipóteses), o desempenho da consulta seria um gargalo dessas partições.

    (b) ***Ajustar setMaxBufferedItemCount\:*** a consulta paralela foi desenvolvida para buscar previamente resultados enquanto o lote atual de resultados está sendo processado pelo cliente. A busca prévia ajuda a melhorar a latência geral de uma consulta. setMaxBufferedItemCount limita o número de resultados pré-buscados. Definir setMaxBufferedItemCount para o número esperado de resultados retornados (ou um número mais alto) permite que a consulta receba o benefício máximo da busca prévia.

    A busca prévia funciona da mesma forma independentemente do MaxDegreeOfParallelism, e há um único buffer para os dados de todas as partições.

5. **Implementar a retirada em intervalos de getRetryAfterInMilliseconds**

    Durante o teste de desempenho, você deve aumentar a carga até que uma pequena taxa de solicitações seja restringida. Se restringida, o aplicativo cliente deve retirar a limitação para a nova tentativa do servidor especificado. Respeitar a retirada garante que você perca o mínimo de tempo de espera entre as tentativas.
6. **Escalar horizontalmente sua carga de trabalho do cliente**

    Se você estiver testando em altos níveis da taxa de transferência (mais de 50.000 RUs/s), o aplicativo cliente poderá tornar-se o gargalo devido à limitação do computador na utilização da CPU ou da rede. Se você chegar a este ponto, poderá continuar aumentando a conta do Azure Cosmos DB ainda mais distribuindo seus aplicativos cliente entre vários servidores.

7. **Usar o endereçamento baseado em nome**

    Use o endereçamento com base em nome, no qual os links têm o formato `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` em vez de SelfLinks (\_self), que tem o formato `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` para evitar recuperar ResourceIds de todos os recursos usados para construir o link. Além disso, como esses recursos foram recriados (possivelmente com o mesmo nome), armazená-los em cache pode não ajudar.

   <a id="tune-page-size"></a>
8. **Ajustar o tamanho da página para os feeds de leitura/consultas para ter o melhor desempenho**

    Ao executar uma leitura em massa dos documentos usando a funcionalidade do feed de leitura (por exemplo, readDocuments) ou ao enviar uma consulta SQL, os resultados serão retornados de uma maneira segmentada se o conjunto de resultados for muito grande. Por padrão, os resultados são retornados em blocos de 100 itens ou 1 MB, o limite que for atingido primeiro.

    Para reduzir o número idas e vindas na rede necessárias para recuperar todos os resultados aplicáveis, você pode aumentar o tamanho da página usando o cabeçalho de solicitação [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para até 1000. Nos casos em que você precisa exibir apenas alguns resultados, por exemplo, se a interface do usuário ou a API do aplicativo retornar apenas 10 resultados de uma vez, também será possível diminuir o tamanho da página para 10 para reduzir a taxa de transferência consumida pelas leituras e consultas.

    Você também poderá definir o tamanho da página usando o método setMaxItemCount.

9. **Usar o Agendador Apropriado (Evitar roubo de threads Netty de E/S Eventloop)**

    O SDK do Java Assíncrono usa [netty](https://netty.io/) para E/S sem bloqueio. O SDK usa um número fixo de threads de eventloop netty de E/S (como muitos núcleos de CPU que seu computador possui) para executar operações de E/S. O Observável retornado pela API emite o resultado em um dos threads de netty eventloop de E/S compartilhados. Portanto, é importante não bloquear os threads de netty eventloop de E/S compartilhados. Fazer o trabalho intensivo de CPU ou bloquear operação no thread de netty eventloop de E/S pode causar deadlock ou reduzir significativamente a taxa de transferência de SDK.

    Por exemplo, o código a seguir executa um trabalho intensivo de CPU no thread de netty eventloop de E/S:

    ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
    ```

    Quando um resultado é recebido, se desejar fazer trabalho intensivo de CPU no resultado, você deve evitar fazer isso em thread de netty eventloop de E/S. Em vez disso, você pode fornecer seu próprio Agendador para fornecer seu próprio thread para executar seu trabalho.

    ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
    ```

    Com base no tipo de seu trabalho, você deve usar o Agendador RxJava existente apropriado para seu trabalho. Leia aqui [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    Para obter mais informações, consulte a [página do Github](https://github.com/Azure/azure-cosmosdb-java) para SDK do Java Assíncrono.

10. **Desabilitar o log do netty** O registro em log da biblioteca Netty é verborrágico e precisa ser desativado (suprimir o log na configuração talvez não seja suficiente) para evitar custos adicionais de CPU. Se você não estiver no modo de depuração, desabilite o registro em log do netty completamente. Portanto, se estiver usando log4j para remover os custos adicionais de CPU devidos por ``org.apache.log4j.Category.callAppenders()`` do netty, adicione a seguinte linha à sua base de código:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **Limite de recursos de arquivos abertos do SO** Alguns sistemas Linux (como Red Hat) têm um limite superior no número de arquivos abertos e, portanto no número total de conexões. Execute o seguinte para exibir os limites atuais:

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

12. **Usar a implementação de SSL nativo para netty** Netty pode usar o OpenSSL diretamente para a pilha de implementação de SSL para conseguir melhor desempenho. Na ausência dessa configuração, o netty voltará à implementação de SSL padrão do Java.

    no Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    e adicione a seguinte dependência para suas dependências do projeto maven:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Para outras plataformas (Red Hat, Windows, Mac, etc.), consulte estas instruções https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Política de indexação
 
1. **Excluir caminhos não utilizados da indexação para ter gravações mais rápidas**

    A política de indexação do Azure Cosmos DB permite que você especifique quais caminhos de documento serão incluídos ou excluídos da indexação, aproveitando os Caminhos de Indexação (setIncludedPaths e setExcludedPaths). O uso dos caminhos de indexação pode oferecer um melhor desempenho de gravação e menor armazenamento de índices para os cenários nos quais os padrões da consulta são conhecidos com antecedência, pois os custos da indexação estão correlacionados diretamente com o número de caminhos exclusivos indexados. Por exemplo, o código a seguir mostra como excluir uma seção inteira de documentos (também conhecida como uma subárvore) de indexação usando o curinga "*".

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Para obter mais informações, consulte [Políticas de indexação do Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Produtividade
<a id="measure-rus"></a>

1. **Medir e ajustar para o uso mais baixo de unidades/segundo da solicitação**

    O Azure Cosmos DB oferece um conjunto avançado de operações do banco de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos – todos operando nos documentos em uma coleção de banco de dados. O custo associado a cada uma dessas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar em e gerenciar recursos de hardware, você pode pensar em uma RU (unidade de solicitação) como uma medida única para os recursos necessários para realizar várias operações de bancos de dados e atender a uma solicitação do aplicativo.

    A taxa de transferência é provisionada com base no número de [unidades de solicitação](request-units.md) definidas para cada contêiner. O consumo da unidade de solicitação é avaliado em termos de taxa por segundo. Os aplicativos que excedem a taxa das unidades de solicitação provisionada para seu contêiner serão limitados até que a taxa fique abaixo do nível reservado para o contêiner. Caso o aplicativo exija um nível mais alto de taxa de transferência, é possível aumentar a taxa de transferência provisionando unidades de solicitação adicionais.

    A complexidade de uma consulta afeta a quantidade de unidades de solicitação consumida para uma operação. O número de predicados, natureza dos predicados, número de UDFs e tamanho do conjunto de dados de origem influenciam o custo das operações de consulta.

    Para medir a sobrecarga de qualquer operação (criar, atualizar ou excluir), examine o cabeçalho [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para medir o número de unidades de solicitação consumidas por essas operações. Você também pode examinar a propriedade RequestCharge equivalente em ResourceResponse\<t > ou FeedResponse\<t >.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    A carga de solicitação retornada nesse cabeçalho é uma fração de sua taxa de transferência provisionada. Por exemplo, se você tem 2000 RUs/s provisionados e se a consulta anterior retornar 1000 documentos de 1 KB, o custo da operação será 1000. Assim, em um segundo, o servidor mantém apenas duas dessas solicitações antes de limitar as solicitações subsequentes. Para saber mais, consulte [Unidades de solicitação](request-units.md) e a [calculadora das unidades de solicitação](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Lidar com uma limitação da taxa/taxa de solicitação muito grande**

    Quando um cliente tentar exceder a taxa de transferência reservada para uma conta, não haverá nenhuma degradação de desempenho no servidor e nenhum uso da capacidade da taxa além do nível reservado. O servidor encerrará antecipadamente a solicitação com RequestRateTooLarge (código de status HTTP 429) e retornará o cabeçalho [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) indicando a quantidade de tempo, em milissegundos, que o usuário deve aguardar antes de tentar novamente a solicitação.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Os SDKs irão capturar implicitamente essa resposta, respeitarão o cabeçalho server-specified retry-after e repetirão a solicitação. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa será bem-sucedida.

    Se você tiver mais de um cliente operando cumulativamente de como consistente acima da taxa de solicitação, a contagem de repetição padrão atualmente definida para 9 internamente pelo cliente poderá não ser suficiente. Nesse caso, o cliente irá gerar uma DocumentClientException com o código de status 429 para o aplicativo. A contagem de repetição padrão pode ser alterada usando setRetryOptions na instância ConnectionPolicy. Por padrão, a DocumentClientException com o código de status 429 será retornada após uma espera cumulativa de 30 segundos se a solicitação continuar a operar acima da taxa de solicitação. Isso ocorre mesmo quando a contagem de repetição atual é menor que a contagem de repetição máxima, seja o padrão 9 seja um valor definido pelo usuário.

    Embora o comportamento de repetição automática ajude a melhorar a resiliência e a utilidade da maioria dos aplicativos, ela pode entrar em conflito ao fazer comparações de desempenho, especialmente ao medir a latência. A latência observada pelo cliente terá um pico se o teste atingir a limitação do servidor e fizer com que o SDK do cliente repita silenciosamente. Para evitar picos de latência durante os testes de desempenho, meça o custo retornado por cada operação e verifique se as solicitações estão operando abaixo da taxa de solicitação reservada. Para saber mais, consulte [Unidades de solicitação](request-units.md).
3. **Design de documentos menores para uma maior taxa de transferência**

    O custo da solicitação (o custo de processamento da solicitação) de uma determinada operação está correlacionado diretamente com o tamanho do documento. As operações em documentos grandes custam mais que as operações de documentos pequenos.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como projetar seu aplicativo para escala e alto desempenho, consulte [Particionamento e escala no Azure Cosmos DB](partition-data.md).
