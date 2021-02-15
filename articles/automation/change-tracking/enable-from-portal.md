---
title: Habilitação dos recursos Controle de Alterações e Inventário da Automação do Azure no portal do Azure
description: Este artigo descreve como habilitar os recursos Controle de Alterações e Inventário no portal do Azure.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 63041e0b1b6e12c765299b12f28aa3637b6a6ccb
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052780"
---
# <a name="enable-change-tracking-and-inventory-from-azure-portal"></a>Habilitação dos recursos de Controle de Alterações e Inventário no portal do Azure

Este artigo descreve como você pode habilitar [controle de alterações e inventário](overview.md) para uma ou mais VMs do Azure no portal do Azure. Para habilitar VMs do Azure em escala, habilite uma VM existente usando os recursos Controle de Alterações e Inventário.

O número de grupos de recursos que você pode usar para gerenciar suas VMs é limitado pelos [limites de implantação do Resource Manager](../../azure-resource-manager/templates/deploy-to-resource-group.md). As implantações do Gerenciador de recursos são limitadas a cinco grupos de recursos por implantação. Dois desses grupos de recursos são reservados para configurar o workspace do Log Analytics, a conta de Automação e os recursos relacionados. Você pode então optar entre três grupos de recursos para gerenciamento pelos recursos Controle de Alterações e Inventário. Esse limite se aplica apenas à configuração simultânea, não ao número de grupos de recursos que podem ser gerenciados por um recurso de Automação.

> [!NOTE]
> Quando habilitar os recursos Controle de Alterações e Inventário, somente determinadas regiões serão compatíveis com a vinculação de um workspace do Log Analytics e uma conta de Automação. Para obter uma lista dos pares de mapeamento compatíveis, veja [Mapeamento de região para conta de Automação e workspace do Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](../automation-security-overview.md) para gerenciar máquinas.
* Uma [máquina virtual](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de Alterações e Inventário

1. No portal do Azure, navegue até **máquinas virtuais**.

2. Use as caixas de seleção para escolher as VMs a serem adicionadas aos recursos Controle de Alterações e Inventário. Você pode adicionar computadores para até três grupos de recursos diferentes por vez. As VMs do Azure podem existir em qualquer região, independentemente do local da sua conta de Automação.

    ![Lista de VMs](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > Use os controles de filtro para selecionar VMs em assinaturas, locais e grupos de recursos diferentes. Você pode clicar na caixa de seleção superior para selecionar todas as máquinas virtuais em uma lista.

3. Selecione **Controle de Alterações** ou **Inventário** em **Gerenciamento de Configuração**.

4. A lista de máquinas virtuais é filtrada para mostrar somente as máquinas virtuais que estão na mesma assinatura e local. Se suas máquinas virtuais estiverem em mais de três grupos de recursos, os três primeiros grupos de recursos serão selecionados.

5. Um workspace do Log Analytics existente e a conta de Automação são selecionados por padrão. Se desejar usar um workspace do Log Analytics e conta de Automação diferentes, clique em **PERSONALIZAR** para selecioná-los na página Configuração Personalizada. Quando você escolhe um workspace do Log Analytics, é feita uma verificação para determinar se ele está vinculado a uma conta de Automação. Se uma conta de Automação vinculada for encontrada, você verá a tela a seguir. Quando terminar, clique em **OK**.

    ![Selecione workspace e conta](media/enable-from-portal/select-workspace-and-account.png)

6. Se o workspace selecionado não estiver vinculado a uma conta de Automação, você verá a tela a seguir. Selecione uma conta de Automação e clique em **OK** ao concluir.

    ![Nenhum workspace](media/enable-from-portal/no-workspace.png)

7. Desmarque a caixa de seleção ao lado de qualquer máquina virtual que você não deseja habilitar. As VMs que não podem ser habilitadas já estão desmarcadas.

8. Clique em **Habilitar** para habilitar o recurso que você selecionou. A instalação leva até 15 minutos para ser concluída.

## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes sobre como trabalhar com o recurso, consulte [gerenciar controle de alterações](manage-change-tracking.md) e [gerenciar o inventário](manage-inventory-vms.md).
* Para solucionar problemas gerais com o recurso, veja [Solucionar problemas de Controle de Alterações e Inventário](../troubleshoot/change-tracking.md).