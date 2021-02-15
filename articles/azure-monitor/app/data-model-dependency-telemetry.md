---
title: Modelo de dados de dependência de Application Insights Azure Monitor
description: Modelo de dados do Application Insights para telemetria de dependências
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0f9fc96479569c3411024068ed614d422035ab17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87315964"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetria de dependências: modelo de dados do Application Insights

A telemetria de dependência (em [Application Insights](./app-insights-overview.md)) representa uma interação do componente monitorado com um componente remoto como SQL ou um ponto de extremidade HTTP.

## <a name="name"></a>Nome

Nome do comando iniciado com esta chamada de dependência. Valor de baixa cardinalidade. Os exemplos são o nome do procedimento armazenado e o modelo do caminho da URL.

## <a name="id"></a>ID

Identificador de uma instância de chamada de dependência. Usado para correlação com o item de telemetria de solicitação correspondente a essa chamada de dependência. Para obter mais informações, consulte a página de [correlação](./correlation.md).

## <a name="data"></a>Dados

Comando iniciado por essa chamada de dependência. Exemplos são a instrução SQL e a URL HTTP com todos os parâmetros de consulta.

## <a name="type"></a>Type

Nome do tipo de dependência. Valor de baixa cardinalidade para agrupamento lógico de dependências e a interpretação de outros campos como commandName e resultCode. Exemplos são HTTP, tabela do Azure e SQL.

## <a name="target"></a>Destino

Site de destino de uma chamada de dependência. Os exemplos são o nome do servidor e o endereço do host. Para obter mais informações, consulte a página de [correlação](./correlation.md).

## <a name="duration"></a>Duration

Duração da solicitação no formato: `DD.HH:MM:SS.MMMMMM`. Deve ser menor que `1000` dias.

## <a name="result-code"></a>Código de Resultado

Código de resultado de uma chamada de dependência. Os exemplos são o código de erro do SQL e o código de status HTTP.

## <a name="success"></a>Êxito

Indicação de chamada bem-sucedida ou malsucedida.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Próximas etapas

- Configurar o acompanhamento de dependência para [.NET](./asp-net-dependencies.md).
- Configurar o acompanhamento de dependência para [Java](./java-agent.md).
- [Escrever telemetria de dependência personalizada](./api-custom-events-metrics.md#trackdependency)
- Consulte [modelo de dados](data-model.md) para modelo de dados e tipos do Application Insights.
- Confira as [plataformas](./platforms.md) com suporte do Application Insights.

