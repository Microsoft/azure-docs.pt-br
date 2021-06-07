---
title: Modelo de dados de telemetria de exceção do Aplicativo Azure insights
description: Modelo de dados do Application Insights para telemetria de exceções
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 6732fb3dde79abd5c69e2b0a91710bdd2356d6aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320588"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetria de exceções: modelo de dados do Application Insights

Um [Application Insights](./app-insights-overview.md), uma instância da exceção representa uma exceção tratada ou sem tratamento que ocorreu durante a execução do aplicativo monitorado.

## <a name="problem-id"></a>ID do Problema

Identificador do local em que a exceção foi lançada no código. Usada para agrupamento de exceções. Normalmente, é uma combinação do tipo de exceção e uma função da pilha de chamadas.

Comprimento máximo: 1.024 caracteres

## <a name="severity-level"></a>Nível de severidade

Nível de severidade de rastreamento. O valor pode ser `Verbose`, `Information`, `Warning`, `Error` ou `Critical`.

## <a name="exception-details"></a>Detalhes da exceção

(A ser estendido)

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Próximas etapas

- Consulte [modelo de dados](data-model.md) para modelo de dados e tipos do Application Insights.
- Aprenda a [diagnosticar exceções em seus aplicativos Web com o Application Insights](./asp-net-exceptions.md).
- Confira as [plataformas](./platforms.md) com suporte do Application Insights.

