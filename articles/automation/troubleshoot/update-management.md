---
title: Solucionar erros com o Gerenciamento de Atualizações
description: Aprenda a solucionar problemas com o Gerenciamento de Atualizações
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 23139755af812f99bce8c2c255805eaf9e30b2da
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477069"
---
# <a name="troubleshooting-issues-with-update-management"></a>Resolução de problemas com o Gerenciamento de Atualizações

Este artigo discute soluções para resolver problemas que você pode encontrar ao usar o Gerenciamento de Atualizações.

Não há uma solução de problemas do agente para agente do Hybrid Worker determinar o problema subjacente. Para saber mais sobre a solução de problemas, consulte [solucionar problemas do agente de atualização](update-agent-issues.md). Para todos os outros problemas, consulte as informações detalhadas abaixo sobre possíveis problemas.

## <a name="general"></a>Geral

### <a name="components-enabled-not-working"></a>Cenário: Os componentes da solução 'Gerenciamento de Atualizações' foram habilitados e agora esta máquina virtual está sendo configurada

#### <a name="issue"></a>Problema

Você continua a ver a seguinte mensagem em uma máquina virtual 15 minutos após a integração:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Causa

Esse erro pode ser causado pelos seguintes motivos:

1. A comunicação com a Conta de Automação está sendo bloqueada.
2. A VM que está sendo integrada pode ter vindo de um computador clonado sem ter sido preparada (sysprep) com o Microsoft Monitoring Agent instalado.

#### <a name="resolution"></a>Resolução

