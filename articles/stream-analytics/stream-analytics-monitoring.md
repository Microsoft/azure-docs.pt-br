---
title: Noções básicas sobre o monitoramento de trabalhos no Azure Stream Analytics
description: Este artigo descreve como monitorar trabalhos do Azure Stream Analytics no portal do Azure.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: seodec18
ms.openlocfilehash: 6d3558511721a91c3a195cb510a1a00d5d8a9a51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102487871"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Noções básicas sobre o monitoramento de trabalhos do Stream Analytics e como monitorar consultas

## <a name="introduction-the-monitor-page"></a>Introdução A página do monitor
O portal do Azure exibe as principais métricas de desempenho que podem ser usadas para monitorar e solucionar problemas de desempenho de seus trabalhos e consultas. Para ver essas métricas, procure o trabalho do Stream Analytics em cujas métricas você está interessado em ver e exiba a seção **Monitoramento** na página de visão geral.  

![Link do monitoramento de trabalho do Stream Analytics](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

A janela será exibida conforme mostrado:

![Painel do monitoramento de trabalho do Stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Métricas disponíveis para o Stream Analytics
| Métrica                 | Definição                               |
| ---------------------- | ---------------------------------------- |
| Eventos de Entrada Acumulados       | Número de eventos de entrada com lista de pendências. Um valor diferente de zero nessa métrica indica que seu trabalho não consegue acompanhar o número de eventos de entrada. Se esse valor for aumentando lentamente ou for consistentemente diferente de zero, escale horizontalmente seu trabalho. Você pode aprender mais visitando [Compreender e ajustar as Unidades de Streaming](stream-analytics-streaming-unit-consumption.md). |
| Erros de conversão de dados | Número de eventos de saída que não pôde ser convertido para o esquema de saída esperado. A política de erro pode ser alterada para 'Remover' a fim de remover os eventos que encontram esse cenário. |
| % De utilização da CPU (versão prévia)       | A porcentagem da CPU utilizada pelo seu trabalho. Mesmo que esse valor seja muito alto (90% ou superior), você não deve aumentar o número de SUs com base nessa métrica sozinho. Se o número de eventos de entrada de pendências ou o atraso de marca d' água aumentar, você poderá usar essa métrica% de utilização da CPU para determinar se a CPU é o afunilamento. É possível que essa métrica tenha picos intermitentes. É recomendável fazer testes de escala para determinar o limite superior de seu trabalho após o qual as entradas obtêm registro posterior ou atraso de marca d' água aumenta devido ao afunilamento da CPU. |
| Eventos de Entrada Antecipados       | Eventos cujos carimbos de data/hora do aplicativo sejam anteriores à hora de chegada por mais de cinco minutos. |
| Solicitações de função com falha | Número de chamadas à função Azure Machine Learning com falha (se presente). |
| Eventos de função        | Número de eventos enviados à função Azure Machine Learning (se presente). |
| Solicitações de função      | Número de chamadas à função Azure Machine Learning (se presente). |
| Erros de desserialização de entrada       | Número de eventos de entrada que não puderam ser desserializados.  |
| Bytes de evento de entrada      | Quantidade de dados recebidos pelo trabalho do Stream Analytics, em bytes. Isso pode ser usado para validar que os eventos estão sendo enviados para a fonte de entrada. |
| Eventos de entrada           | Número de registros desserializados nos eventos de entrada. Essa contagem não inclui eventos de entrada que resultam em erros de desserialização. Os mesmos eventos podem ser ingeridos pelo Stream Analytics várias vezes em cenários como recuperações internas e autojunções. Portanto, é recomendável não esperar que as métricas de eventos de entrada e de eventos de saída correspondam se seu trabalho tiver uma consulta de "passagem" simples. |
| Fontes de Entrada Recebidas       | Número de mensagens recebidas pelo trabalho. Para o Hub de Eventos, uma mensagem é um EventData individual. Para o Blob, uma mensagem é um blob individual. Observe que as fontes de entrada são contadas antes da desserialização. Se houver erros de desserialização, as fontes de entrada poderão ser maiores que os eventos de entrada. Caso contrário, ele pode ser menor ou igual a eventos de entrada, pois cada mensagem pode conter vários eventos. |
| Eventos de entrada atrasados      | Eventos que chegaram mais tarde do que a janela de tolerância de chegada tardia configurada. Saiba mais sobre [Considerações sobre a ordem dos eventos do Azure Stream Analytics](./stream-analytics-time-handling.md). |
| Eventos fora de ordem    | Número de eventos recebidos fora de ordem que foram descartados ou que receberam um carimbo de data/hora ajustado, com base na Política de ordenação de evento. Isso pode ser afetado pela configuração da definição da Janela de tolerância fora de ordem. |
| Eventos de saída          | Quantidade de dados enviados pelo trabalho do Stream Analytics para o destino de saída, em números de evento. |
| Erros de runtime         | O número total de erros relatados ao processamento de consultas (excluindo-se os erros encontrados durante a ingestão de eventos ou a saída de resultados) |
| % de utilização do SU       | A porcentagem de memória utilizada pelo seu trabalho. Se a utilização de SU% for consistente em mais de 80%, o atraso da marca d' água será aumentado e o número de eventos de registro posterior estiver aumentando, considere aumentar as unidades de streaming. A alta utilização indica que o trabalho está usando próximo ao máximo de recursos alocados. |
| Atraso de Marca-d'água       | O atraso máximo de marca d'água em todas as partições de todas as saídas no trabalho. |

É possível usar essas métricas para [monitorar o desempenho de seu trabalho do Stream Analytics](./stream-analytics-set-up-alerts.md#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Personalizando o Monitoramento no Portal do Azure
Você pode ajustar o tipo de gráfico, as métricas mostradas e o intervalo de hora nas configurações de Editar Gráfico. Para obter detalhes, veja [Como personalizar o monitoramento](../azure-monitor/data-platform.md).

  ![Gráfico de tempo do monitor de consultas do Stream Analytics](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Saída mais recente
Outro ponto de dados interessante para monitorar o trabalho é a hora da última saída, mostrada na página Visão geral.
Essa é o tempo de aplicação (ou seja, a hora usando o carimbo de data/hora dos dados de evento) da saída mais recente do trabalho.

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, veja nossa [página de perguntas e respostas da Microsoft sobre o Azure Stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](/rest/api/streamanalytics/)
