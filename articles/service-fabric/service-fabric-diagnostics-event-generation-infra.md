---
title: "Monitoramento no nível de plataforma do Azure Service Fabric | Microsoft Docs"
description: "Saiba mais sobre eventos e logs no nível de plataforma usados para monitorar e diagnosticar clusters do Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: c5857515ae8357b003f0999c4b11bd666c32bbf9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="platform-level-event-and-log-generation"></a>Geração de eventos e logs no nível de plataforma

## <a name="monitoring-the-cluster"></a>Monitoramento de cluster

É importante monitorar o nível de plataforma para determinar se o hardware e o cluster estão funcionando conforme esperado. O Service Fabric pode ajudar a manter os aplicativos em execução durante falhas de hardware, mas você ainda precisa diagnosticar se um erro está ocorrendo em um aplicativo ou na infraestrutura subjacente. Você também deve monitorar os clusters para planejar melhor a capacidade, ajudando nas decisões sobre a adição ou remoção de hardware.

O Service Fabric fornece cinco canais de log diferentes prontos que geram os eventos a seguir:

* Canal operacional: operações de alto nível executadas pelo Service Fabric e pelo cluster, incluindo eventos para um nó surgindo, um novo aplicativo sendo implantado ou uma reversão de upgrade do SF, etc.
* Canal operacional – detalhado: relatórios de integridade e decisões de balanceamento de carga
* Canal de dados e mensagens: logs e eventos críticos gerados em nosso sistema de mensagens (atualmente, apenas o ReverseProxy) e o caminho de dados (modelos de Reliable Services)
* Canal de dados e mensagens – detalhado: canal detalhado que contém todos os logs não críticos dos dados e das mensagens no cluster (esse canal tem um volume muito alto de eventos)   
* [Eventos do Reliable Services](service-fabric-reliable-services-diagnostics.md): eventos específicos do modelo de programação
* [Eventos do Reliable Actors](service-fabric-reliable-actors-diagnostics.md): contadores de desempenho e eventos específicos do modelo de programação
* Logs de suporte: logs do sistema gerados pelo Service Fabric apenas para serem usados por nós ao fornecer suporte

Esses vários canais abrangem a maior parte dos logs no nível de plataforma que são recomendados. Para melhorar o log no nível de plataforma, considere a possibilidade de investir em uma melhor compreensão do modelo de integridade e adicionar relatórios de integridade personalizados, além de adicionar **Contadores de Desempenho** personalizados para desenvolver uma compreensão em tempo real do impacto dos serviços e aplicativos no cluster.

### <a name="azure-service-fabric-health-and-load-reporting"></a>Relatórios de carga e integridade do Azure Service Fabric

O Service Fabric tem seu próprio modelo de integridade que é descrito em detalhes nestes artigos:
- [Introdução ao monitoramento de integridade do Service Fabric](service-fabric-health-introduction.md)
- [Relatar e verificar a integridade de serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Adicionar relatórios de integridade personalizados do Service Fabric](service-fabric-report-health.md)
- [Como exibir relatórios de integridade do Service Fabric](service-fabric-view-entities-aggregated-health.md)

O monitoramento de integridade é fundamental para vários aspectos da operação de um serviço. Monitoramento de integridade é especialmente importante quando o Service Fabric executa uma atualização do aplicativo nomeado. Depois de cada domínio de atualização do serviço é atualizado e está disponível para seus clientes, o domínio de atualização deve passar verificações de integridade antes da implantação passe para o próximo domínio de atualização. Se não for possível obter um status de integridade, a implantação será revertida, deixando o aplicativo em um bom estado conhecido novamente. Embora alguns clientes talvez seja afetados antes que os serviços são revertidos, a maioria dos clientes não enfrentar um problema. Além disso, a resolução ocorreu relativamente rápido e sem precisar aguardar a ação de um operador humano. Quanto mais verificações de integridade são incorporadas ao código, mais flexível será o serviço aos problemas de implantação.

Outro aspecto da integridade do serviço são os relatórios de métrica do serviço. As métricas são importantes no Service Fabric porque eles são usados para equilibrar o uso de recursos. As métricas também podem ser um indicador de integridade do sistema. Por exemplo, você pode ter um aplicativo que tenha muitos serviços, e cada instância informa uma métrica RPS (solicitações por segundo). Se um dos serviços estiver usando mais recursos do que outro serviço, o Service Fabric moverá as instâncias de serviço pelo cluster para tentar manter a utilização de recursos uniforme. Para obter uma explicação mais detalhada de como funciona a utilização de recursos, consulte [gerenciar o consumo de recursos e carga na Service Fabric com métricas](service-fabric-cluster-resource-manager-metrics.md).

Métricas também podem ajudar a fornecer informações sobre o desempenho do seu serviço. Ao longo do tempo, você pode usar métricas para verificar se o serviço está operando dentro dos parâmetros esperados. Por exemplo, se as tendências mostram em 9: 00 na segunda-feira de manhã média RPS é 1.000, você pode definir um relatório de integridade que o alerta se o RPS estiver abaixo de 500 ou acima 1.500. Tudo pode estar ocorrendo perfeitamente bem, mas pode valer a pena dar uma olhada para garantir que seus clientes estão tendo uma ótima experiência. Seu serviço pode definir um conjunto de métricas que podem ser informadas para fins de verificação de integridade sem afetar o balanceamento de recursos do cluster. Para fazer isso, defina o peso da métrica como zero. É recomendável que você inicie todas as métricas com peso zero e não aumente o peso até ter certeza de que você compreende como ponderar a métrica afeta o balanceamento de recursos do cluster.

