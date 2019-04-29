---
title: Modelo de dados do Azure Application Insights Telemetry | Microsoft Docs
description: Visão geral do modelo de dados do Application Insights
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
ms.openlocfilehash: 749b4077b457eff836ec515f21d97e892e663156
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899174"
---
# <a name="application-insights-telemetry-data-model"></a>Modelo de dados do Application Insights Telemetry

O [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) envia a telemetria de seu aplicativo Web ao portal do Azure, para que você possa analisar o desempenho e o uso de seu aplicativo. O modelo de telemetria é padronizado para que seja possível criar a plataforma e o monitoramento independente de linguagem. 

Os dados coletados pelo Application Insights modelam esse padrão de execução típico do aplicativo:

![Modelo de aplicativo do Application Insights](./media/data-model/application-insights-data-model.png)

Os seguintes tipos de telemetria são usados para monitorar a execução de seu aplicativo. Estes três tipos normalmente são coletados automaticamente pelo SDK do Application Insights na estrutura do aplicativo Web:

* [**Solicitação** ](data-model-request-telemetry.md) – Gerada para registrar uma solicitação recebida pelo seu aplicativo. Por exemplo, o SDK Web do Application Insights gera automaticamente um item de telemetria da solicitação para cada solicitação HTTP que seu aplicativo Web recebe. 

    Uma **operação** é o thread de execução que processa uma solicitação. Você também pode [escrever código](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) para monitorar outros tipos de operação, como uma "ativação" em um trabalho ou em uma função Web que processa dados periodicamente.  Cada operação tem uma ID. Essa ID que pode ser usada para [agrupar](../../azure-monitor/app/correlation.md) toda a telemetria gerada enquanto o aplicativo está processando a solicitação. Cada operação é bem-sucedida ou falha, além de ter uma duração.
* [**Exceção** ](data-model-exception-telemetry.md) – Normalmente representa uma exceção que causa falha em uma operação.
* [**Dependência** ](data-model-dependency-telemetry.md) – Representa uma chamada de seu aplicativo para um serviço ou armazenamento externo, como uma API REST ou SQL. No ASP.NET, chamadas de dependência para SQL são definidas por `System.Data`. Chamadas para pontos de extremidade HTTP são definidas por `System.Net`. 

O Application Insights fornece três tipos de dados adicionais para telemetria personalizada:

* [Rastreamento](data-model-trace-telemetry.md) – Usado diretamente ou por meio de um adaptador para implementar o registro em log de diagnóstico usando uma estrutura de instrumentação que lhe é familiar, como `Log4Net` ou `System.Diagnostics`.
* [Evento](data-model-event-telemetry.md) – Normalmente usado para capturar a interação do usuário com o serviço para analisar os padrões de uso.
* [Métrica](data-model-metric-telemetry.md) – Usada para relatar medidas escalares periódicas.

Cada item de telemetria pode definir as [informações de contexto](data-model-context.md) como a versão do aplicativo ou a id de sessão de usuário. O contexto é um conjunto de campos fortemente tipados que desbloqueia determinados cenários. Quando a versão do aplicativo é inicializada corretamente, o Application Insights pode detectar novos padrões no comportamento do aplicativo correlacionado com a reimplantação. A Id da sessão pode ser usada para calcular a interrupção ou o impacto de um problema em relação aos usuários. Calcular a contagem distinta de valores de id de sessão para determinadas dependência com falha, rastreamento de erro ou exceção crítica dá uma boa compreensão de um impacto.

O modelo do Application Insights Telemetry define uma forma de [correlacionar](../../azure-monitor/app/correlation.md) telemetria à operação da qual ele faz parte. Por exemplo, uma solicitação pode tomar informações de diagnóstico registradas e de chamadas de um Banco de Dados SQL. Você pode definir o contexto de correlação para esses itens de telemetria aproximam esse contexto novamente da telemetria de solicitação.

## <a name="schema-improvements"></a>Aprimoramentos de esquema

O modelo de dados do Application Insights é uma maneira simples e básica, mas poderosa de modelar a telemetria de aplicativo. Nos esforçamos para manter o modelo simples e reduzido para dar suporte a cenários essenciais e permitir a extensão do esquema para uso avançado.

Para relatar problemas de esquema ou modelo de dados e sugestões, use o repositório [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) do GitHub.

## <a name="next-steps"></a>Próximas etapas

- [Escrever telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md)
- Saiba como [estender e filtrar a telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Use [amostragem](../../azure-monitor/app/sampling.md) para minimizar a quantidade de telemetria com base no modelo de dados.
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte do Application Insights.
