---
title: Habilitar o Gerenciamento de Atualizações da Automação do Azure a partir da conta de Automação
description: Este artigo informa como habilitar o Gerenciamento de Atualizações a partir de uma conta de Automação.
services: automation
ms.subservice: update-management
ms.date: 11/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 089d5d70d8ad8060455e5c1bee45e0bee4a12fae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575835"
---
# <a name="enable-update-management-from-an-automation-account"></a>Habilitar o Gerenciamento de Atualizações de uma conta de Automação

Este artigo descreve como você pode usar sua conta de automação para habilitar o recurso [Gerenciamento de atualizações](overview.md) para VMs em seu ambiente, incluindo computadores ou servidores registrados com [servidores habilitados para Arc do Azure](../../azure-arc/servers/overview.md). Para habilitar as VMs do Azure em escala, você deve habilitar uma VM do Azure existente usando Gerenciamento de Atualizações.

> [!NOTE]
> Quando habilitamos o Gerenciamento de Atualizações, somente determinadas regiões têm suporte para a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento com suporte, consulte [Mapeamento de região para conta da Automação e workspace do Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](../automation-security-overview.md) para gerenciar máquinas.
* Uma [máquina virtual do Azure](../../virtual-machines/windows/quick-create-portal.md)ou uma VM ou um servidor registrado com servidores habilitados para Arc. As VMs ou servidores não Azure precisam ter o [agente log Analytics](../../azure-monitor/agents/log-analytics-agent.md) para Windows ou Linux instalado e o relatório para o espaço de trabalho vinculado à conta de automação gerenciamento de atualizações está habilitado no. Recomendamos instalar o agente de Log Analytics para Windows ou Linux conectando primeiro o computador aos [servidores habilitados para Arc do Azure](../../azure-arc/servers/overview.md)e, em seguida, use Azure Policy para atribuir a política de [implantação do agente de log Analytics ao *Linux* ou ao *Windows* Azure Arc](../../governance/policy/samples/built-in-policies.md#monitoring) . Como alternativa, se você planeja monitorar as máquinas com Azure Monitor para VMs, use a iniciativa [habilitar Azure monitor para VMs](../../governance/policy/samples/built-in-initiatives.md#monitoring) .


## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Escolha o espaço de trabalho Log Analytics e a conta de automação e selecione **habilitar** para habilitar gerenciamento de atualizações. A instalação leva até 15 minutos para ser concluída.

    ![Habilitar Gerenciamento de Atualizações](media/enable-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Habilitar VMs do Azure

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Selecione **+ Adicionar VMs do Azure** e selecione uma ou mais VMs na lista. Máquinas virtuais que não podem ser habilitadas ficam esmaecidas e não podem ser selecionadas. As VMs do Azure podem existir em qualquer região, independentemente do local da sua conta de Automação.

3. Selecione **habilitar** para adicionar as VMs selecionadas ao grupo de computadores salvo na pesquisa do recurso.

    ![Habilitar VMs do Azure](media/enable-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Habilitar VMs que não sejam do Azure

Para computadores ou servidores hospedados fora do Azure, incluindo aqueles registrados com os servidores habilitados para Arc do Azure, execute as etapas a seguir para habilitá-los com Gerenciamento de Atualizações.  

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Selecione **Adicionar computador não Azure**. Essa ação abre uma nova janela do navegador com [instruções para instalar e configurar o agente do log Analytics para Windows](../../azure-monitor/agents/log-analytics-agent.md) para que o computador possa iniciar a emissão de relatórios para gerenciamento de atualizações. Se você estiver habilitando um computador atualmente gerenciado pelo Operations Manager, um novo agente não será necessário. As informações do espaço de trabalho são adicionadas à configuração dos agentes.

## <a name="enable-machines-in-the-workspace"></a>Habilitar máquinas no workspace

Máquinas ou máquinas instaladas manualmente que já estão se reportando ao seu workspace devem ser adicionadas à Automação do Azure para que o Gerenciamento de Atualizações seja habilitado.

1. Na sua conta da Automação, selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**.

2. Selecione **Gerenciar computadores**. O botão **Gerenciar computadores** poderá ficar esmaecido se você tiver escolhido anteriormente a opção **Habilitar em todos os computadores disponíveis e futuros**

    ![Pesquisas salvas](media/enable-from-automation-account/managemachines.png)

3. Para habilitar o Gerenciamento de Atualizações para todos os computadores disponíveis relatando para o espaço de trabalho, selecione **habilitar em todos os computadores disponíveis** na página Gerenciar computadores. Essa ação desabilita o controle para adicionar computadores individualmente e adiciona todos os computadores relatando ao espaço de trabalho para a consulta de pesquisa salva do grupo de computadores `MicrosoftDefaultComputerGroup` . Quando selecionada, essa ação desabilita a opção **gerenciar computadores** .

4. Para ativar o recurso para todos os computadores disponíveis e futuros, selecione **Habilitar em todos os computadores disponíveis e futuros**. Essa opção exclui a pesquisa salva e a configuração de escopo do espaço de trabalho e permite que o recurso inclua todos os computadores Azure e não Azure que estejam no momento ou no futuro, relate ao espaço de trabalho. Quando selecionada, essa ação desabilita a opção **gerenciar computadores** permanentemente, pois não há nenhuma configuração de escopo disponível.

    > [!NOTE]
    > Como essa opção exclui a pesquisa salva e a configuração de escopo dentro de Log Analytics, é importante remover quaisquer bloqueios de exclusão no espaço de trabalho Log Analytics antes de selecionar essa opção. Se você não fizer isso, a opção falhará ao remover as configurações e você deverá removê-las manualmente.

5. Se necessário, você pode adicionar a configuração de escopo novamente, adicionando novamente a consulta de pesquisa inicial salva. Para obter mais informações, confira [Limitar o escopo de implantação do Gerenciamento de Atualizações](scope-configuration.md).

6. Para habilitar o recurso para um ou mais computadores, selecione **habilitar em computadores selecionados** e selecione **Adicionar** ao lado de cada computador. Essa tarefa adiciona os nomes dos computadores selecionados à consulta de pesquisa salva do grupo de computadores para o recurso.

## <a name="next-steps"></a>Próximas etapas

* Para usar Gerenciamento de Atualizações para VMs, consulte [gerenciar atualizações e patches para suas VMs](manage-updates-for-vm.md).

* Quando você não precisar mais gerenciar VMs ou servidores com Gerenciamento de Atualizações, consulte [remover VMs do gerenciamento de atualizações](remove-vms.md).

* Para solucionar problemas gerais do Gerenciamento de Atualizações, confira [Solucionar problemas do Gerenciamento de Atualizações](../troubleshoot/update-management.md).
