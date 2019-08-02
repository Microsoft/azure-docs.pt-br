---
title: Como usar a API do Detector de anomalias em seus dados de série temporal
titleSuffix: Azure Cognitive Services
description: Saiba como detectar anomalias em seus dados como um lote ou no fluxo de dados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 551196815004cb047680e2ae2f8dbe32186c1a0c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721800"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Como: Usar a API do Detector de anomalias em seus dados de série temporal  

O [API do Detector de anomalias](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) fornece dois métodos de detecção de anomalias. Você também pode detectar anomalias como um lote em todo os tempos de série, ou conforme seus dados são gerados ao detectar o status de anomalias do ponto de dados mais recente. O modelo de detecção retorna resultados de anomalias, juntamente com o valor esperado de cada ponto de dados e as anomalias superiores e inferiores dos limites de detecção. Você pode usar esses valores para visualizar o intervalo de valores normais e anomalias nos dados.

## <a name="anomaly-detection-modes"></a>Modos de detecção de anomalias 

A API do Detector de anomalias fornece modos de detecção: lote e streaming.

> [!NOTE]
> A seguinte solicitação que URLs devem ser combinadas com o ponto de extremidade apropriado para sua assinatura. Por exemplo: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Detecção de lote

Para detectar anomalias ao longo de um lote de pontos de dados em um intervalo de tempo determinado, use o seguinte URI de solicitação com seus dados de série temporal: 

`/timeseries/entire/detect`. 

Enviando seus dados de série temporal ao mesmo tempo, a API de gerar um modelo usando a série inteira e analisar cada ponto de dados com ele.  

### <a name="streaming-detection"></a>Detecção de streaming

Para detectar continuamente anomalias nos dados de streaming, use o seguinte URI de solicitação com seu último ponto de dados: 

`/timeseries/last/detect'`. 

Enviando novos pontos de dados como gerá-los, você pode monitorar seus dados em tempo real. Um modelo será gerado com os pontos de dados que você enviar, e a API determinará se o último ponto na série temporal é uma anomalia.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Ajustar os limites de detecção de anomalias inferior e superior

Por padrão, os limites superiores e inferiores para a detecção de anomalias são calculados usando `expectedValue`, `upperMargin`, e `lowerMargin`. Se você precisar de limites diferentes, é recomendável aplicar uma `marginScale` à `upperMargin` ou `lowerMargin`. Os limites seriam calculados da seguinte maneira:

|Limite  |Cálculo  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Os exemplos a seguir mostram o resultado de uma API do Detector de anomalias no sensibilidades diferentes.

### <a name="example-with-sensitivity-at-99"></a>Exemplo com distinção de 99

![Sensibilidade Padrão](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Exemplo com distinção de 95

![Sensibilidade 99](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Exemplo com distinção de 85

![Sensibilidade 85](../media/sensitivity_85.png)

## <a name="next-steps"></a>Próximas etapas

* [O que é a API do Detector de anomalias?](../overview.md)
* [Início Rápido: Detectar anomalias em seus dados de série temporal usando a API de REST do Detector de anomalias](../quickstarts/detect-data-anomalies-csharp.md)