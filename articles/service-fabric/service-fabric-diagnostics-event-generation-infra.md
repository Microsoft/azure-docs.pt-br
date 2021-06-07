---
title: Monitoramento no nível da plataforma de Service Fabric do Azure
description: Saiba mais sobre eventos e logs no nível de plataforma usados para monitorar e diagnosticar clusters do Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: f9104c390ab4115c626beb4759c6b6952d691ca9
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626513"
---
# <a name="monitoring-the-cluster"></a>Monitoramento de cluster

É importante monitorar o nível de cluster para determinar se o hardware e o cluster estão funcionando conforme esperado. O Service Fabric pode ajudar a manter os aplicativos em execução durante falhas de hardware, mas você ainda precisa diagnosticar se um erro está ocorrendo em um aplicativo ou na infraestrutura subjacente. Você também deve monitorar os clusters para planejar melhor a capacidade, ajudando nas decisões sobre a adição ou remoção de hardware.

O Service Fabric expõe vários eventos de plataforma estruturada, como "[Eventos do Service Fabric](service-fabric-diagnostics-events.md)," por meio do EventStore e vários canais de log fora da caixa. 

No Windows, eventos do Service Fabric estão disponíveis a partir de um único provedor ETW com um conjunto de `logLevelKeywordFilters` relevante usado para escolher entre os canais Operacional e Dados e Mensagens - é assim que separamos eventos do Service Fabric de saída para serem filtrados conforme necessário.

* **Operacional** Operações de alto nível executadas por Service Fabric e o cluster, incluindo eventos para um nó surgindo, um novo aplicativo sendo implantado ou uma reversão de atualização, etc. Consulte a lista completa de eventos [aqui](service-fabric-diagnostics-event-generation-operational.md).  

* **Operacional - detalhado**  
Relatórios de integridade e decisões de balanceamento de carga.

O canal de operação pode ser acessado por meio de uma variedade de maneiras, incluindo Logs de eventos ETW/Windows, o [EventStore](service-fabric-diagnostics-eventstore.md) (disponível no Windows, nas versões 6.2 e posteriores para clusters do Windows). O EventStore fornece acesso a eventos do cluster por entidade (incluindo o cluster, nós, aplicativos, serviços, partições, réplicas e contêineres de entidades) e os expõe por meio de APIs REST e da biblioteca de clientes do Service Fabric. Use o EventStore para monitorar os clusters de desenvolvimento/teste e para obter uma compreensão precisa do estado de seus clusters de produção.

* **Dados e Mensagens**  
Logs e eventos críticos gerados no sistema de mensagens (atualmente, apenas o ReverseProxy) e o caminho de dados (modelos de serviços confiáveis).

* **Dados e Mensagens - detalhados**  
Canal detalhado que contém todos os logs não críticos dos dados e das mensagens no cluster (esse canal tem um volume muito alto de eventos).

Além desses, são fornecidos dois canais estruturados do EventSource, bem como logs que coletamos para fins de suporte.

* [Eventos de Reliable Services](service-fabric-reliable-services-diagnostics.md)  
Eventos específicos do modelo de programação.

* [Eventos de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)  
Eventos específicos do modelo de programação e contadores de desempenho.

* Logs de Suporte  
Logs do sistema gerados pelo Service Fabric apenas para serem usados por nós ao fornecer suporte.

Esses vários canais abrangem a maior parte dos logs no nível de plataforma que são recomendados. Para melhorar o log no nível de plataforma, considere a possibilidade de investir em uma melhor compreensão do modelo de integridade e adicionar relatórios de integridade personalizados, além de adicionar **Contadores de Desempenho** personalizados para desenvolver uma compreensão em tempo real do impacto dos serviços e aplicativos no cluster.

