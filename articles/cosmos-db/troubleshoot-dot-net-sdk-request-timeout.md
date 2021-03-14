---
title: Solucionar problemas Azure Cosmos DB HTTP 408 ou solicitar problemas de tempo limite com o SDK do .NET
description: Saiba como diagnosticar e corrigir exceções de tempo limite de solicitação do SDK do .NET.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 03/05/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: c8d35f7c666562022f503b2777f30f84193d0231
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439996"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>Diagnosticar e solucionar problemas Azure Cosmos DB exceções de tempo limite de solicitação do SDK .NET
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O erro HTTP 408 ocorrerá se o SDK não puder concluir a solicitação antes da ocorrência do tempo limite.

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>Personalizar o tempo limite no SDK do .NET Azure Cosmos DB

O SDK tem duas alternativas distintas para controlar os tempos limite, cada uma com um escopo diferente.

### <a name="requesttimeout"></a>RequestTimeout

A `CosmosClientOptions.RequestTimeout` configuração (ou `ConnectionPolicy.RequestTimeout` para o SDK v2) permite definir um tempo limite que afeta cada solicitação de rede individual. Uma operação iniciada por um usuário pode abranger várias solicitações de rede (por exemplo, pode haver limitação). Essa configuração se aplicaria a cada solicitação de rede na nova tentativa. Esse tempo limite não é um tempo limite de solicitação de operação de ponta a ponta.

### <a name="cancellationtoken"></a>CancellationToken

Todas as operações assíncronas no SDK têm um parâmetro CancellationToken opcional. Esse parâmetro [CancellationToken](/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) é usado em toda a operação, em todas as solicitações de rede. Entre as solicitações de rede, o token de cancelamento pode estar marcado e uma operação cancelada se o token relacionado tiver expirado. O token de cancelamento deve ser usado para definir um tempo limite aproximado esperado no escopo da operação.

> [!NOTE]
> O `CancellationToken` parâmetro é um mecanismo em que a biblioteca verificará o cancelamento quando [não causar um estado inválido](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). A operação pode não ser cancelada exatamente quando o tempo definido no cancelamento estiver ativo. Em vez disso, depois que o tempo for ativado, ele cancelará quando for seguro fazê-lo.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A lista a seguir contém causas conhecidas e soluções para exceções de tempo limite de solicitação.

### <a name="high-cpu-utilization"></a>Alta utilização da CPU
A alta utilização da CPU é o caso mais comum. Para uma latência ideal, o uso da CPU deve ser de aproximadamente 40%. Use 10 segundos como o intervalo para monitorar a utilização máxima (não média) da CPU. Os picos de CPU são mais comuns com consultas entre partições, em que ele pode fazer várias conexões para uma única consulta.

Se o erro contiver `TransportException` informações, ele também poderá conter `CPU History` :

```
CPU history: 
(2020-08-28T00:40:09.1769900Z 0.114), 
(2020-08-28T00:40:19.1763818Z 1.732), 
(2020-08-28T00:40:29.1759235Z 0.000), 
(2020-08-28T00:40:39.1763208Z 0.063), 
(2020-08-28T00:40:49.1767057Z 0.648), 
(2020-08-28T00:40:59.1689401Z 0.137), 
CPU count: 8)
```

* Se as medições de CPU estiverem acima de 70%, o tempo limite provavelmente será causado pelo esgotamento da CPU. Nesse caso, a solução é investigar a origem da alta utilização da CPU e reduzi-la, ou dimensionar a máquina para um tamanho de recurso maior.
* Se as medições da CPU não estiverem ocorrendo a cada 10 segundos (por exemplo, intervalos ou tempos de medição indicam tempos maiores entre as medições), a causa será privação de thread. Nesse caso, a solução é investigar as origens da privação de thread (threads potencialmente bloqueados) ou dimensionar as máquinas para um tamanho de recurso maior.

#### <a name="solution"></a>Solução:
O aplicativo cliente que usa o SDK deve ser expandido ou reduzido.

### <a name="socket-or-port-availability-might-be-low"></a>A disponibilidade de soquete ou porta pode estar baixa
Ao executar no Azure, os clientes que usam o SDK do .NET podem atingir o esgotamento de porta de SNAT do Azure (PAT).

#### <a name="solution-1"></a>Solução 1:
Se você estiver executando em VMs do Azure, siga o [Guia de esgotamento de porta SNAT](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Solução 2:
Se você estiver executando o serviço Azure App, siga o [Guia de solução de problemas de erros de conexão](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) e use o diagnóstico do serviço de [aplicativo](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Solução 3:
Se você estiver executando o Azure Functions, verifique se está seguindo a [recomendação Azure Functions](../azure-functions/manage-connections.md#static-clients) de manter clientes singleton ou estáticos para todos os serviços envolvidos (incluindo Azure Cosmos DB). Verifique os [limites de serviço](../azure-functions/functions-scale.md#service-limits) com base no tipo e no tamanho de sua hospedagem de aplicativo de funções.

#### <a name="solution-4"></a>Solução 4:
Se você usar um proxy HTTP, certifique-se que pode suportar o número de conexões configuradas no SDK `ConnectionPolicy`. Caso contrário, você enfrentará problemas de conexão.

### <a name="create-multiple-client-instances"></a>Criar várias instâncias de cliente
A criação de várias instâncias de cliente pode levar à contenção de conexão e a problemas de tempo limite.

#### <a name="solution"></a>Solução:
Siga as [dicas de desempenho](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)e use uma única instância de CosmosClient em todo o processo.

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
* [Diagnostique e solucione](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB.
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md).