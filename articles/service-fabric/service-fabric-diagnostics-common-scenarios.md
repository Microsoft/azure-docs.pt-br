---
title: Cenários comuns de diagnóstico do Azure Service Fabric | Microsoft Docs
description: Saiba como solucionar problemas de cenários comuns com o Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 265aea1b8873d812859b39175c732c3e7118cbb5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "60394068"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnosticar cenários comuns com o Service Fabric

Este artigo ilustra os cenários comuns que os usuários encontraram na área de monitoramento e diagnóstico com o Service Fabric. Os cenários apresentados abrangem todas as 3 camadas do service fabric: Aplicativos, Cluster e infraestrutura. Cada solução usa logs de Application Insights e Azure Monitor, ferramentas de monitoramento do Azure, para concluir cada cenário. As etapas em cada solução fornecem aos usuários uma introdução sobre como usar os logs Application Insights e Azure Monitor no contexto de Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Pré-requisitos e recomendações

As soluções neste artigo usará as ferramentas a seguir. Recomendamos que você tenha os itens a seguir instalados e configurados:

* [Application Insights com Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Habilite o Diagnóstico do Azure no seu cluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Configurar um espaço de trabalho Log Analytics](service-fabric-diagnostics-oms-setup.md)
* [Agente do Log Analytics para rastrear Contadores de Desempenho](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Como posso ver exceções sem tratamento no meu aplicativo?

1. Navegue até o recurso do Application Insights com o qual seu aplicativo está configurado.
2. Clique em *Pesquisa* no canto superior esquerdo. Em seguida,clique no filtro no painel seguinte.

    ![Visão geral do AI](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Você verá muitos tipos de eventos (rastreamentos, solicitações, eventos personalizados). Escolha "Exceção" como filtro.

    ![Lista de filtros de AI](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Clicando em uma exceção na lista, você pode ver mais detalhes, incluindo o contexto do serviço se você estiver usando o SDK do Application Insights do Service Fabric.

    ![Exceção de AI](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Como visualizo quais chamadas HTTP são usadas em meus serviços?

1. No mesmo recurso do Application Insights, você pode filtrar "solicitações" em vez de exceções e exibir todas as solicitações feitas
2. Se estiver usando o SDK do Application Insights do Service Fabric, você poderá ver uma representação visual dos seus serviços conectados um ao outro e o número de solicitações com êxito e falha. À esquerda, clique em "Mapa do aplicativo"

    ![Folha de mapa do aplicativo AI](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![Mapa de aplicativo do AI](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Para obter mais informações sobre o mapa de aplicativo, visite a [documentação do Mapa do aplicativo](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Como crio um alerta quando um nó falhar

1. Eventos de nó são controlados pelo cluster do Service Fabric. Navegue para o recurso da solução de Análise do Service Fabric chamado **ServiceFabric(NameofResourceGroup)**
2. Clique no gráfico na parte inferior da folha intitulado "Resumo"

    ![Solução de logs de Azure Monitor](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Aqui, você tem muitos gráficos e blocos exibindo várias métricas. Clique em um dos gráficos, e ele o levará para a Pesquisa de Logs. Aqui, você pode consultar quaisquer eventos de cluster ou os contadores de desempenho.
4. Insira a consulta a seguir. Essas IDs de evento são encontradas na [Referência de eventos de nó](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Clique em "Nova Regra de Alerta" na parte superior, e agora, sempre que um evento chegar com base nessa consulta, você receberá um alerta no seu método de comunicação escolhido.

    ![Novo alerta dos logs do Azure Monitor](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Como posso ser avisado de reversões de atualização de aplicativo?

1. Na mesma janela de Pesquisa de Logs, como antes, digite a seguinte consulta para reversões de atualização. Essas IDs de evento são encontradas na [Referência de eventos de aplicativo](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Clique em "Nova Regra de Alerta" na parte superior, e agora, sempre que um evento chegar com base nessa consulta, você receberá um alerta.

## <a name="how-do-i-see-container-metrics"></a>Como posso ver as métricas de contêiner?

A mesma exibição com todos os gráficos, você verá alguns blocos para o desempenho de seus contêineres. Você precisa que o agente do Log Analytics e [solução de Monitoramento de Contêiner](service-fabric-diagnostics-oms-containers.md) para essas peças preencherem.

![Métricas de contêiner do Log Analytics](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Para instrumentar a telemetria de **dentro** de seu contêiner, você precisará adicionar o [pacote do nuget do Application Insights para contêineres](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Como posso monitorar contadores de desempenho?

1. Depois que você tiver adicionado o Agente do Log Analytics ao cluster de que você precisa para adicionar contadores de desempenho específicos que você deseja controlar. Navegue até a página do Espaço de Trabalho do Log Analytics no portal – na página da solução, a guia de espaço de trabalho está no menu à esquerda.

    ![Guia de espaço de trabalho do Log Analytics](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Uma vez na página do workspace, clique em "Configurações avançadas" no mesmo menu à esquerda.

    ![Configurações avançadas do Log Analytics](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Clique em Dados > Contadores de Desempenho do Windows (Dados > Contadores de Desempenho do Linux para Máquinas Linux) para começar a coletar contadores específicos de seus nós através do Agente do Log Analytics. Aqui estão exemplos de formato para os contadores a serem adicionados

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     No início rápido, VotingData e VotingWeb são os nomes de processo usados, portanto, como seria o acompanhamento desses contadores

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Contadores de desempenho do Log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Isso permitirá que você veja como sua infraestrutura está manipulando as cargas de trabalho e definirá alertas relevantes com base na utilização de recursos. Por exemplo, talvez você queira definir um alerta se a utilização total do processador ficar acima de 90% ou abaixo de 5%. O nome do contador que você usaria para isso é "% do Tempo do Processador". Você poderá fazer isso criando uma regra de alerta para a consulta a seguir:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Como acompanhar o desempenho de meu Reliable Services e Atores?

Para acompanhar o desempenho de Reliable Services ou atores em seus aplicativos, você também deve coletar os contadores ator Service Fabric, método de ator, serviço e método de serviço. Aqui estão exemplos de contadores de desempenho de serviço confiável e ator a serem coletados

>[!NOTE]
>Service Fabric contadores de desempenho não podem ser coletados pelo agente do Log Analytics no momento, mas podem ser coletados por [outras soluções de diagnóstico](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Verifique esses links para a lista completa de contadores de desempenho em Reliable [Services](service-fabric-reliable-serviceremoting-diagnostics.md) e [Atores](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Próximas etapas

* [Configurar alertas no AI](../azure-monitor/app/alerts.md) para ser notificado sobre mudanças no desempenho ou uso
* [Detecção Inteligente no Application Insights](../azure-monitor/app/proactive-diagnostics.md) realiza uma análise pró-ativa da telemetria enviada ao AI para avisá-lo sobre possíveis problemas de desempenho
* Saiba mais sobre [alertas](../log-analytics/log-analytics-alerts.md) de Azure monitor logs para auxiliar na detecção e no diagnóstico.
* Para clusters locais, Azure Monitor logs oferece um gateway (proxy de encaminhamento HTTP) que pode ser usado para enviar dados a logs de Azure Monitor. Leia mais sobre isso em [conectando computadores sem acesso à Internet para Azure monitor logs usando o gateway de log Analytics](../azure-monitor/platform/gateway.md)
* Familiarize-se com os recursos de [pesquisa de logs e consulta](../log-analytics/log-analytics-log-searches.md) oferecidos como parte dos logs de Azure monitor
* Obtenha uma visão geral mais detalhada dos logs de Azure Monitor e o que ele oferece, leia [o que são Azure monitor logs?](../operations-management-suite/operations-management-suite-overview.md)