Para tirar proveito desses logs, é altamente recomendável deixar habilitado “Diagnóstico” durante a criação do cluster no portal do Azure. Ao ativar o diagnóstico, quando o cluster é implantado, o Diagnóstico do Microsoft Azure pode reconhecer os canais Operacional, do Reliable Services e do Reliable Actors, e armazenar os dados, conforme explicado mais detalhadamente em [Eventos agregados com o Diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Relatórios de carga e integridade do Azure Service Fabric

O Service Fabric tem seu próprio modelo de integridade que é descrito em detalhes nestes artigos:

- [Introdução ao monitoramento de integridade do Service Fabric](service-fabric-health-introduction.md)
- [Relatar e verificar a integridade de serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Adicionar relatórios de integridade personalizados do Service Fabric](service-fabric-report-health.md)
- [Como exibir relatórios de integridade do Service Fabric](service-fabric-view-entities-aggregated-health.md)

O monitoramento de integridade é fundamental para vários aspectos da operação de um serviço, especialmente durante uma atualização de aplicativo. Depois de cada domínio de atualização do serviço ser atualizado, o domínio de atualização deve passar verificações de integridade antes da implantação passe para o próximo domínio de atualização. Se não for possível obter um status de integridade, a implantação será revertida, deixando o aplicativo em um bom estado conhecido novamente. Embora alguns clientes talvez seja afetados antes que os serviços são revertidos, a maioria dos clientes não enfrentar um problema. Além disso, a resolução ocorreu relativamente rápido, sem precisar aguardar a ação de um operador humano. Quanto mais verificações de integridade são incorporadas ao código, mais flexível será o serviço aos problemas de implantação.

Outro aspecto da integridade do serviço são os relatórios de métrica do serviço. As métricas são importantes no Service Fabric porque eles são usados para equilibrar o uso de recursos. As métricas também podem ser um indicador de integridade do sistema. Por exemplo, você pode ter um aplicativo que tenha muitos serviços, e cada instância informa uma métrica RPS (solicitações por segundo). Se um dos serviços estiver usando mais recursos do que outro serviço, o Service Fabric moverá as instâncias de serviço pelo cluster para tentar manter a utilização de recursos uniforme. Para obter uma explicação mais detalhada de como funciona a utilização de recursos, consulte [gerenciar o consumo de recursos e carga na Service Fabric com métricas](service-fabric-cluster-resource-manager-metrics.md).

Métricas também podem ajudar a fornecer informações sobre o desempenho do seu serviço. Ao longo do tempo, você pode usar métricas para verificar se o serviço está operando dentro dos parâmetros esperados. Por exemplo, se as tendências mostram em 9: 00 na segunda-feira de manhã média RPS é 1.000, você pode definir um relatório de integridade que o alerta se o RPS estiver abaixo de 500 ou acima 1.500. Tudo pode estar ocorrendo perfeitamente bem, mas pode valer a pena dar uma olhada para garantir que seus clientes estão tendo uma ótima experiência. Seu serviço pode definir um conjunto de métricas que podem ser informadas para fins de verificação de integridade sem afetar o balanceamento de recursos do cluster. Para fazer isso, defina o peso da métrica como zero. É recomendável que você inicie todas as métricas com peso zero e não aumente o peso até ter certeza de que você compreende como ponderar a métrica afeta o balanceamento de recursos do cluster.

> [!TIP]
> Não use um número excessivo de métricas ponderadas. Pode ser difícil entender por que instâncias de serviço estão sendo movidas para balanceamento. Algumas métricas podem ir muito longe!

Todas as informações que possam indicar a integridade e o desempenho do seu aplicativo são candidatas aos relatórios de métricas e de integridade. **Um contador de desempenho de CPU pode dizer como seu nó é utilizado, mas não diz se um serviço em particular está íntegro ou não, porque vários serviços podem estar em execução em um único nó.** Mas, métricas como RPS, itens processados, e todos os de latência de solicitação pode indicar a integridade de um serviço específico.

## <a name="service-fabric-support-logs"></a>Logs de suporte do Service Fabric

Se houver necessidade de entrar em contato com o suporte da Microsoft para obter ajuda com o cluster do Azure Service Fabric, os logs de suporte serão quase sempre necessários. Se o cluster está hospedado no Azure, os logs de suporte são automaticamente configurados e coletados como parte da criação de um cluster. Os logs são armazenados em uma conta de armazenamento dedicada no grupo de recursos do cluster. A conta de armazenamento não tem um nome fixo, mas na conta, você vê contêineres de blob e tabelas com nomes que começam com *malha*. Para obter informações sobre como configurar coleções de log para um cluster autônomo, consulte [criar e gerenciar um cluster de Service Fabric Azure autônomo](service-fabric-cluster-creation-for-windows-server.md) e [definições de configuração para um cluster do Windows autônoma](service-fabric-cluster-manifest.md). Para instâncias autônomas do Service Fabric, os logs devem ser enviados para um compartilhamento de arquivos local. Você **deve** ter esses logs em mãos para o suporte, mas eles não serão utilizados por qualquer pessoa que não faça parte da equipe de suporte ao cliente da Microsoft.

## <a name="measuring-performance"></a>Medir o desempenho

A medição do desempenho do cluster ajudará você a entender como ele pode manipular a carga e tomar decisões relacionadas ao dimensionamento do cluster (veja mais sobre como dimensionar um cluster [no Azure](service-fabric-cluster-scale-in-out.md) ou [localmente](service-fabric-cluster-windows-server-add-remove-nodes.md)). Os dados de desempenho também são úteis quando comparados às ações que você ou seus aplicativos e serviços podem obter ao analisar logs no futuro. 

Para obter uma lista de contadores de desempenho para coleta ao usar o Service Fabric, consulte [Contadores de desempenho no Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Aqui estão duas maneiras com as quais você pode configurar a coleta de dados de desempenho para o cluster:

* **Usando um agente**  
Essa é a maneira preferida de coleta de desempenho de um computador, desde que os agentes geralmente tenham uma lista de possíveis métricas de desempenho que podem ser coletadas e é um processo relativamente fácil para escolher as métricas que você deseja coletar ou alterá-las. A leitura sobre o Azure Monitor oferece Azure Monitor logs na integração dos [logs de Azure monitor](service-fabric-diagnostics-event-analysis-oms.md) do Service Fabric e [a configuração do agente de log Analytics](../azure-monitor/agents/agent-windows.md) para saber mais sobre o agente de log Analytics, que é um agente de monitoramento que é capaz de obter dados de desempenho para VMs de cluster e contêineres implantados.

* **Contadores de desempenho para o Armazenamento de Tabelas do Azure**  
Você também pode enviar métricas de desempenho para o mesmo armazenamento de tabela como os eventos. Isso requer alterar a configuração de Diagnóstico do Azure para acompanhar os contadores de desempenho apropriados das VMs no cluster e habilitá-lo para acompanhar as estatísticas do docker se você estiver implantando qualquer contêiner. Leia sobre como configurar os [Contadores de desempenho no WAD](service-fabric-diagnostics-event-aggregation-wad.md) no Service Fabric para configurar a coleta do contador de desempenho.

## <a name="next-steps"></a>Próximas etapas

* Leia sobre a [integração dos logs de Azure monitor](service-fabric-diagnostics-event-analysis-oms.md) do Service Fabric para coletar diagnósticos de cluster e criar consultas e alertas personalizados
* Saiba mais sobre o Service Fabric na experiência de diagnóstico interna, o [EventStore](service-fabric-diagnostics-eventstore.md)
* Percorra alguns [cenários comuns de diagnóstico](service-fabric-diagnostics-common-scenarios.md) no Service Fabric
