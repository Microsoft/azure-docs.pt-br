---
title: Configurar o monitoramento de PV com insights de contêiner | Microsoft Docs
description: Este artigo descreve como você pode configurar o monitoramento de clusters kubernetes com volumes persistentes com informações de contêiner.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 7c6ddd62bf06d313987289e444962378cea43fc8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627890"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Configurar o monitoramento de PV com informações de contêiner

A partir do Agent versão *ciprod10052020*, o agente integrado do Azure monitor para contêineres agora dá suporte ao monitoramento do uso de PV (volume persistente). Com a versão do agente *ciprod01112021*, o agente dá suporte ao monitoramento do inventário de PV, incluindo informações sobre o status, a classe de armazenamento, o tipo, os modos de acesso e outros detalhes.
## <a name="pv-metrics"></a>Métricas de PV

O insights de contêiner inicia automaticamente o monitoramento do uso de PV coletando as seguintes métricas em intervalos de 60 a s e armazenando-as na tabela **InsightMetrics** .

| Nome da métrica | Dimensão métrica (marcas) | Descrição da métrica |
|-----|-----------|----------|
| `pvUsedBytes`| podUID, podName, pvcName, pvcNamespace, capacityBytes, clusterid, ClusterName| Espaço usado em bytes para um volume persistente específico com uma declaração usada por um pod específico. `capacityBytes` é dobrado como uma dimensão no campo marcas para reduzir o custo de ingestão de dados e simplificar as consultas.|

Saiba mais sobre como configurar as métricas de PV coletadas [aqui](./container-insights-agent-config.md).

## <a name="pv-inventory"></a>Inventário de PV

Azure Monitor para contêineres inicia automaticamente o monitoramento de PVs coletando as informações a seguir em intervalos de 60 a s e armazenando-as na tabela **KubePVInventory** .

|Dados |Fonte de dados| Tipo de Dados| Campos|
|-----|-----------|----------|-------|
|Inventário de volumes persistentes em um cluster kubernetes |API Kube |`KubePVInventory` |    PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, timegerou, Clusterid, ClusterName, _ResourceId |

## <a name="monitor-persistent-volumes"></a>Monitorar volumes persistentes

Azure Monitor para contêineres inclui gráficos pré-configurados para essa métrica de uso e informações de inventário em modelos de pasta de trabalho para cada cluster. Você também pode habilitar um alerta recomendado para uso de PV e consultar essas métricas em Log Analytics.  

### <a name="workload-details-workbook"></a>Planilha de detalhes de carga de trabalho

Você pode encontrar gráficos de uso para cargas de trabalho específicas na guia volume persistente da pasta de trabalho **detalhes de Workload** diretamente de um cluster AKs, selecionando pastas de trabalho no painel esquerdo, na lista suspensa **exibir pastas de trabalho** no painel insights ou na **guia relatórios (visualização)** no painel insights.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Exemplo de pasta de trabalho do PV de Azure Monitor":::

### <a name="persistent-volume-details-workbook"></a>Pasta de trabalho detalhes do volume persistente

Você pode encontrar uma visão geral do inventário de volume persistente na pasta de trabalho **detalhes do volume persistente** diretamente de um cluster AKs selecionando pastas de trabalho no painel esquerdo, na lista suspensa **exibir pastas de trabalho** no painel insights ou na guia **relatórios (visualização)** no painel insights.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Exemplo da pasta de trabalho detalhes do Azure Monitor PV":::

### <a name="persistent-volume-usage-recommended-alert"></a>Alerta de uso de volume persistente recomendado
Você pode habilitar um alerta recomendado para alertá-lo quando o uso de PV médio para um pod estiver acima de 80%. Saiba mais sobre alertas [aqui](./container-insights-metric-alerts.md) e como substituir o limite padrão [aqui](./container-insights-metric-alerts.md#configure-alertable-metrics-in-configmaps).
## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as métricas de PV coletadas [aqui](./container-insights-agent-config.md).