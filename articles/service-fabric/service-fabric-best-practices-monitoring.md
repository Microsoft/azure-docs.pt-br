---
title: Práticas recomendadas de monitoramento de Service Fabric do Azure
description: Práticas recomendadas e considerações de design para monitorar clusters e aplicativos usando o Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 0eb9ce24f9ead44b7ba5a4d28d24177e62cb7757
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950511"
---
# <a name="monitoring-and-diagnostic-best-practices-for-azure-service-fabric"></a>Práticas recomendadas de monitoramento e diagnóstico para o Azure Service Fabric

O [monitoramento e o diagnóstico](./service-fabric-diagnostics-overview.md) são essenciais para o desenvolvimento, o teste e a implantação de cargas de trabalho em qualquer ambiente de nuvem. Por exemplo, você pode controlar como seus aplicativos são usados, as ações realizadas pela plataforma do Service Fabric, a utilização de recursos com contadores de desempenho e a integridade geral do seu cluster. Você pode usar essas informações para diagnosticar e corrigir problemas e evitar que eles ocorram no futuro.

## <a name="application-monitoring"></a>Monitoramento de aplicativo

O monitoramento de aplicativo controla como os recursos e componentes de um aplicativo estão sendo usados. Monitore seus aplicativos para garantir que os problemas que afetam os usuários sejam capturados. O monitoramento de aplicativo é a responsabilidade daqueles que desenvolvem o aplicativo e seus serviços, pois depende exclusivamente da lógica de negócios do aplicativo. É recomendável que você configure o monitoramento de aplicativo com o [Application Insights](./service-fabric-tutorial-monitoring-aspnet.md), a ferramenta de monitoramento de aplicativo do Azure.

## <a name="cluster-monitoring"></a>Monitoramento do cluster

Uma das metas do Service Fabric é tornar os aplicativos resilientes a falhas de hardware. Esse objetivo é conseguido por meio da capacidade de serviços de sistema da plataforma para detectar problemas de infraestrutura e rápidas failover de cargas de trabalho para outros nós no cluster. Mas e se os próprios serviços do sistema tiverem problemas? Ou se, ao tentar implantar ou mover uma carga de trabalho, as regras para a veiculação de serviços forem violadas? O Service Fabric fornece diagnóstico para esses e outros problemas, a fim de garantir que você seja informado sobre como a plataforma Service Fabric interage com aplicativos, serviços, contêineres e nós.

Para clusters do Windows, é recomendável que você configure o monitoramento de cluster com o [agente de diagnóstico](./service-fabric-diagnostics-event-aggregation-wad.md) e [os logs de Azure monitor](./service-fabric-diagnostics-oms-setup.md).

Para clusters do Linux, os logs de Azure Monitor também são a ferramenta recomendada para o monitoramento de infraestrutura e plataforma do Azure. O diagnóstico da plataforma Linux exige outra configuração, conforme observado em [Eventos de cluster do Linux do Service Fabric no Syslog](./service-fabric-diagnostics-oms-syslog.md).

## <a name="infrastructure-monitoring"></a>Monitoramento da infraestrutura

[Azure monitor logs](./service-fabric-diagnostics-oms-agent.md) é recomendado para monitorar eventos no nível do cluster. Depois de configurar o agente do Log Analytics no workspace, conforme descrito no link anterior, você poderá coletar métricas de desempenho como Utilização da CPU, contadores de desempenho do .NET como utilização da CPU no nível do processo, contadores de desempenho do Service Fabric como o número de exceções de um serviço confiável e métricas de contêiner como Utilização da CPU.  Você precisará Gravar logs de contêiner para stdout ou stderr para que eles estejam disponíveis nos logs de Azure Monitor.

## <a name="watchdogs"></a>Watchdogs

Em geral, um watchdog é um serviço separado que inspeciona a integridade e a carga entre serviços, executa ping em pontos de extremidade e relata eventos de integridade inesperados no cluster. Isso pode ajudar a evitar erros que podem não ser detectados com base apenas no desempenho de um único serviço. Os Watchdogs também são um bom local para hospedar código que executa ações corretivas que não exigem interação do usuário, como a limpeza de arquivos de log no armazenamento em determinados intervalos de tempo. Se você quiser um serviço de Watchdog de código-fonte aberto totalmente implementado que inclua um modelo de extensibilidade de Watchdog fácil de usar e que seja executado em clusters Windows e Linux, consulte o projeto [FabricObserver](https://aka.ms/sf/FabricObserver) . FabricObserver é um software pronto para produção. Incentivamos você a implantar o FabricObserver em seus clusters de teste e produção e estendê-lo para atender às suas necessidades por meio de seu modelo de plug-in ou com a bifurcação e a criação de seus próprios observadores internos. O primeiro (plug-ins) é a abordagem recomendada.

## <a name="next-steps"></a>Próximas etapas

* Comece a instrumentar seus aplicativos: [geração de log e evento de nível de aplicativo](service-fabric-diagnostics-event-generation-app.md).
* Siga as etapas para configurar o Application Insights para seu aplicativo com [Monitorar e diagnosticar um aplicativo ASP.NET Core no Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Saiba mais sobre como monitorar a plataforma e os eventos Service Fabric fornece a você: [geração de log e evento de nível de plataforma](service-fabric-diagnostics-event-generation-infra.md).
* Configurar a integração de logs de Azure Monitor com o Service Fabric: [Configurar Logs de Azure monitor para um cluster](service-fabric-diagnostics-oms-setup.md)
* Saiba como configurar logs de Azure Monitor para monitorar contêineres: [monitoramento e diagnóstico para contêineres do Windows no Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Confira os problemas de diagnóstico de exemplo e as soluções com o Service Fabric: [Cenários comuns de diagnóstico](service-fabric-diagnostics-common-scenarios.md)
* Saiba mais sobre as recomendações gerais de monitoramento para recursos do Azure: [práticas recomendadas-monitoramento e diagnóstico](/azure/architecture/best-practices/monitoring).
