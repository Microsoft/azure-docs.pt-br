---
title: Solucionar problemas do Hybrid Runbook Worker da Automação do Azure
description: Este artigo informa como solucionar e resolver problemas que surgem com os Hybrid Runbook Workers da Automação do Azure.
services: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.openlocfilehash: 15a18cbfc3a80bbfea0b92e5b616104dc0f593af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580992"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Solucionar problemas do Hybrid Runbook Worker

Este artigo fornece informações sobre como solucionar problemas com os Hybrid Runbook Workers da Automação do Azure. Para obter informações gerais, consulte [Visão geral do Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md).

## <a name="general"></a>Geral

O operador de Runbook híbrido depende de um agente para se comunicar com sua conta de Automação do Azure para registrar o trabalho, receber trabalhos de runbook e relatar status. Para o Windows, esse agente é o agente do Log Analytics para Windows. Para o Linux, é o agente do Log Analytics para Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Cenário: Falha na execução de Runbook

#### <a name="issue"></a>Problema

A execução do runbook falha e você recebe a seguinte mensagem de erro:

`The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times.`

Seu runbook será suspenso logo depois de tentar executar três vezes. Existem condições que podem interromper a conclusão do runbook. A mensagem de erro relacionada pode não incluir nenhuma informação adicional.

#### <a name="cause"></a>Causa

Estas são as possíveis causas:

* Os runbooks não podem autenticar com recursos locais.
* O Hybrid Worker está usando um proxy ou firewall.
* O computador configurado para executar o Hybrid Runbook Worker não atende aos requisitos mínimos de hardware.

#### <a name="resolution"></a>Resolução

Verifique se o computador tem acesso de saída para **\* . Azure-Automation.net** na porta 443.

Os computadores que executam o Hybrid Runbook Worker devem atender aos requisitos mínimos de hardware antes de o trabalho ser configurado para hospedar esse recurso. Os Runbooks e o processo em segundo plano que eles usam podem fazer com que o sistema se sobrecarregue e cause atrasos ou tempos limite de trabalho de runbook.

Confirme se o computador que executa o recurso de Hybrid Runbook Worker atende aos requisitos mínimos de hardware. Se isso acontecer, monitore o uso de CPU e memória para determinar qualquer correlação entre o desempenho de processos do Hybrid Runbook Worker e o Windows. Qualquer pressão de memória ou CPU pode indicar a necessidade de atualizar os recursos. Você também pode selecionar um recurso de computação diferente que dê suporte aos requisitos mínimos e à escala quando as demandas de carga de trabalho indicarem que um aumento é necessário.

Verifique o log de eventos do **Microsoft SMA** para ver um evento correspondente com a descrição `Win32 Process Exited with code [4294967295]`. A causa desse erro é que você ainda não configurou a autenticação em seus runbooks ou especificou as credenciais Executar como para o grupo do Hybrid Runbook Worker. Examine as permissões de runbook em [Executando runbooks em um Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) para confirmar que você configurou corretamente a autenticação para seus runbooks.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Cenário: Evento 15011 no Hybrid Runbook Worker

#### <a name="issue"></a>Problema

O Hybrid Runbook Worker recebe o evento 15011, indicando que um resultado da consulta não é válido. O erro a seguir é exibido quando o trabalho tenta abrir uma conexão com o [servidor SignalR](/aspnet/core/signalr/introduction).

`[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
`

#### <a name="cause"></a>Causa

O Hybrid Runbook Worker não foi configurado corretamente para a implantação de recurso automatizado, por exemplo, para Gerenciamento de Atualizações. A implantação contém uma parte que conecta a VM ao workspace do Log Analytics. O script do PowerShell procura o workspace na assinatura com o nome fornecido. Nesse caso, o workspace do Log Analytics está em uma assinatura diferente. O script não pode localizar o workspace e tenta criar um, mas o nome já está em uso. Como resultado, a implantação falha.

#### <a name="resolution"></a>Resolução

Você tem duas opções para resolver esse problema:

* Modifique o script do PowerShell para procurar o workspace do Log Analytics em outra assinatura. Essa é uma boa resolução a ser usada se você planeja implantar vários computadores do Hybrid Runbook Worker no futuro.

