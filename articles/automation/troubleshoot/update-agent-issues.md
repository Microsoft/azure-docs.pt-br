---
title: Como solucionar problemas do Windows Update Agent na Automação do Azure
description: Este artigo explica como solucionar problemas com o Windows Update Agent no Gerenciamento de Atualizações.
services: automation
ms.date: 01/25/2020
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: 9516210021ce48f069ae3b3b4e02503527e0db24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "100580896"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Solucionar problemas do Windows Update Agent

Podem haver muitas razões pelas quais o computador não está aparecendo como pronto (íntegro) na implantação do Gerenciamento de Atualizações. Você pode verificar a integridade de um agente do Hybrid Runbook Worker do Windows para determinar o problema subjacente. A seguir estão os três estados de preparação para um computador:

* Pronto: O Hybrid Runbook Worker está implantado e foi visualizado pela última vez há menos de uma hora.
* Desconectado: O Hybrid Runbook Worker está implantado e foi visualizado pela última vez há mais de uma hora.
* Não configurado: O Hybrid Runbook Worker não foi encontrado ou não concluiu a implantação.

> [!NOTE]
> Pode haver um pequeno atraso entre o que o portal do Azure mostra e o estado atual de um computador.

Este artigo descreve como executar a solução de problemas em computadores do Azure no portal do Azure e em computadores não Azure no [cenário offline](#troubleshoot-offline).

> [!NOTE]
> O script de solução de problemas agora inclui verificações para o Windows Server Update Services (WSUS), para o download automático e para as chaves de instalação.

## <a name="start-the-troubleshooter"></a>Iniciar a solução de problemas

Para computadores do Azure, abra a página Solução de Problemas do Agente de Atualizações selecionando o link **Solução de Problemas** na coluna **Preparação do Agente de Atualizações** do portal. Para computadores não Azure, o link levará você para este artigo. Veja [Solução de problemas offline](#troubleshoot-offline) para solucionar problemas de um computador não Azure.

![Captura de tela da lista de Gerenciamento de Atualizações das máquinas virtuais](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Para verificar a integridade do Hybrid Runbook Worker, a VM deve estar em execução. Se a VM não estiver em execução, será exibido o botão **Iniciar a VM**.

Na página Solução de Problemas do Agente de Atualizações, selecione **Executar verificações** para iniciar a solução de problemas. A solução de problemas usa [Executar Comando](../../virtual-machines/windows/run-command.md) para executar um script no computador e verificar as dependências. Quando a solução de problemas é concluída, ela retorna o resultado das verificações.

![Captura de tela da página de Solução de Problemas do Agente de Atualizações](../media/update-agent-issues/troubleshoot-page.png)

Os resultados são mostrados na página quando estão prontos. As seções de verificações mostram o que está incluído em cada verificação.

![Captura de tela das verificações da Solução de Problemas do Agente de Atualizações](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A verificação do sistema operacional verifica se o Hybrid Runbook Worker está executando um dos sistemas operacionais mostrados na tabela a seguir.

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Server 2012 e posterior |É necessário o .NET Framework 4.6 ou posterior. ([Baixe o .NET Framework](/dotnet/framework/install/guide-for-developers).)<br/> O Windows PowerShell 5.1 é necessário.  ([Baixe o Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).)        |

### <a name="net-462"></a>.NET 4.6.2

A verificação do .NET Framework verifica se o sistema tem o [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) ou posterior instalado.

### <a name="wmf-51"></a>WMF 5.1

A verificação do WMF verifica se o sistema tem a versão necessária do Windows Management Framework (WMF), ou seja, [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Essa verificação determina se você está usando o TLS 1.2 para criptografar suas comunicações. O TLS 1.0 não é mais suportado pela plataforma. Use o TLS 1.2 para se comunicar com o Gerenciamento de Atualizações.

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="registration-endpoint"></a>Ponto de extremidade de registro

Essa verificação determina se o agente pode se comunicar corretamente com o serviço do agente.

As configurações de proxy e firewall devem permitir que o agente do Hybrid Runbook Worker se comunique com o ponto de extremidade de registro. Para obter uma lista de endereços e portas a serem abertas, consulte [Planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Ponto de extremidade de operações

Essa verificação determina se o agente pode se comunicar corretamente com o Serviço de dados do runtime da tarefa.

As configurações de proxy e firewall devem permitir que o agente do Hybrid Runbook Worker se comunique com o Serviço de Dados de Runtime do Trabalho. Para obter uma lista de endereços e portas a serem abertas, consulte [Planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Verificações de integridade do serviço de VM

### <a name="monitoring-agent-service-status"></a>Monitoramento de status de serviço do agente

Essa verificação determina se o agente do Log Analytics para Windows (`healthservice`) está em execução no computador. Para saber mais sobre a solução de problemas do serviço, veja [O agente do Log Analytics para Windows não está em execução](hybrid-runbook-worker.md#mma-not-running).

Para reinstalar o agente do Log Analytics para Windows, veja [Instalação do agente para Windows](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Monitoramento de eventos de serviço de agente

Essa verificação determina se um evento 4502 aparece no log do Azure Operations Manager do computador nas últimas 24 horas.

Para saber mais sobre esse evento, veja [Evento 4502 no log do Operations Manager](hybrid-runbook-worker.md#event-4502) para esse evento.

## <a name="access-permissions-checks"></a>Verificações de permissões de acesso

> [!NOTE]
> A solução de problemas atualmente não roteia o tráfego por um servidor proxy se houver um configurado.

### <a name="crypto-folder-access"></a>Acesso à pasta Crypto

A verificação de acesso à pasta Crypto determina se a conta do sistema local tem acesso a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Solucionar problemas offline

Você pode usar o solucionador de problemas em um Hybrid Runbook Worker offline, executando o script localmente. Obtenha o seguinte script do GitHub: [UM_Windows_Troubleshooter_Offline.ps1](https://github.com/Azure/updatemanagement/blob/main/UM_Windows_Troubleshooter_Offline.ps1). Para executar o script, você deve ter o WMF 4.0 ou posterior instalado. Para baixar a versão mais recente do PowerShell, veja [Instalação de várias versões do PowerShell](/powershell/scripting/install/installing-powershell).

A saída deste script se parece com o seguinte exemplo:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Próximas etapas

[Solucionar problemas do Hybrid Runbook Worker](hybrid-runbook-worker.md).
