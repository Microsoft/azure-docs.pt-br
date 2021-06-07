---
title: Como escalar de forma elástica com a API do Cassandra no Azure Cosmos DB
description: Saiba mais sobre as opções disponíveis para dimensionar uma conta de API do Cassandra do Azure Cosmos DB e suas vantagens/desvantagens
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: thvankra
ms.openlocfilehash: aad2e80598146be7b45a8a7b8a02cfe050163102
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340933"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Como escalar de forma elástica uma conta da API do Cassandra do Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Há uma variedade de opções para explorar a natureza elástica da API do Azure Cosmos DB para Cassandra. Para entender como dimensionar de forma eficaz no Azure Cosmos DB, é importante entender como provisionar a quantidade certa de unidades de solicitação (RU/s) para considerar as demandas de desempenho em seu sistema. Para saber mais sobre unidades de solicitação, consulte o artigo [Unidades de solicitação](request-units.md). 

Para a API do Cassandra, você pode recuperar o encargo de unidade de solicitação para consultas individuais usando o [.NET e SDKs Java](./find-request-unit-charge-cassandra.md). Isso é útil para determinar a quantidade de RU/s que você precisará provisionar no serviço.

:::image type="content" source="./media/request-units/request-units.png" alt-text="Operações do banco de dados consomem Unidades de Solicitação" border="false":::

## <a name="handling-rate-limiting-429-errors"></a>Limitação da taxa de manipulação (erros 429)

O Azure Cosmos DB retornará erros de taxa limitada (429) se os clientes consumirem mais recursos (RU/s) do que o valor que você provisionou. A API do Cassandra do Azure Cosmos DB converte essas exceções em erros de sobrecarga no protocolo nativo do Cassandra. 

