---
title: Monitoramento de nível de aplicativo Service Fabric do Azure
description: Saiba mais sobre eventos e logs de nível de aplicativo e serviço usados para monitorar e diagnosticar clusters do Service Fabric do Azure.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: a60eef008afae4185acc266c74c4fb0ce694d560
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627482"
---
# <a name="application-logging"></a>Registro em log do aplicativo

Instrumentar seu código não é apenas uma maneira de obter insights sobre seus usuários, mas também a única maneira de saber se algo está errado em seu aplicativo e diagnosticar o que precisa ser corrigido. Embora tecnicamente é possível conectar um depurador a um serviço de produção, ele não é uma prática comum. Portanto, é importante ter dados de instrumentação detalhados.

Alguns produtos instrumentam automaticamente seu código. Embora essas soluções possam funcionar bem, a instrumentação manual é quase sempre necessária para ser específica à sua lógica de negócios. No fim das contas, você deve ter informações suficientes para depurar legalmente o aplicativo. Aplicativos de Service Fabric podem ser instrumentados com qualquer estrutura de log. Este documento descreve algumas abordagens diferentes para instrumentar seu código e quando escolher uma abordagem sobre outra. 

Para exemplos de como usar essas sugestões, consulte [Adicionar registro em log ao aplicativo do Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>SDK do Application Insights

O Application Insights tem uma integração avançada com o Service Fabric pronta para uso. Os usuários podem adicionar os pacotes NuGet do Service Fabric do IA e receber dados e logs criados e coletados visíveis no Portal do Azure. Além disso, os usuários são incentivados a adicionar sua própria telemetria para diagnosticar e depurar os aplicativos e rastrear quais serviços e partes do aplicativo são mais utilizados. A classe [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient) na SDK fornece muitas maneiras de rastrear a telemetria nos aplicativos. Confira um exemplo de como instrumentar e adicionar Application Insights ao aplicativo no tutorial [monitorar e diagnosticar um aplicativo .NET](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource

Quando você cria uma solução do Service Fabric de um modelo do Visual Studio, uma classe derivada de **EventSource** (**ServiceEventSource** ou **ActorEventSource**) é gerada. Um modelo é criado, no qual você pode adicionar eventos para seu aplicativo ou serviço. O  nome EventSource **deve** ser exclusivo e deve ser renomeado da cadeia de caracteres do modelo padrão MyCompany- &lt; Solution &gt; - &lt; Project &gt; . Ter várias definições de **EventSource** que usam o mesmo nome causa um problema no tempo de execução. Cada evento definido deve ter um identificador exclusivo. Se um identificador não for exclusivo, ocorrerá uma falha de runtime. Algumas organizações atribuem antecipadamente intervalos de valores para evitar conflitos entre equipes de desenvolvimento separadas. Para saber mais, veja [blog do Vance](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource) ou a [documentação do MSDN](/previous-versions/msp-n-p/dn774985(v=pandp.20)).

## <a name="aspnet-core-logging"></a>Registro em log de ASP.NET Core

É importante planejar cuidadosamente como você instrumentará o seu código. O plano de instrumentação certa pode ajudar a evitar potencialmente desestabilizar sua base de código e, em seguida, precisar reinstrument o código. Para reduzir o risco, você pode escolher uma biblioteca de instrumentação como [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), que faz parte do Microsoft ASP.NET Core. Núcleo do ASP.NET tem um [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) interface que você pode usar com o provedor de sua escolha, minimizando o efeito no código existente. Você pode usar o código ASP.NET Core no Windows e Linux e no .NET Framework completo, então o código de instrumentação é padronizado.

## <a name="next-steps"></a>Próximas etapas

Após escolher o provedor de log para instrumentar os aplicativos e serviços, os logs e eventos precisam ser agregados para que possam ser enviados a qualquer plataforma de análise. Leia sobre [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) e [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) para reconhecer melhor algumas das opções recomendadas do Monitor do Azure.
