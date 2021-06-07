---
title: incluir arquivo
description: incluir arquivo
services: application-insights
author: lgayhardt
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: lagayhar
ms.custom: include file
ms.openlocfilehash: 1c4f6b876a4aa80c7e51f2bb3ca88234203d0daa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726165"
---
Há alguns limites quanto ao número de métricas e eventos por aplicativo, ou seja, por chave de instrumentação. Os limites dependem do [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) que você escolher.

| Recurso | Limite padrão | Observação
| --- | --- | --- |
| Total de dados por dia | 100 GB | Você pode reduzir os dados ao definir um limite. Caso precise de mais dados, é possível aumentar o limite até 1.000 GB. Para capacidades maiores que 1.000 GB, envie um email para AIDataCap@microsoft.com.
| Limitação | 32.000 eventos/s | O limite é medido em um minuto.
| Logs de retenção de dados | [De 30 a 730 dias](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period)  | Este recurso é para [Logs](../articles/azure-monitor/logs/log-query-overview.md).
| Métricas de retenção de dados | 90 dias| Este recurso é para [Metrics Explorer](../articles/azure-monitor/essentials/metrics-charts.md).
| Retenção de resultados detalhados do [Teste de disponibilidade de várias etapas](../articles/azure-monitor/app/availability-multistep.md) | 90 dias | Esse recurso fornece resultados detalhados de cada etapa.
| Tamanho máximo do item de telemetria | 64 KB |
| Máximo de itens de telemetria por lote | 64 k |
| Tamanho dos nomes de propriedade e métrica | 150 | Veja [esquemas de tipo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Tamanho da cadeia de caracteres do valor da propriedade | 8\.192  | Veja [esquemas de tipo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Comprimento da mensagem de rastreamento e de exceção | 32.768  | Veja [esquemas de tipo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Contagem de [testes de disponibilidade](../articles/azure-monitor/app/monitor-web-app-availability.md) por aplicativo | 100 |
| Retenção de dados do [criador de perfil](../articles/azure-monitor/app/profiler.md) | 5 dias |
| Dados do [criador de perfil](../articles/azure-monitor/app/profiler.md) enviados por dia | 10 GB |

Para obter mais informações, consulte [sobre preços e cotas no Application Insights](../articles/azure-monitor/app/pricing.md).