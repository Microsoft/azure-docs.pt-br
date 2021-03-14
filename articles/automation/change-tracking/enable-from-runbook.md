---
title: Habilitar Controle de Alterações e Inventário da Automação do Azure por um runbook
description: Este artigo mostra como habilitar o Controle de Alterações e Inventário por um runbook.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e5b42d6102737b778ea5d19cd7da3c2f64881b1b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585934"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Habilitar Controle de Alterações e Inventário de um runbook

Este artigo descreve como você pode usar um runbook para habilitar [controle de alterações e inventário](overview.md) para VMs em seu ambiente. Para habilitar VMs do Azure em escala, habilite uma VM existente usando os recursos Controle de Alterações e Inventário.

> [!NOTE]
> Ao habilitar o Controle de Alterações e Inventário, somente determinadas regiões serão compatíveis com a vinculação de um workspace do Log Analytics e uma conta da Automação. Para obter uma lista dos pares de mapeamento compatíveis, confira [Mapeamento de região para conta da Automação e workspace do Log Analytics](../how-to/region-mappings.md).

Esse método usa dois runbooks:

* **Enable-MultipleSolution** -o runbook primário que solicita informações de configuração, consulta a VM especificada e executa outras verificações de validação e, em seguida, invoca o runbook **Enable-AutomationSolution** para configurar controle de alterações e inventário para cada VM dentro do grupo de recursos especificado.
* **Enable-AutomationSolution** -habilita controle de alterações e inventário para uma ou mais VMs especificadas no grupo de recursos de destino. Ele verifica os pré-requisitos são atendidos, verifica se a extensão de VM Log Analytics está instalada e instala se não for encontrada e adiciona as VMs à configuração de escopo no espaço de trabalho Log Analytics especificado vinculado à conta de automação.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](../automation-security-overview.md) para gerenciar máquinas.
* [Espaço de Trabalho do Log Analytics](../../azure-monitor/logs/design-logs-deployment.md)
* Uma [máquina virtual](../../virtual-machines/windows/quick-create-portal.md).
* Dois ativos de automação, que são usados pelo runbook **Enable-AutomationSolution** . Esse runbook, se ele ainda não existir na sua conta de automação, será importado automaticamente pelo runbook **Enable-MultipleSolution** durante sua primeira execução.
    * *LASolutionSubscriptionId*: ID da assinatura em que o espaço de trabalho do log Analytics está localizado.
    * *LASolutionWorkspaceId*: ID do espaço de trabalho do log Analytics espaço de trabalho vinculado à sua conta de automação.

    Essas variáveis são usadas para configurar o espaço de trabalho da VM integrada. Se eles não forem especificados, o script primeiro procura qualquer VM integrada a Controle de Alterações e inventário em sua assinatura, seguida pela assinatura em que a conta de automação está, seguida de todas as outras assinaturas às quais sua conta de usuário tem acesso. Se não estiver configurado corretamente, isso poderá resultar em seus computadores serem integrados a algum espaço de trabalho de Log Analytics aleatório.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de Alterações e Inventário

1. Na portal do Azure, navegue até **contas de automação**. Na página **contas de automação** , selecione sua conta na lista.

1. Em sua conta de automação, selecione **inventário** ou **controle de alterações** em **Gerenciamento de configuração**.

1. Selecione o workspace do Log Analytics e clique em **Habilitar**. Enquanto o inventário ou Controle de Alterações está sendo habilitado, uma faixa é mostrada.

    ![Habilitar Controle de Alterações e Inventário](media/enable-from-automation-account/enable-feature.png)

## <a name="install-and-update-modules"></a>Instalar e atualizar módulos

É necessário atualizar para os módulos mais recentes do Azure e importar o módulo [AZ. OperationalInsights](/powershell/module/az.operationalinsights) para habilitar com êxito gerenciamento de atualizações para suas VMs com o runbook.

1. Na sua conta de Automação, selecione **Módulos** em **Recursos Compartilhados**.

2. Selecione **Atualizar Módulos do Azure** para atualizar os módulos do Azure para a versão mais recente.

