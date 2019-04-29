---
title: Evitar interrupções de serviço nos trabalhos do Azure Stream Analytics
description: Este artigo descreve as diretrizes para tornar o upgrade dos trabalhos do Stream Analytics mais resiliente.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7375fb2763ad83e049b1ef30a623f164e059a792
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479449"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantir a confiabilidade do trabalho do Stream Analytics durante atualizações do serviço

A capacidade de introduzir novos aprimoramentos e funcionalidades de serviço em um ritmo acelerado faz parte do serviço completamente gerenciado. Como resultado, o Stream Analytics pode ter uma implantação semanal (ou mais frequente) da atualização do serviço. Não importa a quantidade de testes realizados, ainda existe o risco de um trabalho atualmente em execução ser interrompido devido à introdução de um bug. Para clientes que executam trabalhos de processamento de streaming críticos, esses riscos precisam ser evitados. Há um mecanismo que os clientes podem usar para reduzir esse risco. É o modelo de **[região emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** do Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Como as regiões emparelhadas do Azure podem resolver essa questão?

O Stream Analytics garante que os trabalhos em regiões emparelhadas sejam atualizados em lotes separados. Como resultado, há um intervalo de tempo suficiente entre as atualizações para identificar possíveis bugs que causam interrupções e para corrigi-los.

_Com exceção da Índia central_ (cuja região emparelhada, sul da Índia, não tem a presença do Stream Analytics), não ocorrerá a implantação de uma atualização simultânea do Stream Analytics em um conjunto de regiões emparelhadas. Implantações em várias regiões **no mesmo grupo de** podem ocorrer **ao mesmo tempo**.

O artigo em **[regiões de disponibilidade e emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** tem as informações mais atualizadas sobre quais regiões são emparelhadas.

Os clientes são aconselhados a implantar trabalhos idênticos para ambas as regiões emparelhadas. Além das funcionalidades de monitoramento interno do Stream Analytics, também é recomendável que os clientes monitorem os trabalhos como se **ambos** fossem trabalhos de produção. Se uma interrupção for identificada como sendo o resultado da atualização do serviço do Stream Analytics, escalone adequadamente e realize o failover de consumidores downstream para que a saída do trabalho seja íntegra. O escalonamento ao suporte impedirá a região emparelhada de ser afetada pela nova implantação e manterá a integridade dos trabalhos emparelhados.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência da linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST de gerenciamento do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
