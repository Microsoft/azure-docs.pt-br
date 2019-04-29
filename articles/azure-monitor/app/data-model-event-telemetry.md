---
title: Modelo de dados do Azure Application Insights Telemetry – telemetria de eventos | Microsoft Docs
description: Modelo de dados do Application Insights para telemetria de eventos
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 8e519e5a5e5fe6b893fa5663e1426630f46f885f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900843"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetria de eventos: Modelo de dados do Application Insights

Você pode criar itens de telemetria do evento (em [Application Insights](../../azure-monitor/app/app-insights-overview.md)) para representar um evento que ocorreu em seu aplicativo. Geralmente trata-se de uma interação do usuário como um clique de botão ou finalização de compra. Também pode ser um evento de ciclo de vida do aplicativo como a inicialização ou atualização de configuração. 

Semanticamente, eventos podem ou não ser correlacionados às solicitações. No entanto, se usada corretamente, a telemetria de eventos é mais importante que solicitações ou rastreamentos. Os eventos representam a telemetria de negócios e devem estar sujeitos a uma [amostragem](../../azure-monitor/app/api-filtering-sampling.md) separada, menos agressiva.

## <a name="name"></a>NOME

Nome do evento. Para permitir o agrupamento adequado e métricas úteis, restrinja seu aplicativo de maneira que ele gere um pequeno número de nomes de eventos separados. Por exemplo, não use um nome à parte para cada instância gerado de um evento.

Tamanho máx.: 512 caracteres

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Próximas etapas

- Consulte [modelo de dados](data-model.md) para modelo de dados e tipos do Application Insights.
- [Escrever telemetria do evento personalizada](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte do Application Insights.
