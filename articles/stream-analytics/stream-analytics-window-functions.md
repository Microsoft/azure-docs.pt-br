---
title: Introdução às funções de janela do Stream Analytics do Azure
description: Este artigo descreve as quatro funções de janela (em cascata, de salto, deslizante, sessão) que são usadas em trabalhos do Azure Stream Analytics.
services: stream-analytics
author: lingliw
ms.author: v-lingwu
manager: digimobile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/21/19
ms.openlocfilehash: 04c19e7e51777db4c59bfab3d5a8a7598560556a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844676"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Introdução às funções de janela do Stream Analytics
Em cenários de streaming em tempo real, executar operações nos dados contidos nas janelas temporais é um padrão comum. O Stream Analytics tem suporte nativo para funções de janela, permitindo que os desenvolvedores criem trabalhos de processamento de streaming complexos com o mínimo de esforço.

Você pode escolher entre quatro tipos de janelas temporais: [**em cascata**](https://msdn.microsoft.com/azure/stream-analytics/reference/tumbling-window-azure-stream-analytics), [**salto**](https://msdn.microsoft.com/azure/stream-analytics/reference/hopping-window-azure-stream-analytics), [**deslizante**](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics) e [**sessão**](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics).  Use as funções de janela na cláusula [**GROUP BY**](https://msdn.microsoft.com/azure/stream-analytics/reference/group-by-azure-stream-analytics) da sintaxe de consulta em seus trabalhos do Stream Analytics.

Todas as operações de [janela](https://msdn.microsoft.com/azure/stream-analytics/reference/windowing-azure-stream-analytics) resultam no **fim** da janela. A saída da janela será um evento único baseado na função agregada usada. O evento de saída terá o carimbo de data/hora do término da janela e todas as funções de janela serão definidas com um comprimento fixo. 

![Conceitos de funções de janela do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Janela em Cascata
As funções de Janela em Cascata são usadas para segmentar um fluxo de dados em segmentos de tempo distintos e executar uma função neles, como no exemplo abaixo. Os principais diferenciais de uma Janela em Cascata são: elas se repetem, não se sobrepõem e um evento não pode pertencer a mais de uma janela em cascata.

![Janela em cascata do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Janela de Salto
As funções da Janela de Salto pulam para frente um período fixo no tempo. É fácil pensar nelas como Janelas em Cascata que podem se sobrepor, de modo que os eventos podem pertencer a mais de um conjunto de resultados da Janela de Salto. Para criar uma Janela de Salto da mesma forma que uma Janela em Cascata, especifique o tamanho do salto para ser do mesmo tamanho da janela. 

![Janela de salto do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Janela Deslizante
As funções da Janela Deslizante, diferentemente das Janelas em Cascata e de Salto, produzem uma saída **apenas** quando um evento ocorre. Cada janela terá pelo menos um evento e a janela move-se continuamente para frente por um € (epsílon). Como nas janelas de salto, os eventos podem pertencer a mais de uma janela deslizante.

![Janela deslizante do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Janela de sessão
As funções de janela de sessão agrupam os eventos que chegam em momentos semelhantes, filtrando períodos de tempo em que não há nenhum dado. Possuem três parâmetros principais: tempo limite, duração máxima e chave de particionamento (opcional).

![Janela de sessão do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Uma janela de sessão começa quando o primeiro evento ocorre. Se outro evento ocorrer dentro do tempo limite especificado a partir do último evento ingerido, a janela se estende para incluir o novo evento. Caso contrário, se não ocorrer nenhum evento dentro do tempo limite, a janela será fechada conforme o tempo limite.

Se eventos continuarem dentro do tempo limite especificado, a janela de sessão continuará se estendendo até que a duração máxima seja atingida. Os intervalos de verificação de duração máxima estão definidos para serem do mesmo tamanho que a duração máxima especificada. Por exemplo, se a duração máxima é 10, então as verificações para saber se a janela excede a duração máxima ocorrerão em t = 0, 10, 20, 30, etc.

Quando uma chave de partição é fornecida, os eventos são agrupados pela chave e a janela de sessão é aplicada a cada grupo de forma independente. Esse particionamento é útil para casos em que você precisa de diferentes janelas de sessão para diferentes usuários ou dispositivos.

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Update_Description: update meta properties -->