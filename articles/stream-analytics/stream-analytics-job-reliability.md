---
title: Evitar interrupções de serviço nos trabalhos do Azure Stream Analytics
description: Este artigo descreve as diretrizes para tornar o upgrade dos trabalhos do Stream Analytics mais resiliente.
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: e38f8a923daa210d8aa5b56631e5f8157d4b3f70
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620878"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantir a confiabilidade do trabalho do Stream Analytics durante atualizações do serviço

A capacidade de introduzir novos aprimoramentos e funcionalidades de serviço em um ritmo acelerado faz parte do serviço completamente gerenciado. Como resultado, o Stream Analytics pode ter uma implantação semanal (ou mais frequente) da atualização do serviço. Não importa a quantidade de testes realizados, ainda existe o risco de um trabalho atualmente em execução ser interrompido devido à introdução de um bug. Se você estiver executando missão crítica trabalhos, esses riscos precisam ser evitados. Você pode reduzir esse risco por seguir do Azure **[região emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modelo. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Como as regiões emparelhadas do Azure podem resolver essa questão?

O Stream Analytics garante que os trabalhos em regiões emparelhadas sejam atualizados em lotes separados. Como resultado, há um intervalo de tempo suficiente entre as atualizações para identificar possíveis problemas e corrigi-los.

_Com exceção da Índia central_ (cuja região emparelhada, sul da Índia, não tem a presença do Stream Analytics), não ocorrerá a implantação de uma atualização simultânea do Stream Analytics em um conjunto de regiões emparelhadas. Implantações em várias regiões **no mesmo grupo de** podem ocorrer **ao mesmo tempo**.

O artigo em **[regiões de disponibilidade e emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** tem as informações mais atualizadas sobre quais regiões são emparelhadas.

É recomendável implantar trabalhos idênticos para ambas as regiões emparelhadas. Você deve, em seguida, [monitorar esses trabalhos](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) para ser notificado quando algo inesperado acontecer. Se um desses trabalhos extremidades para cima em uma [estado de falha](https://docs.microsoft.com/azure/stream-analytics/job-states) após uma atualização de serviço do Stream Analytics, você pode contatar o atendimento ao cliente para ajudar a identificar a causa raiz. Você também deve fazer failover dos consumidores downstream para a saída de trabalho seja íntegra.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência da linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST de gerenciamento do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
