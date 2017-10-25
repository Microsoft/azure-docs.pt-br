---
title: "Evitar interrupções de serviço com trabalhos do Stream Analytics do Azure | Microsoft Docs"
description: "Orientação sobre como tornar resilientes suas atualizações de trabalhos do Stream Analytics."
services: stream-analytics
documentationCenter: 
authors: samacha
manager: jhubbard
editor: cgronlun
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 76e347ee62ffc07db1d8e74cf0ac5327a154fe4f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantir a confiabilidade do trabalho do Stream Analytics durante atualizações do serviço

A capacidade de introduzir novos aprimoramentos e funcionalidades de serviço em um ritmo acelerado faz parte do serviço completamente gerenciado. Como resultado, o Stream Analytics pode ter uma implantação semanal (ou mais frequente) da atualização do serviço. Não importa a quantidade de testes realizados, ainda existe o risco de um trabalho atualmente em execução ser interrompido devido à introdução de um bug. Para clientes que executam trabalhos de processamento de streaming críticos, esses riscos precisam ser evitados. Há um mecanismo que os clientes podem usar para reduzir esse risco. É o modelo de **[região emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** do Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Como as regiões emparelhadas do Azure podem resolver essa questão?

O Stream Analytics garante que os trabalhos em regiões emparelhadas sejam atualizados em lotes separados. Como resultado, há um intervalo de tempo suficiente entre as atualizações para identificar possíveis bugs que causam interrupções e para corrigi-los.

_Com exceção da Índia central_ (cuja região emparelhada, sul da Índia, não tem a presença do Stream Analytics), não ocorrerá a implantação de uma atualização simultânea do Stream Analytics em um conjunto de regiões emparelhadas. Implantações em várias regiões **no mesmo grupo de** podem ocorrer **ao mesmo tempo**.

O artigo em **[regiões de disponibilidade e emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** tem as informações mais atualizadas sobre quais regiões são emparelhadas.

Os clientes são aconselhados a implantar trabalhos idênticos para ambas as regiões emparelhadas. Além das funcionalidades de monitoramento interno do Stream Analytics, também é recomendável que os clientes monitorem os trabalhos como se **ambos** fossem trabalhos de produção. Se uma interrupção for identificada como sendo o resultado da atualização do serviço do Stream Analytics, escalone adequadamente e realize o failover de consumidores downstream para que a saída do trabalho seja íntegra. O escalonamento ao suporte impedirá a região emparelhada de ser afetada pela nova implantação e manterá a integridade dos trabalhos emparelhados.
