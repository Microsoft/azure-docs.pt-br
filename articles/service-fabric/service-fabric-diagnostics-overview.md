---
title: Visão geral de diagnóstico e monitoramento do Azure Service Fabric| Microsoft Docs
description: Saiba mais sobre monitoramento e diagnóstico para clusters, aplicativos e serviços do Service Fabric do Azure.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: a6c32058c68adbfd11a4cede6332b42076bea015
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952047"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitoramento e diagnóstico no Azure Service Fabric

Este artigo fornece uma visão geral de monitoramento e diagnóstico do Azure Service Fabric. O monitoramento e o diagnóstico são essenciais para o desenvolvimento, os testes e a implantação de cargas de trabalho em qualquer ambiente. Por exemplo, você pode controlar como seus aplicativos são usados, as ações realizadas pela plataforma do Service Fabric, a utilização de recursos com contadores de desempenho e a integridade geral do seu cluster. Você pode usar essas informações para diagnosticar e corrigir problemas e evitar que eles ocorram no futuro. As próximas seções explicarão brevemente cada área do monitoramento do Service Fabric a ser considerada para as cargas de trabalho de produção. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Monitoramento de aplicativo
O monitoramento de aplicativo controla como os recursos e componentes de um aplicativo estão sendo usados. Você deseja monitorar seus aplicativos para garantir que os problemas que impactam os usuários são capturados. A responsabilidade do monitoramento de aplicativos é dos usuários que desenvolvem um aplicativo e seus serviços, pois depende exclusivamente da lógica de negócios do aplicativo. Monitorar os seus aplicativos pode ser útil nos seguintes cenários:
* A quantidade de tráfego está enfrentando com o meu aplicativo? - Você precisa dimensionar seus serviços para atender às demandas dos usuários ou resolver um possível gargalo em seu aplicativo?
* São minhas chamadas de serviço a serviço com êxito e controladas?
* Quais ações são executadas pelos usuários do meu aplicativo? -Coleta de telemetria pode guiar o desenvolvimento futuro de recursos e melhorar diagnósticos para erros de aplicativo
* Meu aplicativo está lançando exceções não tratadas? 
* O que está acontecendo dentro dos serviços em execução dentro de Meus contêineres?

