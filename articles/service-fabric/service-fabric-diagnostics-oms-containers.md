---
title: Monitorar contêineres com logs de Azure Monitor
description: Use logs de Azure Monitor para monitorar contêineres em execução em clusters de Service Fabric do Azure.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 6217569dc50517c88a5a8a7bc0f3752e7e327f4e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626649"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitorar contêineres com logs de Azure Monitor
 
Este artigo aborda as etapas necessárias para configurar a solução de monitoramento de contêiner de logs de Azure Monitor para exibir eventos de contêiner. Para configurar o cluster para coletar eventos de contêiner, consulte [tutorial passo a passo](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurar a solução de monitoramento de contêineres

> [!NOTE]
> Você precisa ter Azure Monitor logs configurados para o cluster, bem como ter o agente de Log Analytics implantado em seus nós. Se não estiver, siga as etapas em [Configurar Logs de Azure monitor](service-fabric-diagnostics-oms-setup.md) e [adicione o agente de log Analytics a um cluster](service-fabric-diagnostics-oms-agent.md) primeiro.

1. Depois que o cluster estiver configurado com os logs de Azure Monitor e o agente de Log Analytics, implante seus contêineres. Aguarde até que os contêineres sejam implantados antes de passar para a próxima etapa.

2. No Azure Marketplace, pesquise *Solução de monitoramento de contêiner* e clique no recurso **Solução de monitoramento de contêiner** que aparecerá abaixo da categoria Monitoramento + Gerenciamento.

    ![Adicionando a solução de Contêineres](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Crie a solução dentro do mesmo workspace que já foi criado para o cluster. Essa alteração automaticamente aciona o agente para iniciar a coleta de dados de docker nos contêineres. Em cerca de 15 minutos, você verá a solução se acender com os logs e estatísticas de entrada, como mostra a imagem abaixo.

    ![Painel de análise de Log básica](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

O agente permite a coleta de vários logs específicos de contêiner que podem ser consultados em logs de Azure Monitor ou usados para visualizar indicadores de desempenho. Os tipos de log coletados são:

* ContainerInventory: mostra informações sobre imagens, nome e localização do contêiner
* ContainerImageInventory: informações sobre imagens implantadas, inclusive IDs ou tamanhos
* ContainerLog: logs de erros específicos, logs de docker (stdout etc.) e outras entradas
* ContainerServiceLog: comandos de daemon do docker que foram executados
* Perf: contadores de desempenho incluindo o CPU, memória, tráfego de rede, E/S do disco e métricas personalizadas das máquinas de host do contêiner



## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [solução de contêineres de logs de Azure monitor](../azure-monitor/containers/containers.md).
* Leia mais sobre a orquestração de contêineres no Service Fabric: [Service Fabric e contêineres](service-fabric-containers-overview.md)
* Familiarize-se com os recursos de [pesquisa e consulta de logs](../azure-monitor/logs/log-query-overview.md) oferecidos como parte dos logs do Azure Monitor
* Configurar logs de Azure Monitor para configurar regras de [alerta automatizado](../azure-monitor/alerts/alerts-overview.md) para auxiliar na detecção e no diagnóstico