* Configure manualmente o computador de trabalho para ser executado em uma área restrita do Orchestrator. Em seguida, execute um runbook criado na conta de Automação do Azure no trabalho para testar a funcionalidade.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Cenário: VMs do Windows Azure descartadas automaticamente de um grupo do Hybrid Worker

#### <a name="issue"></a>Problema

Você não pode ver o Hybrid Runbook Worker ou as VMs quando o computador de trabalho foi desativado por muito tempo.

#### <a name="cause"></a>Causa

O computador do Hybrid Runbook Worker não tem efetuado ping na Automação do Azure por mais de 30 dias. Como resultado, a Automação limpou o grupo de Hybrid Runbook Worker ou o grupo de trabalho do sistema. 

#### <a name="resolution"></a>Resolução

Inicie o computador de trabalho e, em seguida, registre-o novamente com a Automação do Azure. Para obter instruções sobre como instalar o ambiente de runbook e conectar-se à Automação do Azure, consulte [Implantar um Hybrid Runbook Worker do Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Cenário: Nenhum certificado foi encontrado no repositório de certificados no Hybrid Runbook Worker

#### <a name="issue"></a>Problema

Um runbook em execução em um Hybrid Runbook Worker falha com a seguinte mensagem de erro:

`Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000`  
`At line:3 char:1`  
`+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...`  
`+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~`  
`    + CategoryInfo          : CloseError: (:) [Connect-AzAccount],ArgumentException`  
`    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand`

#### <a name="cause"></a>Causa

Esse erro ocorre quando você tenta usar uma [conta Executar como](../automation-security-overview.md#run-as-accounts) em um runbook que é executado em um Hybrid Runbook Worker, em que o certificado da conta Executar como não está presente. Os Hybrid Runbook Workers não têm o ativo de certificado localmente por padrão. A conta Executar como requer que este ativo funcione corretamente.

#### <a name="resolution"></a>Resolução

Se seu Hybrid Runbook Worker for uma VM do Azure, você poderá usar a [autenticação de runbook com identidades gerenciadas](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) em vez disso. Esse cenário simplifica a autenticação, permitindo que você se autentique nos recursos do Azure usando a identidade gerenciada da VM do Azure em vez da conta Executar como. Quando o Hybrid Runbook Worker é um computador local, você precisará instalar o certificado da conta Executar como no computador. Para saber como instalar o certificado, consulte as etapas para executar o runbook do PowerShell **Export-RunAsCertificateToHybridWorker** em [Executar runbooks em um Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Cenário: Erro 403 durante o registro de um Hybrid Runbook Worker

#### <a name="issue"></a>Problema

A fase de registro inicial do trabalho falha e você recebe o seguinte erro (403):

`Forbidden: You don't have permission to access / on this server.`

#### <a name="cause"></a>Causa

Os problemas a seguir são as possíveis causas:

* Há uma ID de workspace mal digitada ou uma chave de workspace (primária) nas configurações do agente. 
* O Hybrid Runbook Worker não pode baixar a configuração, o que causa um erro de vinculação de conta. Quando o Azure habilita recursos em computadores, ele dá suporte a apenas determinadas regiões para vincular um workspace do Log Analytics e uma conta de Automação. Também é possível que uma data ou hora incorreta esteja definida no computador. Se a diferença horária for +/- 15 minutos a partir da hora atual, a implantação do recurso falhará.

#### <a name="resolution"></a>Resolução

##### <a name="mistyped-workspace-id-or-key"></a>ID ou chave do workspace digitada incorretamente
Para verificar se a ID do workspace do agente ou a chave do workspace foi digitada incorretamente, consulte [Adicionar ou remover um workspace – agente do Windows](../../azure-monitor/agents/agent-manage.md#windows-agent) para o agente do Windows ou [Adicionar ou remover um espaço de trabalho – agente do Linux](../../azure-monitor/agents/agent-manage.md#linux-agent) para o agente do Linux. Certifique-se de selecionar a cadeia de caracteres completa do portal do Azure, em seguida, copie e cole-a com cuidado.

##### <a name="configuration-not-downloaded"></a>Configuração não baixada

Seu espaço de trabalho do Log Analytics e a conta de Automação devem estar em uma região vinculada. Para obter uma lista de regiões com suporte, consulte [Mapeamentos do workspace do Log Analytics e da Automação do Azure](../how-to/region-mappings.md).

Você também poderá precisar atualizar a data ou o fuso horário do seu computador. Se você selecionar um intervalo de tempo personalizado, verifique se ele está em UTC, o que pode ser diferente do seu fuso horário local.

### <a name="scenario-set-azstorageblobcontent-fails-on-a-hybrid-runbook-worker"></a><a name="set-azstorageblobcontent-execution-fails"></a>Cenário: o Set-AzStorageBlobContent falha em um Hybrid Runbook Worker 

#### <a name="issue"></a>Problema

O runbook falha quando tenta executar `Set-AzStorageBlobContent` , e você recebe a seguinte mensagem de erro:

`Set-AzStorageBlobContent : Failed to open file xxxxxxxxxxxxxxxx: Illegal characters in path`

#### <a name="cause"></a>Causa

 Esse erro é causado pelo comportamento de nome de arquivo longo de chamadas às `[System.IO.Path]::GetFullPath()` quais o adiciona caminhos UNC.

#### <a name="resolution"></a>Resolução

Como alternativa, você pode criar um arquivo de configuração chamado `OrchestratorSandbox.exe.config` com o seguinte conteúdo:

```azurecli
<configuration>
  <runtime>
    <AppContextSwitchOverrides value="Switch.System.IO.UseLegacyPathHandling=false" />
  </runtime>
</configuration>
```

Coloque esse arquivo na mesma pasta que o arquivo executável `OrchestratorSandbox.exe` . Por exemplo,

`%ProgramFiles%\Microsoft Monitoring Agent\Agent\AzureAutomation\7.3.702.0\HybridAgent`

>[!Note]
> Se você atualizar o agente, esse arquivo de configuração será excluído e precisará ser recriado.

## <a name="linux"></a>Linux

O Hybrid Runbook Worker do Linux depende do [Agente do Log Analytics para Linux](../../azure-monitor/agents/log-analytics-agent.md) para se comunicar com sua conta de Automação para registrar o trabalho, receber trabalhos de runbook e relatar o status. Se o registro do trabalhador falhar, aqui estão algumas das possíveis causas do erro.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Cenário: O Linux Hybrid Runbook Worker recebe uma solicitação de senha ao assinar um runbook

#### <a name="issue"></a>Problema

A execução do comando `sudo` para um Hybrid Runbook Worker do Linux recupera uma solicitação inesperada para uma senha.

#### <a name="cause"></a>Causa

A conta **nxautomationuser** para o agente do Log Analytics para Linux não está configurada corretamente no arquivo **sudoers**. O Hybrid Runbook Worker precisa da configuração apropriada de permissões de conta e outros dados para que ele possa assinar runbooks no Runbook Worker do Linux.

#### <a name="resolution"></a>Resolução

* Verifique se o Hybrid Runbook Worker tem o executável GnuPG (GPG) no computador.

* Verifique a configuração da conta do **nxautomationuser** no arquivo **sudoers**. Consulte [Executar runbooks em um Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Cenário: O agente do Log Analytics para Linux não está em execução

#### <a name="issue"></a>Problema

O agente do Log Analytics para Linux não está em execução.

#### <a name="cause"></a>Causa

Se o agente não estiver em execução, ele impedirá que o Hybrid Runbook Worker do Linux se comunique com a Automação do Azure. O agente pode não estar em execução por vários motivos.

#### <a name="resolution"></a>Resolução

 Verifique se o agente está em execução, digitando o comando `ps -ef | grep python`. Você deve ver saídas semelhantes às seguintes. Os processos do Python com a conta de usuário do **nxautomation**. Se o recurso de Automação do Azure não estiver habilitado, nenhum dos processos a seguir será executado.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

A lista a seguir mostra os processos que são iniciados para um Hybrid Runbook Worker do Linux. Eles estão todos localizados no diretório /var/opt/microsoft/omsagent/state/automationworker/.

* **oms.conf**: O processo do gerenciador de trabalho. Ele é iniciado diretamente do DSC.
* **worker.conf**: O processo de trabalho híbrido registrado automaticamente. É iniciado pelo gerenciador do trabalhador. Esse processo é usado pelo Gerenciamento de Atualizações e é transparente para o usuário. Esse processo não estará presente se o Gerenciamento de Atualizações não estiver habilitado no computador.
* **diy/worker.conf**: O processo de trabalho híbrido do DIY. O processo do Hybrid Worker DIY é usado para executar runbooks de usuário no Hybrid Runbook Worker. Ele é diferente apenas do processo de trabalho híbrido registrado automaticamente nos detalhes da chave que usa uma configuração diferente. Esse processo não estará presente se a Automação do Azure estiver desabilitada e o Hybrid Worker DIY do Linux não estiver registrado.

Se o agente não estiver em execução, execute o comando a seguir para iniciar o serviço: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Cenário: A classe especificada não existe

Se você vir a mensagem de erro `The specified class does not exist..` em **/var/opt/Microsoft/omsconfig/omsconfig.log**, o agente do Log Analytics para Linux precisará ser atualizado. Execute o comando a seguir para reinstalar o agente.

```Bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

O Hybrid Runbook Worker do Windows depende do [Agente do Log Analytics para Windows](../../azure-monitor/agents/log-analytics-agent.md) para se comunicar com sua conta de Automação para registrar o trabalho, receber trabalhos de runbook e relatar o status. Se o registro do trabalho falhar, esta seção incluirá alguns motivos possíveis.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Cenário: O agente do Log Analytics para Windows não está em execução

#### <a name="issue"></a>Problema

O serviço `healthservice` não está em execução no computador do Hybrid Runbook Worker.

#### <a name="cause"></a>Causa

Se o Log Analytics para o serviço do Windows não estiver em execução, o Hybrid Runbook Worker não poderá se comunicar com a Automação do Azure.

#### <a name="resolution"></a>Resolução

Verifique se o agente está em execução digitando o seguinte comando no PowerShell: `Get-Service healthservice`. Se o serviço for interrompido, digite o seguinte comando no PowerShell para iniciar o serviço: `Start-Service healthservice`.

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Cenário: Evento 4502 no log do Operations Manager

#### <a name="issue"></a>Problema

No log de eventos **Application and Services Logs\Operations Manager**, você vê o evento 4502 e uma mensagem de evento que contém `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` com a seguinte descrição:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Causa

Esse problema poderá ocorrer se seu proxy ou firewall de rede estiver bloqueando a comunicação com o Microsoft Azure. Verifique se o computador tem acesso de saída para **\* . Azure-Automation.net** na porta 443.

#### <a name="resolution"></a>Resolução

Os logs são armazenados localmente em cada hybrid worker, em C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Você pode verificar se há algum evento de aviso ou de erro nos logs de eventos **Application and Services Logs\Microsoft-SMA\Operations** e **Application and Services logs Manager**. Esses logs indicam uma conectividade ou outro tipo de problema que afeta a habilitação da função para a Automação do Azure ou um problema encontrado em operações normais. Para obter ajuda adicional sobre como solucionar problemas com o agente do Log Analytics, consulte [Solucionar problemas com o agente Windows do Log Analytics](../../azure-monitor/agents/agent-windows-troubleshoot.md).

Os Hybrid Workers enviam [saída de runbook e mensagens](../automation-runbook-output-and-messages.md) para a Automação do Azure da mesma forma que os trabalhos de runbook em execução na nuvem enviam a saída e as mensagens. Você pode habilitar os fluxos Detalhado e de Progresso da mesma forma como faz para os runbooks.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>Cenário: Orchestrator.Sandbox.exe não pode se conectar ao Microsoft 365 por meio do proxy

#### <a name="issue"></a>Problema

Um script em execução em um Hybrid Runbook Worker do Windows não pode se conectar conforme o esperado para Microsoft 365 em uma área restrita do Orchestrator. O script está usando [Connect-MsolService](/powershell/module/msonline/connect-msolservice) para se conectar. 

Se você ajustar **Orchestrator.Sandbox.exe.config** para definir o proxy e a lista de bypass, a área restrita ainda não se conectará corretamente. Um arquivo **Powershell_ise.exe.config** com as mesmas configurações de lista de proxies e de bypass parece funcionar conforme o esperado. Os logs do Service Management Automation (SMA) e os logs do PowerShell não fornecem nenhuma informação sobre o proxy.

#### <a name="cause"></a>Causa

A conexão com Serviços de Federação do Active Directory (AD FS) no servidor não pode ignorar o proxy. Lembre-se de que uma área restrita do PowerShell é executada como o usuário registrado. No entanto, uma área restrita do Orchestrator é bastante personalizada e pode ignorar as configurações do arquivo **Orchestrator.Sandbox.exe.config**. Ele tem um código especial para lidar com configurações de proxy do computador ou do agente do Log Analytics, mas não para lidar com outras configurações de proxy personalizadas. 

#### <a name="resolution"></a>Resolução

Você pode resolver o problema para a área restrita do Orchestrator migrando seu script para usar os módulos do Azure Active Directory em vez do módulo MSOnline para cmdlets do PowerShell. Para obter mais informações, consulte [Migrar do Orchestrator para a Automação do Azure (versão beta)](../automation-orchestrator-migration.md).

Se você quiser continuar a usar os cmdlets do módulo MSOnline, altere seu script para usar [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command). Especifique valores para os parâmetros `ComputerName` e `Credential`. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Essa alteração de código inicia uma sessão totalmente nova do PowerShell no contexto das credenciais especificadas. Ele deve permitir que o tráfego flua por um servidor proxy que está autenticando o usuário ativo.

>[!NOTE]
>Essa resolução torna desnecessário manipular o arquivo de configuração da área restrita. Mesmo que você tenha sucesso em fazer com que o arquivo de configuração funcione com o script, o arquivo será apagado sempre que o agente do Hybrid Runbook Worker for atualizado.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Cenário: Hybrid Runbook Worker não está relatando

#### <a name="issue"></a>Problema

Seu computador do Hybrid Runbook Worker está em execução, mas você não verá dados de pulsação para a máquina no workspace.

A consulta de exemplo a seguir mostra as máquinas em um workspace e sua última pulsação:

```kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Causa

Esse problema pode ser causado por um cache corrompido no Hybrid Runbook Worker.

#### <a name="resolution"></a>Resolução

Para resolver esse problema, entre no Hybrid Runbook Worker e execute o seguinte script. Esse script interrompe o agente do Log Analytics para Windows, remove seu cache e reinicia o serviço. Essa ação força o Hybrid Runbook Worker baixar novamente sua configuração da automação do Azure.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>Cenário: não é possível adicionar um Hybrid Runbook Worker do Windows

#### <a name="issue"></a>Problema

Você recebe a seguinte mensagem ao tentar adicionar um Hybrid Runbook Worker usando o cmdlet `Add-HybridRunbookWorker`:

`Machine is already registered`

#### <a name="cause"></a>Causa

Esse problema pode ser causado se o computador já estiver registrado com uma conta de Automação diferente ou se você tentar adicionar novamente o Hybrid Runbook Worker após tê-lo removido de um computador.

#### <a name="resolution"></a>Resolução

Para resolver esse problema, remova a seguinte chave do registro, reinicie `HealthService` e tente o cmdlet `Add-HybridRunbookWorker` novamente.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>Cenário: não é possível adicionar um Hybrid Runbook Worker do Linux

#### <a name="issue"></a>Problema

Você recebe a seguinte mensagem ao tentar adicionar um Hybrid Runbook Worker usando o `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` script Python:

`Unable to register, an existing worker was found. Please deregister any existing worker and try again.`

Além disso, tentar cancelar o registro de um Hybrid Runbook Worker usando o `sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` script Python:

`Failed to deregister worker. [response_status=404]`

#### <a name="cause"></a>Causa

Esse problema pode ocorrer se o computador já estiver registrado com uma conta de automação diferente, se o grupo de Hybrid Worker do Azure tiver sido excluído ou se você tentar adicionar novamente o Hybrid Runbook Worker depois de removê-lo de um computador.

#### <a name="resolution"></a>Resolução

Para resolver o problema:

1. Remova o agente `sudo sh onboard_agent.sh --purge` .

1. Execute estes comandos:

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. Re-integrar o agente `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com` .

1. Aguarde até que a pasta `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` seja populada.

1. Experimente o `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` script Python novamente.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar seu problema aqui ou não conseguir resolvê-lo, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure nos [Fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Suporte do Azure conecta a Comunidade do Azure a respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Acesse o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**.
