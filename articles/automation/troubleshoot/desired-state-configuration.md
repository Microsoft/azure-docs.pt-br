---
title: Solucionar problemas de State Configuration da Automação do Azure
description: Este artigo informa como solucionar e resolver problemas de State Configuration da Automação do Azure.
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.openlocfilehash: e6caf3fed708e89b55a88719ca5358f6174c2ac8
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896521"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Solucionar problemas de State Configuration da Automação do Azure

Este artigo fornece informações sobre como solucionar problemas que surgem enquanto você compila ou implanta configurações na State Configuration da Automação do Azure. Para obter informações gerais sobre o recurso de State Configuration, confira [Visão geral da State Configuration da Automação do Azure](../automation-dsc-overview.md).

## <a name="diagnose-an-issue"></a>Diagnosticar um problema

Quando você recebe um erro de compilação ou implantação na configuração, aqui estão algumas etapas para ajudá-lo a diagnosticar o problema.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Assegure a compilação bem-sucedida da configuração no computador local

A State Configuration da Automação do Azure é baseada na DSC (Desired State Configuration) do PowerShell. Você pode encontrar a documentação para a linguagem e a sintaxe da DSC nos [Documentos da DSC do PowerShell](/powershell/scripting/overview).

Ao compilar uma configuração de DSC em sua máquina local, você pode descobrir e resolver erros comuns, como:

   - Módulos ausentes.
   - Erros de sintaxe.
   - Erros de lógica.

### <a name="2-view-dsc-logs-on-your-node"></a>2. Exibir logs da DSC em seu nó

