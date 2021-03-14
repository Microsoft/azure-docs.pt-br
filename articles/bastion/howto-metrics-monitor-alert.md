---
title: Configurar o monitoramento e as métricas usando Azure Monitor
titleSuffix: Azure Bastion
description: Saiba mais sobre o monitoramento de bastiões do Azure e métricas usando o Azure Monitor, a solução para métricas, alertas e logs de diagnóstico no Azure.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/12/2021
ms.author: mialdrid
ms.openlocfilehash: 3a5beba3938b5a845a378ede155f2f64e6baac7a
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417936"
---
# <a name="how-to-configure-monitoring-and-metrics-for-azure-bastion-using-azure-monitor"></a>Como configurar o monitoramento e as métricas para a bastiões do Azure usando o Azure Monitor

Este artigo ajuda você a trabalhar com monitoramento e métricas para a bastiões do Azure usando o Azure Monitor.

>[!NOTE]
>Usar **Métricas Clássicas** não é recomendado.
>

## <a name="about-metrics"></a>Sobre métricas

A bastiões do Azure tem várias métricas que estão disponíveis. A tabela a seguir mostra a categoria e as dimensões de cada métrica disponível.

|**Métrica**|**Categoria**|**Dimensão (ões)**|
| --- | --- | --- |
|Status de comunicação de bastiões * *|[Disponibilidade](#availability)|N/D|
|Memória total|[Disponibilidade](#availability)|Instância|
|CPU usada|[Tráfego](#traffic)|Instância
|Memória usada|[Tráfego](#traffic)|Instância
|Contagem de sessões|[Desempenho](#performance)|Instância|

* * O status de comunicação de bastiões é aplicável somente para hosts bastiões implantados após novembro de 2020.

### <a name="availability-metrics"></a><a name="availability"></a>Métricas de disponibilidade

#### <a name="bastion-communication-status"></a><a name="communication-status"></a>Status de comunicação de bastiões

Você pode exibir o status de comunicação da bastiões do Azure, agregado em todas as instâncias que compõem o host bastião.

* Um valor de **1** indica que a bastiões está disponível.
* Um valor de **0** indica que o serviço de bastiões não está disponível.

:::image type="content" source="./media/metrics-monitor-alert/communication-status.png" alt-text="Captura de tela mostrando o status de comunicação.":::

#### <a name="total-memory"></a><a name="total-memory"></a>Memória total

Você pode exibir a memória total da bastiões do Azure, dividida em cada instância de bastiões.

:::image type="content" source="./media/metrics-monitor-alert/total-memory.png" alt-text="Captura de tela mostrando a memória total.":::

### <a name="traffic-metrics"></a><a name="traffic"></a>Métricas de tráfego

#### <a name="used-cpu"></a><a name="used-cpu"></a>CPU usada

Você pode exibir a utilização da CPU da bastiões do Azure, dividida em cada instância de bastiões. O monitoramento dessa métrica ajudará a medir a disponibilidade e a capacidade das instâncias que compõem a bastiões do Azure.

:::image type="content" source="./media/metrics-monitor-alert/used-cpu.png" alt-text="Captura de tela mostrando a CPU usada.":::

#### <a name="used-memory"></a><a name="used-memory"></a>Memória usada

Você pode exibir a utilização de memória em cada instância de bastiões, dividida em cada instância de bastiões. O monitoramento dessa métrica ajudará a medir a disponibilidade e a capacidade das instâncias que compõem a bastiões do Azure.

:::image type="content" source="./media/metrics-monitor-alert/used-memory.png" alt-text="Captura de tela mostrando a memória usada.":::

### <a name="performance-metrics"></a><a name="performance"></a>Métricas de desempenho

#### <a name="session-count"></a>Contagem de sessões

Você pode exibir a contagem de sessões ativas por instância de bastiões, agregadas em cada tipo de sessão (RDP e SSH). Cada bastiões do Azure pode dar suporte a uma variedade de sessões ativas de RDP e SSH. O monitoramento dessa métrica ajudará você a entender se precisa ajustar o número de instâncias que executam o serviço de bastiões. Para obter mais informações sobre a contagem de sessões que o Azure bastiões pode dar suporte, consulte as [perguntas frequentes](bastion-faq.md)sobre a bastiões do Azure.

:::image type="content" source="./media/metrics-monitor-alert/session-count.png" alt-text="Captura de tela mostrando a contagem de sessão.":::

## <a name="how-to-view-metrics"></a><a name="metrics"></a>Como exibir métricas

1. Para exibir as métricas, navegue até o host de bastiões.
1. Na lista **monitoramento** , selecione **métricas**.
1. Selecione os parâmetros. Se nenhuma métrica estiver definida, clique em **Adicionar métrica** e selecione os parâmetros.

   * **Escopo:** Por padrão, o escopo é definido como o host de bastiões.
   * **Namespace de métrica:** Métricas padrão.
   * **Métrica:** Selecione a métrica que você deseja exibir.

1. Depois que uma métrica for selecionada, a agregação padrão será aplicada. Opcionalmente, você pode aplicar a divisão, que mostrará a métrica com dimensões diferentes.

## <a name="next-steps"></a>Próximas etapas

Leia as [perguntas frequentes do Bastion](bastion-faq.md).
  
