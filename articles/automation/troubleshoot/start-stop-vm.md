---
title: Solucionar problemas do recurso Iniciar/Parar VMs fora do horário comercial da Automação do Azure
description: Este artigo informa como solucionar problemas que surgem durante o uso do recurso Iniciar/Parar VMs fora do horário comercial.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: troubleshooting
ms.openlocfilehash: f6d2696a68643f87de0fcaf2e723da9365d9953e
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953843"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>Solucionar problemas de Iniciar/Parar VMs fora do horário comercial

Este artigo fornece informações sobre como solucionar problemas que surgem enquanto você implanta o recurso Iniciar/Parar VMs fora do horário comercial da Automação do Azure nas suas VMs. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Cenário: O recurso Iniciar/Parar VMs fora do horário comercial não é implantado corretamente

### <a name="issue"></a>Problema

Ao implantar o recurso [Iniciar/Parar VMs fora do horário comercial](../automation-solution-vm-management.md), você recebe um dos seguintes erros:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Causa

As implantações podem falhar devido a um dos seguintes motivos:

- Já existe uma conta de Automação com o mesmo nome na região selecionada.
- Uma política não permite a implantação de Iniciar/Parar VMs fora do horário comercial.
- O tipo de recurso `Microsoft.OperationsManagement`, `Microsoft.Insights` ou `Microsoft.Automation` não está registrado.
- O workspace do Log Analytics está bloqueado.
- Você tem uma versão desatualizada dos módulos do AzureRM ou do recurso Iniciar/Parar VMs fora do horário comercial.

### <a name="resolution"></a>Resolução

Vejas as possíveis resoluções a seguir:

