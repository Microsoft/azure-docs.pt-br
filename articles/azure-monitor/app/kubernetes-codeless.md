---
title: Monitorar aplicativos no serviço Kubernetes do Azure (AKS) com Application Insights – Azure Monitor | Microsoft Docs
description: O Azure Monitor integra-se perfeitamente ao seu aplicativo em execução no Kubernetes e permite que você identifique os problemas com seus aplicativos rapidamente.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 52bd6d2a98e5126ff2463de1ef99da03ca555567
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87075298"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Monitoramento de aplicativo de instrumentação zero para aplicativos Kubernetes - Application Insights do Azure Monitor

> [!IMPORTANT]
>  No momento, você pode habilitar o monitoramento para os aplicativos Java em execução no Kubernetes sem instrumentar seu código - use o [agente independente do Java](./java-in-process-agent.md). Embora a solução para habilitar o monitoramento de aplicativos diretamente funcione para outras linguagens, use os SDKs para monitorar seus aplicativos em execução no AKS: [ASP.NET Core](./asp-net-core.md), [ASP.NET](./asp-net.md), [Node.js](./nodejs.md), [JavaScript](./javascript.md) e [Python](./opencensus-python.md).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Monitoramento de aplicativos sem instrumentação do código
No momento, somente o Java permite habilitar o monitoramento de aplicativos sem instrumentar o código. Para monitorar aplicativos em outros idiomas, use os SDKs. 

## <a name="java"></a>Java
Uma vez habilitado, o agente Java coletará automaticamente uma infinidade de solicitações, dependências, logs e métricas das bibliotecas e estruturas mais amplamente usadas.

Siga as [instruções detalhadas](./java-in-process-agent.md) para monitorar os aplicativos Java em execução em aplicativos Kubernetes e em outros ambientes. 

## <a name="other-languages"></a>Outros idiomas

Para os aplicativos em outros idiomas, atualmente recomendamos usar os SDKs:
* [ASP.NET Core](./asp-net-core.md)
* [ASP.NET](./asp-net.md)
* [Node.js](./nodejs.md) 
* [JavaScript](./javascript.md)
* [Python](./opencensus-python.md)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Azure Monitor](../overview.md) e o [Application Insights](./app-insights-overview.md)
* Tenha uma visão geral do [Rastreamento distribuído](./distributed-tracing.md) e veja o que o [Mapa do aplicativo](./app-map.md?tabs=net) pode fazer por sua empresa
