---
title: Habilitar o Azure Monitor para uma única máquina virtual ou para um conjunto de dimensionamento de máquinas virtuais no portal do Azure
description: Saiba como habilitar o Revisions da VM em uma única máquina virtual do Azure ou um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 076fcab7b0747a7993407edd65f9d08efc27309f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035578"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Habilitar o Azure Monitor para uma única máquina virtual ou para um conjunto de dimensionamento de máquinas virtuais no portal do Azure
Este artigo descreve como habilitar o Revisions da VM para uma máquina virtual ou um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure. Esse procedimento pode ser usado para o seguinte:

- Máquina virtual do Azure
- Conjunto de dimensionamento de máquinas virtuais do Azure
- Máquina virtual híbrida conectada ao Azure Arc

## <a name="prerequisites"></a>Pré-requisitos

- [Criar e configurar um espaço de trabalho log Analytics](./vminsights-configure-workspace.md). Como alternativa, você pode criar um novo espaço de trabalho durante esse processo.
- Consulte [sistemas operacionais com suporte](./vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operacional da máquina virtual ou do conjunto de dimensionamento de máquinas virtuais que você está habilitando tem suporte. 

## <a name="enable-vm-insights"></a>Habilitar informações de VM

No portal do Azure, selecione **máquinas virtuais**, **conjuntos de dimensionamento de máquinas virtuais** ou **servidores – arco do Azure** e selecione um recurso na lista. Na seção **monitoramento** do menu, selecione **insights** e, em seguida, **habilitar**. O exemplo a seguir mostra uma máquina virtual do Azure, mas o menu é semelhante para o conjunto de dimensionamento de máquinas virtuais do Azure ou o arco do Azure.

![Habilitar as informações de VM para uma VM](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

Se a máquina virtual ainda não estiver conectada a um espaço de trabalho Log Analytics, você será solicitado a selecionar uma. Se você ainda não tiver [criado um espaço de trabalho](../logs/quick-create-workspace.md), poderá selecionar um padrão para o local onde a máquina virtual ou o conjunto de dimensionamento de máquinas virtuais está implantado na assinatura. Esse espaço de trabalho será criado e configurado se ele ainda não existir. Se você selecionar um espaço de trabalho existente, ele será configurado para o VM insights se ele ainda não tiver sido feito.

> [!NOTE]
> Se você selecionar um espaço de trabalho que não foi configurado anteriormente para o virtual insights, o pacote de gerenciamento *VMInsights* será adicionado a esse espaço de trabalho. Isso será aplicado a qualquer agente já conectado ao espaço de trabalho, esteja ele habilitado ou não para o insights da VM. Os dados de desempenho serão coletados dessas máquinas virtuais e armazenados na tabela *InsightsMetrics* .

![Selecione o workspace](media/vminsights-enable-portal/select-workspace.png)

Você receberá mensagens de status conforme a configuração é executada.

>[!NOTE]
>Se você usar um modelo de atualização manual para o conjunto de dimensionamento de máquinas virtuais, atualize as instâncias para concluir a instalação. Você pode iniciar as atualizações na página **instâncias** , na seção **configurações** .

![Habilitar o processamento de implantação de monitoramento do Revisions do VM](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Próximas etapas

* Consulte [usar o mapa do virtual insights](vminsights-maps.md) para exibir dependências de aplicativo descobertas. 
* Consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md) para identificar afunilamentos, utilização geral e desempenho da VM.