Se o sistema não for sensível à latência, poderá ser suficiente lidar com a limitação da taxa de transferência usando repetições. Consulte o [exemplo de código Java](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) para saber como manipular a limitação de taxa de forma transparente usando a [extensão do Azure Cosmos DB](https://github.com/Azure/azure-cosmos-cassandra-extensions) para a [política de repetição do Cassandra](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) em Java. Você também pode usar a [extensão do Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) para lidar com a limitação de taxa.

## <a name="manage-scaling"></a>Gerencie o dimensionamento

Se você precisar minimizar a latência, há um espectro de opções para gerenciar a escala e a taxa de transferência de provisionamento (RUs) na API do Cassandra:

* [Manualmente, usando o portal do Azure](#use-azure-portal)
* [Programaticamente, usando os recursos do painel de controle](#use-control-plane)
* [Programaticamente, usando comandos CQL com um SDK específico](#use-cql-queries)
* [Dinamicamente, usando autoescala](#use-autoscale)

As seções a seguir explicam as vantagens e desvantagens de cada abordagem. Em seguida, você pode decidir sobre a melhor estratégia para equilibrar as necessidades de dimensionamento do seu sistema, o custo geral e as necessidades de eficiência para a sua solução.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Use o portal do Azure

Você pode dimensionar os recursos na conta da API do Cassandra do Azure Cosmos DB usando o portal do Azure. Para saber mais, consulte o artigo sobre [Taxa de transferência de provisionamento em contêineres e bancos de dados](set-throughput.md). Este artigo explica os benefícios relativos de definir a taxa de transferência em [banco de dados](set-throughput.md#set-throughput-on-a-database) ou [contêineres](set-throughput.md#set-throughput-on-a-container) no portal do Azure. Os termos "banco de dados" e "contêiner" mencionados nesses artigos servem, respectivamente, como mapa para o "keyspace" e a "tabela" da API do Cassandra.

A vantagem desse método é que ele é uma maneira simples e completa de gerenciar a capacidade de taxa de transferência no banco de dados. No entanto, a desvantagem é que, em muitos casos, sua abordagem de dimensionamento pode exigir que determinados níveis de automação sejam econômicos e de alto desempenho. As próximas seções explicam os cenários e os métodos relevantes.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Use o painel de controle

A API do Azure Cosmos DB para Cassandra permite ajustar a taxa de transferência de forma programática usando nossos vários recursos de painel de controle. Consulte os artigos do [Azure Resource Manager](./templates-samples-cassandra.md), [PowerShell](powershell-samples.md) e [Azure CLI](cli-samples.md) para obter orientações e exemplos.

A vantagem desse método é que você pode automatizar a expansão ou redução dos recursos com base em um temporizador para considerar a atividade de pico ou períodos de baixa atividade. Dê uma olhada em nosso exemplo [aqui](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) para saber como fazer isso usando o Azure Functions e o PowerShell.

Uma desvantagem dessa abordagem é que talvez você não possa responder a necessidades de alteração de escala imprevisíveis em tempo real. Em vez disso, talvez seja necessário aproveitar o contexto do aplicativo em seu sistema, no nível do cliente/SDK ou usando o [dimensionamento automático](provision-throughput-autoscale.md).

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>Use consultas CQL com um SDK específico

Você pode dimensionar o sistema dinamicamente com código executando os [comandos CQL ALTER](cassandra-support.md#keyspace-and-table-options) para o banco de dados ou contêiner fornecido.

A vantagem dessa abordagem é que ela permite que você responda às necessidades de dimensionamento dinamicamente e de uma maneira personalizada adequada ao seu aplicativo. Com essa abordagem, você ainda pode aproveitar os encargos e as tarifas de RU/s padrão. Se as necessidades de dimensionamento do seu sistema forem principalmente previsíveis (cerca de 70% ou mais), usar o SDK com CQL pode ser um método mais econômico de dimensionamento automático do que o uso do recurso de dimensionamento automático. A desvantagem dessa abordagem é que ela pode ser bastante complexa para implementar repetições, enquanto a limitação de taxa pode aumentar a latência.

## <a name="use-autoscale-provisioned-throughput"></a><a id="use-autoscale"></a>Use a taxa de transferência provisionada de dimensionamento automático

Além da maneira padrão (manual) ou programática de provisionar a taxa de transferência, você também pode configurar contêineres do Azure Cosmos na taxa de transferência provisionada de dimensionamento automático. O dimensionamento automático será escalado de forma automática e instantânea para suas necessidades de consumo dentro dos intervalos de RU especificados sem comprometer os SLAs. Para saber mais, consulte o artigo [Criar contêineres e bancos de dados do Azure Cosmos no dimensionamento automático](provision-throughput-autoscale.md).

A vantagem dessa abordagem é que ela é a maneira mais fácil de gerenciar as necessidades de dimensionamento em seu sistema. Ele não aplicará a limitação de taxa **dentro dos intervalos de ru configurados**. A desvantagem é que, se as necessidades de dimensionamento em seu sistema forem previsíveis, o dimensionamento automático poderá ser uma maneira menos econômica de lidar com suas necessidades de dimensionamento do que usar as abordagens de nível personalizadas do painel de controle ou do SDK mencionadas acima.

Para definir ou alterar a taxa de transferência máxima (RUs) para dimensionamento automático usando CQL, use o seguinte (substituindo adequadamente o nome de keyspace/tabela):

```Bash
# to set max throughput (RUs) for autoscale at keyspace level:
create keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at keyspace level:
alter keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=4000;

# to set max throughput (RUs) for autoscale at table level:
create table <keyspace name>.<table name> (pk int PRIMARY KEY, ck int) WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at table level:
alter table <keyspace name>.<table name> WITH cosmosdb_autoscale_max_throughput=4000;
```

## <a name="next-steps"></a>Próximas etapas

- Introdução à [criação de uma conta de API do Cassandra, banco de dados e uma tabela](create-cassandra-api-account-java.md) pelo uso de um aplicativo Java