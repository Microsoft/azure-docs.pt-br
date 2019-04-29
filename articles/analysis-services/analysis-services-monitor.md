---
title: Monitorar métricas do servidor do Azure Analysis Services | Microsoft Docs
description: Saiba como monitorar as métricas do servidor do Analysis Services no portal do Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cdffa8e138062a91bd1876ac6e44728c47d9cdd7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61065027"
---
# <a name="monitor-server-metrics"></a>Monitorar métricas do servidor

O Analysis Services fornece métricas para ajudá-lo a monitorar o desempenho e a integridade de seus servidores. Por exemplo, monitore a memória e o uso da CPU, o número de conexões de cliente e o consumo de recursos de consulta. O Analysis Services usa a mesma estrutura de monitoramento que a maioria dos outros serviços do Azure. Para saber mais, confira [Métricas no Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Para executar diagnóstico mais detalhado, rastrear o desempenho e identificar tendências em vários recursos de serviço em um grupo de recursos ou assinatura, use o [Azure Monitor](https://azure.microsoft.com/services/monitor/). Azure Monitor (serviço) pode resultar em um serviço faturável.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Para monitorar as métricas para um servidor do Analysis Services

1. No portal do Azure, selecione **Métricas**.

    ![Monitorar no Portal do Azure](./media/analysis-services-monitor/aas-monitor-portal.png)

2. Na lista de **Métricas disponíveis**, selecione as métricas a incluir no seu gráfico. 

    ![Gráfico do monitor](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Métricas do servidor

Use essa tabela para determinar quais métricas são melhores para o seu cenário de monitoramento. Apenas as métricas da mesma unidade podem ser mostradas no mesmo gráfico.

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|DESCRIÇÃO|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Comprimento da fila de trabalho do pool de comando|Contagem|Média|Número de trabalhos na fila do pool de threads de comando.|
|CurrentConnections|Conexão: Conexões atuais|Contagem|Média|Número atual de conexões de cliente estabelecidas.|
|CurrentUserSessions|Sessões de usuário atuais|Contagem|Média|Número atual de sessões de usuário estabelecidas.|
|mashup_engine_memory_metric|Memória do mecanismo M|Bytes|Média|Uso de memória pelos processos do mecanismo de mashup|
|mashup_engine_qpu_metric|QPU do mecanismo M|Contagem|Média|Uso de QPU por processos de mecanismo de mashup|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0 a 25 GB para S1, 0 a 50 GB para S2 e 0 a 100 GB para S4|
|memory_thrashing_metric|Sobrecarga de memória|Porcentagem|Média|Sobrecarga de memória média.|
|CleanerCurrentPrice|Memória: Preço atual do limpador|Contagem|Média|Preço atual da memória, $/byte/tempo, normalizado em 1000.|
|CleanerMemoryNonshrinkable|Memória: Memória do limpador não reduzível|Bytes|Média|Quantidade de memória, em bytes, não sujeita a eliminação pelo limpador na tela de fundo.|
|CleanerMemoryShrinkable|Memória: Memória do limpador reduzível|Bytes|Média|Quantidade de memória, em bytes, sujeita a eliminação pelo limpador na tela de fundo.|
|MemoryLimitHard|Memória: Limite de memória física|Bytes|Média|Limite de memória física, do arquivo de configuração.|
|MemoryLimitHigh|Memória: Limite de memória superior|Bytes|Média|Limite de memória superior, do arquivo de configuração.|
|MemoryLimitLow|Memória: Limite de memória inferior|Bytes|Média|Limite de memória inferior, do arquivo de configuração.|
|MemoryLimitVertiPaq|Memória: Limite de memória VertiPaq|Bytes|Média|Limite na memória, do arquivo de configuração.|
|MemoryUsage|Memória: Uso de Memória|Bytes|Média|Uso de memória do processo do servidor, como usado no cálculo de preço de memória do limpador. Igual ao contador Process\PrivateBytes mais o tamanho dos dados mapeados em memória, ignorando qualquer memória mapeada ou alocada pelo mecanismo de análise in-memory (VertiPaq), além do Limite de Memória do mecanismo.|
|Quota|Memória: Quota|Bytes|Média|Cota de memória atual, em bytes. A cota de memória também é conhecida como uma reserva de memória ou concessão de memória.|
|QuotaBlocked|Memória: Cota bloqueada|Contagem|Média|Número atual de solicitações de cota bloqueadas até que outras cotas de memória sejam liberadas.|
|VertiPaqNonpaged|Memória: VertiPaq não paginado|Bytes|Média|Bytes de memória bloqueada no conjunto de trabalho para uso pelo mecanismo na memória.|
|VertiPaqPaged|Memória: VertiPaq paginado|Bytes|Média|Bytes de memória paginada em uso para dados na memória.|
|ProcessingPoolJobQueueLength|Comprimento da fila de trabalho do pool de processamento|Contagem|Média|Número de trabalhos não de E/S na fila do pool de threads de processamento.|
|RowsConvertedPerSec|Processamento: Linhas convertidas por segundo|CountPerSecond|Média|Taxa de linhas convertidas durante o processamento.|
|RowsReadPerSec|Processamento: Linhas lidas por segundo|CountPerSecond|Média|Taxa de linhas lidas de todos os bancos de dados relacionais.|
|RowsWrittenPerSec|Processamento: Linhas gravadas por segundo|CountPerSecond|Média|Taxa de linhas gravadas durante o processamento.|
|qpu_metric|QPU|Contagem|Média|QPU. Intervalo de 0 a 100 para S1, 0 a 200 para S2 e 0 a 400 para S4|
|QueryPoolBusyThreads|Threads ocupados do pool de consulta|Contagem|Média|Número de threads ocupados no pool de threads de consulta.|
|SuccessfullConnectionsPerSec|Conexões bem-sucedidas por segundo|CountPerSecond|Média|Taxa de conclusões de conexão bem-sucedidas.|
|CommandPoolBusyThreads|Threads: Threads ocupados do pool comando|Contagem|Média|Número de threads ocupados no pool de threads de comando.|
|CommandPoolIdleThreads|Threads: Threads ociosos do pool comando|Contagem|Média|Número de threads ociosos no pool de threads de comando.|
|LongParsingBusyThreads|Threads: Threads ocupados de análise longa|Contagem|Média|Número de threads ocupados no pool de threads de análise longa.|
|LongParsingIdleThreads|Threads: Threads ociosos de análise longa|Contagem|Média|Número de threads ociosos no pool de threads de análise longa.|
|LongParsingJobQueueLength|Threads: Tamanho da fila de trabalhos de análise longa|Contagem|Média|Número de trabalhos na fila do pool de threads de análise longa.|
|ProcessingPoolIOJobQueueLength|Threads: Comprimento da fila de trabalho de E/S do pool de processamento|Contagem|Média|Número de trabalhos de E/S na fila do pool de threads de processamento.|
|ProcessingPoolBusyIOJobThreads|Threads: Threads de trabalho de E/S ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos de E/S no pool de threads de processamento.|
|ProcessingPoolBusyNonIOThreads|Threads: Threads de trabalho não E/S ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos não E/S no pool de threads de processamento.|
|ProcessingPoolIdleIOJobThreads|Threads: Threads de trabalho de E/S ociosos do pool de processamento|Contagem|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|
|ProcessingPoolIdleNonIOThreads|Threads: Threads de trabalho não E/S ociosos do pool de processamento|Contagem|Média|Número de threads ociosos no pool de threads de processamento dedicado a trabalhos não E/S.|
|QueryPoolIdleThreads|Threads: Threads ociosos do pool de consultas|Contagem|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|
|QueryPoolJobQueueLength|Threads: Tamanho da fila de trabalhos do pool consultas|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|
|ShortParsingBusyThreads|Threads: Threads ocupados de análise resumida|Contagem|Média|Número de threads ocupados no pool de threads de análise resumida.|
|ShortParsingIdleThreads|Threads: Threads ociosos de análise resumida|Contagem|Média|Número de threads ociosos no pool de threads de análise resumida.|
|ShortParsingJobQueueLength|Threads: Tamanho da fila de trabalhos de análise resumida|Contagem|Média|Número de trabalhos na fila do pool de threads de análise resumida.|
|TotalConnectionFailures|Falhas de conexão totais|Contagem|Média|Total de falhas em tentativas de conexão.|
|TotalConnectionRequests|Solicitações de conexão totais|Contagem|Média|Solicitações de conexão totais. |

## <a name="next-steps"></a>Próximas etapas
[Monitoramento no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Métricas no Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Métricas na API REST do Azure Monitor](/rest/api/monitor/metrics)
