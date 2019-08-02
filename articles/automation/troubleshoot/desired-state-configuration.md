---
title: Solução de problemas com a configuração de estado desejado do DSL (Azure Automation Desired State Configuration)
description: Este artigo fornece informações sobre solução de problemas de configuração de estado desejado (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 53fef426c927c690a3b697055f467f6cd35c532c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477517"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Solucionar problemas de configuração de estado desejado (DSC)

Este artigo fornece informações sobre como solucionar problemas com a DSC (Configuração de Estado Desejado).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Erros comuns ao trabalhar com DSC (Configuração de estado desejado)

### <a name="unsupported-characters"></a>Cenário: Uma configuração com caracteres especiais não pode ser excluída do portal

#### <a name="issue"></a>Problema

Ao tentar excluir uma configuração de DSC do portal, você verá o seguinte erro:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Causa

Esse erro é um problema temporário que está planejado para ser resolvido.

#### <a name="resolution"></a>Resolução

* Use o Cmdlet de Az "Remove-AzAutomationDscConfiguration" para excluir a configuração.
* A documentação para este cmdlet ainda não foram atualizada.  Até lá, consulte a documentação para o módulo AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Cenário: Falha ao registrar o agente do Dsc

#### <a name="issue"></a>Problema

Ao tentar executar `Set-DscLocalConfigurationManager` ou outro cmdlet de DSC, você receberá o erro:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

#### <a name="cause"></a>Causa

Esse erro normalmente é causado por um firewall, o computador que está sendo protegido por um servidor proxy ou outros erros de rede.

#### <a name="resolution"></a>Resolução

Verifique se que seu computador tem acesso aos pontos de extremidade apropriados para o DSC de automação do Azure e tente novamente. Para obter uma lista de portas e endereços necessários, consulte [planejamento de rede](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>Cenário: O nó está com o status Falha com um erro "Não encontrado"

#### <a name="issue"></a>Problema

O nó tem um relatório com o status **Falha** e contendo o erro:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Causa

Esse erro normalmente ocorre quando o nó é atribuído a um nome de configuração (por exemplo, ABC) em vez de um nome de configuração de nó (por exemplo, ABC.WebServer).

#### <a name="resolution"></a>Resolução

* Certifique-se de que você está atribuindo o nó com o "nome de configuração de nó" e não o "nome de configuração".
* Você pode atribuir uma configuração de nó para um nó usando o Portal do Azure ou com um cmdlet do PowerShell.

  * Para atribuir uma configuração de nó a um nó usando o portal do Azure, abra o **nós DSC** página, em seguida, selecione um nó e clique em **atribuir configuração de nó** botão.  
  * Para atribuir uma configuração de nó a um nó usando o cmdlet do PowerShell, use **Set-AzureRmAutomationDscNode** cmdlet

### <a name="no-mof-files"></a>Cenário: Nenhuma configuração de nó (arquivos MOF) foi produzida quando uma configuração é compilada

#### <a name="issue"></a>Problema

Seu trabalho de compilação do DSC é suspenso com o erro:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Causa

Quando a expressão após a palavra-chave **Node** na configuração do DSC for avaliada como `$null`, nenhuma configuração de nó será produzida.

#### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:

* Verifique se a expressão ao lado de **nó** palavra-chave na definição de configuração não é avaliada como $null.
* Se você estiver passando ConfigurationData ao compilar a configuração, certifique-se de que esteja passando os valores esperados e que a configuração exige de [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Cenário: O relatório do nó DSC fica preso no estado "Em andamento"

#### <a name="issue"></a>Problema

O agente DSC produz:

```error
No instance found with given property values
```

#### <a name="cause"></a>Causa

Você atualizou sua versão do WMF e tem o WMI corrompido.

#### <a name="resolution"></a>Resolução

Para corrigir o problema, siga as instruções de [problemas conhecidos e limitações do DSC](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) artigo.

### <a name="issue-using-credential"></a>Cenário: Não é possível usar uma credencial em uma configuração DSC

#### <a name="issue"></a>Problema

Seu trabalho de compilação do DSC foi suspenso com o erro:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Causa

Você já usou uma credencial em uma configuração, mas não forneceu adequada **ConfigurationData** para definir **PSDscAllowPlainTextPassword** como true para cada configuração de nó.

#### <a name="resolution"></a>Resolução

* Certifique-se de transmitir as devidas **ConfigurationData** para definir **PSDscAllowPlainTextPassword** como true para cada configuração de nó mencionada na configuração. Para obter mais informações, consulte [Ativos no DSC de Automação do Azure](../automation-dsc-compile.md#assets).

### <a name="failure-processing-extension"></a>Cenário: Integração de extensão de dsc, o erro "Falha de processamento de extensão"

#### <a name="issue"></a>Problema

Quando ocorre de integração usando a extensão de DSC, uma falha que contém o erro:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Causa

Esse erro normalmente ocorre quando o nó é atribuído um nome de configuração de nó que não existe no serviço.

#### <a name="resolution"></a>Resolução

* Certifique-se de que você está atribuindo o nó com um nome de configuração de nó que corresponda exatamente ao nome no serviço.
* Você pode optar por não incluir o nome de configuração de nó, que resultará na integração, o nó, mas não atribuir uma configuração de nó

### <a name="failure-linux-temp-noexec"></a>Cenário: Aplicação de uma configuração no Linux, uma falha ocorre com um erro geral

#### <a name="issue"></a>Problema

Ao aplicar uma configuração no Linux, ocorre uma falha que contém o erro:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Causa

Os clientes identificou que, se o local /tmp é definido como noexec, a versão atual do DSC falhará aplicar as configurações.

#### <a name="resolution"></a>Resolução

* Remova a opção noexec do local /tmp.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