A grande novidade sobre monitoramento de aplicativos é que os desenvolvedores podem usar qualquer estrutura que gostariam de uma vez que ele reside dentro do contexto de seu aplicativo e as ferramentas! Você pode aprender mais sobre a solução do Azure para o monitoramento de aplicativos com o Azure Monitor - Application Insights no [análise de eventos com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
Também temos um tutorial com a maneira [configurar isso para aplicativos .NET](service-fabric-tutorial-monitoring-aspnet.md). Este tutorial aborda como instalar as ferramentas corretas, um exemplo para gravar a telemetria personalizada em seu aplicativo e visualizar o diagnóstico e a telemetria do aplicativo no portal do Azure. 


## <a name="platform-cluster-monitoring"></a>Monitoramento da plataforma (cluster)
Um usuário está no controle sobre qual telemetria é proveniente de seus aplicativos, pois um usuário escreve o código em si, mas o que sobre o diagnóstico da plataforma do Service Fabric? Uma das metas do Service Fabric é manter os aplicativos resilientes a falhas de hardware. Esse objetivo é conseguido por meio da capacidade de serviços de sistema da plataforma para detectar problemas de infraestrutura e rápidas failover de cargas de trabalho para outros nós no cluster. Mas, neste caso específico, e se os próprios serviços do sistema tiverem problemas? Ou se, ao tentar implantar ou mover uma carga de trabalho, as regras para a veiculação de serviços forem violadas? O Service Fabric fornece diagnósticos para esses e mais para garantir que você seja informado sobre a atividade que ocorre em seu cluster. Alguns cenários de amostra para monitoramento de cluster incluem:

O Service Fabric fornece um conjunto abrangente de eventos fora da caixa. Esses [eventos do Service Fabric](service-fabric-diagnostics-events.md) podem ser acessados por meio do EventStore ou do canal operacional (canal de eventos exposto pela plataforma). 

* Canais de eventos do Service Fabric - No Windows, eventos do Service Fabric estão disponíveis a partir de um único provedor ETW com um conjunto de `logLevelKeywordFilters` relevante usado para escolher entre os canais Operacional e Dados e Mensagens - é assim que separamos eventos do Service Fabric de saída para serem filtrados conforme necessário. No Linux, todos os eventos do Service Fabric são fornecidos por meio de LTTng e são colocados em uma tabela de Armazenamento, de onde eles podem ser filtrados conforme necessário. Esses canais contém eventos curados e estruturados que podem ser usados para entender melhor o estado do cluster. O Diagnóstico é habilitado por padrão no momento da criação do cluster, que cria uma tabela de Armazenamento do Azure onde os eventos desses canais são enviados para a consulta no futuro. 

* EventStore - o EventStore é um recurso oferecido pela plataforma que fornece eventos de plataforma do Service Fabric disponíveis no Service Fabric Explorer e por meio da API REST. Você pode ver uma exibição de instantâneo do que está acontecendo em seu cluster para cada entidade, por exemplo, nó, serviço, aplicativo e consulta com base na hora do evento. Você também pode ler mais sobre o EventStore na [Visão geral do EventStore](service-fabric-diagnostics-eventstore.md).    

![EventStore](media/service-fabric-diagnostics-overview/eventstore.png)

Os diagnósticos fornecidos estão na forma de um conjunto abrangente de eventos prontos para uso. Esses [eventos do Service Fabric](service-fabric-diagnostics-events.md) ilustram as ações feitas pela plataforma em entidades diferentes, como Nós, Aplicativos, Serviços, Partições, etc. No último cenário acima, se um nó fosse desativado, a plataforma emitiria um evento `NodeDown` e você poderia ser notificado imediatamente por sua ferramenta de monitoramento preferida. Outros exemplos comuns incluem `ApplicationUpgradeRollbackStarted` ou `PartitionReconfigured` durante um failover. **Os mesmos eventos estão disponíveis nos clusters Windows e Linux.**

Os eventos são enviados através de canais padrão no Windows e no Linux e podem ser lidos por qualquer ferramenta de monitoramento que os suporte. A solução do Azure Monitor é logs do Azure Monitor. Fique à vontade ler mais sobre nossos [integração de logs do Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) que inclui um painel operacional personalizado para seu cluster e alguns exemplos de consultas do qual você pode criar alertas. Mais conceitos de monitoramento de cluster estão disponíveis em [evento de nível de plataforma e geração de log](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Monitoramento da integridade
A plataforma do Service Fabric inclui um modelo de integridade, que fornece o relatório de integridade extensível para o status de entidades em um cluster. Cada nó, aplicativo, serviço, partição, réplicas ou instância, tem um status de integridade continuamente atualizável. O status de integridade pode ser "OK", "Aviso" ou "Erro". Pense em eventos do Service Fabric como verbos feitos pelo cluster para várias entidades e health como um adjetivo para cada entidade. Cada vez que a integridade de uma determinada entidade transita, um evento também será emitido. Dessa forma, você pode configurar consultas e alertas para eventos de integridade em sua ferramenta de monitoramento escolhida, assim como qualquer outro evento. 

Além disso, até permitimos que os usuários substituam a integridade de entidades. Se o seu aplicativo estiver passando por uma atualização e você tiver testes de validação com falha, poderá gravar no Service Fabric Health usando a Health API para indicar que seu aplicativo não está mais íntegro e o Service Fabric reverterá automaticamente a atualização! Para obter mais informações sobre o modelo de integridade, confira a introdução [ao monitoramento de integridade do Service Fabric](service-fabric-health-introduction.md)

![Painel de integridade SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Watchdogs
Um watchdog é um serviço separado que pode inspecionar a integridade e a carga entre serviços, executar ping de endpoints e informar a integridade de qualquer coisa no cluster. Isso pode ajudar a evitar erros que não seriam detectados com base no modo de exibição de um único serviço. Os watchdogs também são um bom local para hospedar código que executa ações corretivas sem interação do usuário (por exemplo, limpar arquivos de log no armazenamento em determinados intervalos de tempo). Você pode encontrar uma implementação de serviço de watchdog de exemplo [aqui](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Monitoramento de infraestrutura (desempenho)
Agora que abordamos os diagnósticos em seu aplicativo e na plataforma, como sabemos se o hardware está funcionando conforme o esperado? Monitoramento de sua infraestrutura subjacente é uma parte importante para compreender o estado do seu cluster e a utilização de recursos. O desempenho do sistema de medição depende de muitos fatores que podem ser subjetivos, dependendo de suas cargas de trabalho. Esses fatores geralmente são medidos por meio de contadores de desempenho. Esses contadores de desempenho podem vir de uma variedade de fontes, incluindo o sistema operacional, o .NET framework ou a própria plataforma do Service Fabric. Alguns cenários em que eles seriam úteis são

* Eu estou utilizando o meu hardware com eficiência? Você quer usar seu hardware com 90% da CPU ou 10% da CPU. Isso é útil ao dimensionar seu cluster ou otimizar os processos do seu aplicativo.
* Posso prever problemas de infraestrutura proativamente? - muitos problemas são precedidos por alterações bruscas no desempenho, para que você possa usar contadores de desempenho, como I / O de rede e utilização da CPU, para prever e diagnosticar os problemas de maneira proativa.

Uma lista de contadores de desempenho que devem ser coletados no nível de infraestrutura pode ser encontrada em [Métricas de desempenho](service-fabric-diagnostics-event-generation-perf.md). 

O Service Fabric também fornece um conjunto de contadores de desempenho para os modelos de programação de atores e Reliable Services. Se você estiver usando um desses modelos, esses contadores de desempenho poderão fornecer informações para garantir que seus atores estejam girando corretamente, ou que suas solicitações de serviço confiáveis sejam tratadas com rapidez suficiente. Para obter mais informações, consulte [Monitoramento para comunicação remota do Reliable Service](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) e [Monitoramento de desempenho para Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

A solução de Monitor do Azure para coletar esses é que os logs do Azure Monitor assim como o monitoramento de nível de plataforma. Você deve usar o [agente do Log Analytics](service-fabric-diagnostics-oms-agent.md) para coletar os contadores de desempenho apropriados e exibi-los nos logs do Azure Monitor.

## <a name="recommended-setup"></a>Configuração recomendada
Agora que passamos sobre cada área de cenários de monitoramento e exemplo, aqui está um resumo do Azure, ferramentas de monitoramento e configurar necessários para monitorar todas as áreas acima. 

* Monitoramento de aplicativos com [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Monitoramento de cluster com [agente de diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md) e [registra em log do Azure Monitor](service-fabric-diagnostics-oms-setup.md)
* Monitoramento de infraestrutura com [registra em log do Azure Monitor](service-fabric-diagnostics-oms-agent.md)

Você também pode usar e modificar o modelo de ARM de exemplo localizado [aqui](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) para automatizar a implantação de todos os recursos necessários e os agentes. 

## <a name="other-logging-solutions"></a>Outras soluções de registro em log

Embora as duas soluções que recomendamos, [registra em log do Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) e [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) criaram na integração com o Service Fabric, muitos eventos são gravados por meio de provedores de ETW e são extensível com outras soluções de registro em log. Você também deve examinar o [Elastic Stack](https://www.elastic.co/products) (especialmente se estiver considerando a execução de um cluster em um ambiente offline), [Dynatrace](https://www.dynatrace.com/), ou qualquer outra plataforma de sua preferência. Temos uma lista de parceiros integrados disponíveis [aqui](service-fabric-diagnostics-partners.md).

Os pontos-chave para qualquer plataforma que você escolher devem incluir o quanto você está confortável com a interface do usuário, os recursos de consulta, as visualizações e painéis personalizados disponíveis e as ferramentas adicionais que eles fornecem para aprimorar sua experiência de monitoramento. 

## <a name="next-steps"></a>Próximas etapas

* Para começar a instrumentar seus aplicativos, consulte [Geração de log e evento de nível de aplicativo](service-fabric-diagnostics-event-generation-app.md).
* Siga as etapas para configurar o Application Insights para seu aplicativo com [Monitorar e diagnosticar um aplicativo ASP.NET Core no Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Saiba mais sobre como monitorar a plataforma e os eventos que o Service Fabric fornece para você em [Geração de log e evento nível de plataforma](service-fabric-diagnostics-event-generation-infra.md).
* Configurar a integração de logs do Azure Monitor com o Service Fabric em [configurar logs do Azure Monitor para um cluster](service-fabric-diagnostics-oms-setup.md)
* Saiba como configurar logs do Azure Monitor para monitorar os contêineres- [monitoramento e diagnóstico para contêineres do Windows no Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Consulte os problemas de diagnóstico de exemplo e soluções com o Service Fabric no [cenários comuns de diagnóstico](service-fabric-diagnostics-common-scenarios.md)
* Check-out de outros produtos de diagnóstico que se integram ao Service Fabric no [parceiros de diagnóstico do Service Fabric](service-fabric-diagnostics-partners.md)
* Saiba mais sobre as recomendações gerais de monitoramentos para recursos do Azure - [Práticas Recomendadas - Monitoramento e Diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 