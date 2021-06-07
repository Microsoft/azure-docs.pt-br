---
title: Modelo de dados do Aplicativo Azure insights – telemetria de rastreamento
description: Modelo de dados do Application Insights para telemetria de rastreamento
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8fea4bbf590816b2ef168a2ed16f197389ee282e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320537"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria de rastreamento: modelo de dados do Application Insights

A telemetria de rastreamento (em [Application Insights](./app-insights-overview.md)) representa instruções de rastreamento de estilo `printf` pesquisadas por texto. `Log4Net`, `NLog` e outras entradas do arquivo de log baseadas em texto são convertidas em instâncias desse tipo. O rastreamento não tem medidas como uma extensibilidade.

## <a name="message"></a>Mensagem

Mensagem de rastreamento.

Comprimento máximo: 32.768 caracteres

## <a name="severity-level"></a>Nível de severidade

Nível de severidade de rastreamento. O valor pode ser `Verbose`, `Information`, `Warning`, `Error` ou `Critical`.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Próximas etapas

- [Explore os logs de rastreamento do .net em Application insights](./asp-net-trace-logs.md).
- [Explore os logs de rastreamento do Java em Application insights](./java-trace-logs.md).
- Consulte [modelo de dados](data-model.md) para modelo de dados e tipos do Application Insights.
- [Escrever telemetria personalizada de rastreamento](./api-custom-events-metrics.md#tracktrace)
- Confira as [plataformas](./platforms.md) com suporte do Application Insights.

