---
title: Monitorar instâncias de contêiner
description: Como monitorar o consumo de recursos de computação, como CPU e memória, pelos contêineres nas Instâncias de Contêiner do Azure.
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: ae9725ffe66bebbed26745c311b2ada07d5d2c00
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589288"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorar os recursos de contêiner em Instâncias de Contêiner do Azure

[Azure monitor][azure-monitoring] fornece informações sobre os recursos de computação usados pelas instâncias de contêineres. Esses dados de uso de recursos ajudam você a determinar as melhores configurações de recurso para seus grupos de contêiner. O Azure Monitor também fornece métricas que rastreiam a atividade de rede em suas instâncias de contêiner.

Este documento detalha a coleta de métricas do Azure Monitor para instâncias de contêiner usando o portal do Azure e a CLI do Azure.

> [!IMPORTANT]
> No momento, as métricas do Azure Monitor em Instâncias de Contêiner do Azure estão em versão prévia e algumas [limitações se aplicam](#preview-limitations). As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="preview-limitations"></a>Limitações de visualização

Neste momento, as métricas do Azure Monitor estão disponíveis apenas para contêineres do Linux.

## <a name="available-metrics"></a>Métricas disponíveis

O Azure Monitor fornece as seguintes [métricas para Instâncias de Contêiner do Azure][supported-metrics]. Essas métricas estão disponíveis para um grupo de contêineres e contêineres individuais. Por padrão, as métricas são agregadas como médias.

- **Uso de CPU** medido em **milicores**. 
  - Um millicore é 1/1000th de um núcleo de CPU; portanto, 500 milicores representa o uso do núcleo de CPU de 0,5.
- **Uso de memória** em bytes
- **Bytes de rede recebidos** por segundo
- **Bytes de rede transmitidos** por segundo 

## <a name="get-metrics---azure-portal"></a>Obter métricas – portal do Azure

Quando um grupo de contêineres é criado, os dados do Azure Monitor ficam disponíveis no portal do Azure. Para ver as métricas de um grupo de contêineres, acesse a página **Visão Geral** do grupo de contêineres. Aqui você pode ver gráficos pré-criados para cada uma das métricas disponíveis.

![gráfico duplo][dual-chart]

Em um grupo de contêineres que contém vários contêineres, use uma [dimensão][monitor-dimension] para exibir as métricas por contêiner. Para criar um gráfico com métricas individuais do contêiner, execute as seguintes etapas:

1. Na página **Visão geral**, selecione um dos gráficos de métricas, como **CPU**. 
1. Selecione o botão **Aplicar divisão** e o **Nome do Contêiner**.

![Captura de tela mostra as métricas para uma instância de contêiner com aplicar divisão selecionada e nome do contêiner selecionado.][dimension]

## <a name="get-metrics---azure-cli"></a>Obter métricas – CLI do Azure

As métricas para instâncias de contêiner também podem ser coletadas usando a CLI do Azure. Primeiro, obtenha a ID do grupo de contêineres usando o comando a seguir. Substitua `<resource-group>` pelo nome do grupo de recursos e `<container-group>` pelo nome do grupo de contêineres.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Use o comando a seguir para obter as métricas de uso de **CPU**.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table
```

```output
Timestamp            Name       Average
-------------------  ---------  ---------
2020-12-17 23:34:00  CPU Usage
. . .
2020-12-18 00:25:00  CPU Usage
2020-12-18 00:26:00  CPU Usage  0.4
2020-12-18 00:27:00  CPU Usage  0.0
```

Altere o valor do parâmetro `--metric` no comando para obter outras [métricas compatíveis][supported-metrics]. Por exemplo, use o comando a seguir para obter métricas de uso de **memória**. 

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table
```

```output
Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

Para um grupo de vários contêineres, a dimensão `containerName` pode ser adicionada para retornar métricas por contêiner.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table
```

```output
Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Monitoramento do Azure na [Visão geral do Monitoramento do Azure][azure-monitoring].

Saiba como criar [alertas de métrica][metric-alert] para ser notificado quando uma métrica para Instâncias de Contêiner do Azure cruzar um limite.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/alerts/alerts-metric.md
[monitor-dimension]: ../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups
