---
title: "Introdução às funções de janela do Stream Analytics | Microsoft Docs"
description: "Saiba mais sobre as três funções de Janela no Stream Analytics (em cascata, de salto, deslizante)."
keywords: janela em cascata, janela deslizante, janela de salto
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 0d8d8717-5d23-43f0-b475-af078ab4627d
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 2a6559551f608cf435e89997392a6a0ba995c583
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-stream-analytics-window-functions"></a>Introdução às funções de janela do Stream Analytics
Em muitos cenários de streaming em tempo real, é necessário executar operações apenas nos dados contidos nas janelas temporais. O suporte nativo para funções de janela é um importante recurso do Stream Analytics do Azure, que faz toda a diferença na produtividade do desenvolvedor em trabalhos complexos de processamento de fluxo. O Stream Analytics permite aos desenvolvedores usar as janelas [**Em cascata**](https://msdn.microsoft.com/library/dn835055.aspx), [**De salto**](https://msdn.microsoft.com/library/dn835041.aspx) e [**Deslizante**](https://msdn.microsoft.com/library/dn835051.aspx) para executar operações temporais nos dados de streaming. Vale a pena observar que toda saída de operações de [Janela](https://msdn.microsoft.com/library/dn835019.aspx) resulta no **fim** da janela. A saída da janela será um evento único baseado na função agregada usada. O evento terá o carimbo de data/hora do término da janela e todas as funções de Janela serão definidas com um comprimento fixo. Por fim, é importante observar que todas as funções de Janela devem ser usadas em uma cláusula [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx).

![Conceitos de funções de Janela do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Janela em Cascata
As funções de Janela em Cascata são usadas para segmentar um fluxo de dados em segmentos de tempo distintos e executar uma função neles, como no exemplo abaixo. Os principais diferenciais de uma Janela em Cascata são: elas se repetem, não se sobrepõem e um evento não pode pertencer a mais de uma janela em cascata.

![Introdução às funções de Janela em cascata do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Janela de Salto
As funções da Janela de Salto pulam para frente um período fixo no tempo. É fácil pensar nelas como Janelas em Cascata que podem se sobrepor, de modo que os eventos podem pertencer a mais de um conjunto de resultados da Janela de Salto. Para criar uma Janela de Salto da mesma forma que uma Janela em Cascata, basta especificar o tamanho do salto para ser do mesmo tamanho da janela. 

![Introdução às funções de Janela de salto do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Janela Deslizante
As funções da Janela Deslizante, diferentemente das Janelas em Cascata e de Salto, produzem uma saída **apenas** quando um evento ocorre. Cada janela terá pelo menos um evento e a janela move-se continuamente para frente por um € (epsílon). Como nas Janelas de Salto, os eventos podem pertencer a mais de uma Janela Deslizante.

![Introdução às funções de Janela deslizante do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Obtendo ajuda com funções de janela
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