* Contas de Automação precisam ser exclusivas em uma região do Azure, mesmo se elas estiverem em grupos de recursos diferentes. Verifique suas contas de Automação na região de destino.
* Uma política existente impede a implantação de um recurso necessário para a implantação de Iniciar/Parar VMs fora do horário comercial. Acesse suas atribuições de política no portal do Azure e verifique se você tem uma atribuição de política que não permite a implantação deste recurso. Para saber mais, confira [Erro RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Para implantar o recurso Iniciar/Parar VMs fora do horário comercial, sua assinatura precisa estar registrada nos seguintes namespaces de recursos do Azure:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Para saber mais sobre erros ao registrar provedores, consulte [Solucionar erros de registro do provedor de recursos](../../azure-resource-manager/templates/error-register-resource-provider.md).
* Se você tiver um bloqueio em seu espaço de trabalho do Log Analytics, vá até o seu espaço de trabalho no portal do Azure e remova quaisquer bloqueios no recurso.
* Se essas resoluções não resolverem o problema, siga as instruções em [Atualizar o recurso](../automation-solution-vm-management.md#update-the-feature) para reimplantar Iniciar/Parar VMs fora do horário comercial.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Cenário: Todas as VMs não conseguem iniciar ou parar

### <a name="issue"></a>Problema

Você configurou o recurso Iniciar/Parar VMs fora do horário comercial, mas ele não inicia nem para todas as VMs.

### <a name="cause"></a>Causa

Esse erro pode ser causado por um dos seguintes motivos:

- Um agendamento não está configurado corretamente.
- Pode ser que a conta Executar como não esteja configurada corretamente.
- Podem ser erros em um runbook.
- É possível que as VMs tenham sido excluídas.

### <a name="resolution"></a>Resolução

Vejas as possíveis resoluções na lista a seguir:

* Verifique se você configurou corretamente o agendamento de Iniciar/Parar VMs fora do horário comercial. Para saber como configurar um agendamento, confira o artigo [Agendamentos](../shared-resources/schedules.md).

* Verifique se há erros nos [fluxos de trabalho](../automation-runbook-execution.md#job-statuses). Procure os trabalhos de um dos seguintes runbooks:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Verifique se a sua [conta Executar como](../automation-security-overview.md#run-as-accounts) tem as permissões apropriadas para acessar as VMs que você está tentando iniciar ou parar. Para saber como verificar as permissões em um recurso, confira [Início Rápido: exibir funções atribuídas a um usuário usando o portal do Azure](../../role-based-access-control/check-access.md). Você precisará fornecer a ID de aplicativo da entidade de serviço usada pela conta Executar como. Você pode recuperar esse valor acessando sua conta de Automação no portal do Azure. Selecione **Conta Executar como** em **Configurações da Conta** e escolha a conta Executar como apropriada.

* Você não pode iniciar ou parar as VMs caso elas estejam sendo excluídas explicitamente. As VMs excluídas estão definidas na variável `External_ExcludeVMNames` na conta de Automação na qual o recurso está implantado. O exemplo a seguir mostra como você pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Cenário: Algumas das minhas VMs falham ao iniciar ou parar

### <a name="issue"></a>Problema

Você configurou o recurso Iniciar/Parar VMs fora do horário comercial, mas ele não inicia nem para algumas das VMs configuradas.

### <a name="cause"></a>Causa

Esse erro pode ser causado por um dos seguintes motivos:

- No cenário de sequência, uma marca pode estar faltando ou incorreta.
- A VM pode ser excluída.
- A conta Executar como pode não ter permissões suficientes na VM.
- A VM pode ter um problema que a impediu de iniciar ou parar.

### <a name="resolution"></a>Resolução

Vejas as possíveis resoluções na lista a seguir:

* Ao usar o [cenário de sequência](../automation-solution-vm-management.md) de Iniciar/Parar VMs fora do horário comercial, você deve verificar se cada VM que deseja iniciar ou parar tem a marca adequada. Verifique se as VMs que você deseja iniciar têm a marca `sequencestart` e as VMs que você deseja parar têm a marca `sequencestop`. Ambas as marcas necessitam de um valor inteiro positivo. Você pode usar uma consulta semelhante ao exemplo a seguir para procurar todas as VMs com as marcas e seus valores.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Você não pode iniciar ou parar as VMs caso elas estejam sendo excluídas explicitamente. As VMs excluídas estão definidas na variável `External_ExcludeVMNames` na conta de Automação na qual o recurso está implantado. O exemplo a seguir mostra como você pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Para iniciar e parar VMs, a conta Executar como da conta de Automação deve ter as permissões apropriadas para acessá-las. Para saber como verificar as permissões em um recurso, confira [Início Rápido: exibir funções atribuídas a um usuário usando o portal do Azure](../../role-based-access-control/check-access.md). Você precisará fornecer a ID de aplicativo da entidade de serviço usada pela conta Executar como. Você pode recuperar esse valor acessando sua conta de Automação no portal do Azure. Selecione **Conta Executar como** em **Configurações da Conta** e escolha a conta “Executar como” apropriada.
* Se a VM estiver com problema na inicialização ou desalocação, talvez seja erro na própria VM. Os exemplos são: uma atualização que está sendo aplicada quando a VM tenta desligar, um serviço que para de funcionar e muito mais. Acesse o recurso da VM e confira os **Logs de atividades** para ver se existem erros nos logs. Você também pode tentar fazer logon na VM para ver se existem erros nos logs de eventos. Para saber mais sobre como solucionar problemas da VM, confira [Solução de problemas de máquinas virtuais do Azure](/troubleshoot/azure/virtual-machines/welcome-virtual-machines).
* Verifique se há erros nos [fluxos de trabalho](../automation-runbook-execution.md#job-statuses). No portal, acesse a conta de Automação e selecione **Trabalhos**, em **Automação de Processos**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Cenário: meu runbook personalizado falha ao iniciar ou parar minhas VMs

### <a name="issue"></a>Problema

Você criou um runbook personalizado ou baixou um da Galeria do PowerShell e ele não está funcionando corretamente.

### <a name="cause"></a>Causa

Pode haver muitas causas para a falha. Acesse a conta de Automação no portal do Azure e selecione **Trabalhos**, em **Automação de Processos**. Na página **Trabalhos**, procure por trabalhos do seu runbook para exibir as falhas de trabalho.

### <a name="resolution"></a>Resolução

É recomendável que você:

* Use o recurso [Iniciar/Parar VMs fora do horário comercial](../automation-solution-vm-management.md) para iniciar e parar VMs na Automação do Azure. 
* Lembre-se de que a Microsoft não é compatível com runbooks personalizados. Você pode encontrar uma resolução para seu runbook personalizado no [Solucionar problemas de runbook](runbooks.md). Verifique se há erros nos [fluxos de trabalho](../automation-runbook-execution.md#job-statuses). 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Cenário: as VMs não iniciam ou param na sequência correta

### <a name="issue"></a>Problema

As VMs que você habilitou para o recurso não iniciam ou param na sequência correta.

### <a name="cause"></a>Causa

Esse problema é causado pela marcação incorreta nas VMs.

### <a name="resolution"></a>Resolução

Siga estas etapas para verificar se o recurso está habilitado corretamente:

1. Verifique se todas as VMs que devem ser iniciadas ou paradas têm uma marca `sequencestart` ou `sequencestop`, dependendo da situação. Essas marcas precisam ter como valor um número inteiro positivo. As VMs são processadas em ordem crescente com base nesse valor.
1. Verifique se os grupos de recursos das VMs que devem ser iniciadas ou paradas estão nas variáveis `External_Start_ResourceGroupNames` ou `External_Stop_ResourceGroupNames`, dependendo da situação.
1. Teste suas alterações executando o runbook **SequencedStartStop_Parent** com o parâmetro `WHATIF` definido como True para visualizar as alterações.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Cenário: Trabalho de Iniciar/Parar VMs fora do horário comercial falha com erro 403 forbidden

### <a name="issue"></a>Problema

Você encontra trabalhos que falharam com um erro `403 forbidden` em runbooks do recurso Iniciar/Parar VMs fora do horário comercial.

### <a name="cause"></a>Causa

Esse problema pode ser causado por uma conta Executar como configurada incorretamente ou expirada. Ele também pode ocorrer devido a permissões inadequadas para os recursos da VM pela conta Executar como.

### <a name="resolution"></a>Resolução

Para verificar se sua conta Executar como está configurada corretamente, acesse sua conta de Automação no portal do Azure e selecione **Contas Executar como** em **Configurações da Conta**. Se uma conta Executar como estiver configurada incorretamente ou expirada, o status mostrará a condição.

Se a conta Executar como estiver configurada incorretamente, exclua a conta Executar como e crie novamente. Para obter mais informações, consulte [contas Executar como da automação do Azure](../automation-security-overview.md#run-as-accounts).

Se o certificado da conta Executar como expirou, siga as etapas de [Renovação do certificado autoassinados](../manage-runas-account.md#cert-renewal) para renová-lo.

Se estiverem faltando permissões de acesso, confira [Início Rápido: exibir funções atribuídas a um usuário usando o portal do Azure](../../role-based-access-control/check-access.md). Você deve fornecer a ID do aplicativo da entidade de serviço usada pela conta Executar como. Você pode recuperar esse valor acessando sua conta de Automação no portal do Azure. Selecione **Conta Executar como** em **Configurações da Conta** e escolha a conta Executar como apropriada.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Cenário: Meu problema não está nesta lista

### <a name="issue"></a>Problema

Ao usar o recurso Iniciar/Parar VMs fora do horário comercial, você pode enfrentar um problema ou um resultado inesperado não abordado nesta página.

### <a name="cause"></a>Causa

Muitas vezes os erros podem ser causados ao usar uma versão antiga e desatualizada do recurso.

> [!NOTE]
> O recurso Iniciar/Parar VMs fora do horário comercial foi testado com os módulos do Azure que são importados para sua conta de Automação quando você implanta o recurso em VMs. No momento, o recurso não funciona com versões mais recentes do módulo do Azure. Essa restrição afeta apenas a conta de Automação que você usa para executar Iniciar/Parar VMs fora do horário comercial. Você ainda pode usar versões mais recentes do módulo do Azure em suas outras contas de Automação, conforme descrito em [Atualizar módulos do Azure PowerShell](../automation-update-azure-modules.md).

### <a name="resolution"></a>Resolução

Para resolver vários erros, remova e [atualize Iniciar/Parar VMs fora do horário comercial](../automation-solution-vm-management.md#update-the-feature). Verifique também se há erros nos [fluxos de trabalho](../automation-runbook-execution.md#job-statuses). 

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar seu problema aqui ou não conseguir resolvê-lo, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure nos [Fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Suporte do Azure conecta a Comunidade do Azure a respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Acesse o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**.