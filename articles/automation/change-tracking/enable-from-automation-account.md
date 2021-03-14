---
title: Habilitar o Controle de Alterações da Automação do Azure e Inventário da conta de Automação
description: Este artigo conta como habilitar o Controle de Alterações e Inventário de uma conta da Automação.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 32fb95c88d632cc2c51cd2390f0244e9c1927051
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585899"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Habilitar Controle de Alterações e Inventário de uma conta de Automação

Este artigo descreve como você pode usar sua conta de automação para habilitar [controle de alterações e inventário](overview.md) para VMs em seu ambiente. Para habilitar VMs do Azure em escala, habilite uma VM existente usando os recursos Controle de Alterações e Inventário.

> [!NOTE]
> Ao habilitar o Controle de Alterações e Inventário, somente determinadas regiões serão compatíveis com a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento compatíveis, confira [Mapeamento de região para conta da Automação e workspace do Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](../automation-security-overview.md) para gerenciar máquinas.
* Uma [máquina virtual](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de Alterações e Inventário

1. Navegue até sua conta de Automação e selecione **Inventário** ou **Controle de alterações** em **Gerenciamento de configuração**.

2. Escolha o workspace do Log Analytics e a conta da Automação e clique em **Habilitar** para habilitar o Controle de Alterações e Inventário. A instalação leva até 15 minutos para ser concluída.

    ![Habilitar Controle de Alterações e Inventário](media/enable-from-automation-account/enable-feature.png)

## <a name="enable-azure-vms"></a>Habilitar VMs do Azure

1. Na sua conta de Automação, selecione **Inventário** ou **Controle de Alterações** em **Gerenciamento de Configuração**.

2. Clique em **+ Adicionar VMs do Azure** e selecione uma ou mais VMs na lista. Máquinas virtuais que não podem ser habilitadas ficam esmaecidas e não podem ser selecionadas. As VMs do Azure podem existir em qualquer região, independentemente do local da sua conta de Automação. 

3. Clique em **Habilitar** para adicionar as VMs selecionadas à pesquisa salva do grupo de computadores para o recurso. Para obter mais informações, consulte [limitar controle de alterações e escopo de implantação de estoque](manage-scope-configurations.md).

      [![Habilitar VMs do Azure](./media/enable-from-automation-account/enable-azure-vms.png)](./media/enable-from-automation-account/enable-azure-vms-expanded.png#lightbox)

## <a name="enable-non-azure-vms"></a>Habilitar VMs que não sejam do Azure

Computadores que não estão no Azure precisam ser adicionados manualmente. Recomendamos instalar o agente de Log Analytics para Windows ou Linux conectando primeiro o computador aos [servidores habilitados para Arc do Azure](../../azure-arc/servers/overview.md)e, em seguida, usando Azure Policy para atribuir a política de [implantação do agente de log Analytics ao *Linux* ou ao *Windows* Azure Arc](../../governance/policy/samples/built-in-policies.md#monitoring) . Se você também planeja monitorar as máquinas com Azure Monitor para VMs, use a iniciativa [habilitar Azure monitor para VMs](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

1. Na sua conta de Automação, selecione **Inventário** ou **Controle de Alterações** em **Gerenciamento de Configuração**.

2. Clique em **Adicionar computador não Azure**. Essa ação abre uma nova janela do navegador com [instruções para instalar e configurar o agente do Log Analytics para o Windows](../../azure-monitor/agents/log-analytics-agent.md). Isso serve para o computador começar a relatar operações de Controle de Alterações e Inventário. Se você estiver habilitando um computador que seja gerenciado atualmente pelo Operations Manager, não será necessário um novo agente e as informações do workspace serão inseridas no agente existente.

## <a name="enable-machines-in-the-workspace"></a>Habilitar computadores no workspace

Computadores instalados manualmente ou que já relatam para seu workspace precisam ser adicionados à Automação do Azure para que o Controle de Alterações e Inventário seja habilitado.

1. Na sua conta de Automação, selecione **Inventário** ou **Controle de Alterações** em **Gerenciamento de Configuração**.

2. Selecione **Gerenciar computadores**. A opção **gerenciar computadores** pode estar esmaecida se você tiver escolhido anteriormente a opção **habilitar em todos os computadores disponíveis e futuros**

    ![Pesquisas salvas](media/enable-from-automation-account/manage-machines.png)

3. Para habilitar Controle de Alterações e inventário para todos os computadores disponíveis, selecione **habilitar em todos os computadores disponíveis** na página **gerenciar computadores** . Essa ação desabilita o controle para adicionar computadores individualmente e adiciona todos os computadores relatando ao espaço de trabalho para a consulta de pesquisa salva do grupo de computadores. Quando selecionada, essa ação desabilita a opção **gerenciar computadores** .

4. Para ativar o recurso para todos os computadores disponíveis e futuros, selecione **Habilitar em todos os computadores disponíveis e futuros**. Essa opção exclui a pesquisa salva e a configuração de escopo do espaço de trabalho e abre o recurso para todos os computadores Azure e não Azure que estão se comunicando com o espaço de trabalho. Quando selecionada, essa ação desabilita a opção **gerenciar computadores** permanentemente, pois não há nenhuma configuração de escopo restante.

    > [!NOTE]
    > Como essa opção exclui a pesquisa salva e a configuração de escopo dentro de Log Analytics, é importante remover quaisquer bloqueios de exclusão no espaço de trabalho Log Analytics antes de selecionar essa opção. Se você não fizer isso, a opção falhará ao remover as configurações e você deverá removê-las manualmente.

5. Se necessário, você pode adicionar a configuração de escopo novamente, adicionando novamente a pesquisa salva inicial. Para obter mais informações, consulte [limitar controle de alterações e escopo de implantação de estoque](manage-scope-configurations.md).

6. Para habilitar a funcionalidade em um ou mais computadores, selecione **Habilitar nos computadores selecionados** e clique em **Adicionar** próximo a cada computador em que você quer habilitar a funcionalidade. Essa tarefa adiciona os nomes dos computadores selecionados à consulta de pesquisa salva do grupo de computadores para o recurso.

## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes sobre como trabalhar com o recurso, consulte [gerenciar controle de alterações](manage-change-tracking.md) e [gerenciar o inventário](manage-inventory-vms.md).

* Para solucionar problemas gerais com o recurso, veja [Solucionar problemas de Controle de Alterações e Inventário](../troubleshoot/change-tracking.md).
