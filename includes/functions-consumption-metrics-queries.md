---
ms.openlocfilehash: 0bae8946b7d81fbf45698cefe7c8b00bbc94d00c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92168096"
---
#### <a name="determine-memory-usage"></a>Determinar o uso de memória 

Em **monitoramento**, selecione **logs (análise)**, em seguida, copie a seguinte consulta de telemetria e cole-a na janela de consulta e selecione **executar**. Essa consulta retorna o uso de memória total em cada tempo de amostra.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Os resultados são semelhantes ao exemplo a seguir:

| carimbo de data/hora \[ UTC\]          | name          | value       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14 \. 947 am | Bytes Particulares | 209.932.288 |
| 9/12/2019, 1:06:14 \. 994 am | Bytes Particulares | 212.189.184 |
| 9/12/2019, 1:06:30 \. 010 | Bytes Particulares | 231.714.816 |
| 9/12/2019, 1:07:15 \. 040 am | Bytes Particulares | 210.591.744 |
| 9/12/2019, 1:12:16 \. 285 am | Bytes Particulares | 216.285.184 |
| 9/12/2019, 1:12:31 \. 376 am | Bytes Particulares | 235.806.720 |

#### <a name="determine-duration"></a>Determinar duração 

Azure Monitor acompanha as métricas no nível de recurso, que para funções é o aplicativo de funções. A integração do Application Insights emite métricas de acordo com a função. Aqui está um exemplo de consulta de análise para obter a duração média de uma função:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16 \. 087                     |
| QueueTrigger MaxDurationMs | 90 \. 249                     |
| QueueTrigger MinDurationMs | 8 \. 522                      |
