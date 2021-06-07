---
title: Análise de eventos de Service Fabric do Azure com logs de Azure Monitor
description: Saiba mais sobre como Visualizar e analisar eventos usando logs de Azure Monitor para monitoramento e diagnóstico de clusters de Service Fabric do Azure.
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: f1e22213c857b400cc36c51cefb90e2379352893
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628961"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Análise de eventos e visualização com logs de Azure Monitor
 O Azure Monitor coleta e analisa a telemetria dos aplicativos e serviços hospedados na nuvem e fornece as ferramentas de análise para ajudar a maximizar sua disponibilidade e desempenho. Este artigo descreve como executar consultas em logs de Azure Monitor para obter informações e solucionar problemas do que está acontecendo no cluster. As seguintes perguntas comuns são abordadas:

* Como solucionar problemas de eventos de integridade?
* Como saber quando um nó fica inoperante?
* Como saber se os serviços do meu aplicativo foram iniciados ou interrompidos?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Visão geral do espaço de trabalho Log Analytics

>[!NOTE] 
>Enquanto o armazenamento de diagnóstico é habilitado por padrão no momento da criação do cluster, você deve ainda configurar o espaço de trabalho do Log Analytics para ler do armazenamento de diagnóstico.

Azure Monitor logs coleta dados de recursos gerenciados, incluindo uma tabela de armazenamento do Azure ou um agente, e os mantém em um repositório central. Os dados podem ser usado para análise, alertas e visualização ou para mais exportação. Os logs de Azure Monitor dão suporte a eventos, dados de desempenho ou qualquer outro dado personalizado. Confira [as etapas para configurar a extensão de diagnóstico para agregar eventos](service-fabric-diagnostics-event-aggregation-wad.md) e [etapas para criar um log Analytics espaço de trabalho para ler os eventos no armazenamento](service-fabric-diagnostics-oms-setup.md) para certificar-se de que os dados estão fluindo para Azure monitor logs.

Depois que os dados são recebidos por logs de Azure Monitor, o Azure tem várias *soluções de monitoramento* que são soluções em pacotes ou painéis operacionais para monitorar dados de entrada, personalizados para vários cenários. Isso inclui uma solução de *Análise do Service Fabric* e uma solução de *Contêineres*, que são as duas mais relevantes para diagnóstico e monitoramento ao usar clusters do Service Fabric. Este artigo descreve como usar a solução de Análise do Service Fabric, que é criada com o workspace.

## <a name="access-the-service-fabric-analytics-solution"></a>Acesse a solução de Análise do Service Fabric

No [portal do Azure](https://portal.azure.com), vá para o grupo de recursos no qual você criou a solução de análise do Service Fabric.

Selecione o Resource **Fabric \<nameOfOMSWorkspace\>**.

No `Summary`, você verá o bloco na forma de um gráfico para cada uma das soluções habilitadas, incluindo uma para o Service Fabric. Clique no grafo **Service Fabric** para ir para a solução de Análise do Service Fabric.

![Solução do Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

A imagem acima é a home page da solução de Análise do Service Fabric. Essa home page fornece uma visão instantânea do que está acontecendo no seu cluster.

![Captura de tela que mostra a home page da solução de Análise do Service Fabric.](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Se você habilitou o diagnóstico após a criação do cluster, você pode ver eventos para 

* [Eventos de cluster do Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Eventos do modelo de programação Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Eventos do modelo de programação Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Além dos eventos do Service Fabric prontos para uso, é possível coletar eventos do sistema mais detalhados ao [atualizar a configuração da sua extensão de diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Exibir eventos do Service Fabric, incluindo ações em nós

Na página Análise do Service Fabric, clique no grafo para **Eventos do Service Fabric**.

![Canal Operacional de Solução do Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Clique em **Lista** para exibir os eventos em uma lista. Uma vez aqui, você verá todos os eventos do sistema que foram coletados. Para referência, esses são os de **WADServiceFabricSystemEventsTable** na conta de Armazenamento do Microsoft Azure, e igualmente os eventos de atores e serviços confiáveis que você vê ao lado são dessas respectivas tabelas.
    
![Canal Operacional de Consulta](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Como alternativa, você pode clicar na lupa à esquerda e usar a linguagem de consulta Kusto para localizar o que você está procurando. Por exemplo, para localizar todas as ações executadas em nós no cluster, você pode usar a consulta a seguir. As IDs de evento usadas abaixo são encontradas em [referência de eventos do canal operacional](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Você pode consultar em vários outros campos como em nós específicos (computador), o serviço do sistema (TaskName).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Exibir eventos dos Atores e Serviços Confiáveis do Service Fabric

Na página Análise do Service Fabric, clique no grafo para o **Reliable Services**.

![Reliable Services de Solução do Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Clique em **Lista** para exibir os eventos em uma lista. Aqui você pode ver eventos dos serviços confiáveis. Você pode ver eventos diferentes para quando o runasync de serviço é iniciado e concluído que normalmente acontece em implantações e atualizações. 

![Reliable Services de Consulta](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Os eventos de ator confiável podem ser exibidos de forma semelhante. Para configurar os eventos mais detalhados para atores confiáveis, você precisa alterar o `scheduledTransferKeywordFilter` na configuração para a extensão de diagnóstico (mostrada abaixo). Detalhes sobre os valores deles estão em [referência de eventos de atores confiáveis](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

A linguagem de consulta Kusto é eficiente. Outra consulta valiosa que você pode executar é descobrir quais nós estão gerando a maioria dos eventos. A consulta na captura de tela abaixo mostra os eventos operacionais do Service Fabric agregados ao serviço e ao nó específico.

![Eventos de Consulta por nó](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Próximas etapas

* Para habilitar o monitoramento de infraestrutura, ou seja, contadores de desempenho, vá para [Adicionando o agente do Log Analytics](service-fabric-diagnostics-oms-agent.md). O agente coleta contadores de desempenho e os adiciona ao workspace existente.
* Para clusters locais, Azure Monitor logs oferece um gateway (proxy de encaminhamento HTTP) que pode ser usado para enviar dados a logs de Azure Monitor. Leia mais sobre isso em [conectando computadores sem acesso à Internet para Azure monitor logs usando o gateway de log Analytics](../azure-monitor/agents/gateway.md).
* Configure os [alertas automatizados](../azure-monitor/alerts/alerts-overview.md) para auxiliar na detecção e no diagnóstico.
* Familiarize-se com os recursos de [pesquisa e consulta de logs](../azure-monitor/logs/log-query-overview.md) oferecidos como parte dos logs do Azure Monitor.
* Obtenha uma visão geral mais detalhada dos logs de Azure Monitor e o que ele oferece, leia [o que são Azure monitor logs?](../azure-monitor/overview.md).
