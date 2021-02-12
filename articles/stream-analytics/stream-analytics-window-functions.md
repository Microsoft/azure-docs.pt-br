---
title: Introdução às funções de janela do Stream Analytics do Azure
description: Este artigo descreve as quatro funções de janela (em cascata, de salto, deslizante, sessão) que são usadas em trabalhos do Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: feea44741fbc2c81d781f0bba12b280abdb9f68a
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020241"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Introdução às funções de janela do Stream Analytics

Em cenários de streaming em tempo real, executar operações nos dados contidos nas janelas temporais é um padrão comum. O Stream Analytics tem suporte nativo para funções de janela, permitindo que os desenvolvedores criem trabalhos de processamento de streaming complexos com o mínimo de esforço.

Há cinco tipos de janelas temporais para escolher: [**em cascata**](/stream-analytics-query/tumbling-window-azure-stream-analytics), [**salto**](/stream-analytics-query/hopping-window-azure-stream-analytics), [**deslizante**](/stream-analytics-query/sliding-window-azure-stream-analytics), [**sessão**](/stream-analytics-query/session-window-azure-stream-analytics)e janelas de [**instantâneo**](/stream-analytics-query/snapshot-window-azure-stream-analytics) .  Use as funções de janela na cláusula [**GROUP BY**](/stream-analytics-query/group-by-azure-stream-analytics) da sintaxe de consulta em seus trabalhos do Stream Analytics. Você também pode agregar eventos em várias janelas usando a [função **Windows ()**](/stream-analytics-query/windows-azure-stream-analytics).

Todas as operações de [janela](/stream-analytics-query/windowing-azure-stream-analytics) resultam no **fim** da janela. Observe que quando você inicia um trabalho do Stream Analytics, você pode especificar a *hora de início da saída do trabalho* e o sistema buscará automaticamente eventos anteriores nos fluxos de entrada para gerar a primeira janela no horário especificado; por exemplo, quando você começa com a opção *Now* , ele começará a emitir dados imediatamente. A saída da janela será um evento único baseado na função agregada usada. O evento de saída terá o carimbo de data/hora do término da janela e todas as funções de janela serão definidas com um comprimento fixo. 

![Conceitos de funções de janela do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Janela em Cascata
As funções de Janela em Cascata são usadas para segmentar um fluxo de dados em segmentos de tempo distintos e executar uma função neles, como no exemplo abaixo. Os principais diferenciais de uma Janela em Cascata são: elas se repetem, não se sobrepõem e um evento não pode pertencer a mais de uma janela em cascata.

![Janela em cascata do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Janela de Salto
As funções da Janela de Salto pulam para frente um período fixo no tempo. Pode ser fácil considerá-los como janelas em cascata que podem se sobrepor e ser emitidos com mais frequência do que o tamanho da janela. Os eventos podem pertencer a mais de um conjunto de resultados da janela de salto. Para criar uma Janela de Salto da mesma forma que uma Janela em Cascata, especifique o tamanho do salto para ser do mesmo tamanho da janela. 

![Janela de salto do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Janela Deslizante

Janelas deslizantes, diferentemente de em cascata ou de salto, são eventos de saída apenas para pontos no tempo em que o conteúdo da janela realmente é alterado. Em outras palavras, quando um evento entra ou sai da janela. Portanto, cada janela tem pelo menos um evento. Semelhante a janelas de salto, os eventos podem pertencer a mais de uma janela deslizante.

![Janela deslizante do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Janela de sessão
As funções de janela de sessão agrupam os eventos que chegam em momentos semelhantes, filtrando períodos de tempo em que não há nenhum dado. Possuem três parâmetros principais: tempo limite, duração máxima e chave de particionamento (opcional).

![Janela de sessão do Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Uma janela de sessão começa quando o primeiro evento ocorre. Se outro evento ocorrer dentro do tempo limite especificado a partir do último evento ingerido, a janela se estende para incluir o novo evento. Caso contrário, se não ocorrer nenhum evento dentro do tempo limite, a janela será fechada conforme o tempo limite.

Se eventos continuarem dentro do tempo limite especificado, a janela de sessão continuará se estendendo até que a duração máxima seja atingida. Os intervalos de verificação de duração máxima estão definidos para serem do mesmo tamanho que a duração máxima especificada. Por exemplo, se a duração máxima é 10, então as verificações para saber se a janela excede a duração máxima ocorrerão em t = 0, 10, 20, 30, etc.

Quando uma chave de partição é fornecida, os eventos são agrupados pela chave e a janela de sessão é aplicada a cada grupo de forma independente. Esse particionamento é útil para casos em que você precisa de diferentes janelas de sessão para diferentes usuários ou dispositivos.

## <a name="snapshot-window"></a>Janela de instantâneo

Instantâneos agrupam eventos que têm o mesmo carimbo de data/hora. Ao contrário de outros tipos de janela, que exigem uma função de janela específica (como [SessionWindow ()](/stream-analytics-query/session-window-azure-stream-analytics), você pode aplicar uma janela de instantâneo adicionando System. Timestamp () à cláusula Group by.

![Stream Analytics janela de instantâneo](media/stream-analytics-window-functions/snapshot.png)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](/rest/api/streamanalytics/)