> [!TIP]
> Não use um número excessivo de métricas ponderadas. Pode ser difícil entender por que instâncias de serviço estão sendo movidas para balanceamento. Algumas métricas podem ir muito longe!

Todas as informações que possam indicar a integridade e o desempenho do seu aplicativo são candidatas aos relatórios de métricas e de integridade. Um contador de desempenho de CPU pode dizer como seu nó é utilizado, mas não diz se um serviço em particular está íntegro ou não, porque vários serviços podem estar em execução em um único nó. Mas, métricas como RPS, itens processados, e todos os de latência de solicitação pode indicar a integridade de um serviço específico.

Para relatar a integridade, use um código semelhante a este:

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

Para relatar uma métrica, use um código semelhante a este:

  ```csharp
    this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

### <a name="service-fabric-support-logs"></a>Logs de suporte do Service Fabric

Se houver necessidade de entrar em contato com o suporte da Microsoft para obter ajuda com o cluster do Azure Service Fabric, os logs de suporte serão quase sempre necessários. Se o cluster está hospedado no Azure, os logs de suporte são automaticamente configurados e coletados como parte da criação de um cluster. Os logs são armazenados em uma conta de armazenamento dedicada no grupo de recursos do cluster. A conta de armazenamento não tem um nome fixo, mas na conta, você vê contêineres de blob e tabelas com nomes que começam com *malha*. Para obter informações sobre como configurar coleções de log para um cluster autônomo, consulte [criar e gerenciar um cluster de Service Fabric Azure autônomo](service-fabric-cluster-creation-for-windows-server.md) e [definições de configuração para um cluster do Windows autônoma](service-fabric-cluster-manifest.md). Para instâncias autônomas do Service Fabric, os logs devem ser enviados para um compartilhamento de arquivos local. Você **deve** ter esses logs em mãos para o suporte, mas eles não serão utilizados por qualquer pessoa que não faça parte da equipe de suporte ao cliente da Microsoft.

## <a name="enabling-diagnostics-for-a-cluster"></a>Habilitar o diagnóstico para um cluster

Para tirar proveito desses logs, é altamente recomendável que, durante a criação do cluster, o "Diagnóstico" seja habilitado. Ao ativar o diagnóstico, quando o cluster é implantado, o Diagnóstico do Microsoft Azure pode reconhecer os canais Operacional, do Reliable Services e do Reliable Actors, e armazenar os dados, conforme explicado mais detalhadamente em [Eventos agregados com o Diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md).

Como visto acima, há também um campo opcional para adicionar uma chave de instrumentação do AI (Application Insights). Se você optar por usar o AI para a análise de eventos (leia mais sobre isso em [Análise de eventos com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)), inclua o recurso instrumentationKey (GUID) do AppInsights aqui.


Se você pretende implantar contêineres para seu cluster, habilite o WAD para acompanhar as estatísticas do docker adicionando eles ao seu "WadCfg > DiagnosticMonitorConfiguration":

```json
"DockerSources": {
    "Stats": {
        "enabled": true,
        "sampleRate": "PT1M"
    }
},

```

## <a name="measuring-performance"></a>Medir o desempenho

A medição do desempenho do cluster ajudará você a entender como ele pode manipular a carga e tomar decisões relacionadas ao dimensionamento do cluster (veja mais sobre como dimensionar um cluster [no Azure](service-fabric-cluster-scale-up-down.md) ou [localmente](service-fabric-cluster-windows-server-add-remove-nodes.md)). Os dados de desempenho também são úteis quando comparados às ações que você ou seus aplicativos e serviços podem obter ao analisar logs no futuro. 

Para obter uma lista de contadores de desempenho para coleta ao usar o Service Fabric, consulte [Contadores de desempenho no Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Aqui estão duas maneiras com as quais você pode configurar a coleta de dados de desempenho para o cluster:

* Usando um agente: essa é a maneira preferida de coleta de desempenho de um computador, desde que os agentes geralmente tenham uma lista de possíveis métricas de desempenho que podem ser coletadas e é um processo relativamente fácil para escolher as métricas que você deseja coletar ou alterá-las. Leia sobre os artigos [Como configurar o OMS para o Service Fabric](service-fabric-diagnostics-event-analysis-oms.md) e [Configurando o agente do OMS Windows](../log-analytics/log-analytics-windows-agents.md) para saber mais sobre o agente do OMS, que é um agente de monitoramento que é capaz de obter dados de desempenho para as VMs do cluster e contêineres implantados.

* Configurar o diagnóstico para gravar contadores de desempenho em uma tabela: para clusters no Azure, isso significa alterar a configuração de Diagnóstico do Azure para acompanhar os contadores de desempenho apropriadas das VMs no cluster e habilitá-la para acompanhar as estatísticas do docker se você estiver implantando qualquer contêiner. Leia sobre como configurar os [Contadores de desempenho no WAD](service-fabric-diagnostics-event-aggregation-wad.md) no Service Fabric para configurar a coleta do contador de desempenho.

## <a name="next-steps"></a>Próximas etapas

Seus logs e eventos precisam ser agregados antes que eles possam ser enviados para qualquer plataforma de análise. Leia sobre o [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) e o [WAD](service-fabric-diagnostics-event-aggregation-wad.md) para entender melhor algumas das opções recomendadas.
