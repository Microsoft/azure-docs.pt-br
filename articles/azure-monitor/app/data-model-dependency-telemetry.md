---
title: Modelo de dados do Azure Application Insights Telemetry – telemetria de dependências | Microsoft Docs
description: Modelo de dados do Application Insights para telemetria de dependências
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 3e3d6b8fdc9ac8dd28f73fecd6231e97a5645407
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901008"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetria de dependência: Modelo de dados do Application Insights

A telemetria de dependência (em [Application Insights](../../azure-monitor/app/app-insights-overview.md)) representa uma interação do componente monitorado com um componente remoto como SQL ou um ponto de extremidade HTTP.

## <a name="name"></a>NOME

Nome do comando iniciado com esta chamada de dependência. Valor de baixa cardinalidade. Os exemplos são o nome do procedimento armazenado e o modelo do caminho da URL.

## <a name="id"></a>ID

Identificador de uma instância de chamada de dependência. Usado para correlação com o item de telemetria de solicitação correspondente a essa chamada de dependência. Para obter mais informações, consulte a página de [correlação](../../azure-monitor/app/correlation.md).

## <a name="data"></a>Dados

Comando iniciado por essa chamada de dependência. Exemplos são a instrução SQL e a URL HTTP com todos os parâmetros de consulta.

## <a name="type"></a>Type

Nome do tipo de dependência. Valor de baixa cardinalidade para agrupamento lógico de dependências e a interpretação de outros campos como commandName e resultCode. Exemplos são HTTP, tabela do Azure e SQL.

## <a name="target"></a>Destino

Site de destino de uma chamada de dependência. Os exemplos são o nome do servidor e o endereço do host. Para obter mais informações, consulte a página de [correlação](../../azure-monitor/app/correlation.md).

## <a name="duration"></a>Duration

Duração da solicitação no formato: `DD.HH:MM:SS.MMMMMM`. Deve ser menor que `1000` dias.

## <a name="result-code"></a>Código de resultado

Código de resultado de uma chamada de dependência. Os exemplos são o código de erro do SQL e o código de status HTTP.

## <a name="success"></a>Sucesso

Indicação de chamada bem-sucedida ou malsucedida.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Próximas etapas

- Configurar o acompanhamento de dependência para [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Configurar o acompanhamento de dependência para [Java](../../azure-monitor/app/java-agent.md).
- [Escrever telemetria de dependência personalizada](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Consulte [modelo de dados](data-model.md) para modelo de dados e tipos do Application Insights.
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte do Application Insights.
