---
title: Estados de trabalho do Azure Stream Analytics
description: Este artigo descreve os quatro Estados diferentes de um trabalho de Stream Analytics; em execução, parado, degradado e com falha.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: dd298fae148b9e1e51072e98a4e0e5ece10967f3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020444"
---
# <a name="azure-stream-analytics-job-states"></a>Estados de trabalho do Azure Stream Analytics

Um trabalho de Stream Analytics pode estar em um dos quatro Estados em um determinado momento: em execução, parado, degradado ou com falha. É possível descobrir o estado do seu trabalho na página de Visão Geral do trabalho do Stream Analytics no portal do Azure. 

| Estado | Descrição | Ações recomendadas |
| --- | --- | --- |
| **Executando** | O trabalho está em execução nos eventos de leitura do Azure provenientes das fontes de entrada definidas, processando-as e gravando os resultados nos coletores de saída configurados. | É uma melhor prática acompanhar o desempenho do seu trabalho monitorando as [principais métricas](./stream-analytics-set-up-alerts.md#scenarios-to-monitor). |
| **Parado** | Seu trabalho é interrompido e não processa eventos. | NA | 
| **Degradado** | Pode haver problemas intermitentes com as conexões de entrada e saída. Esses erros são chamados de erros transitórios que podem fazer seu trabalho entrar em um estado degradado. O Stream Analytics tentará imediatamente se recuperar desses erros e retornar para um estado de execução (em alguns minutos). Esses erros podem ocorrer devido a problemas de rede, disponibilidade de outros recursos do Azure, erros de desserialização etc. O desempenho do trabalho pode ser afetado quando o trabalho está em estado degradado.| É possível examinar os [logs de diagnóstico ou de atividade](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) para saber mais sobre a causa desses erros transitórios. Em casos como o de erros de desserialização, é recomendável executar uma ação corretiva para garantir que os eventos não estão malformados. Se o trabalho continuar atingindo o limite de utilização de recursos, experimente aumentar o número de UA ou [paralelizar seu trabalho](./stream-analytics-parallelization.md). Em outros casos em que não é possível executar nenhuma ação, o Stream Analytics tentará recuperar para um estado de *Execução*. <br> Você pode usar a métrica de [atraso de marca d' água](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) para entender se esses erros transitórios estão afetando o desempenho do trabalho.|
| **Falha** | Seu trabalho encontrou um erro crítico, resultando em um estado de falha. Os eventos não são lidos nem processados. Erros de runtime são uma causa comum para trabalhos que terminam em um estado de falha. | É possível [configurar alertas](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) para que você seja notificado quando o trabalho termina em Estado de falha. <br> <br>Você pode depurar usando [logs de atividade e de recursos](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) para identificar a causa raiz e resolver o problema.|

## <a name="next-steps"></a>Próximas etapas
* [Configurar alertas para trabalhos do Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Métricas disponíveis no Stream Analytics](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)
* [Solucionar problemas usando logs de atividade e de recursos](./stream-analytics-job-diagnostic-logs.md)