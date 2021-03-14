---
title: Detecção de anomalias no Azure Stream Analytics
description: Este artigo descreve como usar o Azure Stream Analytics e o Azure Machine Learning em conjunto para detectar anomalias.
ms.service: stream-analytics
author: jseb225
ms.author: jeanb
ms.topic: how-to
ms.date: 06/21/2019
ms.openlocfilehash: ec37ea6cbb1c1c6693aab1f6855948d32b85e95b
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441186"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Detecção de anomalias no Azure Stream Analytics

Disponível na nuvem e no Azure IoT Edge, o Azure Stream Analytics oferece recursos internos de detecção de anomalias baseados em aprendizado de máquina que podem ser usados para monitorar as duas anomalias que ocorrem com mais frequência: temporárias e persistentes. Com as funções **AnomalyDetection_SpikeAndDip** e **AnomalyDetection_ChangePoint**, você pode detectar anomalias diretamente no seu trabalho do Stream Analytics.

Os modelos de machine learning supõem uma série temporal incluída na amostra de maneira uniforme. Se a série temporal não for uniforme, insira uma etapa de agregação com uma janela em cascata antes de chamar a detecção de anomalias.

As operações de Machine Learning não dão suporte a tendências sazonalidade ou correlações entre variate no momento.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Detecção de anomalias usando o aprendizado de máquina no Azure Stream Analytics

O vídeo a seguir demonstra como detectar uma anomalia em tempo real usando funções de aprendizado de máquina no Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Comportamento do modelo

De modo geral, a precisão do modelo melhora com mais dados na janela deslizante. Os dados na janela deslizante especificada são tratados como parte de seu intervalo de valores normal para o período. O modelo considera o histórico de eventos ao longo da janela deslizante apenas para verificar se o evento atual é anormal. À medida que a janela deslizante é movida, os valores antigos são removidos do treinamento do modelo.

As funções operam estabelecendo um determinado valor normal com base no que foi observado até então. As exceções são identificadas pela comparação em relação ao normal estabelecido, no nível de confiança. O tamanho da janela deve se basear nos eventos mínimos necessários para treinar o modelo para comportamento normal, de modo que ele esteja apto a reconhecê-la quando uma anomalia ocorrer.

O tempo de resposta do modelo aumenta com o tamanho do histórico porque ele precisa comparar com um número maior de eventos anteriores. É recomendável incluir apenas o número necessário de eventos para obter melhor desempenho.

As lacunas na série temporal podem ser um resultado do não recebimento de eventos pelo modelo em determinados pontos no tempo. Essa situação é manipulada por Stream Analytics usando a lógica imputação. O tamanho do histórico e a duração para a mesma janela deslizante são usados para calcular a taxa média na qual os eventos são esperados.

