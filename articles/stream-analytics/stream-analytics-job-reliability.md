---
title: Evitar interrupções de serviço nos trabalhos do Azure Stream Analytics
description: Este artigo descreve as diretrizes para tornar o upgrade dos trabalhos do Stream Analytics mais resiliente.
author: jseb225
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: f4eda779b1bc719706f9eb42cf805a7d5ce864aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020325"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantir a confiabilidade do trabalho do Stream Analytics durante atualizações do serviço

A capacidade de introduzir novos aprimoramentos e funcionalidades de serviço em um ritmo acelerado faz parte do serviço completamente gerenciado. Como resultado, o Stream Analytics pode ter uma implantação semanal (ou mais frequente) da atualização do serviço. Não importa a quantidade de testes realizados, ainda existe o risco de um trabalho atualmente em execução ser interrompido devido à introdução de um bug. Se você estiver executando trabalhos de missão crítica, esses riscos precisam ser evitados. Você pode reduzir esse risco seguindo o modelo de **[região emparelhada](../best-practices-availability-paired-regions.md)** do Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Como as regiões emparelhadas do Azure podem resolver essa questão?

O Stream Analytics garante que os trabalhos em regiões emparelhadas sejam atualizados em lotes separados. Como resultado, há um intervalo de tempo suficiente entre as atualizações para identificar possíveis problemas e corrigi-los.

_Com exceção da Índia central_ (cuja região emparelhada, sul da Índia, não tem a presença do Stream Analytics), não ocorrerá a implantação de uma atualização simultânea do Stream Analytics em um conjunto de regiões emparelhadas. Implantações em várias regiões **no mesmo grupo de** podem ocorrer **ao mesmo tempo**.

O artigo em **[regiões de disponibilidade e emparelhadas](../best-practices-availability-paired-regions.md)** tem as informações mais atualizadas sobre quais regiões são emparelhadas.

É recomendável implantar trabalhos idênticos em ambas as regiões emparelhadas. Em seguida, você deve [monitorar esses trabalhos](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) para ser notificado quando algo inesperado ocorrer. Se um desses trabalhos terminar em um estado de [falha](./job-states.md) após uma atualização de serviço Stream Analytics, você poderá entrar em contato com o atendimento ao cliente para ajudar a identificar a causa raiz. Você também deve fazer failover de todos os consumidores downstream para a saída do trabalho íntegro.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência da linguagem de consulta do Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST de gerenciamento do Stream Analytics](/rest/api/streamanalytics/)