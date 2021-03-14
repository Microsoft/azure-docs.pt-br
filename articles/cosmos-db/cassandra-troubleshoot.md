---
title: Solucionar erros comuns no Azure Cosmos DB API do Cassandra
description: Este documento discute as maneiras de solucionar problemas comuns encontrados no Azure Cosmos DB API do Cassandra
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: f9b6e586879b8697660ced7aa6f1e75083e3ee29
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658564"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Solucionar problemas comuns no Azure Cosmos DB API do Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API do Cassandra no Azure Cosmos DB é uma camada de compatibilidade, que fornece [suporte de protocolo de transmissão](cassandra-support.md) para o popular banco de dados Apache Cassandra de software livre e é alimentado por [Azure Cosmos DB](./introduction.md). Como um serviço nativo de nuvem totalmente gerenciado, a Azure Cosmos DB fornece [garantias sobre disponibilidade, taxa de transferência e consistência](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) para API do Cassandra. Essas garantias não são possíveis em implementações herdadas do Apache Cassandra. API do Cassandra também facilita operações de plataforma sem manutenção e patches sem tempo de inatividade. Assim, muitas das operações de back-end são diferentes do Apache Cassandra, portanto, recomendamos configurações e abordagens específicas para evitar erros comuns. 

Este artigo descreve os erros comuns e as soluções para aplicativos que consomem Azure Cosmos DB API do Cassandra. Se o seu erro não estiver listado abaixo, e você estiver recebendo um erro ao executar uma [operação com suporte no API do Cassandra](cassandra-support.md), em que o erro *não está presente ao usar o Apache Cassandra nativo*, [crie uma solicitação de suporte do Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="nonodeavailableexception"></a>NoNodeAvailableException
Essa é uma exceção de wrapper de nível superior com um grande número de possíveis causas e exceções internas, muitas das quais podem ser relacionadas ao cliente. 
### <a name="solution"></a>Solução
Algumas causas e soluções populares são as seguintes: 
- Tempo limite de ociosidade dos balanceadores do Azure: isso também pode ser manifestado como `ClosedConnectionException` . Para resolver isso, defina a configuração Keep Alive no driver (veja [abaixo](#enable-keep-alive-for-java-driver)) e aumente as configurações Keep-Alive no sistema operacional ou [ajuste o tempo limite de ociosidade no Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). 
- **Esgotamento de recursos do aplicativo cliente:** Verifique se os computadores cliente têm recursos suficientes para concluir a solicitação. 

## <a name="cannot-connect-to-host"></a>Não é possível conectar ao host
Você pode ver este erro: `Cannot connect to any host, scheduling retry in 600000 milliseconds` . 

### <a name="solution"></a>Solução
Isso pode ser o esgotamento de SNAT no lado do cliente. Siga as etapas em [SNAT para conexões de saída](../load-balancer/load-balancer-outbound-connections.md) para eliminar esse problema. Isso também pode ser um problema de tempo limite de ociosidade em que o balanceador de carga do Azure tem 4 minutos de tempo limite de ociosidade por padrão. Consulte a documentação no [tempo limite de ociosidade do balanceador de carga](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). Habilite o TCP-Keep Alive nas configurações do driver (veja [abaixo](#enable-keep-alive-for-java-driver)) e defina o `keepAlive` intervalo no sistema operacional para menos de 4 minutos.

 

## <a name="overloadedexception-java"></a>Com sobrecarregamentoexception (Java)
O número total de unidades de solicitação consumidas é maior do que as unidades de solicitação provisionadas na tabela ou no espaço de keyspace. Portanto, as solicitações são limitadas.
### <a name="solution"></a>Solução
Considere dimensionar a taxa de transferência atribuída a um keyspace ou uma tabela do portal do Azure (consulte [aqui](manage-scale-cassandra.md) para operações de dimensionamento no API do Cassandra) ou você pode implementar uma política de repetição. Para Java, consulte amostras de repetição para [Driver v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) e [Driver v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Consulte também [extensões do Cassandra do Azure Cosmos para Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-even-with-sufficient-throughput"></a>Sobreloadedexception mesmo com taxa de transferência suficiente 
O sistema parece estar limitando as solicitações, apesar da taxa de transferência suficiente ser provisionada para o volume de solicitação e/ou o custo de unidade de solicitação consumido. Há duas causas possíveis de limitação de taxa inesperada:
- **Operações de nível de esquema:** API do Cassandra implementa um orçamento de taxa de transferência do sistema para operações de nível de esquema (CREATE TABLE, ALTER TABLE, DROP TABLE). Esse orçamento deve ser suficiente para operações de esquema em um sistema de produção. No entanto, se você tiver um grande número de operações de nível de esquema, é possível que você esteja excedendo esse limite. Como esse orçamento não é controlado pelo usuário, você precisará considerar reduzir o número de operações de esquema que estão sendo executadas. Se a execução dessa ação não resolver o problema, ou não for viável para sua carga de trabalho, [crie uma solicitação de suporte do Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).
- **Distorção de dados:** quando a taxa de transferência é provisionada no API do Cassandra, ela é dividida igualmente entre as partições físicas e cada partição física tem um limite superior. Se você tiver uma grande quantidade de dados sendo inseridos ou consultados de uma partição específica, será possível ter uma taxa limitada, apesar de o provisionamento de uma grande quantidade de produtividade geral (unidades de solicitação) para essa tabela. Examine seu modelo de dados e verifique se você não tem uma distorção excessiva que pode estar causando partições ativas. 

## <a name="intermittent-connectivity-errors-java"></a>Erros de conectividade intermitente (Java) 
A conexão cai ou expira inesperadamente.

### <a name="solution"></a>Solução 
Os drivers do Apache Cassandra para Java fornecem duas políticas de reconexão nativas: `ExponentialReconnectionPolicy` e `ConstantReconnectionPolicy` . O padrão é `ExponentialReconnectionPolicy`. No entanto, para Azure Cosmos DB API do Cassandra, é recomendável ter `ConstantReconnectionPolicy` um atraso de 2 segundos. Consulte a [documentação do driver](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  para o driver Java v4. x e [aqui](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) para obter as diretrizes do Java 3. x, consulte também [Configurando o ReconnectionPolicy para exemplos de driver Java](#configuring-reconnectionpolicy-for-java-driver) abaixo.

## <a name="error-with-load-balancing-policy"></a>Erro com a política de balanceamento de carga

Se você tiver implementado uma política de balanceamento de carga no v3. x do driver Datastax do Java, com um código semelhante ao seguinte:

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

Se o valor de não `withLocalDc()` corresponder ao Datacenter do ponto de contato, você poderá enfrentar um erro muito intermitente: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` . 

### <a name="solution"></a>Solução 
Implemente [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java) (talvez seja necessário atualizar a versão secundária do datastax para fazê-lo funcionar):

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="count-fails-on-large-table"></a>A contagem falha em uma tabela grande
Ao executar `select count(*) from table` ou semelhante para um grande número de linhas, o servidor atinge o tempo limite.

### <a name="solution"></a>Solução 
Se estiver usando um cliente CQLSH local, você poderá tentar alterar `--connect-timeout` as `--request-timeout` configurações ou (veja mais detalhes [aqui](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)). Se isso não for suficiente e a contagem ainda estiver expirando, você poderá obter uma contagem de registros da telemetria de Azure Cosmos DB back-end acessando a guia métricas em portal do Azure, selecionando a métrica `document count` e, em seguida, adicionando um filtro para o banco de dados ou a coleção (a analogia da tabela em Azure Cosmos DB). Em seguida, você pode passar o mouse sobre o grafo resultante para o ponto no tempo em que você deseja uma contagem do número de registros.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="exibição de métricas":::


## <a name="configuring-reconnectionpolicy-for-java-driver"></a>Configurando o ReconnectionPolicy para driver Java

### <a name="version-3x"></a>Versão 3.x

Para a versão 3. x do driver do Java, configure a política de reconexão ao criar um objeto de cluster:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Versão 4. x

Para a versão 4. x do driver do Java, configure a política de reconexão substituindo as configurações no `reference.conf` arquivo:

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-java-driver"></a>Habilitar Keep-Alive para o driver Java

### <a name="version-3x"></a>Versão 3.x

Para a versão 3. x do driver do Java, defina Keep-Alive ao criar um objeto de cluster e verifique se Keep-Alive está [habilitado no sistema operacional](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>Versão 4. x

Para a versão 4. x do driver do Java, defina Keep-Alive substituindo as configurações no `reference.conf` e verifique se Keep-Alive está [habilitado no sistema operacional](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [recursos com suporte](cassandra-support.md) no API do Cassandra Azure Cosmos DB.
- Saiba como [migrar do Apache Cassandra nativo para Azure Cosmos DB API do Cassandra](cassandra-migrate-cosmos-db-databricks.md)
