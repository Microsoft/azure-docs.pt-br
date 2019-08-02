---
title: Detecção de anomalias no Azure Stream Analytics
description: Este artigo descreve como usar o Azure Stream Analytics e o Azure Machine Learning em conjunto para detectar anomalias.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: e2fd226f1c605821f0fd595832b2cbe26d994fb4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612332"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Detecção de anomalias no Azure Stream Analytics

Disponível na nuvem e no Azure IoT Edge, o Azure Stream Analytics oferece recursos internos de detecção de anomalias baseados em aprendizado de máquina que podem ser usados para monitorar as duas anomalias que ocorrem com mais frequência: temporárias e persistentes. Com as funções **AnomalyDetection_SpikeAndDip** e **AnomalyDetection_ChangePoint**, você pode detectar anomalias diretamente no seu trabalho do Stream Analytics.

Os modelos de machine learning supõem uma série temporal incluída na amostra de maneira uniforme. Se a série temporal não for uniforme, insira uma etapa de agregação com uma janela em cascata antes de chamar a detecção de anomalias.

As operações de aprendizado de máquina não suportam as tendências de sazonalidade ou você com vários correlações neste momento.

## <a name="model-behavior"></a>Comportamento do modelo

De modo geral, a precisão do modelo melhora com mais dados na janela deslizante. Os dados na janela deslizante especificada são tratados como parte de seu intervalo de valores normal para o período. O modelo considera o histórico de eventos ao longo da janela deslizante apenas para verificar se o evento atual é anormal. Conforme a janela deslizante se move, os valores antigos são removidos do treinamento do modelo.

As funções operam estabelecendo um determinado valor normal com base no que foi observado até então. As exceções são identificadas pela comparação em relação ao normal estabelecido, no nível de confiança. O tamanho da janela deve se basear nos eventos mínimos necessários para treinar o modelo para comportamento normal, de modo que ele esteja apto a reconhecê-la quando uma anomalia ocorrer.

Tempo de resposta do modelo aumenta com o tamanho do histórico, porque ele precisa ser comparada com um número maior de eventos passados. É recomendável incluir apenas o número necessário de eventos para obter melhor desempenho.

As lacunas na série temporal podem ser um resultado do não recebimento de eventos pelo modelo em determinados pontos no tempo. Essa situação é tratada pela análise de Stream usando imputação lógica. O tamanho do histórico e a duração para a mesma janela deslizante são usados para calcular a taxa média na qual os eventos são esperados.

Um gerador de anomalias disponível [aqui](https://aka.ms/asaanomalygenerator) pode ser usado para alimentar um Iot Hub com os dados com padrões diferentes de anomalias. Um trabalho ASA pode ser configurado com essas funções de detecção de anomalias para ler esse Iot Hub e detectar anomalias.

## <a name="spike-and-dip"></a>Pico e queda

As anomalias temporárias em um fluxo de eventos de série temporal são conhecidas como picos e quedas. Os picos e quedas podem ser monitorados usando o operador baseado em Machine Learning, [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exemplo de anomalia de pico e queda](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Na mesma janela deslizante, se um segundo pico for menor que o primeiro, a pontuação calculada para o menor pico provavelmente não será significativa o suficiente em comparação com a pontuação para o primeiro pico no nível de confiança especificado. Você pode tentar diminuir o nível de confiança do modelo para detectar essas anomalias. No entanto, se começar a receber muitos alertas, é possível usar um intervalo de confiança maior.

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

## <a name="performance-characteristics"></a>Características de desempenho

O desempenho desses modelos depende do tamanho do histórico, duração da janela, a carga de eventos, e se o particionamento de nível de função é usado. Esta seção aborda essas configurações e fornece exemplos de como manter as taxas de ingestão de 1K, K 5 e 10 mil eventos por segundo.

* **Tamanho do histórico** -esses modelos executam linearmente com **tamanho do histórico**. Quanto maior for o tamanho do histórico, mais tempo os modelos de levam para um novo evento de pontuação. Isso ocorre porque os modelos de comparam o novo evento com cada um dos últimos eventos no buffer de histórico.
* **Duração da janela** - o **duração da janela** deve refletir quanto tempo leva para receber quantos eventos conforme especificado pelo tamanho do histórico. Sem que vários eventos da janela, o Azure Stream Analytics faria imputar valores ausentes. Portanto, o consumo de CPU é uma função do tamanho do histórico.
* **Carga de eventos** - quanto maior a **carga do evento**, mais trabalho que é executada pelos modelos, que afeta o consumo de CPU. O trabalho pode ser escalado horizontalmente, tornando embaraçosamente paralelos, supondo que ele faça sentido para lógica de negócios para usar mais partições de entrada.
* **Particionamento de nível de função** - **particionamento de nível de função** é feito usando ```PARTITION BY``` dentro da chamada de função de detecção de anomalias. Esse tipo de particionamento adiciona uma sobrecarga, como o estado precisa ser mantido para vários modelos ao mesmo tempo. Particionamento de nível de função é usado em cenários como o particionamento de nível de dispositivo.

### <a name="relationship"></a>Relação
O tamanho do histórico, duração da janela e carga total de eventos são relacionadas da seguinte maneira:

windowDuration (em ms) = 1000 * historySize / (Total eventos de entrada por segundo / contagem de partições de entrada)

Quando a função de particionamento por deviceId, adicione "PARTITION BY deviceId" para a chamada de função de detecção de anomalias.

### <a name="observations"></a>Observações
A tabela a seguir inclui as observações de taxa de transferência para um único nó (6 UAS) para o caso não particionado:

| Tamanho do histórico (eventos) | Duração da janela (ms) | Eventos de entrada total por segundo |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6\.000 | 10,910 | 1,100 |

A tabela a seguir inclui as observações de taxa de transferência para um único nó (6 UAS) para o caso particionado:

| Tamanho do histórico (eventos) | Duração da janela (ms) | Eventos de entrada total por segundo | Contagem de dispositivos |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6\.000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6\.000 | 2,181,819 | <550 | 100 |

Código de exemplo para executar as configurações acima não particionada está localizado na [repositório de Streaming em escala](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) de amostras do Azure. O código cria um trabalho do stream analytics com nenhuma função nível particionamento, que usa o Hub de eventos como entrada e saída. A carga de entrada é gerada usando clientes de teste. Cada evento de entrada é um documento de json de 1KB. Eventos de simulam um dispositivo de IoT enviando dados JSON (para dispositivos de até 1 K). O tamanho do histórico, duração da janela e total de eventos de carga variam ao longo de 2 partições de entrada.

> [!Note]
> Para obter uma estimativa mais precisa, personalize os exemplos para ajustar seu cenário.

### <a name="identifying-bottlenecks"></a>Identificando afunilamentos
Use o painel de métricas no trabalho do Azure Stream Analytics para identificar gargalos no seu pipeline. Revisão **eventos de entrada/saída** taxa de transferência e ["Atraso de marca d'água"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) ou **eventos com lista de pendências** para ver se o trabalho está acompanhando a taxa de entrada. Para métricas do Hub de eventos, procure **solicitações limitadas** e ajustar as unidades de limite adequadamente. Para métricas do Cosmos DB, examine **máximo de RU/s consumidas por intervalo de chaves de partição** na taxa de transferência para garantir que sua partição de intervalos de chaves são consumidos uniformemente. Para o Azure SQL DB, monitore **e/s de Log** e **CPU**.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Detecção de anomalias usando o machine learning no Azure Stream Analytics

O vídeo a seguir demonstra como detectar uma anomalia em tempo real usando funções de machine learning no Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

