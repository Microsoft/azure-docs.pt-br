---
title: Habilitar a Gerenciamento de Atualizações de automação do Azure do portal do Azure
description: Este artigo informa como habilitar o Gerenciamento de Atualizações do portal do Azure.
services: automation
ms.subservice: update-management
ms.date: 01/07/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 089c5fea6ac4a6fc4fb25af2d631335ef51cf4cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054899"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Habilitar o Gerenciamento de Atualizações do portal do Azure

Este artigo descreve como você pode habilitar o recurso [Gerenciamento de Atualizações](overview.md) para VMs navegando no portal do Azure. Para habilitar as VMs do Azure em escala, você deve habilitar uma VM do Azure existente usando Gerenciamento de Atualizações.

O número de grupos de recursos que você pode usar para gerenciar suas VMs é limitado pelos [limites de implantação do Resource Manager](../../azure-resource-manager/templates/deploy-to-resource-group.md). As implantações do Resource Manager, que não devem ser confundidas com implantações de atualização, estão limitadas a cinco grupos de recursos por implantação. Dois desses grupos de recursos são reservados para configurar o workspace do Log Analytics, a conta de Automação e os recursos relacionados. Você pode então optar entre três grupos de recursos para gerenciamento pelo Gerenciamento de Atualizações. Esse limite se aplica apenas à configuração simultânea, não ao número de grupos de recursos que podem ser gerenciados por um recurso de Automação.

> [!NOTE]
> Ao habilitar o Gerenciamento de Atualizações, somente determinadas regiões são compatíveis com a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento compatíveis, confira [Mapeamento de região para conta da Automação e workspace do Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](../automation-security-overview.md) para gerenciar máquinas.
* Uma [máquina virtual](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure em https://portal.azure.com.

## <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

1. No portal do Azure, navegue até **máquinas virtuais**.

2. Na página **máquinas virtuais** , use as caixas de seleção para escolher as VMs a serem adicionadas gerenciamento de atualizações. Você pode adicionar computadores para até três grupos de recursos diferentes por vez. As VMs do Azure podem existir em qualquer região, independentemente do local da sua conta de Automação.

    ![Lista de VMs](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > Use os controles de filtro para selecionar VMs em assinaturas, locais e grupos de recursos diferentes. Você pode clicar na caixa de seleção superior para selecionar todas as máquinas virtuais em uma lista.

    [![Habilitar gerenciamento de atualizações](./media/enable-from-portal/onboard-feature.png)](./media/enable-from-portal/onboard-feature-expanded.png#lightbox)

3. Selecione **Serviços** e selecione **Gerenciamento de atualizações** para o recurso gerenciamento de atualizações.

4. A lista de máquinas virtuais é filtrada para mostrar somente as máquinas virtuais que estão na mesma assinatura e local. Se suas máquinas virtuais estiverem em mais de três grupos de recursos, os três primeiros grupos de recursos serão selecionados.

5. Um workspace do Log Analytics existente e a conta de Automação são selecionados por padrão. Se você quiser usar um espaço de trabalho Log Analytics e uma conta de automação diferentes, selecione **personalizado** para selecioná-los na página configuração personalizada. Quando você escolhe um workspace do Log Analytics, é feita uma verificação para determinar se ele está vinculado a uma conta de Automação. Se uma conta de Automação vinculada for encontrada, você verá a tela a seguir. Ao terminar, selecione **OK**.

    [![Selecionar espaço de trabalho e conta](./media/enable-from-portal/select-workspace-and-account.png)](./media/enable-from-portal/select-workspace-and-account-expanded.png#lightbox)

6. Se o workspace selecionado não estiver vinculado a uma conta de Automação, você verá a tela a seguir. Selecione uma conta de automação e selecione **OK** quando terminar.

    ![Nenhum workspace](media/enable-from-portal/no-workspace.png)

7. Desmarque qualquer máquina virtual que você não deseja habilitar. As VMs que não podem ser habilitadas já estão desmarcadas.

8. Selecione **habilitar** para habilitar o recurso. Depois de habilitar Gerenciamento de Atualizações, pode levar cerca de 15 minutos para que você possa exibir a avaliação de atualização deles.

## <a name="next-steps"></a>Próximas etapas

* Para usar Gerenciamento de Atualizações para VMs, consulte [gerenciar atualizações e patches para suas VMs](manage-updates-for-vm.md).
* Para solucionar problemas gerais do Gerenciamento de Atualizações, confira [Solucionar problemas do Gerenciamento de Atualizações](../troubleshoot/update-management.md).
* Para solucionar problemas com o agente de atualização do Windows, confira [Solucionar problemas do agente de atualização do Windows](../troubleshoot/update-agent-issues.md).
* Para solucionar problemas com o agente de atualização do Linux, confira [Solucionar problemas do agente de atualização do Linux](../troubleshoot/update-agent-issues-linux.md).