Se a sua configuração for compilada com êxito, mas falhar quando aplicada a um nó, você poderá encontrar informações detalhadas nos logs da DSC. Para obter informações sobre onde encontrar esses logs, confira [Onde estão os logs de eventos da DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

O módulo [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) pode ajudá-lo a analisar informações detalhadas dos logs da DSC. Se você entrar em contato com o suporte, eles precisarão desses logs para diagnosticar seu problema.

Você pode instalar o módulo `xDscDiagnostics` em sua máquina local seguindo as instruções em [Instalar o módulo da versão estável](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Para instalar o módulo `xDscDiagnostics` na sua máquina do Azure, use [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand). Você também pode usar a opção **Executar comando** no portal do Azure, seguindo as etapas em [Executar scripts do PowerShell na VM do Windows com Executar Comando](../../virtual-machines/windows/run-command.md).

Para obter informações sobre o uso de **xDscDiagnostics**, confira [Usar xDscDiagnostics para analisar logs da DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Confira também [Cmdlets xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Verifique se os nós e o workspace da Automação têm os módulos necessários

A DSC depende dos módulos instalados no nó. Ao usar a State Configuration da Automação do Azure, importe todos os módulos necessários para sua conta de automação seguindo as etapas em [Importar módulos](../shared-resources/modules.md#import-modules). As configurações também podem depender de versões específicas de módulos. Para obter mais informações, confira [Solucionar problemas de módulos](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Cenário: Uma configuração com caracteres especiais não pode ser excluída do portal

### <a name="issue"></a>Problema

Ao tentar excluir uma configuração da DSC do portal, você vê o seguinte erro:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Causa

Esse erro é um problema temporário com uma solução já planejada.

### <a name="resolution"></a>Resolução

Use o cmdlet [Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration) para excluir a configuração.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Cenário: Falha ao registrar o agente DSC

### <a name="issue"></a>Problema

Quando [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) ou outro cmdlet DSC, você recebe o erro:

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

### <a name="cause"></a>Causa

Esse erro normalmente é causado por um firewall, porque computador está atrás de um servidor proxy ou por outros erros de rede.

### <a name="resolution"></a>Resolução

Verifique se o seu computador tem acesso aos pontos de extremidade apropriados para a DSC e tente novamente. Para obter uma lista de portas e endereços, confira [Planejamento de rede](../automation-dsc-overview.md#network-planning).

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Cenário: Relatórios de status retornam o código de resposta Não autorizado

### <a name="issue"></a>Problema

Ao registrar um nó com a State Configuration da Automação do Azure, você recebe uma das seguintes mensagens de erro:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Causa

Esse problema é causado por um certificado insatisfatório ou expirado. Confira [Registrar um nó novamente](../automation-dsc-onboarding.md#re-register-a-node).

Esse problema também pode ser causado por uma configuração de proxy que não permite o acesso a **_. Azure-Automation.net_*. Para obter mais informações, confira [Configuração de redes privadas](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Resolução

Use as etapas a seguir para registrar novamente o nó da DSC com falha.

#### <a name="step-1-unregister-the-node"></a>Etapa 1: cancelar o registro do servidor

1. Na portal do Azure, acesse **Início** > **Contas de Automação** > (sua conta de Automação) > **State configuration (DSC)** .
1. Selecione **Nós** e o nó com problemas.
1. Selecione **Cancelar registro** para cancelar o registro do nó.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Etapa 2: desinstalar a extensão de DSC do nó

1. Na portal do Azure, vá para **Início** > **Máquina Virtual** > (nó com falha) > **Extensões**.
1. Selecione **Microsoft.PowerShell.DSC**, a extensão de DSC do PowerShell.
1. Selecione **Desinstalar** para desinstalar a extensão.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Etapa 3: remover todos os certificados inválidos ou expirados do nó

No nó com falha, em um prompt do PowerShell com privilégios elevados, execute estes comandos:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

#### <a name="step-4-reregister-the-failing-node"></a>Etapa 4: registrar novamente o nó com falha

1. Na portal do Azure, acesse **Início** > **Contas de Automação** > (sua conta de Automação) > **State configuration (DSC)** .
1. Selecione **Nós**.
1. Selecione **Adicionar**.
1. Selecione o nó com falha.
1. Selecione **Conectar** e as opções desejadas.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Cenário: O nó está com o status Falha com um erro "Não encontrado"

### <a name="issue"></a>Problema

O nó tem um relatório com o status Falha que contém o seguinte erro:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Causa

Esse erro normalmente ocorre quando o nó é atribuído a um nome de configuração (por exemplo, **ABC**) em vez de um nome de configuração de nó (arquio MOF), como **ABC.WebServer**.

### <a name="resolution"></a>Resolução

* Verifique se você está atribuindo o nó com o nome da configuração do nó, e não o nome da configuração.
* Você pode atribuir uma configuração para um nó usando o Portal do Azure ou com um cmdlet do PowerShell.

  * Na portal do Azure, acesse **Início** > **Contas de Automação** > (sua conta de Automação) > **State configuration (DSC)** . Em seguida, selecione um nó e **Atribuir configuração de nó**.
  * Use o cmdlet [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode).

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Cenário: Nenhuma configuração de nó (arquivos MOF) foi produzida quando uma configuração foi compilada

### <a name="issue"></a>Problema

Seu trabalho de compilação do DSC é suspenso com o erro:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Causa

Quando a expressão após a palavra-chave `Node` na configuração da DSC for avaliada como `$null`, nenhuma configuração de nó será produzida.

### <a name="resolution"></a>Resolução

Use uma das seguintes soluções para corrigir o problema:

* Verifique se a expressão ao lado da palavra-chave `Node` na definição de configuração não está sendo avaliada como Nula.
* Se você estiver passando [ConfigurationData](../automation-dsc-compile.md) ao compilar a configuração, verifique se estão passando os valores esperados pela configuração dos dados de configuração.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Cenário: O relatório do nó da DSC fica preso no estado Em Andamento

### <a name="issue"></a>Problema

O agente DSC produz:

```error
No instance found with given property values
```

### <a name="cause"></a>Causa

Você atualizou sua versão do WMF (Windows Management Framework) e danificou a WMI (Instrumentação de Gerenciamento do Windows).

### <a name="resolution"></a>Resolução

Siga as instruções no artigo [Problemas e limitações conhecidos da DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Cenário: Não é possível usar uma credencial em uma configuração DSC

### <a name="issue"></a>Problema

Seu trabalho de compilação da DSC foi suspenso com o erro:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Causa

Você usou uma credencial em uma configuração, mas não forneceu os `ConfigurationData` adequados para definir `PSDscAllowPlainTextPassword` como true em cada configuração de nó.

### <a name="resolution"></a>Resolução

Transmita os `ConfigurationData` adequados para definir `PSDscAllowPlainTextPassword` como true em cada configuração de nó mencionada na configuração. Confira [Compilação das configurações DSC na State Configuration da Automação do Azure](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Cenário: Erro de "Falha ao processar extensão" ao habilitar um computador em uma extensão de DSC

### <a name="issue"></a>Problema

Quando você habilita um computador usando uma extensão de DSC, ocorre uma falha que contém o erro:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Causa

Esse erro normalmente ocorre quando o nó recebe um nome de configuração de nó que não existe no serviço.

### <a name="resolution"></a>Resolução

* Verifique se está atribuindo o nó com um nome que corresponda exatamente ao nome no serviço.
* Você pode optar por não incluir o nome da configuração do nó, o que resulta na habilitação do nó, mas não na atribuição de uma configuração de nó.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Cenário: Erro "Ocorreu um ou mais erros" ao registrar um nó usando o PowerShell

### <a name="issue"></a>Problema

Ao registrar um nó usando [Register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) ou [Register-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode), você receberá o seguinte erro:

```error
One or more errors occurred.
```

### <a name="cause"></a>Causa

Esse erro ocorre quando você tenta registrar um nó em uma assinatura separada da que é usada pela conta de Automação.

### <a name="resolution"></a>Resolução

Trate o nó de assinatura cruzada como se ele estivesse definido para uma nuvem separada ou localmente. Registre o nó usando uma destas opções para habilitar computadores:

* Windows: [Máquinas físicas/virtuais locais do Windows ou em uma nuvem diferente do Azure/AWS](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines).
* Linux: [Máquinas físicas/virtuais locais do Linux ou em uma nuvem diferente do Azure](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Cenário: Mensagem de erro "Falha no provisionamento"

### <a name="issue"></a>Problema

Ao registrar um nó, você vê o erro:

```error
Provisioning has failed
```

### <a name="cause"></a>Causa

Essa mensagem ocorre quando há um problema com a conectividade entre o nó e o Azure.

### <a name="resolution"></a>Resolução

Determine se o nó está em uma VPN (rede virtual privada) ou se há outros problemas para se conectar ao Azure. Confira [Solucionar problemas de implantação de recurso](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Cenário: Falha com um erro geral ao aplicar uma configuração no Linux

### <a name="issue"></a>Problema

Quando você aplica uma configuração no Linux, ocorre uma falha que contém o erro:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Causa

Se o local **/tmp** for definido como `noexec`, a versão atual da DSC falhará ao aplicar as configurações.

### <a name="resolution"></a>Resolução

Remova a opção `noexec` do local **/tmp**.

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Cenário: Nomes de configuração de nó que se sobrepõem podem resultar em uma versão incorreta

### <a name="issue"></a>Problema

Quando você usa um único script de configuração para gerar várias configurações de nó, e alguns nomes de configuração de nó são subconjuntos de outros nomes, o serviço de compilação pode acabar atribuindo a configuração errada. Esse problema ocorre apenas quando você usa um único script para gerar configurações com dados de configuração por nó e somente quando a sobreposição de nome ocorre no início da cadeia de caracteres. Um exemplo é um script de configuração único usado para gerar configurações com base nos dados de nó passados como uma tabela de hash usando cmdlets, e os dados de nó incluem servidores chamados **server** e **1server**.

### <a name="cause"></a>Causa

Esse é um problema conhecido do serviço de compilação.

### <a name="resolution"></a>Resolução

A melhor solução é compilar localmente ou em um pipeline de CI/CD e carregar os arquivos MOF de configuração de nó diretamente no serviço. Se a compilação no serviço for obrigatória, a próxima solução alternativa será dividir os trabalhos de compilação para que não haja sobreposição de nomes.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Cenário: Erro de tempo limite do gateway no carregamento da configuração DSC

#### <a name="issue"></a>Problema

Você recebe um erro `GatewayTimeout` ao carregar uma configuração DSC. 

### <a name="cause"></a>Causa

Configurações DSC que levam muito tempo para serem compiladas podem causar esse erro.

### <a name="resolution"></a>Resolução

Você pode fazer com que suas configurações DSC sejam analisadas mais rapidamente, incluindo de forma explícita o parâmetro `ModuleName` para quaisquer chamadas de [Import-DSCResource](/powershell/scripting/dsc/configurations/import-dscresource).

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar seu problema aqui ou não conseguir resolvê-lo, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio dos [Fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O suporte do Azure conecta a Comunidade do Azure a respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**.
