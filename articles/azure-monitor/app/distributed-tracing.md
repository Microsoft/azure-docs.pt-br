---
title: Rastreamento distribuído no Azure Application Insights | Microsoft Docs
description: Fornece informações sobre o suporte da Microsoft para rastreamento distribuído por meio de nossa parceria no projeto OpenCensus
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a47b41a8b7f4e18be58c32c97cf279b9229f26da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579519"
---
# <a name="what-is-distributed-tracing"></a>O que é o Rastreamento distribuído?

O advento das arquiteturas modernas de nuvem e de [microserviço](https://azure.com/microservices) deu origem aos serviços simples e implantáveis de forma independente que podem ajudar a reduzir os custos enquanto aumentam a disponibilidade e a taxa de transferência. Mas embora esses movimentos tenham tornado serviços individuais mais fáceis de entender como um todo, eles tornaram os sistemas em geral mais difíceis de descobrir e depurar.

Em arquiteturas monolíticos, estamos acostumados a depurar com pilhas de chamadas. Pilhas de chamadas são ferramentas incríveis que mostram o fluxo de execução (Método A que chama o Método B que chama o Método C), junto com detalhes e parâmetros sobre cada uma dessas chamadas. Isso é ótimo para monolitos ou serviços em execução em um único processo, mas como depuramos quando a chamada estiver em um limite de processo, não uma simples referência na pilha local? 

É aí que entra o rastreamento distribuído.  

O rastreamento distribuído é o equivalente às pilhas de chamadas nas arquiteturas modernas de nuvem e microsserviços, com a adição de um criador de perfil de desempenho simplista. No Azure Monitor, fornecemos duas experiências para consumo dos dados de rastreamento distribuído. A primeira é nossa exibição de [diagnóstico de transação](./transaction-diagnostics.md), que é como uma pilha de chamadas com uma dimensão de tempo adicionada. A exibição de diagnóstico de transação fornece visibilidade de uma única transação/solicitação, e é útil para encontrar a causa raiz de problemas de confiabilidade e gargalos de desempenho de cada solicitação.

O Azure Monitor também oferece uma exibição de [mapa de aplicativo](./app-map.md) que agrega muitas transações para mostrar uma exibição topológica de como os sistemas interagem e quais são as taxas média de desempenho e erro. 

## <a name="how-to-enable-distributed-tracing"></a>Como habilitar o rastreamento distribuído

Habilitar o rastreamento distribuído entre os serviços em um aplicativo é tão simples quanto adicionar o agente, o SDK ou a biblioteca apropriada a cada serviço, com base no idioma em que o serviço foi implementado.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Habilitando via Application Insights por meio de instrumentação ou SDKs automáticos

Os agentes de Application Insights e/ou SDKs para .NET, .NET Core, Java, Node.js e JavaScript oferecem suporte ao rastreamento distribuído nativamente. Veja abaixo as instruções para instalar e configurar cada SDK do Application Insights:

* [.NET](asp-net.md)
* [.NET Core](asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.js](../app/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

Com SDK adequado do Application Insights instalado e configurado, as informações de rastreamento são coletadas automaticamente para estruturas, bibliotecas e tecnologias populares pelos coletores automáticos de dependência do SDK. A lista completa de tecnologias com suporte está disponível na [documentação de coleta automática de dependência](./auto-collect-dependencies.md).

 Além disso, qualquer tecnologia pode ser rastreada manualmente com uma chamada para [TrackDependency](./api-custom-events-metrics.md) sobre o [TelemetryClient](./api-custom-events-metrics.md).

## <a name="enable-via-opencensus"></a>Habilitar por meio do OpenCensus

Além dos SDKs do Application Insights, o Application Insights também dá suporte ao rastreamento distribuído por meio do [OpenCensus](https://opencensus.io/). O OpenCensus é uma distribuição única de bibliotecas de software livre e independente de fornecedor, que tem como objetivo fornecer coleta de métricas e rastreamento distribuído para os serviços. Ele também permite que a comunidade de software livre habilite o rastreamento distribuído com tecnologias populares, como Redis, Memcached ou MongoDB. [A Microsoft colabora no OpenCensus com vários outros parceiros de monitoramento e de nuvem](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

O site do OpenCensus mantém a documentação de referência da API para [Python](https://opencensus.io/api/python/trace/usage.html) e [Go](https://godoc.org/go.opencensus.io), assim como vários guias diferentes para usar o OpenCensus. 

## <a name="next-steps"></a>Próximas etapas

* [Guia de uso do OpenCensus Python](https://opencensus.io/api/python/trace/usage.html)
* [Mapa do aplicativo](./app-map.md)
* [Monitoramento de desempenho de ponta a ponta](../app/tutorial-performance.md)