Um gerador de anomalias disponível [aqui](https://aka.ms/asaanomalygenerator) pode ser usado para alimentar um hub IOT com dados com padrões de anomalia diferentes. Um trabalho ASA pode ser configurado com essas funções de detecção de anomalias para ler esse Hub IOT e detectar anomalias.

## <a name="spike-and-dip"></a>Pico e queda

As anomalias temporárias em um fluxo de eventos de série temporal são conhecidas como picos e quedas. Os picos e quedas podem ser monitorados usando o operador baseado em Machine Learning, [AnomalyDetection_SpikeAndDip](/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exemplo de anomalia de pico e queda](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Na mesma janela deslizante, se um segundo pico for menor que o primeiro, a pontuação calculada para o menor pico provavelmente não será significativa o suficiente em comparação com a pontuação para o primeiro pico no nível de confiança especificado. Você pode tentar diminuir o nível de confiança do modelo para detectar tais anomalias. No entanto, se começar a receber muitos alertas, é possível usar um intervalo de confiança maior.

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

As anomalias persistentes em um fluxo de eventos de série temporal são alterações na distribuição de valores no fluxo de eventos, como alterações e tendências no nível. No Stream Analytics, tais anomalias são detectadas usando o operador [AnomalyDetection_ChangePoint](/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) baseado em Machine Learning.

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

O desempenho desses modelos depende do tamanho do histórico, da duração da janela, da carga do evento e se o particionamento no nível da função é usado. Esta seção aborda essas configurações e fornece exemplos de como sustentar taxas de ingestão de eventos de 1K, 5K e 10K por segundo.

* **Tamanho do histórico** -esses modelos são executados linearmente com o **tamanho do histórico**. Quanto maior o tamanho do histórico, mais longos os modelos levam para pontuar um novo evento. Isso ocorre porque os modelos comparam o novo evento com cada um dos eventos passados no buffer de histórico.
* **Duração da janela** – a **duração da janela** deve refletir o tempo necessário para receber tantos eventos quantos forem especificados pelo tamanho do histórico. Sem esses muitos eventos na janela, Azure Stream Analytics imputar valores ausentes. Portanto, o consumo de CPU é uma função do tamanho do histórico.
* **Carga de eventos** -quanto maior a **carga de eventos**, mais trabalho é executado pelos modelos, o que afeta o consumo da CPU. O trabalho pode ser escalado horizontalmente, tornando-o embaraçosamente paralelo, pressupondo que faça sentido para que a lógica comercial use mais partições de entrada.
* Particionamento de nível de **função**  -  O **particionamento de nível de função** é feito usando ```PARTITION BY``` a chamada de função de detecção de anomalias. Esse tipo de particionamento adiciona uma sobrecarga, pois o estado precisa ser mantido para vários modelos ao mesmo tempo. O particionamento no nível de função é usado em cenários como o particionamento no nível do dispositivo.

### <a name="relationship"></a>Relationship
O tamanho do histórico, a duração da janela e a carga total do evento são relacionados da seguinte maneira:

windowDuration (em MS) = 1000 * historySize/(total de eventos de entrada por segundo/contagem de partições de entrada)

Ao particionar a função por DeviceID, adicione "PARTITION BY DeviceID" à chamada de função de detecção de anomalias.

### <a name="observations"></a>Amo
A tabela a seguir inclui as observações de taxa de transferência para um único nó (6 SU) para o caso não particionado:

| Tamanho do histórico (eventos)    | Duração da janela (MS) | Total de eventos de entrada por segundo |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2.200 |
| 600 | 728 | 1.650 |
| 6\.000 | 10.910 | 1.100 |

A tabela a seguir inclui as observações de taxa de transferência para um único nó (6 SU) para o caso particionado:

| Tamanho do histórico (eventos) | Duração da janela (MS) | Total de eventos de entrada por segundo | Contagem de dispositivos |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1.091 | 1.100 | 10 |
| 600 | 10.910 | 1.100 | 10 |
| 6\.000 | 218.182 | <550 | 10 |
| 60 | 21.819 | 550 | 100 |
| 600 | 218.182 | 550 | 100 |
| 6\.000 | 2.181.819 | <550 | 100 |

O código de exemplo para executar as configurações não particionadas acima está localizado no [repositório streaming em escala](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) dos exemplos do Azure. O código cria um trabalho do Stream Analytics sem particionamento de nível de função, que usa o Hub de eventos como entrada e saída. A carga de entrada é gerada usando clientes de teste. Cada evento de entrada é um documento JSON 1 KB. Eventos simulam um dispositivo IoT enviando dados JSON (para dispositivos com até 1K). O tamanho do histórico, a duração da janela e a carga total do evento são variados em 2 partições de entrada.

> [!Note]
> Para obter uma estimativa mais precisa, personalize os exemplos para se ajustarem ao seu cenário.

### <a name="identifying-bottlenecks"></a>Identificando afunilamentos
Use o painel Métricas em seu trabalho do Azure Stream Analytics para identificar gargalos em seu pipeline. Examine **Eventos de Entrada/Saída** para taxa de transferência e ["Atraso de Marca-d'água"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) ou **Eventos Acumulados** para ver se o trabalho está acompanhando a taxa de entrada. Para as métricas do Hub de Eventos, procure **Solicitações Limitadas** e ajuste as Unidades de Limite adequadamente. Para métricas do Cosmos DB, examine **Máximo de RU/s consumidas por intervalo de chaves de partição** em Taxa de Transferência para verificar se os intervalos de chave de partição foram consumidos uniformemente. Para o BD SQL do Azure, monitore a **E/S de Log** e **CPU**.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](/rest/api/streamanalytics/)