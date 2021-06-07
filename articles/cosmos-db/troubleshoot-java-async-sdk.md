---
title: Diagnosticar e solucionar problemas do SDK do Java Assíncrono v2 do Azure Cosmos DB
description: Use recursos como registro em log do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas do Azure Cosmos DB no SDK do Java Assíncrono v2.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: b39a74bd06f942cf21d201c8cef48bc6dfc57d46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96548079"
---
# <a name="troubleshoot-issues-when-you-use-the-azure-cosmos-db-async-java-sdk-v2-with-sql-api-accounts"></a>Solucionar problemas ao usar o SDK do Java Assíncrono v2 do Azure Cosmos DB com contas de API do SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK do Java v4](troubleshoot-java-sdk-v4-sql.md)
> * [SDK do Java Assíncrono v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Esse *não* é o SDK do Java mais recente para o Azure Cosmos DB! Você deve atualizar seu projeto para [SDK do Java v4 do Azure Cosmos DB](sql-api-sdk-java-v4.md) e, em seguida, ler o [guia de solução de problemas](troubleshoot-java-sdk-v4-sql.md) do SDK do Java v4 do Azure Cosmos DB. Siga as instruções nos guias [Migrar para o SDK do Java v4 do Azure Cosmos DB](migrate-java-v4-sdk.md) e [Reator vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md). 
>
> Este artigo aborda apenas a solução de problemas para o SDK do Java Assíncrono v2 do Azure Cosmos DB. Confira as [Notas sobre a Versão](sql-api-sdk-async-java.md) do SDK do Java Assíncrono v2 do Azure Cosmos DB, o [repositório Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) e as [dicas de desempenho](performance-tips-async-java.md) para obter mais informações.
>

Este artigo aborda problemas comuns, soluções alternativas, etapas de diagnóstico e ferramentas ao usar do [SDK do Java Assíncrono](sql-api-sdk-async-java.md) com contas da API Azure Cosmos DB SQL.
O SDK do Java Assíncrono fornece uma representação lógica do lado do cliente para acessar a API do Azure Cosmos DB SQL. Este artigo descreve as ferramentas e as abordagens para ajudá-lo se você tiver algum problema.

Comece com esta lista:

* Dê uma olhada na seção [Problemas comuns e soluções alternativas] neste artigo.
* Examine o SDK, que está disponível em [software livre no GitHub](https://github.com/Azure/azure-cosmosdb-java). Ele tem um [seção de problemas](https://github.com/Azure/azure-cosmosdb-java/issues) que está sendo ativamente monitorada. Verifique se você encontrar algum problema semelhante com uma solução alternativa já arquivada.
* Examine [dicas de desempenho](performance-tips-async-java.md) e siga as práticas sugeridas.
* Se você não encontrar uma solução, leia o restante deste artigo. Em seguida, arquive um [problema do GitHub](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemas comuns e soluções alternativas

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemas de rede, falha de tempo limite do Netty, baixa taxa de transferência, alta latência de leitura

#### <a name="general-suggestions"></a>Sugestões gerais
* Verifique se o aplicativo está em execução na mesma região que sua conta do Azure Cosmos DB. 
* Verifique o uso da CPU no host em que o aplicativo está sendo executado. Se o uso da CPU é de 90% ou mais, execute seu aplicativo em um host com uma configuração superior. Ou você pode distribuir a carga em mais computadores.

#### <a name="connection-throttling"></a>Limitação de conexão
A limitação de conexão pode ocorrer devido a um [limite de conexão no computador host] ou a [Esgotamento da porta SNAT (PAT) do Azure].

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Limite de conexão no computador host
Alguns sistemas Linux, como Red Hat, têm um limite superior para o número total de arquivos abertos. Soquetes no Linux são implementados como arquivos, portanto, esse número limita o número total de conexões também.
Execute o comando a seguir.

```bash
ulimit -a
```
O número de arquivos abertos máximos permitos, que são identificados como "nofile", devem ser pelo menos duas vezes o tamanho do pool de conexão. Para obter mais informações, consulte [Dicas de desempenho](performance-tips-async-java.md).

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Esgotamento da porta SNAT (PAT) do Azure

Se o seu aplicativo for desenvolvido nas Máquinas Virtuais do Microsoft Azure sem um endereço IP público, por padrão as [portas do Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) estabelecem conexões a qualquer ponto de extremidade fora da sua VM. O número de conexões permitidas da VM para o ponto de extremidade do Azure Cosmos DB é limitado pela [configuração do Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports).

 As portas Azure SNAT são usadas somente quando sua VM do Azure tiver um endereço IP privado e um processo da VM tenta estabelecer uma conexão com um endereço IP público. Há duas soluções alternativas para evitar a limitação do Azure SNAT:

* Adicione seu ponto de extremidade de serviço do Azure Cosmos DB para a sub-rede da sua rede virtual de Máquinas Virtuais do Azure. Para saber mais, consulte [pontos de extremidade de serviço de Rede Virtual do Microsoft Azure](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quando o ponto de extremidade for habilitado, as solicitações não são mais enviadas de um IP público para o Azure Cosmos DB. Em vez disso, a rede virtual e a identidade de sub-rede são enviadas. Essa alteração poderá resultar em quedas de firewall se apenas IPs públicos forem permitidos. Se você usar um firewall, quando você habilitar o ponto de extremidade de serviço, adicione uma sub-rede para o firewall usando as [ACLs de Rede Virtual](/previous-versions/azure/virtual-network/virtual-networks-acl).
* Atribua um IP público à sua VM do Azure.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Não é possível acessar o Serviço – firewall
``ConnectTimeoutException`` indica que o SDK não pode acessar o serviço.
Você pode obter uma falha semelhante à seguinte ao usar o modo direto:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Se você tiver um firewall em execução em seu computador do aplicativo, abra o intervalo de portas 10.000 a 20.000 que são usadas pelo modo direto.
Além disso, siga o [Limite de conexão em um computador host](#connection-limit-on-host).

#### <a name="http-proxy"></a>Proxy HTTP

Se você usar um proxy HTTP, certifique-se que pode suportar o número de conexões configuradas no SDK `ConnectionPolicy`.
Caso contrário, você enfrentará problemas de conexão.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Padrão de codificação inválido: Bloqueio do thread de E/S do Netty

O SDK usa a biblioteca de E/S [Netty](https://netty.io/) biblioteca para se comunicar com o Azure Cosmos DB. O SDK tem APIs assíncronas e usas as APIs de E/S sem bloqueio do Netty. O trabalho de E/S do SDK é executado em threads de Netty de E/S. O número de threads de E/S Netty está configurado para ser igual ao número de núcleos da CPU no computador do aplicativo. 

Os threads de E/S Netty destinam-se somente a serem usados para o trabalho de E/S Netty sem bloqueio. O SDK retorna o resultado da invocação de API em um dos threads de E/S Netty para o código de aplicativos. Se o aplicativo executa uma operação de longa duração, depois de receber os resultados no thread de Netty, o SDK pode não ter threads de E/S suficientes para realizar seu trabalho de E/S interno. Essa codificação de aplicativo pode resultar em baixa taxa de transferência, latência alta e `io.netty.handler.timeout.ReadTimeoutException` falhas. A solução alternativa é mudar o thread quando você sabe que a operação levará tempo.

Por exemplo, dê uma olhada no seguinte snippet de código. Você pode executar o trabalho de longa duração que leva mais de alguns milissegundos no thread de Netty. Nesse caso, você, eventualmente, pode colocar em um estado em que nenhum thread de e/s do Netty está presente para processar o trabalho de e/s. Como resultado, você obterá uma falha de ReadTimeoutException.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-readtimeout"></a>SDK do Java Assíncrono V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
A solução alternativa é alterar o thread em que você executa o trabalho demorado. Defina uma instância singleton do agendador para seu aplicativo.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-scheduler"></a>SDK do Java Assíncrono V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
```
Você talvez precise fazer o trabalho que leva tempo, por exemplo, recursos computacionais pesados corporativos ou bloqueio de e/s. Nesse caso, alterne o thread para um trabalhador fornecido pelo seu `customScheduler` usando a `.observeOn(customScheduler)` API.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-applycustomscheduler"></a>SDK do Java Assíncrono V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Usando `observeOn(customScheduler)`, você libera o thread de E/S Netty e muda para seu próprio thread personalizado fornecido pelo agendador personalizado. Essa modificação resolve o problema. Você não obterá uma `io.netty.handler.timeout.ReadTimeoutException` falha mais.

### <a name="connection-pool-exhausted-issue"></a>Problema de pool de conexão esgotado

`PoolExhaustedException` é uma falha do lado do cliente. Essa falha indica que sua carga de trabalho do aplicativo é maior do que o pool de conexão do SDK pode atender. Aumenta o tamanho do pool de conexão ou distribui a carga em vários aplicativos.

### <a name="request-rate-too-large"></a>Taxa de solicitação grande demais
Essa falha é uma falha no lado do servidor. Indica que você consumiu sua taxa de transferência provisionada. Tente novamente mais tarde. Se você recebe essa falha com frequência, considere aumentar a taxa de transferência da coleção.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Falha ao conectar-se ao emulador de Azure Cosmos DB

O certificado HTTPS do emulador de Azure Cosmos DB é autoassinado. Para o SDK funcionar com o emulador, importe o certificado do emulador para um Java TrustStore. Para obter mais informações, consulte [Exportar certificados do emulador Azure Cosmos DB](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problemas de conflito de dependência

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

A exceção acima sugere que você tem uma dependência em uma versão mais antiga da biblioteca RxJava (por exemplo, 1.2.2). Nosso SDK se baseia em RxJava 1.3.8, que tem APIs não disponíveis na versão anterior do RxJava. 

A solução alternativa para esses problemas é identificar qual outra dependência traz o RxJava-1.2.2, excluir a dependência transitiva em RxJava-1.2.2 e permitir que o SDK do CosmosDB traga a versão mais recente.

Para identificar qual biblioteca apresenta o RxJava-1.2.2, execute o seguinte comando ao lado do arquivo pom.xml do projeto:
```bash
mvn dependency:tree
```
Para obter mais informações, confira o [guia de árvore de dependências do Maven](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Depois de identificar de qual outra dependência do seu projeto o RxJava-1.2.2 é uma dependência transitiva, você pode modificar a dependência na biblioteca em seu arquivo pom e excluir a dependência transitiva do RXJava:

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Para obter mais informações, confira o [guia Excluir dependência transitiva](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Habilitar o log do SDK do cliente

Os usos do SDK do Java assíncrono SLF4j como a fachada de registro em log que dá suporte ao registro em log em estruturas de log populares como o log4j, logback.

Por exemplo, se você quiser usar log4j como a estrutura de registros, adicione estas bibliotecas ao seu classpath de Java.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Adicione também uma configuração de log4j.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Para obter mais informações, consulte o [manual de log sfl4j](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Estatísticas de rede do sistema operacional
Execute o comando netstat para ter uma noção de quantas conexões estão nos estados como `ESTABLISHED` e `CLOSE_WAIT`.

No Linux, você pode executar o comando a seguir.
```bash
netstat -nap
```
Filtre o resultado somente para conexões ao ponto de extremidade do Azure Cosmos DB.

O número de conexões ao ponto de extremidade do Azure Cosmos DB no `ESTABLISHED` estado não deve ser maior que o tamanho do pool de conexão configurado.

Muitas conexões para o ponto de extremidade do Azure Cosmos DB podem estar no `CLOSE_WAIT` estado. Pode haver mais de 1.000. Um número tão alto indica que as conexões são estabelecidas e interrompidas rapidamente. Essa situação potencialmente causa problemas. Para obter mais informações, consulte a seção [Problemas comuns e soluções alternativas].

 <!--Anchors-->
[Problemas comuns e soluções alternativas]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limite de conexão no computador host]: #connection-limit-on-host
[Esgotamento da porta SNAT (PAT) do Azure]: #snat
