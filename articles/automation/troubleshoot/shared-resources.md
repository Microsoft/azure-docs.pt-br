---
title: Solucionar problemas com recursos compartilhados de Automação do Azure
description: Este artigo informa como solucionar problemas com os recursos compartilhados de Automação do Azure.
services: automation
ms.subservice: ''
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 1a822166ae4c2bf793e0fa50e93018f499fcc27a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99053612"
---
# <a name="troubleshoot-shared-resource-issues"></a>Solucionar problemas de recursos compartilhados

Este artigo aborda os problemas que podem surgir quando você estiver usando [recursos compartilhados](../automation-intro.md#shared-resources) na Automação do Azure.

## <a name="modules"></a>Módulos

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Cenário: Um módulo está paralisado durante a importação

#### <a name="issue"></a>Problema

Um módulo está paralisado no estado *Importando* quando você importa ou atualiza os módulos na Automação do Azure.

#### <a name="cause"></a>Causa

Como a importação de módulos do PowerShell é um processo complexo e de várias etapas, um módulo pode não ser importado corretamente e pode estar preso em um estado transitório. Para saber mais sobre esse processo, consulte [Importação de um módulo do PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Resolução

Para resolver esse problema, você deve remover o módulo que está preso usando o cmdlet [Remove-AzureRmAutomationModule.](/powershell/module/Az.Automation/Remove-AzAutomationModule) Você pode, em seguida, tente importar novamente o módulo.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Cenário: Os módulos AzureRM estão presos durante a importação após uma tentativa de atualização

#### <a name="issue"></a>Problema

Uma faixa com a seguinte mensagem permanece em sua conta depois de tentar atualizar os módulos do AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Causa

Há um problema conhecido com a atualização dos módulos AzureRM em uma conta de automação. Especificamente, o problema ocorre se os módulos estiverem em um grupo de recursos com um nome numérico começando com 0.

#### <a name="resolution"></a>Resolução

Para atualizar os módulos do AzureRM na sua conta de automação, a conta deve estar em um grupo de recursos com um nome alfanumérico. Os grupos de recursos com nomes numéricos que começam com 0 não podem atualizar os módulos AzureRM no momento.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Cenário: Falha de módulo importar ou cmdlets não pode ser executados após a importação

#### <a name="issue"></a>Problema

Falha ao importar de um módulo ou importa com êxito, mas nenhum cmdlets são extraídos.

#### <a name="cause"></a>Causa

Algumas razões comuns para que um módulo não pode importar com êxito à automação do Azure são:

* A estrutura não corresponde à estrutura em que a Automação precisa.
* O módulo depende de outro módulo que não tenha sido implantado em sua conta de Automação.
* O módulo não tem suas dependências na pasta.
* O cmdlet [New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule) está sendo usado para carregar o módulo, e você não forneceu o caminho de armazenamento completo ou não carregou o módulo usando uma URL acessível publicamente.

#### <a name="resolution"></a>Resolução

Use qualquer uma dessas soluções para corrigir o problema:

* Certifique-se de que o módulo segue o formato a seguir: NomeMódulo.Zip -> NomeMódulo ou Número de Versão -> (NomeMódulo.psm1, NomeMódulo.psd1).
* Abra o arquivo **.psd1** e veja se o módulo tem dependências. Se tiver, carregue esses módulos para a conta de Automação.
* Verifique se quaisquer **.dll** referenciadas estão presentes na pasta do módulo.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Cenário: Update-AzureModule.ps1 é suspenso ao atualizar os módulos

#### <a name="issue"></a>Problema

Ao usar o runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) para atualizar os módulos do Azure, o processo de atualização de módulo é suspenso.

#### <a name="cause"></a>Causa

Para este runbook, a configuração padrão para determinar quantos módulos são atualizados simultaneamente é 10. O processo de atualização é propenso a erros quando muitos módulos estão sendo atualizados ao mesmo tempo.

#### <a name="resolution"></a>Resolução

Não é comum todos os módulos AzureRM ou Az serem necessários na mesma conta de automação. Você só deve importar os módulos específicos de que precisa.

> [!NOTE]
> Evite importar todo o `Az.Automation` ou o módulo `AzureRM.Automation`, que importa todos os módulos contidos.

Se o processo de atualização ficar suspenso, você precisará adicionar o parâmetro `SimultaneousModuleImportJobCount` ao script **Update-AzureModules.ps1** e fornecer um valor inferior ao padrão, que é 10. Caso implemente essa lógica, tente iniciar com um valor de 3 ou 5. O `SimultaneousModuleImportJobCount` é um parâmetro do runbook do sistema **Update-AutomationAzureModulesForAccount** que é usado para atualizar módulos do Azure. Se você fizer esse ajuste, o processo de atualização será executado por mais tempo, mas terá uma chance melhor de concluir. O exemplo a seguir mostra o parâmetro e onde colocá-lo no runbook:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Contas Executar como

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Cenário: Não é possível criar ou atualizar uma conta Executar como

#### <a name="issue"></a>Problema

Ao tentar criar ou atualizar uma conta Executar como, um erro semelhante ao seguinte é exibido:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Causa

Você não tem as permissões necessárias para criar ou atualizar a conta Executar como ou o recurso está bloqueado em um nível de grupo de recursos.

#### <a name="resolution"></a>Resolução

Para criar ou atualizar uma conta Executar como, você deve ter [permissões](../automation-security-overview.md#permissions) apropriadas para os diversos recursos usados pela conta Executar como.

Se o problema for por causa de um bloqueio, verifique se pode ser removido. Em seguida, vá para o recurso bloqueado no portal do Azure, clique com o botão direito do mouse no bloqueio e selecione **Excluir**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Cenário: Você recebe o erro "não foi possível encontrar um ponto de entrada chamado 'GetPerAdapterInfo' in DLL 'iplpapi.dll'" ao executar um runbook

#### <a name="issue"></a>Problema

Quando estiver executando um runbook, você receberá a seguinte exceção:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Causa

Esse erro é provavelmente causado por uma [conta Executar como ](../automation-security-overview.md)configurada incorretamente.

#### <a name="resolution"></a>Resolução

Verifique se a conta Executar como está configurada corretamente. Em seguida, verifique se você tem o código adequado em seu runbook para autenticar com o Azure. O exemplo a seguir mostra um trecho de código para autenticar no Azure em um runbook usando uma conta Executar como.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Próximas etapas

Se este artigo não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure nos [Fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport). Esta é a conta oficial do Microsoft Azure para conectar-se aos recursos certos de respostas, suporte e especialistas da comunidade do Azure.
* Registrar um incidente de suporte do Azure. Acesse o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**.