---
title: Monitoramento de desempenho com logs de Azure Monitor
description: Saiba como configurar o Agente do Log Analytics para monitorar os contêineres e os contadores de desempenho para os clusters do Azure Service Fabric.
ms.topic: conceptual
ms.date: 04/16/2018
ms.openlocfilehash: 9bb89dc2eebe584a0a9f81a6707c0a2e4fa2fc30
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626666"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Monitoramento de desempenho com logs de Azure Monitor

Este artigo aborda as etapas para adicionar o Agente do Log Analytics como extensão do conjunto de dimensionamento de máquinas virtuais e conectá-lo ao espaço de trabalho do Log Analytics do Azure existente. Isso permite coletar dados de diagnóstico sobre contêineres, aplicativos e monitoramento do desempenho. Ao adicioná-lo como uma extensão para o recurso de conjunto de dimensionamento de máquinas virtuais, o Azure Resource Manager garante que ele seja instalado em cada nó, mesmo ao dimensionar o cluster.

> [!NOTE]
> Este artigo pressupõe que você já tenha o espaço de trabalho do Log Analytics do Azure configurado. Se você não fizer isso, vá para [Configurar Logs de Azure monitor](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Adicionar a extensão do agente por meio da CLI do Azure

A melhor maneira de adicionar o Agente do Log Analytics ao cluster é por meio das APIs de conjunto de dimensionamento de máquinas virtuais disponíveis com a CLI do Azure. Se você não tiver a CLI do Azure configurada ainda, vá para o Portal do Azure e abra uma instância do [Cloud Shell](../cloud-shell/overview.md) ou [instale a CLI do Azure](/cli/azure/install-azure-cli).

1. Depois que o Cloud Shell for solicitado, certifique-se de que você esteja trabalhando na mesma assinatura do recurso. Verifique isso com `az account show` e certifique-se de que o valor “nome” corresponde ao da assinatura de cluster.

2. No Portal, navegue até o grupo de recursos em que o espaço de trabalho do Log Analytics está localizado. Clique no recurso log Analytics (o tipo do recurso será Log Analytics espaço de trabalho). Quando estiver na página de visão geral do recurso, clique em **Configurações Avançadas** na seção de Configurações no menu à esquerda.

    ![Página de propriedades do log Analytics](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Clique em **Servidores Windows** se você estiver aguardando um cluster do Windows e **Servidores Linux** se estiver criando um cluster do Linux. Essa página mostrará a você o `workspace ID` e `workspace key` (listados como chave primária no portal). Você precisará deles para a próxima etapa.

4. Execute o comando para instalar o agente de Log Analytics no cluster, usando a `vmss extension set` API:

    Para um cluster do Windows:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Para um cluster do Linux:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Aqui está um exemplo de como o Agente do Log Analytics sendo adicionado a um cluster do Windows.

    ![Comando da cli do agente Log Analytics](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Isso deve levar menos de 15 minutos para adicionar com êxito o agente aos nós. Você pode verificar se os agentes foram adicionados usando a API `az vmss extension list`:

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Adicionar o agente por meio do modelo do Resource Manager

Os modelos do Gerenciador de Recursos de exemplo que implantam o espaço de trabalho do Log Analytics do Azure e adiciona um agente a cada um dos nós estão disponíveis para [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) ou [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS).

Você pode baixar e modificar este modelo para implantar um cluster que melhor atenda às suas necessidades.

## <a name="view-performance-counters"></a>Contadores de desempenho

Agora que você adicionou o agente do Log Analytics, vá direto para o portal do Log Analytics para escolher quais contadores de desempenho você deseja coletar.

1. No portal do Azure, vá para o grupo de recursos em que você criou a solução Análise do Service Fabric. Selecione o **infabric \<nameOfLog AnalyticsWorkspace\>**.

2. Clique em **Log Analytics**.

3. Clique em **Configurações Avançadas**.

4. Clique em **Dados**, depois clique em **Contadores de Desempenho Windows ou Linux**. Há uma lista de contadores padrão que você pode habilitar, além de também poder definir o intervalo de coleta. Você pode também adicionar [contadores de desempenho adicionais](service-fabric-diagnostics-event-generation-perf.md) para coletar. O formato correto é referenciado neste [artigo](/windows/win32/perfctrs/specifying-a-counter-path).

5. Clique em **Salvar**, depois em **OK**.

6. Feche a folha Configurações Avançadas.

7. No título em geral, clique em **Resumo de workspace**.

8. Quando você estiver usando o portal, verá um bloco na forma de um grafo para cada uma das soluções habilitadas, inclusive uma para Service Fabric. Clique no grafo **Service Fabric** para ir para a solução de Análise do Service Fabric.

9. Você verá alguns blocos com elementos gráficos no canal operacional e eventos de serviços confiáveis. A representação gráfica dos dados que fluem para os contadores selecionados aparecerá em Métricas de Nó.

10. Clique em um grafo de Métrica de Contêiner para ver detalhes adicionais. Você também pode consultar dados do contador de desempenho da mesma forma que os eventos de cluster e filtrar por nome do contador de desempenho, nós e valores, usando a linguagem de consulta Kusto.

![Consulta de contador de desempenho do Log Analytics](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Próximas etapas

* Coletar [contador de desempenho](service-fabric-diagnostics-event-generation-perf.md) relevantes. Para configurar o agente do Log Analytics para coletar contadores de desempenho específicos, veja [configurando fontes de dados](../azure-monitor/agents/agent-data-sources.md#configuring-data-sources).
* Configurar logs de Azure Monitor para configurar [alertas automatizados](../azure-monitor/alerts/alerts-overview.md) para auxiliar na detecção e no diagnóstico
* Como alternativa, você pode coletar os contadores de desempenho por meio da [extensão de Diagnóstico do Azure e enviá-los ao Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
