---
title: Solucionar problemas Azure Cosmos DB HTTP 408 ou solicitar problemas de tempo limite com o SDK do Java v4
description: Saiba como diagnosticar e corrigir exceções de tempo limite de solicitação do SDK do Java com o SDK do Java v4.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a805300ac62d0627c9b06188c9764a6887947afe
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411279"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Diagnosticar e solucionar problemas Azure Cosmos DB exceções de tempo limite de solicitação do SDK do Java v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O erro HTTP 408 ocorrerá se o SDK não puder concluir a solicitação antes da ocorrência do tempo limite.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A lista a seguir contém causas conhecidas e soluções para exceções de tempo limite de solicitação.

### <a name="existing-issues"></a>Problemas existentes
Se você estiver vendo solicitações ficando presas por duração maior ou atingir o tempo limite com mais frequência, atualize o SDK do Java v4 para a versão mais recente. Observação: é altamente recomendável usar a versão 4.7.0 e posterior. Faça checkout das [notas de versão do SDK do Java v4](sql-api-sdk-java-v4.md) para obter mais detalhes.

### <a name="high-cpu-utilization"></a>Alta utilização da CPU
A alta utilização da CPU é o caso mais comum. Para uma latência ideal, o uso da CPU deve ser de aproximadamente 40%. Use 10 segundos como o intervalo para monitorar a utilização máxima (não média) da CPU. Os picos de CPU são mais comuns com consultas entre partições, em que ele pode fazer várias conexões para uma única consulta.

#### <a name="solution"></a>Solução:
O aplicativo cliente que usa o SDK deve ser expandido ou reduzido.

### <a name="connection-throttling"></a>Limitação de conexão
A limitação de conexão pode ocorrer devido a um limite de conexão no computador host ou a Esgotamento da porta SNAT (PAT) do Azure.

### <a name="connection-limit-on-a-host-machine"></a>Limite de conexão no computador host
Alguns sistemas Linux, como Red Hat, têm um limite superior para o número total de arquivos abertos. Soquetes no Linux são implementados como arquivos, portanto, esse número limita o número total de conexões também. Execute o comando a seguir.

```bash
ulimit -a
```

#### <a name="solution"></a>Solução:
O número máximo de arquivos abertos permitidos, que são identificados como "nofile", precisa ser pelo menos 10.000 ou mais. Para obter mais informações, consulte as [dicas de desempenho](performance-tips-java-sdk-v4-sql.md) do SDK do Java v4 do Azure Cosmos DB.

### <a name="socket-or-port-availability-might-be-low"></a>A disponibilidade de soquete ou porta pode estar baixa
Durante a execução no Azure, os clientes que usam o SDK do Java podem atingir o esgotamento de porta de SNAT do Azure (PAT).

#### <a name="solution-1"></a>Solução 1:
Se você estiver executando em VMs do Azure, siga o [Guia de esgotamento de porta SNAT](troubleshoot-java-sdk-v4-sql.md#snat).

#### <a name="solution-2"></a>Solução 2:
Se você estiver executando o serviço Azure App, siga o [Guia de solução de problemas de erros de conexão](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) e use o diagnóstico do serviço de [aplicativo](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Solução 3:
Se você estiver executando o Azure Functions, verifique se está seguindo a [recomendação Azure Functions](../azure-functions/manage-connections.md#static-clients) de manter clientes singleton ou estáticos para todos os serviços envolvidos (incluindo Azure Cosmos DB). Verifique os [limites de serviço](../azure-functions/functions-scale.md#service-limits) com base no tipo e no tamanho de sua hospedagem de aplicativo de funções.

#### <a name="solution-4"></a>Solução 4:
Se você usar um proxy HTTP, certifique-se que pode suportar o número de conexões configuradas no SDK `GatewayConnectionConfig`. Caso contrário, você enfrentará problemas de conexão.

### <a name="create-multiple-client-instances"></a>Criar várias instâncias de cliente
A criação de várias instâncias de cliente pode levar à contenção de conexão e a problemas de tempo limite.

#### <a name="solution-1"></a>Solução 1:
Siga as [dicas de desempenho](performance-tips-java-sdk-v4-sql.md#sdk-usage)e use uma única instância de CosmosClient em todo o aplicativo.

#### <a name="solution-2"></a>Solução 2:
Se CosmosClient singleton não for possível ter em um aplicativo, é recomendável usar o compartilhamento de conexão entre vários clientes Cosmos por meio dessa API `connectionSharingAcrossClientsEnabled(true)` no CosmosClient. Quando você tiver várias instâncias do cliente Cosmos na mesma interagindo com várias contas do cosmos, habilitar isso permitirá o compartilhamento de conexão no modo direto, se possível, entre instâncias do cliente Cosmos. Observe que, ao definir essa opção, a configuração de conexão (por exemplo, configuração de tempo limite de soquete, configuração de tempo limite ocioso) do primeiro cliente instanciado será usada para todas as outras instâncias de cliente.

### <a name="hot-partition-key"></a>Chave de partição ativa
Azure Cosmos DB distribui a taxa de transferência provisionada de maneira uniforme entre as partições físicas. Quando há uma partição ativa, uma ou mais chaves de partição lógica em uma partição física estão consumindo todas as unidades de solicitação por segundo da partição física (RU/s). Ao mesmo tempo, as RU/s em outras partições físicas ficarão não utilizadas. Como sintoma, o total de RU/s consumido será menor do que o RU/s provisionado geral no banco de dados ou contêiner, mas você ainda verá limitação (429s) nas solicitações em relação à chave de partição lógica ativa. Use a [métrica de consumo de ru normalizada](monitor-normalized-request-units.md) para ver se a carga de trabalho está encontrando uma partição ativa. 

#### <a name="solution"></a>Solução:
Escolha uma boa chave de partição que distribua uniformemente o volume de solicitação e o armazenamento. Saiba como [alterar sua chave de partição](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="high-degree-of-concurrency"></a>Alto grau de simultaneidade
O aplicativo está fazendo um alto nível de simultaneidade, o que pode levar à contenção no canal.

#### <a name="solution"></a>Solução:
O aplicativo cliente que usa o SDK deve ser expandido ou reduzido.

### <a name="large-requests-or-responses"></a>Solicitações ou respostas grandes
Solicitações ou respostas grandes podem levar ao bloqueio de cabeçalho de linha no canal e exacerbar a contenção, mesmo com um grau relativamente baixo de simultaneidade.

#### <a name="solution"></a>Solução:
O aplicativo cliente que usa o SDK deve ser expandido ou reduzido.

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>A taxa de falha está dentro do SLA de Azure Cosmos DB
O aplicativo deve ser capaz de lidar com falhas transitórias e tentar novamente quando necessário. As exceções 408 não são repetidas porque, em Create Paths, é impossível saber se o serviço criou o item ou não. O envio do mesmo item novamente para Create causará uma exceção de conflito. A lógica de negócios dos aplicativos do usuário pode ter uma lógica personalizada para lidar com conflitos, o que pode ser interrompido da ambiguidade de um item existente em comparação com uma repetição de criação.

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>A taxa de falha viola o SLA de Azure Cosmos DB
Contate o [suporte do Azure](https://aka.ms/azure-support).

## <a name="next-steps"></a>Próximas etapas
* [Diagnostique e solucione](troubleshoot-java-sdk-v4-sql.md) problemas ao usar o SDK do Azure Cosmos DB Java v4.
* Saiba mais sobre as diretrizes de desempenho para [Java v4](performance-tips-java-sdk-v4-sql.md).
