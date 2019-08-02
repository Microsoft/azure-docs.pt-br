---
title: Estados de trabalho do Azure Stream Analytics
description: Este artigo descreve os quatro Estados diferentes de um trabalho de Stream Analytics; em execução, parado, degradado e com falha.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: c533463ff544dc315142f7fb95c34c67933f9614
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444258"
---
# <a name="azure-stream-analytics-job-states"></a>Estados de trabalho do Azure Stream Analytics

Um trabalho de Stream Analytics pode estar em um dos quatro Estados em um determinado momento: em execução, parado, degradado ou com falha. É possível descobrir o estado do seu trabalho na página de Visão Geral do trabalho do Stream Analytics no portal do Azure. 

| Estado | Descrição | Ações recomendadas |
| --- | --- | --- |
| **Executando** | O trabalho está em execução nos eventos de leitura do Azure provenientes das fontes de entrada definidas, processando-as e gravando os resultados nos coletores de saída configurados. | É uma melhor prática acompanhar o desempenho do seu trabalho monitorando as [principais métricas](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Interrompido** | Seu trabalho é interrompido e não processa eventos. | N/A | 
| **Degradado** | Pode haver problemas intermitentes com as conexões de entrada e saída. Esses erros são chamados de erros transitórios que podem fazer seu trabalho entrar em um estado degradado. O Stream Analytics tentará imediatamente se recuperar desses erros e retornar para um estado de execução (em alguns minutos). Esses erros podem acontecer devido a problemas de rede, disponibilidade de outros recursos do Azure, erros de desserialização, etc. O desempenho do seu trabalho pode ser afetado quando ele está em estado degradado.| É possível examinar os [logs de diagnóstico ou de atividade](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) para saber mais sobre a causa desses erros transitórios. Em casos como o de erros de desserialização, é recomendável executar uma ação corretiva para garantir que os eventos não estão malformados. Se o trabalho continuar atingindo o limite de utilização de recursos, experimente aumentar o número de UA ou [paralelizar seu trabalho](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). Em outros casos em que não é possível executar nenhuma ação, o Stream Analytics tentará recuperar para um estado de *Execução*. <br> Você pode usar a métrica de [atraso de marca d' água](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) para entender se esses erros transitórios estão afetando o desempenho do trabalho.|
| **Com falha** | Seu trabalho encontrou um erro crítico, resultando em um estado de falha. Os eventos não são lidos nem processados. Erros de tempo de execução são uma causa comum para trabalhos que terminam em um estado de falha. | É possível [configurar alertas](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) para que você seja notificado quando o trabalho termina em Estado de falha. <br> <br>É possível depurar usando [logs de atividade e de diagnóstico](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) para identificar a causa raiz e resolver o problema.|

## <a name="next-steps"></a>Próximas etapas
* [Configurar alertas para trabalhos do Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Métricas disponíveis no Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Solucionar problemas usando logs de atividade e de diagnóstico](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
