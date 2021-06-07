---
title: incluir arquivo
description: incluir arquivo
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 03d83d96bb58ddaf9be4e46c4b62f10e2476e549
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101751139"
---
Há alguns limites quanto ao número de métricas e eventos por aplicativo, ou seja, por chave de instrumentação. Os limites dependem do [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) que você escolher.

| Recurso | Limite | Observação
| --- | --- | --- |
| Total de dados por dia | 100 GB | Você pode reduzir os dados ao definir um limite. Caso precise de mais dados, é possível aumentar o limite até 1.000 GB. Para capacidades maiores que 1.000 GB, envie um email para AIDataCap@microsoft.com.
| Limitação | 32.000 eventos/s | O limite é medido em um minuto.
| Retenção de dados | [de 30 a 730 dias](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period) | Este recurso destina-se a [Pesquisa](../articles/azure-monitor/app/diagnostic-search.md), [Análise](../articles/azure-monitor/logs/log-query-overview.md) e [Metrics Explorer](../articles/azure-monitor/essentials/metrics-charts.md).
| Retenção de resultados detalhados do [Teste de disponibilidade de várias etapas](../articles/azure-monitor/app/availability-multistep.md) | 90 dias | Esse recurso fornece resultados detalhados de cada etapa.
| Tamanho máximo de evento | 64.000 bytes |
| Tamanho dos nomes de propriedade e métrica | 150 | Veja [esquemas de tipo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Tamanho da cadeia de caracteres do valor da propriedade | 8\.192 | Veja [esquemas de tipo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Comprimento da mensagem de rastreamento e de exceção | 32.768  | Veja [esquemas de tipo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Contagem de [testes de disponibilidade](../articles/azure-monitor/app/monitor-web-app-availability.md) por aplicativo | 100 |
| Retenção de dados do [criador de perfil](../articles/azure-monitor/app/profiler.md) | 5 dias |
| Dados do [criador de perfil](../articles/azure-monitor/app/profiler.md) enviados por dia | 10 GB |

Para obter mais informações, consulte [sobre preços e cotas no Application Insights](../articles/azure-monitor/app/pricing.md).