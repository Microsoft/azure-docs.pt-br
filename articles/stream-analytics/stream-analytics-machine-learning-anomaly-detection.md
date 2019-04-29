---
title: Detecção de anomalias no Azure Stream Analytics (versão prévia)
description: Este artigo descreve como usar o Azure Stream Analytics e o Azure Machine Learning em conjunto para detectar anomalias.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.custom: seodec18
ms.openlocfilehash: 9ea9cc116a13aac2dca9edf8ba86c933310b5198
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479469"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Detecção de anomalias no Azure Stream Analytics

Disponível na nuvem e no Azure IoT Edge, o Azure Stream Analytics oferece recursos internos de detecção de anomalias baseados em aprendizado de máquina que podem ser usados para monitorar as duas anomalias que ocorrem com mais frequência: temporárias e persistentes. Com as funções **AnomalyDetection_SpikeAndDip** e **AnomalyDetection_ChangePoint**, você pode detectar anomalias diretamente no seu trabalho do Stream Analytics.

Os modelos de machine learning supõem uma série temporal incluída na amostra de maneira uniforme. Se a série temporal não for uniforme, insira uma etapa de agregação com uma janela em cascata antes de chamar a detecção de anomalias.

As operações de aprendizado de máquina não são compatíveis com tendências de sazonalidade, nem com correlações multivariadas.

## <a name="model-accuracy-and-performance"></a>Precisão e desempenho do modelo

De modo geral, a precisão do modelo melhora com mais dados na janela deslizante. Os dados na janela deslizante especificada são tratados como parte de seu intervalo de valores normal para o período. O modelo considera o histórico de eventos ao longo da janela deslizante apenas para verificar se o evento atual é anormal. Conforme a janela deslizante se move, os valores antigos são removidos do treinamento do modelo.

As funções operam estabelecendo um determinado valor normal com base no que foi observado até então. As exceções são identificadas pela comparação em relação ao normal estabelecido, no nível de confiança. O tamanho da janela deve se basear nos eventos mínimos necessários para treinar o modelo para comportamento normal, de modo que ele esteja apto a reconhecê-la quando uma anomalia ocorrer.

Lembre-se de que o tempo de resposta do modelo aumenta com o tamanho do histórico, pois é preciso fazer a comparação com um número maior de eventos passados. É recomendável incluir apenas o número necessário de eventos para obter melhor desempenho.

As lacunas na série temporal podem ser um resultado do não recebimento de eventos pelo modelo em determinados pontos no tempo. Essa situação é tratada pelo Stream Analytics usando a imputação. O tamanho do histórico e a duração para a mesma janela deslizante são usados para calcular a taxa média na qual os eventos são esperados.

## <a name="spike-and-dip"></a>Pico e queda

As anomalias temporárias em um fluxo de eventos de série temporal são conhecidas como picos e quedas. Os picos e quedas podem ser monitorados usando o operador baseado em Machine Learning, [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exemplo de anomalia de pico e queda](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Na mesma janela deslizante, se um segundo pico for menor que o primeiro, a pontuação calculada para o menor pico provavelmente não será significativa o suficiente em comparação com a pontuação para o primeiro pico no nível de confiança especificado. Você pode tentar diminuir a configuração do nível de confiança do modelo para capturar essas anomalias. No entanto, se começar a receber muitos alertas, é possível usar um intervalo de confiança maior.

O exemplo de consulta a seguir pressupõe uma taxa uniforme de entrada de um evento por segundo em uma janela deslizante de 2 minutos com um histórico de 120 eventos. A instrução SELECT final extrai e gera a pontuação e o status da anomalia com um nível de confiança de 95%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Ponto de alteração

As anomalias persistentes em um fluxo de eventos de série temporal são alterações na distribuição de valores no fluxo de eventos, como alterações e tendências no nível. No Stream Analytics, tais anomalias são detectadas usando o operador [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) baseado em Machine Learning.

As alterações persistentes duram muito mais que picos e quedas e podem indicar eventos catastróficos. As alterações persistentes geralmente não são vistas a olho nu, mas podem ser detectadas com o operador **AnomalyDetection_ChangePoint**.

A seguinte imagem é um exemplo de alteração no nível:

![Exemplo de anomalia de alteração no nível](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

A seguinte imagem é um exemplo de alteração na tendência:

![Exemplo de anomalia de alteração na tendência](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

O exemplo de consulta a seguir pressupõe uma taxa uniforme de entrada de um evento por segundo em uma janela deslizante de 20 minutos com um tamanho de histórico de 1200 eventos. A instrução SELECT final extrai e gera a pontuação e o status da anomalia com um nível de confiança de 80%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

