---
title: Modelo de dados do Azure Application Insights Telemetry – telemetria de métricas | Microsoft Docs
description: Modelo de dados do Application Insights para telemetria de métricas
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 0973c86d055ff4ebbe7e5a3c4a2ca4e3dcabc6a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900453"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Telemetria de métrica: Modelo de dados do Application Insights

Há dois tipos de telemetria de métricas com suporte do [Application Insights](../../azure-monitor/app/app-insights-overview.md): medida única e métrica agregada previamente. A medida única é apenas um nome e valor. A métrica agregada previamente especifica os valores mínimo e máximo da métrica no intervalo de agregação e o desvio padrão desses valores.

A telemetria de métrica agregada previamente supõe que esse período de agregação foi de um minuto.

Há vários nomes de métrica conhecidos com suporte do Application Insights. Essas métricas são colocadas em tabela performanceCounters.

Métrica representando os contadores de processo e de sistema:

| **Nome do .NET**             | **Nome independente de plataforma** | **Nome da API REST** | **Descrição**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Trabalho em andamento... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | total de CPU do computador
| `\Memory\Available Bytes`                 | Trabalho em andamento... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Mostra a quantidade de memória física, em bytes, disponível para processos em execução no computador. É calculado, somando a quantidade de espaço nas listas de memória em espera, livre e zero. A memória livre está pronta para uso; a memória zero é constituída por páginas de memória preenchidas com zeros para impedir que processos posteriores vejam dados usados por um processo anterior; a memória em espera é a memória que foi removida do conjunto de trabalho de um processo (a memória física) em rota para o disco, mas ainda está disponível para ser recuperado. Consulte [Objeto de Memória](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Trabalho em andamento... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU do processo que hospeda o aplicativo
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Trabalho em andamento... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | memória usada pelo processo que hospeda o aplicativo
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Trabalho em andamento... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | taxa de execuções de operações de E/S pelo processo que hospeda o aplicativo
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Trabalho em andamento... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | taxa de solicitações processadas por aplicativo 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Trabalho em andamento... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | taxa de exceções geradas por aplicativo
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Trabalho em andamento... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | tempo médio de execução de solicitações
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Trabalho em andamento... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | número de solicitações aguardando processamento em uma fila

## <a name="name"></a>NOME

Nome da métrica que você gostaria de ver na interface do usuário e no portal do Application Insights. 

## <a name="value"></a>Value

Valor único para medida. Soma de medidas individuais para a agregação.

## <a name="count"></a>Contagem

Peso da métrica agregada. Não deve ser definido para uma medida.

## <a name="min"></a>Min

Valor mínimo da métrica agregada. Não deve ser definido para uma medida.

## <a name="max"></a>max

Valor máximo da métrica agregada. Não deve ser definido para uma medida.

## <a name="standard-deviation"></a>Desvio padrão

Desvio padrão da métrica agregada. Não deve ser definido para uma medida.

## <a name="custom-properties"></a>Propriedades personalizadas

Métrica com a propriedade personalizada `CustomPerfCounter` definida para `true` indicar que a métrica representa o contador de desempenho do Windows. Essas métricas são colocadas em tabela performanceCounters. Não estão em customMetrics. Além disso, o nome dessa métrica é analisado para extrair os nomes de instância, contador e categoria.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar a [API do Application Insights para métricas e eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Consulte [modelo de dados](data-model.md) para modelo de dados e tipos do Application Insights.
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte do Application Insights.