1. Visite [Planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning) para saber mais sobre quais endereços e portas devem ter permissão para que Gerenciamento de Atualizações funcione.
2. Se estiver usando uma imagem clonada:
   1. No espaço de trabalho do Log Analytics, remova a VM da pesquisa salva para a configuração de escopo `MicrosoftDefaultScopeConfig-Updates` se ele for exibido. As pesquisas salvas podem ser encontradas em **Geral** no espaço de trabalho.
   2. Execute `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. Execute `Restart-Service HealthService` para reiniciar o `HealthService`. Isso recriará a chave e gerará um novo UUID.
   4. Se isso não funcionar, a imagem de sysprep primeiro e instale o agente MMA após o fato.

### <a name="multi-tenant"></a>Cenário: Você recebe um erro de assinatura vinculado ao criar uma implantação de atualização para computadores em outro locatário do Azure.

#### <a name="issue"></a>Problema

Você recebe o seguinte erro ao tentar criar uma implantação de atualização para computadores em outro locatário do Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Causa

Esse erro ocorre quando você cria uma implantação de atualização que tem máquinas virtuais do Azure em outro locatário incluído em uma implantação de atualização.

#### <a name="resolution"></a>Resolução

Você precisará usar a solução alternativa a seguir para agendá-las. Use o cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) com a opção `-ForUpdate` para criar um agendamento e use o cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passe os computadores no outro locatário para o parâmetro `-NonAzureComputer`. O seguinte exemplo mostra um exemplo de como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>Cenário: As máquinas não são exibidas no portal em gerenciamento de atualizações

#### <a name="issue"></a>Problema

Você pode encontrar os seguintes cenários:

* Mostra sua máquina **não configurado** da exibição de gerenciamento de atualização de uma VM

* As máquinas estão ausentes da exibição de gerenciamento de atualização de sua conta de automação

* Você tiver máquinas que mostram como **não avaliado** sob **conformidade**, mas você verá os dados de pulsação nos logs do Azure Monitor para o Hybrid Runbook Worker, mas não o gerenciamento de atualizações.

#### <a name="cause"></a>Causa

Isso pode ser causado por problemas potenciais de configuração local ou por configuração de escopo configurado incorretamente.

O Hybrid Runbook Worker talvez precise ser registrados novamente e reinstalado.

Você pode ter definido uma cota em seu espaço de trabalho que tenha sido atingidos e parando dados sejam armazenados.

#### <a name="resolution"></a>Resolução

* Certifique-se de que seu computador está relatando ao espaço de trabalho correto. Verifique se sua máquina está relatando ao qual espaço de trabalho. Para obter instruções sobre como verificar isso, consulte [verificar a conectividade do agente para o Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Em seguida, verifique se que esse é o espaço de trabalho que esteja vinculado à sua conta de automação do Azure. Para confirmar isso, navegue até sua conta de automação e clique em **espaço de trabalho vinculado** sob **recursos relacionados**.

* Verifique se os computadores aparecem no seu espaço de trabalho do Log Analytics. Execute a consulta a seguir no seu espaço de trabalho do Log Analytics que esteja vinculado à sua conta de automação. Se você não vir seu computador nos resultados da consulta, seu computador não é pulsação, o que significa que provavelmente há um problema de configuração local. Você pode executar a solução de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux](update-agent-issues-linux.md#troubleshoot-offline) dependendo do sistema operacional, ou você pode [reinstalar o agente](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Se sua máquina aparece nos resultados da consulta, você precisa de muito a configuração do escopo especificada no marcador seguinte.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Verifique se há problemas de configuração de escopo. [A configuração de escopo](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quais máquinas obterem configuradas para a solução. Se seu computador está aparecendo no seu espaço de trabalho, mas não está aparecendo você precisará configurar a configuração de escopo para as máquinas de destino. Para saber como fazer isso, consulte [integrar computadores no espaço de trabalho](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Se as etapas acima não resolverem seu problema, siga as etapas em [implantar um Hybrid Runbook Worker do Windows](../automation-windows-hrw-install.md) reinstalar o Hybrid Worker para Windows ou [implantar um Hybrid Runbook Worker do Linux](../automation-linux-hrw-install.md) para Linux.

* No espaço de trabalho, execute a consulta a seguir. Se você vir o resultado `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` você tem uma cota definida no seu espaço de trabalho que foi atingido e parou de dados sejam salvos. No espaço de trabalho, navegue até **uso e custos estimados** > **gerenciamento de volume de dados** e verificar sua cota ou remover a cota que você tem.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

Se você encontrar problemas ao tentar integrar a solução em uma máquina virtual, verifique o log de eventos **Operations Manager** em **Logs de Aplicativos e Serviços** na máquina local para eventos com o ID de evento **4502** e mensagem de evento contendo **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

A seção a seguir destaca mensagens de erro específicas e uma possível resolução para cada uma delas. Para outros problemas de integração, consulte [como solucionar problemas de integração de soluções](onboarding.md).

### <a name="machine-already-registered"></a>Cenário: O computador já está registrado em outra conta

#### <a name="issue"></a>Problema

Você vê a seguinte mensagem de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Causa

A máquina já está integrada a outro workspace para o Gerenciamento de Atualizações.

#### <a name="resolution"></a>Resolução

Execute a limpeza de artefatos antigos na máquina, [excluindo o grupo de runbooks híbridos](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) e tente novamente.

### <a name="machine-unable-to-communicate"></a>Cenário: O computador não consegue se comunicar com o serviço

#### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Causa

Pode haver um proxy, gateway ou firewall bloqueando a comunicação de rede.

#### <a name="resolution"></a>Resolução

Revise sua rede e garanta que portas e endereços apropriados sejam permitidos. Veja [requisitos de rede](../automation-hybrid-runbook-worker.md#network-planning), para uma lista de portas e endereços que são exigidos pelo Gerenciamento de Atualizações e pelos Trabalhadores de Runbooks Híbridos.

### <a name="unable-to-create-selfsigned-cert"></a>Cenário: Não é possível criar um certificado autoassinado

#### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Causa

O Hybrid Runbook Worker não conseguiu gerar um certificado auto-assinado

#### <a name="resolution"></a>Resolução

Verifique se a conta do sistema tem acesso de leitura à pasta **C:\ProgramData\Microsoft\Crypto\RSA** e tente novamente.

### <a name="failed-to-start"></a>Cenário: Falha ao iniciar em uma implantação de atualização de mostra uma máquina

#### <a name="issue"></a>Problema

Uma máquina tem o status **falhou ao iniciar** para uma máquina. Quando você exibir os detalhes específicos para a máquina, você verá o seguinte erro:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Causa

Este erro pode ocorrer devido a um dos seguintes motivos:

* A máquina não existe mais.
* A máquina está ativada, desativado e inacessível.
* O computador tem um problema de conectividade de rede e o hybrid worker no computador está inacessível.
* Houve uma atualização para o Microsoft Monitoring Agent que alterou o SourceComputerId
* Sua execução de atualização pode ter sido limitada se você atingir o limite de 2.000 trabalhos simultâneos em uma conta de automação. Cada implantação é considerada um trabalho e cada computador em uma contagem de implantação de atualização como um trabalho. Qualquer outra automação atualização ou trabalho de implantação em execução no momento em sua contagem de conta de automação até o limite de trabalhos simultâneos.

#### <a name="resolution"></a>Resolução

Quando uso aplicáveis [grupos dinâmicos](../automation-update-management.md#using-dynamic-groups) para suas implantações de atualização.

* Verifique se a máquina ainda existe e está acessível. Se não existir, edite sua implantação e remova a máquina.
* Consulte a seção sobre [planejamento de rede](../automation-update-management.md#ports) para obter uma lista de portas e endereços que são necessárias para o gerenciamento de atualizações e verifique se seu computador atende a esses requisitos.
* Execute a seguinte consulta no Log Analytics localizar computadores em seu ambiente cujo `SourceComputerId` alterado. Procure por computadores que têm o mesmo `Computer` valor, mas diferentes `SourceComputerId` valor. Depois de encontrar máquinas afetadas, você deve editar as implantações de atualização que essas máquinas de destino e removem e adicionar novamente as máquinas para que o `SourceComputerId` reflete o valor correto.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>Cenário: O computador é exibido como Não avaliado e mostra uma exceção HResult

#### <a name="issue"></a>Problema

Você tem máquinas que são exibidas como **Não avaliadas** em **Conformidade** e vê uma mensagem de exceção abaixo dela.

#### <a name="cause"></a>Causa

O Windows Update ou o WSUS não está configurado corretamente no computador. O Gerenciamento de Atualizações depende do Windows Update ou do WSUS para fornecer as atualizações que são necessárias, o status do patch e os resultados de patches implantados. Sem essas informações, o Gerenciamento de Atualizações não pode relatar corretamente sobre os patches necessários ou instalados.

#### <a name="resolution"></a>Resolução

Clique duas vezes na exceção exibida em vermelho para ver a mensagem de exceção completa. Examine a tabela a seguir para saber as possíveis soluções ou ações a tomar:

|Exceção  |Resolução ou ação  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Pesquisar o código de erro relevante na [Lista de códigos de erro da atualização do Windows](https://support.microsoft.com/help/938205/windows-update-error-code-list) para localizar detalhes adicionais sobre a causa da exceção.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Esses erros são problemas de conectividade de rede. Verifique se seu computador tem a conectividade de rede apropriada para o Gerenciamento de Atualizações. Consulte a seção sobre [planejamento de rede](../automation-update-management.md#ports) para obter uma lista de portas e endereços que são necessários.        |
|`0x8024001E`| A operação de atualização não foi concluída porque o serviço ou sistema estava sendo desligado.|
|`0x8024002E`| Serviço Windows Update está desabilitado.|
|`0x8024402C`     | Se você estiver usando um servidor WSUS, verifique se os valores do registro para `WUServer` e `WUStatusServer` sob a chave do registro `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` têm o servidor WSUS correto.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Verifique se o serviço Windows Update (wuauserv) está em execução e não está desabilitado.        |
|Qualquer outra exceção genérica     | Faça uma pesquisa na Internet para as soluções possíveis e trabalhe com o suporte de TI local.         |

Revisando o `windowsupdate.log` pode ajudá-lo determinar a possível causa. Para obter mais informações sobre como ler o log, consulte [como ler o arquivo windowsupdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Além disso, você pode baixar e executar a [solução de problemas do Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) para verificar se há problemas com o Windows Update no computador.

> [!NOTE]
> A [solução de problemas do Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) declara que é para clientes do Windows, mas também funciona bem no Windows Server.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Cenário: A execução da atualização falha ao ser iniciada

#### <a name="issue"></a>Problema

Uma atualização é executada falha em iniciar em uma máquina Linux.

#### <a name="cause"></a>Causa

O Linux Hybrid Worker não é saudável.

#### <a name="resolution"></a>Resolução

Faça uma cópia do seguinte arquivo de log e preserve-o para fins de solução de problemas:

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Cenário: A execução da atualização é iniciada, mas encontra erros

#### <a name="issue"></a>Problema

Uma execução de atualização é iniciada, mas encontra erros durante a execução.

#### <a name="cause"></a>Causa

Causas possíveis podem ser:

* Gerenciador de pacotes não é saudável
* Pacotes específicos podem interferir na correção baseada em nuvem
* Outras razões

#### <a name="resolution"></a>Resolução

Se ocorrerem falhas durante uma atualização após a inicialização bem-sucedida no Linux, verifique a saída da tarefa da máquina afetada na execução. Você pode encontrar mensagens de erro específicas do gerenciador de pacotes de sua máquina, sobre as quais você pode pesquisar e realizar ações. O Gerenciamento de Atualizações requer que o gerenciador de pacotes seja saudável para implantações de atualização bem-sucedidas.

Em alguns casos, as atualizações de pacote podem interferir no Gerenciamento de Atualizações impedindo que uma implantação de atualização seja concluída. Se você perceber isso, terá que excluir esses pacotes de futuras atualizações ou instalá-los manualmente.

Se você não conseguir resolver um problema de patch, faça uma cópia do seguinte arquivo de log e preserve-o **antes de** a próxima implantação de atualização começar para fins de solução de problemas:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
