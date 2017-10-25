---
title: "Modelo de dados do Azure Application Insights Telemetry – telemetria de dependências | Microsoft Docs"
description: "Modelo de dados do Application Insights para telemetria de dependências"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: bwren
ms.openlocfilehash: 2e97c3f951f46c32802aea543b93d5ab1bb76228
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetria de dependências: modelo de dados do Application Insights

A telemetria de dependência (em [Application Insights](app-insights-overview.md)) representa uma interação do componente monitorado com um componente remoto como SQL ou um ponto de extremidade HTTP.

## <a name="name"></a>Nome

Nome do comando iniciado com esta chamada de dependência. Valor de baixa cardinalidade. Os exemplos são o nome do procedimento armazenado e o modelo do caminho da URL.

## <a name="id"></a>ID

Identificador de uma instância de chamada de dependência. Usado para correlação com o item de telemetria de solicitação correspondente a essa chamada de dependência. Para obter mais informações, consulte a página de [correlação](application-insights-correlation.md).

## <a name="data"></a>Dados

Comando iniciado por essa chamada de dependência. Exemplos são a instrução SQL e a URL HTTP com todos os parâmetros de consulta.

## <a name="type"></a>Tipo

Nome do tipo de dependência. Valor de baixa cardinalidade para agrupamento lógico de dependências e a interpretação de outros campos como commandName e resultCode. Exemplos são HTTP, tabela do Azure e SQL.

## <a name="target"></a>Destino

Site de destino de uma chamada de dependência. Os exemplos são o nome do servidor e o endereço do host. Para obter mais informações, consulte a página de [correlação](application-insights-correlation.md).

## <a name="duration"></a>Duração

Duração da solicitação no formato: `DD.HH:MM:SS.MMMMMM`. Deve ser menor que `1000` dias.

## <a name="result-code"></a>Código de resultado

Código de resultado de uma chamada de dependência. Os exemplos são o código de erro do SQL e o código de status HTTP.

## <a name="success"></a>Sucesso

Indicação de chamada bem-sucedida ou malsucedida.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Próximas etapas

- Configurar o acompanhamento de dependência para [.NET](app-insights-asp-net-dependencies.md).
- Configurar o acompanhamento de dependência para [Java](app-insights-java-agent.md).
- [Escrever telemetria de dependência personalizada](app-insights-api-custom-events-metrics.md#trackdependency)
- Consulte [modelo de dados](application-insights-data-model.md) para modelo de dados e tipos do Application Insights.
- Confira as [plataformas](app-insights-platforms.md) com suporte do Application Insights.
