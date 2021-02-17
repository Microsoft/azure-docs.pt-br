---
title: Habilitar o Azure Monitor para uma única máquina virtual ou para um conjunto de dimensionamento de máquinas virtuais no portal do Azure
description: Saiba como habilitar Azure Monitor para VMs em uma única máquina virtual do Azure ou conjunto de dimensionamento de máquinas virtuais usando o portal do Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: ba075930aa3541d0453b678c7d654635ae20da58
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100606621"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Habilitar o Azure Monitor para uma única máquina virtual ou para um conjunto de dimensionamento de máquinas virtuais no portal do Azure
Este artigo descreve como habilitar Azure Monitor para VMs para uma máquina virtual ou um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure. Esse procedimento pode ser usado para o seguinte:

- Máquina virtual do Azure
- Conjunto de dimensionamento de máquinas virtuais do Azure
- Máquina virtual híbrida conectada ao Azure Arc

## <a name="prerequisites"></a>Pré-requisitos

- [Criar e configurar um espaço de trabalho log Analytics](../insights/vminsights-configure-workspace.md). Como alternativa, você pode criar um novo espaço de trabalho durante esse processo.
- Consulte [sistemas operacionais com suporte](../insights/vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operacional da máquina virtual ou do conjunto de dimensionamento de máquinas virtuais que você está habilitando tem suporte. 

## <a name="enable-azure-monitor-for-vms"></a>Habilitar o Azure Monitor para VMs

No portal do Azure, selecione **máquinas virtuais**, **conjuntos de dimensionamento de máquinas virtuais** ou **servidores – arco do Azure** e selecione um recurso na lista. Na seção **monitoramento** do menu, selecione **insights** e, em seguida, **habilitar**. O exemplo a seguir mostra uma máquina virtual do Azure, mas o menu é semelhante para o conjunto de dimensionamento de máquinas virtuais do Azure ou o arco do Azure.

![Habilitar o Azure Monitor para VMs em uma VM](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

Se a máquina virtual ainda não estiver conectada a um espaço de trabalho Log Analytics, você será solicitado a selecionar uma. Se você ainda não tiver [criado um espaço de trabalho](../../azure-monitor/learn/quick-create-workspace.md), poderá selecionar um padrão para o local onde a máquina virtual ou o conjunto de dimensionamento de máquinas virtuais está implantado na assinatura. Esse espaço de trabalho será criado e configurado se ele ainda não existir. Se você selecionar um espaço de trabalho existente, ele será configurado para Azure Monitor para VMs se ele ainda não tiver sido feito.

> [!NOTE]
> Se você selecionar um espaço de trabalho que não foi configurado anteriormente para Azure Monitor para VMs, o pacote de gerenciamento *VMInsights* será adicionado a esse espaço de trabalho. Isso será aplicado a qualquer agente já conectado ao espaço de trabalho, independentemente de estar habilitado ou não por Azure Monitor para VMs. Os dados de desempenho serão coletados dessas máquinas virtuais e armazenados na tabela *InsightsMetrics* .

![Selecione o workspace](media/vminsights-enable-portal/select-workspace.png)

Você receberá mensagens de status conforme a configuração é executada.

>[!NOTE]
>Se você usar um modelo de atualização manual para o conjunto de dimensionamento de máquinas virtuais, atualize as instâncias para concluir a instalação. Você pode iniciar as atualizações na página **instâncias** , na seção **configurações** .

![Habilitar o Azure Monitor para o processamento da implantação do monitoramento das VMs](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Próximas etapas

* Consulte [usar o mapa de Azure monitor para VMs](vminsights-maps.md) para exibir dependências de aplicativo descobertas. 
* Consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md) para identificar afunilamentos, utilização geral e desempenho da VM.
