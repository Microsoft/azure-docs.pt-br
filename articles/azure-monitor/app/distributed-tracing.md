---
title: Rastreamento distribuído no Azure Application Insights | Microsoft Docs
description: Fornece informações sobre o suporte da Microsoft para o rastreamento distribuído por meio do encaminhador local e parceria no projeto OpenCensus
services: application-insights
keywords: ''
author: nikmd23
ms.author: nimolnar
ms.reviewer: mbullwin
ms.date: 09/17/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7bc04748f2a5b8caa8f589140dd46f0650b7b390
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898826"
---
# <a name="what-is-distributed-tracing"></a>O que é o Rastreamento distribuído?

O advento de arquiteturas modernas de nuvem e [microsserviços](https://azure.com/microservices) deu origem a serviços simples e implantáveis de forma independente que podem ajudar a reduzir custos, aumentando a disponibilidade e a taxa de transferência. Mas, embora esses movimentos tenham facilitado a compreensão dos serviços individuais como um todo, eles tornaram os sistemas em geral mais difíceis de justificar e depurar.

Em arquiteturas monolíticas, nos acostumamos à depuração com as pilhas de chamadas. Pilhas de chamadas são ferramentas incríveis que mostram o fluxo de execução (Método A que chama o Método B que chama o Método C), junto com detalhes e parâmetros sobre cada uma dessas chamadas. Isso é ótimo para monolitos ou serviços em execução em um único processo, mas como depuramos quando a chamada estiver em um limite de processo, não uma simples referência na pilha local? 

É aí que entra o rastreamento distribuído.  

O rastreamento distribuído é o equivalente às pilhas de chamadas nas arquiteturas modernas de nuvem e microsserviços, com a adição de um criador de perfil de desempenho simplista. No Azure Monitor, fornecemos duas experiências para consumo dos dados de rastreamento distribuído. A primeira é nossa exibição de [diagnóstico de transação](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics), que é como uma pilha de chamadas com uma dimensão de tempo adicionada. A exibição de diagnóstico de transação fornece visibilidade de uma única transação/solicitação, e é útil para encontrar a causa raiz de problemas de confiabilidade e gargalos de desempenho de cada solicitação.

O Azure Monitor também oferece uma exibição de [mapa de aplicativo](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) que agrega muitas transações para mostrar uma exibição topológica de como os sistemas interagem e quais são as taxas média de desempenho e erro. 

## <a name="how-to-enable-distributed-tracing"></a>Como habilitar o rastreamento distribuído

Habilitar o rastreamento distribuído nos serviços em um aplicativo é tão simples quanto adicionar o SDK ou biblioteca adequada a cada serviço, com base na linguagem de implementação do serviço.

## <a name="enabling-via-application-insights-sdks"></a>Habilitação via SDKs do Application Insights

Os SDKs do Application Insights para .NET, .NET Core, Java, Node.js e JavaScript oferecem suporte nativo ao rastreamento distribuído. Veja abaixo as instruções para instalar e configurar cada SDK do Application Insights:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)

Com SDK adequado do Application Insights instalado e configurado, as informações de rastreamento são coletadas automaticamente para estruturas, bibliotecas e tecnologias populares pelos coletores automáticos de dependência do SDK. A lista completa de tecnologias com suporte está disponível na [documentação de coleta automática de dependência](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 Além disso, qualquer tecnologia pode ser rastreada manualmente com uma chamada para [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) sobre o [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Habilitar por meio do OpenCensus

Além dos SDKs do Application Insights, o Application Insights também dá suporte ao rastreamento distribuído por meio do [OpenCensus](https://opencensus.io/). O OpenCensus é uma distribuição única de bibliotecas de software livre e independente de fornecedor, que tem como objetivo fornecer coleta de métricas e rastreamento distribuído para os serviços. Ele também permite que a comunidade de software livre habilite o rastreamento distribuído com tecnologias populares, como Redis, Memcached ou MongoDB. [A Microsoft colabora no OpenCensus com vários outros parceiros de monitoramento e de nuvem](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

Para adicionar recursos de rastreamento distribuído a um aplicativo com OpenCensus, primeiro [instale e configure o Encaminhador Local do Application Insights](./../../azure-monitor/app/opencensus-local-forwarder.md). A partir daí, configure o OpenCensus para rotear os dados de rastreamento distribuído pelo Encaminhador Local. Há suporte para [Python](./../../azure-monitor/app/opencensus-python.md) e [Go](./../../azure-monitor/app/opencensus-go.md).

O site do OpenCensus mantém a documentação de referência da API para [Python](https://opencensus.io/api/python/trace/usage.html) e [Go](https://godoc.org/go.opencensus.io), assim como vários guias diferentes para usar o OpenCensus. 

## <a name="next-steps"></a>Próximas etapas

* [Guia de uso do OpenCensus Python](https://opencensus.io/api/python/trace/usage.html)
* [Mapa do aplicativo](./../../azure-monitor/app/app-map.md)
* [Monitoramento de desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)
