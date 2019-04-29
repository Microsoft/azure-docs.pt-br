---
title: Estados de trabalho do Azure Stream Analytics
description: Este artigo descreve diferentes estados de um trabalho do Stream Analytics
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/06/2019
ms.openlocfilehash: 28e0e69d3a6a4d3a38146cbf2c49426b3b16c784
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789443"
---
# <a name="azure-stream-analytics-job-states"></a>Estados de trabalho do Azure Stream Analytics

Um trabalho do Stream Analytics poderia estar em um dos quatro estados em um determinado momento. É possível descobrir o estado do seu trabalho na página de Visão Geral do trabalho do Stream Analytics no portal do Azure. 

| Estado | DESCRIÇÃO | Ações recomendadas |
| --- | --- | --- |
| **Executando** | O trabalho está em execução nos eventos de leitura do Azure provenientes das fontes de entrada definidas, processando-as e gravando os resultados nos coletores de saída configurados. | É uma melhor prática acompanhar o desempenho do seu trabalho monitorando as [principais métricas](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Interrompido** | Seu trabalho é interrompido e não processa eventos. | ND | 
| **Degradado** | Erros transitórios provavelmente estão afetando seu trabalho. O Stream Analytics tentará imediatamente se recuperar desses erros e retornar para um estado de execução (em alguns minutos). Esses erros podem acontecer devido a problemas de rede, disponibilidade de outros recursos do Azure, erros de desserialização, etc. O desempenho do seu trabalho pode ser afetado quando ele está em estado degradado.| É possível examinar os [logs de diagnóstico ou de atividade](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) para saber mais sobre a causa desses erros transitórios. Em casos como o de erros de desserialização, é recomendável executar uma ação corretiva para garantir que os eventos não estão malformados. Se o trabalho continuar atingindo o limite de utilização de recursos, experimente aumentar o número de UA ou [paralelizar seu trabalho](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). Em outros casos em que não é possível executar nenhuma ação, o Stream Analytics tentará recuperar para um estado de *Execução*.  |
| **Com falha** | Seu trabalho encontrou um erro crítico, resultando em um estado de falha. Os eventos não são lidos nem processados. Erros de tempo de execução são uma causa comum para trabalhos que terminam em um estado de falha. | É possível [configurar alertas](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) para que você seja notificado quando o trabalho termina em Estado de falha. <br> <br>É possível depurar usando [logs de atividade e de diagnóstico](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) para identificar a causa raiz e resolver o problema.|

## <a name="next-steps"></a>Próximas etapas
* [Configurar alertas para trabalhos do Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Métricas disponíveis no Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Solucionar problemas usando logs de atividade e de diagnóstico](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
