---
title: Mover sua conta de automação do Azure para outra assinatura
description: Este artigo descreve como mover sua conta de automação para outra assinatura
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a82358a2194f10a2112ed89109f0f2933dfd5fe2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478602"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Mover sua conta de automação do Azure para outra assinatura

Azure fornece a capacidade de mover alguns recursos para um novo grupo de recursos ou assinatura. Você pode mover recursos por meio do portal do Azure, PowerShell, a CLI do Azure ou a API REST. Para saber mais sobre o processo, consulte [mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/resource-group-move-resources.md). 

Contas de automação do Azure são um dos recursos que podem ser movidos. Neste artigo, você aprenderá as etapas para mover contas de automação para outro recurso ou da assinatura.

As etapas de alto nível para mover a sua conta de automação são:

1. Remova suas soluções.
2. Desvincule o espaço de trabalho.
3. Mova a conta de automação.
4. Excluir e recriar as contas executar como.
5. Habilite novamente suas soluções.

## <a name="remove-solutions"></a>Remover soluções

Para desvincular o espaço de trabalho da sua conta de automação, essas soluções devem ser removidas do seu espaço de trabalho:
- **Controle de alterações e inventário**
- **Gerenciamento de atualizações** 
- **Iniciar/Parar VMs durante horários inativos** 

No grupo de recursos, encontre cada solução e selecione **excluir**. Sobre o **excluir recursos** , confirme os recursos a ser removido e selecione **excluir**.

![Excluir soluções do portal do Azure](../media/move-account/delete-solutions.png)

Você pode realizar a mesma tarefa com o [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) cmdlet, conforme mostrado no exemplo a seguir:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Etapas adicionais para iniciar/parar VMs

Para o **iniciar/parar VMs** solução, você também precisará remover as regras de alerta criadas pela solução.

No portal do Azure, vá para seu grupo de recursos e selecione **Monitoring** > **alertas** > **gerenciar regras de alerta**.

![Seleção de mostrando página de regras de alerta de gerenciar de alertas](../media/move-account/alert-rules.png)

Sobre o **regras** página, você deve ver uma lista dos alertas configurados no grupo de recursos. O **iniciar/parar VMs** solução cria três regras de alerta:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Selecione esses três regras de alerta e, em seguida, selecione **excluir**. Esta ação removerá essas regras de alerta.

![Página de regras solicitando a confirmação de exclusão para as regras selecionadas](../media/move-account/delete-rules.png)

> [!NOTE]
> Se você não vir nenhuma regra de alerta na **regras** página, altere a **Status** para mostrar **desabilitada** alertas, porque você pode ter desabilitado-los.

Quando as regras de alerta são removidas, remova o grupo de ação que foi criado para o **iniciar/parar VMs** notificações de solução.

No portal do Azure, selecione **Monitor** > **alertas** > **gerenciar grupos de ação**.

Selecione **StartStop_VM_Notification** na lista. Na página do grupo de ação, selecione **excluir**.

![Página de grupo de ação, selecione Excluir](../media/move-account/delete-action-group.png)

Da mesma forma, você pode excluir o grupo de ações usando o PowerShell com o [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdlet, como visto no exemplo a seguir:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Desvincular o espaço de trabalho

No portal do Azure, selecione **conta de automação** > **recursos relacionados** > **espaço de trabalho vinculado**. Selecione **desvincular espaço de trabalho** ao desvincular o espaço de trabalho da sua conta de automação.

![Desvincular um espaço de trabalho de uma conta de automação](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Mover a sua conta de automação

Depois de remover os itens anteriores, você pode continuar a remover sua conta de automação e seus runbooks. No portal do Azure, navegue até o grupo de recursos de sua conta de automação. Selecione **mova** > **mover para outra assinatura**.

![Página de grupo de recursos, mover para outra assinatura](../media/move-account/move-resources.png)

Selecione os recursos no grupo de recursos que você deseja mover. Verifique se você incluir seu **conta de automação**, **Runbook**, e **espaço de trabalho do Log Analytics** recursos.

Após a migração for concluída, há etapas adicionais necessárias para fazer tudo funcionar.

## <a name="re-create-run-as-accounts"></a>Recrie as contas executar como

[Contas executar como](../manage-runas-account.md) criar uma entidade de serviço no Azure Active Directory para autenticar com recursos do Azure. Quando você altera as assinaturas, a conta de automação não usa mais a conta executar como existente.

Vá para sua conta de automação na nova assinatura e selecione **contas executar como** sob **configurações de conta**. Você verá que as contas executar como mostram como incompletas agora.

![Contas executar como estão incompletas](../media/move-account/run-as-accounts.png)

Selecione cada conta executar como. Sobre o **propriedades** página, selecione **excluir** para excluir a conta executar como.

> [!NOTE]
> Se você não tem permissões para criar ou exibir as contas executar como, você verá a seguinte mensagem: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Para saber mais sobre as permissões necessárias para configurar uma conta executar como, consulte [permissões necessárias para configurar contas executar como](../manage-runas-account.md#permissions).

Depois de excluir as contas executar como, selecione **Create** sob **conta executar como do Azure**. Sobre o **adicionar Azure executar como conta** página, selecione **criar** para criar a conta executar como e o serviço principal. Repita as etapas anteriores com o **do Azure conta executar como clássica**.

## <a name="enable-solutions"></a>Habilitar soluções

Depois de criar as contas executar como novamente, você vai habilitar novamente as soluções que você removeu antes da movimentação. Para ativar **controle de alterações e inventário** e **gerenciamento de atualizações**, selecione o respectivo recurso em sua conta de automação. Escolha o espaço de trabalho do Log Analytics de mudar e selecione **habilitar**.

![Habilitar novamente as soluções em sua conta de automação movida](../media/move-account/reenable-solutions.png)

As máquinas que estão integrados com suas soluções ficará visíveis quando você se conectou o espaço de trabalho do Log Analytics existente.

Para ativar o **iniciar/parar VMs** durante a solução de fora do horário comercial, você precisará reimplantar a solução. Sob **recursos relacionados**, selecione **iniciar/parar VMs** > **Saiba mais sobre e habilite a solução** > **criar** para iniciar a implantação.

Sobre o **adicionar solução** , escolha sua conta de automação e espaço de trabalho do Log Analytics.  

![Adicionar menu de solução](../media/move-account/add-solution-vm.png)

Para obter instruções detalhadas sobre como configurar a solução, consulte [iniciar/parar VMs durante a solução de fora do horário comercial na automação do Azure](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Pós-mover verificação

Quando a migração for concluída, verifique a seguinte lista de tarefas que devem ser verificados:

|Recurso|Testes|Link de solução de problemas|
|---|---|---|
|Runbooks|Um runbook pode executar e conectar-se aos recursos do Azure com êxito.|[Solucionar problemas de runbooks](../troubleshoot/runbooks.md)
| Controle do código-fonte|Você pode executar uma sincronização manual em seu repositório de controle do código-fonte.|[Integração de controle de origem](../source-control-integration.md)|
|Controle de alterações e inventário|Verifique se que você ver os dados de inventário atual de suas máquinas.|[Solucionar problemas de controle de alterações](../troubleshoot/change-tracking.md)|
|Gerenciamento de atualizações|Verifique se você ver suas máquinas e estão íntegros.</br>Execute uma implantação de atualização de software de teste.|[Solucionar problemas de gerenciamento de atualizações](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como mover recursos no Azure, consulte [mover os recursos no Azure](../../azure-resource-manager/move-support-resources.md).
