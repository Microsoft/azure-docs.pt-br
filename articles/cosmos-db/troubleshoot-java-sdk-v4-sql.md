---
title: Diagnosticar e solucionar problemas no SDK do Java v4 do Azure Cosmos DB
description: Use recursos como registro em log do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas do Azure Cosmos DB no SDK do Java v4.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.custom: devx-track-java
ms.openlocfilehash: d6b23a831426a3308a0b47946d5a82679e937bbe
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97683128"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>Solução de problemas ao usar o SDK do Java v4 do Azure Cosmos DB com contas de API do SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK do Java v4](troubleshoot-java-sdk-v4-sql.md)
> * [SDK do Java v2 Assíncrono](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Este artigo aborda a solução de problemas apenas no SDK do Java v4 do Azure Cosmos DB. Consulte as [Notas de versão do SDK do Java v4 do Azure Cosmos DB](sql-api-sdk-java-v4.md), o [Repositório do Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) e as [Dicas de desempenho](performance-tips-java-sdk-v4-sql.md) para obter mais informações. Se você estiver usando uma versão mais antiga do que a v4, confira o guia [Migrar para o SDK do Java v4 do Azure Cosmos DB](migrate-java-v4-sdk.md) para obter ajuda na atualização para a v4.
>

Este artigo aborda problemas comuns, soluções alternativas, etapas de diagnóstico e ferramentas ao usar o SDK do Java v4 do Azure Cosmos DB com contas da API do SQL do Azure Cosmos DB.
O SDK do Java v4 do Azure Cosmos DB fornece uma representação lógica do lado do cliente para acessar a API do Azure Cosmos DB SQL. Este artigo descreve as ferramentas e as abordagens para ajudá-lo se você tiver algum problema.

Comece com esta lista:

* Dê uma olhada na seção [Problemas comuns e soluções alternativas] neste artigo.
* Examine o SDK do Java no repositório central do Azure Cosmos DB, disponível no [software livre no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos). Ele tem um [seção de problemas](https://github.com/Azure/azure-sdk-for-java/issues) que está sendo ativamente monitorada. Verifique se você encontrar algum problema semelhante com uma solução alternativa já arquivada. Uma dica útil é filtrar os problemas pela tag *cosmos:v4-item*.
* Consulte as [dicas de desempenho](performance-tips-java-sdk-v4-sql.md) do SDK do Java v4 do Azure Cosmos DB e siga as práticas sugeridas.
* Se você não encontrar uma solução, leia o restante deste artigo. Em seguida, arquive um [problema do GitHub](https://github.com/Azure/azure-sdk-for-java/issues). Se houver uma opção para adicionar marcas ao problema do GitHub, adicione uma tag *cosmos:v4-item*.

### <a name="retry-logic"></a>Lógica de repetição <a id="retry-logics"></a>
Cosmos DB SDK em qualquer falha de e/s tentará repetir a operação com falha se tentar novamente no SDK for viável. Ter uma nova tentativa em vigor para qualquer falha é uma boa prática, mas o tratamento e a repetição de falhas de gravação é necessário. É recomendável usar o SDK mais recente, pois a lógica de repetição está sendo continuamente aprimorada.

1. As falhas de leitura e consulta de e/s serão repetidas pelo SDK sem identificando-las ao usuário final.
2. As gravações (criar, Upsert, substituir, excluir) são "não" idempotentes e, portanto, o SDK nem sempre pode repetir as operações de gravação com falha. É necessário que a lógica do aplicativo do usuário manipule a falha e tente novamente.
3. A [disponibilidade do SDK de solução de problemas](troubleshoot-sdk-availability.md) explica as repetições para contas de Cosmos DB de várias regiões.

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemas comuns e soluções alternativas

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemas de rede, falha de tempo limite do Netty, baixa taxa de transferência, alta latência de leitura

#### <a name="general-suggestions"></a>Sugestões gerais
Para obter o melhor desempenho:
* Verifique se o aplicativo está em execução na mesma região que sua conta do Azure Cosmos DB. 
* Verifique o uso da CPU no host em que o aplicativo está sendo executado. Se o uso da CPU estiver em 50% ou mais, execute seu aplicativo em um host com uma configuração superior. Ou você pode distribuir a carga em mais computadores.
    * Se você estiver executando o aplicativo no Serviço de Kubernetes do Azure, poderá [usar o Azure Monitor para monitorar a utilização da CPU](../azure-monitor/insights/container-insights-analyze.md).

#### <a name="connection-throttling"></a>Limitação de conexão
A limitação de conexão pode ocorrer devido a um [limite de conexão no computador host] ou a [Esgotamento da porta SNAT (PAT) do Azure].

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Limite de conexão no computador host
Alguns sistemas Linux, como Red Hat, têm um limite superior para o número total de arquivos abertos. Soquetes no Linux são implementados como arquivos, portanto, esse número limita o número total de conexões também.
Execute o comando a seguir.

```bash
ulimit -a
```
O número de arquivos abertos máximos permitos, que são identificados como "nofile", devem ser pelo menos duas vezes o tamanho do pool de conexão. Para obter mais informações, consulte as [dicas de desempenho](performance-tips-java-sdk-v4-sql.md) do SDK do Java v4 do Azure Cosmos DB.

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

O SDK usa a biblioteca de E/S [Netty](https://netty.io/) biblioteca para se comunicar com o Azure Cosmos DB. O SDK tem uma API assíncrona e usa as APIs de E/S sem bloqueio do Netty. O trabalho de E/S do SDK é executado em threads de Netty de E/S. O número de threads de E/S Netty está configurado para ser igual ao número de núcleos da CPU no computador do aplicativo. 

Os threads de E/S Netty destinam-se somente a serem usados para o trabalho de E/S Netty sem bloqueio. O SDK retorna o resultado da invocação de API em um dos threads de E/S Netty para o código de aplicativos. Se o aplicativo executa uma operação de longa duração, depois de receber os resultados no thread de Netty, o SDK pode não ter threads de E/S suficientes para realizar seu trabalho de E/S interno. Essa codificação de aplicativo pode resultar em baixa taxa de transferência, latência alta e `io.netty.handler.timeout.ReadTimeoutException` falhas. A solução alternativa é mudar o thread quando você sabe que a operação levará tempo.

Por exemplo, observe o trecho de código a seguir, que adiciona itens a um contêiner (procure [aqui](create-sql-api-java.md) por instruções sobre como configurar o banco de dados e o contêiner.) Você pode executar o trabalho de longa duração que leva mais de alguns milissegundos no thread de Netty. Nesse caso, você, eventualmente, pode colocar em um estado em que nenhum thread de e/s do Netty está presente para processar o trabalho de e/s. Como resultado, você obterá uma falha de ReadTimeoutException.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>API assíncrona do SDK v4 do Java (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootNeedsSchedulerAsync)]

A solução alternativa é alterar o thread em que você executa o trabalho demorado. Defina uma instância singleton do agendador para seu aplicativo.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>API assíncrona do SDK v4 do Java (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootCustomSchedulerAsync)]

Você talvez precise fazer o trabalho que leva tempo, por exemplo, recursos computacionais pesados corporativos ou bloqueio de e/s. Nesse caso, alterne o thread para um trabalhador fornecido pelo seu `customScheduler` usando a `.publishOn(customScheduler)` API.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>API assíncrona do SDK v4 do Java (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootPublishOnSchedulerAsync)]

Usando `publishOn(customScheduler)`, você libera o thread de E/S Netty e muda para seu próprio thread personalizado fornecido pelo agendador personalizado. Essa modificação resolve o problema. Você não obterá uma `io.netty.handler.timeout.ReadTimeoutException` falha mais.

### <a name="request-rate-too-large"></a>Taxa de solicitação grande demais
Essa falha é uma falha no lado do servidor. Indica que você consumiu sua taxa de transferência provisionada. Tente novamente mais tarde. Se você recebe essa falha com frequência, considere aumentar a taxa de transferência da coleção.

* **Implementar a retirada em intervalos de getRetryAfterInMilliseconds**

    Durante o teste de desempenho, você deve aumentar a carga até que uma pequena taxa de solicitações seja restringida. Se restringida, o aplicativo cliente deve retirar a limitação para a nova tentativa do servidor especificado. Respeitar a retirada garante que você perca o mínimo de tempo de espera entre as tentativas.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Falha ao conectar-se ao emulador de Azure Cosmos DB

O certificado HTTPS do emulador de Azure Cosmos DB é autoassinado. Para o SDK funcionar com o emulador, importe o certificado do emulador para um Java TrustStore. Para obter mais informações, consulte [Exportar certificados do emulador Azure Cosmos DB](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problemas de conflito de dependência

O SDK do Java Azure Cosmos DB recebe várias dependências. Em termos gerais, se a árvore de dependências do projeto incluir uma versão mais antiga de um artefato do qual o SDK do Java do Azure Cosmos DB depende, isso poderá resultar na geração de erros inesperados ao executar o aplicativo. Se estiver depurando o motivo de seu aplicativo lançar uma exceção inesperadamente, verifique se a árvore de dependências não está trazendo acidentalmente uma versão mais antiga de uma ou mais dependências do SDK do Java do Azure Cosmos DB.

A solução alternativa para esse problema é identificar qual das dependências do projeto traz a versão antiga, excluir a dependência transitiva nessa versão mais antiga e permitir que o SDK do Java do Azure Cosmos DB traga a versão mais recente.

Para identificar qual das dependências do projeto traz uma versão antiga de algo ao qual o SDK do Java do Azure Cosmos DB depende, execute o seguinte comando no arquivo pom.xml do projeto:
```bash
mvn dependency:tree
```
Para obter mais informações, consulte o [guia de árvore de dependências do Maven](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Depois de saber qual dependência do projeto depende de uma versão antiga, você pode modificar a dependência nesse lib no arquivo pom e excluir a dependência transitiva, seguindo o exemplo abaixo (que pressupõe que *reator-core* é a dependência desatualizada):

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Para obter mais informações, consulte o [guia Excluir dependência transitiva](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Habilitar o log do SDK do cliente

O SDK do Java v4 do Azure Cosmos DB usa SLF4 como a fachada de log que oferece suporte ao log em estruturas de log populares, como log4j e logback.

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

# Set root logger level to INFO and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.azure.cosmos=INFO
#log4j.category.io.netty=OFF
#log4j.category.io.projectreactor=OFF
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

No Windows, você pode executar o mesmo comando com sinalizadores de argumento diferentes:
```bash
netstat -abn
```

Filtre o resultado somente para conexões ao ponto de extremidade do Azure Cosmos DB.

O número de conexões ao ponto de extremidade do Azure Cosmos DB no `ESTABLISHED` estado não deve ser maior que o tamanho do pool de conexão configurado.

Muitas conexões para o ponto de extremidade do Azure Cosmos DB podem estar no `CLOSE_WAIT` estado. Pode haver mais de 1.000. Um número tão alto indica que as conexões são estabelecidas e interrompidas rapidamente. Essa situação potencialmente causa problemas. Para obter mais informações, consulte a seção [Problemas comuns e soluções alternativas].

 <!--Anchors-->
[Problemas comuns e soluções alternativas]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limite de conexão no computador host]: #connection-limit-on-host
[Esgotamento da porta SNAT (PAT) do Azure]: #snat
