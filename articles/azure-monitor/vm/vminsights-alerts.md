---
title: Alertas de informações de VM
description: Descreve como criar regras de alerta de dados de desempenho coletados pelo VM insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 06c58b7081ed68724a3c907f8fe76dcf5f7b8057
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046798"
---
# <a name="how-to-create-alerts-from-vm-insights"></a>Como criar alertas de informações da VM
[Alertas no Azure monitor](../alerts/alerts-overview.md) notificá-lo proativamente de dados e padrões interessantes em seus dados de monitoramento. As informações da VM não incluem regras de alerta pré-configuradas, mas você pode criar suas próprias com base nos dados coletados. Este artigo fornece orientação sobre como criar regras de alerta, incluindo um conjunto de consultas de exemplo.

> [!IMPORTANT]
> Os alertas descritos neste artigo baseiam-se em consultas de log de dados coletados pela VM. Isso é diferente dos alertas criados por [Azure monitor para a integridade de convidado da VM](vminsights-health-overview.md) , que é um recurso atualmente em visualização pública. Como esse recurso se aproxima da disponibilidade geral, as diretrizes para alertas serão consolidadas.


## <a name="alert-rule-types"></a>Tipos de regra de alerta
Azure Monitor tem [tipos diferentes de regras de alerta](../alerts/alerts-overview.md#what-you-can-alert-on) com base nos dados usados para criar o alerta. Todos os dados coletados pelo virtual insights são armazenados em logs de Azure Monitor que dão suporte a [alertas de log](../alerts/alerts-log.md). No momento, não é possível usar [alertas de métrica](../alerts/alerts-log.md) com dados de desempenho coletados do VM insights porque os dados não são coletados em métricas de Azure monitor. Para coletar dados para alertas de métrica, instale a [extensão de diagnóstico](../agents/diagnostics-extension-overview.md) para VMs do Windows ou o [agente Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) para VMs do Linux para coletar dados de desempenho em métricas.

Há dois tipos de alertas de log no Azure Monitor:

- [Número de alertas de resultados](../alerts/alerts-unified-log.md#count-of-the-results-table-rows) crie um único alerta quando uma consulta retornar pelo menos um número especificado de registros. Eles são ideais para dados não numéricos, como eventos do Windows e de syslog coletados pelo [agente de log Analytics](../agents/log-analytics-agent.md) ou para analisar tendências de desempenho em vários computadores.
- Os [alertas de medição de métricas](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) criam um alerta separado para cada registro em uma consulta com um valor que exceda um limite definido na regra de alerta. Essas regras de alerta são ideais para dados de desempenho coletados pelo virtual insights, pois eles podem criar alertas individuais para cada computador.


## <a name="alert-rule-walkthrough"></a>Instruções de regra de alerta
Esta seção percorre a criação de uma regra de alerta de medição de métrica usando dados de desempenho do VM insights. Você pode usar esse processo básico com uma variedade de consultas de log para alertar sobre diferentes contadores de desempenho.

Comece criando uma nova regra de alerta seguindo o procedimento em [criar, exibir e gerenciar alertas de log usando Azure monitor](../alerts/alerts-log.md). Para o **recurso**, selecione o espaço de trabalho Log Analytics que Azure monitor VMs usa em sua assinatura. Como o recurso de destino para regras de alerta de log é sempre um espaço de trabalho Log Analytics, a consulta de log deve incluir qualquer filtro para máquinas virtuais específicas ou conjuntos de dimensionamento de máquinas virtuais. 

Para a **condição** da regra de alerta, use uma das consultas na [seção abaixo](#sample-alert-queries) como a consulta de **pesquisa**. A consulta deve retornar uma propriedade numérica chamada *AggregatedValue*. Ele deve resumir os dados por computador para que você possa criar um alerta separado para cada máquina virtual que exceda o limite.

Na **lógica de alerta**, selecione **medição de métrica** e forneça um **valor de limite**. Em **gatilho de alerta baseado em**, especifique quantas vezes o limite deve ser excedido antes de um alerta ser criado. Por exemplo, você provavelmente não se importa se o processador excede um limite uma vez e, em seguida, retorna ao normal, mas você se encarrega de que ele continua excedendo o limite em relação a várias medidas consecutivas.

O **avaliado com base na** seção define a frequência com que a consulta é executada e a janela de tempo da consulta. No exemplo mostrado abaixo, a consulta será executada a cada 15 minutos e avaliará os valores de desempenho coletados nos 15 minutos anteriores.


![Regra de alerta de medição métrica](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Consultas de alerta de exemplo
As consultas a seguir podem ser usadas com uma regra de alerta de medição de métrica usando dados de desempenho coletados pelo VM insights. Cada um resume os dados por computador para que um alerta seja criado para cada computador com um valor que exceda o limite.

### <a name="cpu-utilization"></a>Utilização da CPU

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Memória disponível em MB

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Memória disponível em porcentagem

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Disco lógico usado-todos os discos em cada computador

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Disco lógico usado-discos individuais

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>IOPS de disco lógico

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Taxa de dados do disco lógico

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Interfaces de rede bytes recebidos-todas as interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Interfaces de rede bytes recebidos-interfaces individuais

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Interfaces de rede bytes enviados-todas as interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Interfaces de rede bytes enviados-interfaces individuais

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Conjunto de escala de máquina virtual
Modifique com sua ID de assinatura, grupo de recursos e nome do conjunto de dimensionamento de máquinas virtuais.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Máquina virtual específica
Modifique com sua ID de assinatura, grupo de recursos e nome da VM.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>Utilização da CPU para todos os recursos de computação em uma assinatura
Modifique com sua ID de assinatura.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>Utilização da CPU para todos os recursos de computação em um grupo de recursos
Modifique com sua ID de assinatura e grupo de recursos.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [alertas no Azure monitor](../alerts/alerts-overview.md).
- Saiba mais sobre [consultas de log usando dados de informações da VM](vminsights-log-search.md).
