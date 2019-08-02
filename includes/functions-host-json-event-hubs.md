---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171716"
---
```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------| 
|maxBatchSize|64|A contagem máxima de eventos recebidos por loop de recebimento.|
|prefetchCount|n/d|O PrefetchCount padrão que será usado pelo EventProcessorHost subjacente.| 
|batchCheckpointFrequency|1|O número de lotes de eventos para processar antes de criar um ponto de verificação do cursor do EventHub.| 
