---
title: "Depuração orientada a dados do Stream Analytics do Azure usando o diagrama de trabalho | Microsoft Docs"
description: "Solucionar problemas do trabalho no Stream Analytics usando o diagrama de trabalho e métricas."
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 05/01/2017
ms.author: samacha
ms.openlocfilehash: 5b689c07bf8baa531c7a50ca50ed5140c1787e7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Depuração orientada a dados usando o diagrama de trabalho

O diagrama de trabalho na folha **Monitoramento** do Portal do Azure pode ajudar a visualizar o pipeline de trabalho. Ele mostra entradas, saídas e etapas de consulta. Você pode usar o diagrama de trabalho para examinar as métricas de cada etapa e isolar mais rapidamente a origem de um problema ao solucioná-lo.

## <a name="using-the-job-diagram"></a>Usando o diagrama de trabalho

No Portal do Azure, enquanto estiver em um trabalho do Stream Analytics, em **SUPORTE + SOLUÇÃO DE PROBLEMAS**, selecione **Diagrama de trabalho**:

![Diagrama de trabalho com métricas - local](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Selecione cada etapa da consulta para ver a seção correspondente em um painel de edição de consulta. Um gráfico de métrica para a etapa é exibido em um painel inferior da página.

![Diagrama de trabalho com métricas - local](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Para ver as partições da entrada dos Hubs de Eventos do Azure, selecione **. . .** O menu de contexto é exibido. Você também pode ver a fusão de entrada.

![Diagrama de trabalho com métricas - expandir partição](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Para ver o gráfico de métrica para uma única partição, selecione o nó da partição. As métricas são mostradas na parte inferior da página.

![Diagrama de trabalho com métricas - mais métricas](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Para ver o gráfico de métricas para uma fusão, selecione o nó da fusão. O gráfico a seguir mostra que nenhum evento foi removido ou ajustado.

![Diagrama de trabalho com métricas - grade](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Para ver os detalhes do valor da métrica e do tempo, aponte para o gráfico.

![Diagrama de trabalho com métricas - focalizar](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Solucionar problemas usando métricas

A métrica **QueryLastProcessedTime** indica quando uma etapa específica recebeu dados. Observando a topologia, você pode trabalhar retroativamente do processador de saída para ver qual etapa não está recebendo dados. Se uma etapa não estiver recebendo dados, vá para a etapa de consulta imediatamente anterior a ela. Verifique se a etapa de consulta anterior tem uma janela de tempo e se já passou tempo suficiente para a geração de dados. (Observe que essas janelas de tempo são ajustadas com a hora.)
 
Se a etapa de consulta anterior for um processador de entrada, use as métricas de entrada para ajudar a responder às perguntas direcionadas a seguir. Elas podem ajudar a determinar se um trabalho está recebendo dados de suas fontes de entrada. Se a consulta for particionada, examine cada partição.
 
### <a name="how-much-data-is-being-read"></a>Qual o volume de dados que está sendo lindo?

*   **InputEventsSourcesTotal** é o número de unidades de dados lidas. Por exemplo, o número de blobs.
*   **InputEventsTotal** é o número de eventos lidos. Essa métrica está disponível por partição.
*   **InputEventsInBytesTotal** é o número de bytes lidos.
*   **InputEventsLastArrivalTime** é atualizado com o tempo na fila de cada evento recebido.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>O tempo está avançando? Se eventos reais forem lidos, a pontuação não poderá ser emitida.

*   **InputEventsLastPunctuationTime** indica quando uma pontuação foi emitida para manter o tempo avançando. Se a pontuação não for emitida, o fluxo de dados poderá ser bloqueado.
 
### <a name="are-there-any-errors-in-the-input"></a>Há erros na entrada?

*   **InputEventsEventDataNullTotal** é uma contagem de eventos que têm dados nulos.
*   **InputEventsSerializerErrorsTotal** é uma contagem de eventos que não puderam ser desserializados corretamente.
*   **InputEventsDegradedTotal** é uma contagem de eventos que tiveram um problema diferente de desserialização.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Os eventos estão sendo removidos ou ajustados?

*   **InputEventsEarlyTotal** é o número de eventos que têm um carimbo de data/hora do aplicativo antes da marca d'água alta.
*   **InputEventsLateTotal** é o número de eventos que têm um carimbo de data/hora do aplicativo depois da marca d'água alta.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** é o número de eventos removidos antes da hora de início do trabalho.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Estamos ficando para trás na leitura dos dados?

*   **InputEventsSourcesBackloggedTotal** informa quantas mensagens mais precisam ser lidas das entradas dos Hubs de Eventos e do Hub IoT do Azure.


## <a name="get-help"></a>Obter ajuda
Para obter mais ajuda, teste nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência da linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST de gerenciamento do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
