---
title: Mover a conta de Automação do Azure para outra assinatura
description: Esse artigo informa como mover a conta de Automação para outra assinatura.
services: automation
ms.subservice: process-automation
ms.date: 01/07/2021
ms.topic: conceptual
ms.openlocfilehash: a86d876a723c89eb8dcdf18c8318f2a9c740a229
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99051017"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Mover a conta de Automação do Azure para outra assinatura

A Automação do Azure permite mover recursos para um novo grupo de recursos ou uma nova assinatura. Você pode usar o portal do Azure, o PowerShell, a CLI do Azure ou a API REST para mover recursos. Para saber mais sobre o processo, confira [Mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

A conta de Automação é um dos recursos que você pode mover. Neste artigo, você aprenderá a mover as contas de Automação para outro recurso ou assinatura. As etapas de alto nível para mover sua conta de Automação são:

1. Desabilite os recursos.
2. Desvincule o workspace.
3. Mova a conta da Automação.
4. Exclua e depois recrie as contas Executar como.
5. Reabilite os recursos.

## <a name="remove-features"></a>Remover recursos

Para desvincular seu workspace da sua conta de Automação, você precisa remover os recursos em seu workspace:

- Controle de Alterações e Inventário
- Gerenciamento de atualizações
- Iniciar/Parar VMs durante os horários inativos

1. No portal do Azure, encontre seu grupo de recursos.
2. Localize cada recurso e selecione **Excluir** na página **Excluir Recursos**.

    ![Captura de tela da exclusão de recursos do portal do Azure](../media/move-account/delete-solutions.png)

Se preferir, você pode excluir os recursos usando o cmdlet [Remove-AzResource](/powershell/module/Az.Resources/Remove-AzResource):

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>Remover regras de alerta para Iniciar/Parar VMs fora do horário comercial

Por Iniciar/Parar VMs fora do horário comercial, você também precisa remover as regras de alerta criadas pelo recurso.

1. Na portal do Azure, vá até o grupo de recursos e selecione **Monitoramento** > **Alertas** > **Gerenciar regras de alerta**.

   ![Captura de tela da página alertas, mostrando a seleção de Gerenciar regras de alerta](../media/move-account/alert-rules.png)

2. Na página Regras, você verá uma lista dos alertas configurados nesse grupo de recursos. O recurso cria estas regras:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Selecione as regras uma de cada vez e selecione **Excluir** para removê-las.

    ![Captura de tela da página Regras, solicitando confirmação de exclusão para as regras selecionadas](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Se você não vê nenhuma regra de alerta na página Regras, altere o campo **Status** para **Desabilitado** para mostrar alertas desabilitados. 

4. Ao remover as regras de alerta, você precisa remover o grupo de ações criado para notificações de Iniciar/Parar VMs fora do horário comercial. No portal do Azure, selecione **Monitor** > **Alertas** > **Gerenciar grupos de ações**.

5. Selecione **StartStop_VM_Notification**. 

6. Na página do grupo de ações, selecione **Excluir**.

    ![Captura de tela da página Grupo de ações](../media/move-account/delete-action-group.png)

Se preferir, você pode excluir o grupo de ações usando o cmdlet [Remove-AzActionGroup](/powershell/module/az.monitor/remove-azactiongroup):

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Desvincular o workspace

Agora você pode desvincular o workspace:

1. Na portal do Azure, selecione **Conta de Automação** > **Recursos Relacionados** > **Workspace vinculado**. 

2. Selecione **Desvincular workspace** para desvincular o workspace da sua conta de Automação.

    ![Captura de tela de desvincular um workspace de uma conta de Automação](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Mova a sua conta de Automação

Agora você pode mover sua conta de Automação e os respectivos runbooks. 

1. Na portal do Azure, navegue até o grupo de recursos da sua conta de Automação. Selecione **Mover** > **Mover para outra assinatura**.

    ![Captura de tela da página do grupo de recursos, mover para outra assinatura](../media/move-account/move-resources.png)

2. Selecione os recursos no grupo de recursos que você deseja mover. É preciso que você inclua seus recursos de conta de Automação, runbooks e workspace do Log Analytics.

## <a name="re-create-run-as-accounts"></a>Recriar contas Executar como

[Contas Executar como](../automation-security-overview.md#run-as-accounts) criam uma entidade de serviço no Azure Active Directory para autenticar com os recursos do Azure. Quando você altera as assinaturas, a conta de Automação deixa de usar a conta Executar como existente. Para recriar as contas Executar como:

1. Acesse sua conta de Automação na nova assinatura e selecione **contas Executar como** em **Configurações da Conta**. Você verá que as contas Executar como são mostradas como incompletas agora.

    ![Captura de tela de contas Executar como, mostrando-as como incompletas](../media/move-account/run-as-accounts.png)

2. Exclua as contas Executar como, uma de cada vez, selecionando **Excluir** na página **Propriedades**. 

    > [!NOTE]
    > Se você não tiver permissões para criar nem exibir as contas Executar Como, verá a seguinte mensagem: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Para obter mais informações, confira [Permissões necessárias para configurar contas Executar como](../automation-security-overview.md#permissions).

3. Depois de excluir as contas Executar como, selecione **Criar** em **conta Executar como do Azure**. 

4. Na página Adicionar conta Executar como do Azure, selecione **Criar** para criar a conta Executar como e a entidade de serviço. 

5. Repita as etapas acima com a conta Executar como do Azure Clássico.

## <a name="enable-features"></a>Habilitar recursos

Depois de recriar as contas Executar como, você precisa reabilitar os recursos que removeu antes da movimentação:

1. Para ativar o Controle de Alterações e Inventário, selecione **Controle de Alterações e Inventário** em sua conta de Automação. Escolha o workspace do Log Analytics que você moveu e selecione **Habilitar**.

2. Repita a etapa 1 para Gerenciamento de Atualizações.

    ![Captura de tela de reabilitar recursos em sua conta de Automação movida](../media/move-account/reenable-solutions.png)

3. Os computadores habilitados com seus recursos ficam visíveis quando você conectou o workspace do Log Analytics existente. Para ativar o recurso Iniciar/Parar VMs fora do horário comercial, você precisa reabilitá-lo. Em **Recursos Relacionados**, selecione **Iniciar/Parar VMs** > **Saiba mais sobre a solução e habilitá-la** > **Criar** para iniciar a implantação.

4. Na página Adicionar Solução, escolha o workspace do Log Analytics e a conta de Automação.

    ![Captura de tela do menu Adicionar Solução](../media/move-account/add-solution-vm.png)

5. Configure o recurso conforme descrito na [visão geral Iniciar/Parar VMs fora do horário comercial](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Verificar a movimentação

Quando a movimentação for concluída, verifique se os recursos listados abaixo estão habilitados. 

|Recurso|Testes|Solução de problemas|
|---|---|---|
|Runbooks|É possível executar um runbook com êxito conectá-lo a recursos do Azure.|[Solucionar problemas de runbooks](../troubleshoot/runbooks.md)
|Controle do código-fonte|Você pode executar uma sincronização manual em seu repositório de controle do código-fonte.|[Integração de controle do código-fonte](../source-control-integration.md)|
|Controle de alterações e inventário|Verifique se você vê os dados de inventário atuais de seus computadores.|[Solucionar problemas do controle de alterações](../troubleshoot/change-tracking.md)|
|Gerenciamento de atualizações|Verifique se você vê seus computadores e se eles estão íntegros.</br>Execute uma implantação de teste de atualização de software.|[Solucionar problemas de gerenciamento de atualizações](../troubleshoot/update-management.md)|
|Recursos compartilhados|Verifique se você vê todos os seus recursos compartilhados, como [credenciais](../shared-resources/credentials.md) e [variáveis](../shared-resources/variables.md).|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como mover recursos no Azure, confira [Mover recursos no Azure](../../azure-resource-manager/management/move-support-resources.md).