3. Clique em **Sim** no prompt para atualizar todos os módulos do Azure existentes para a versão mais recente.

    ![Atualizar módulos](media/enable-from-runbook/update-modules.png)

4. Retorne para **Módulos** em **Recursos Compartilhados**.

5. Selecione **Procurar na galeria** para abrir a galeria de módulos.

6. Pesquise `Az.OperationalInsights` e importe esse módulo para a sua conta da Automação.

    ![Importar o módulo OperationalInsights](media/enable-from-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>Selecione a VM do Azure a ser gerenciada

Com o Controle de Alterações e Inventário habilitado, é possível adicionar uma VM do Azure para gerenciamento pelo recurso.

1. Na sua conta de Automação, selecione **Controle de Alterações** ou **Inventário** em **Gerenciamento de Configuração**.

2. Clique em **Adicionar VMs do Azure** para adicionar sua VM.

3. Escolha sua VM na lista e clique em **Habilitar**. Essa ação habilita o Controle de Alterações e Inventário para a VM.

   ![Habilitar o Controle de Alterações e Inventário para a VM](media/enable-from-runbook/enable-azure-vm.png)

    > [!NOTE]
    > Se tentar habilitar outro recurso antes que a configuração do Controle de Alterações e Inventário tenha sido concluída, você receberá esta mensagem: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Importar um runbook para habilitar o Controle de Alterações e Inventário

1. Na sua conta de Automação, selecione **Runbooks** em **Automação de Processos**.

2. Selecione **Procurar na galeria**.

3. Pesquise por **atualização e controle de alterações**.

4. Selecione o runbook e clique em **importar** na página **Exibir origem** .

5. Clique **OK** para importar o runbook para a conta de Automação.

   ![Importar runbook para configuração](media/enable-from-runbook/import-from-gallery.png)

6. Na página **runbook** , selecione o runbook **Enable-MultipleSolution** e clique em **Editar**. No editor de texto, selecione  **publicar**.

7. Quando for solicitado a confirmar, clique em **Sim** para publicar o runbook.

## <a name="start-the-runbook"></a>Iniciar o runbook

Você deve ter habilitado Controle de Alterações e Inventário para uma VM do Azure para iniciar este runbook. Ele requer uma VM e um grupo de recursos existentes com o recurso habilitado para configurar uma ou mais VMs no grupo de recursos de destino.

1. Abra o runbook **Enable-MultipleSolution**.

   ![Runbook de várias soluções](media/enable-from-runbook/runbook-overview.png)

2. Clique no botão Iniciar e insira os valores de parâmetros nos seguintes campos:

   * **VMNAME**: o nome de uma VM existente a ser adicionada ao Controle de Alterações e Inventário. Deixe esse campo em branco para adicionar todas as VMs no grupo de recursos.
   * **VMRESOURCEGROUP** – O nome do grupo de recursos para as VMs a serem habilitadas.
   * **SUBSCRIPTIONID** – A ID da assinatura da nova VM a ser habilitada. Deixe esse campo em branco para usar a assinatura do workspace. Ao usar uma ID de assinatura diferente, adicione a conta Executar como da sua conta da Automação como um colaborador da assinatura.
   * **ALREADYONBOARDEDVM** – O nome da VM que já está habilitada manualmente para atualizações.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – o nome do grupo de recursos ao qual a VM pertence.
   * **SOLUTIONTYPE**: insira **ChangeTracking**.

   ![Parâmetros do runbook Enable-MultipleSolution](media/enable-from-runbook/runbook-parameters.png)

3. Selecione **OK** para iniciar o trabalho de runbook.

4. Monitore o progresso do trabalho de runbook e quaisquer erros na página de **trabalhos** .

## <a name="next-steps"></a>Próximas etapas

* Para agendar um runbook, consulte [Gerenciar agendamentos na Automação do Azure](../shared-resources/schedules.md).

* Para obter detalhes sobre como trabalhar com o recurso, consulte [gerenciar controle de alterações](manage-change-tracking.md) e [gerenciar o inventário](manage-inventory-vms.md).

* Para solucionar problemas gerais com o recurso, veja [Solucionar problemas de Controle de Alterações e Inventário](../troubleshoot/change-tracking.md).


