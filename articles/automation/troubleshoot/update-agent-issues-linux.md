---
title: Como solucionar problemas do agente de atualização do Linux na Automação do Azure
description: Este artigo explica como solucionar problemas e resolver problemas com o agente do Windows Update do Linux no Gerenciamento de Atualizações.
services: automation
ms.date: 01/25/2021
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: da7c0ea670b4c4201930ce5d0f01e7bd9d9835e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "100581035"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Solucionar problemas do agente de atualização do Linux

Pode haver muitas razões pelas quais o computador não está aparecendo como pronto (íntegro) no Gerenciamento de Atualizações. Você pode verificar a integridade de um agente do Hybrid Runbook Worker do Linux para determinar o problema subjacente. A seguir estão os três estados de preparação para um computador:

* Pronto: O Hybrid Runbook Worker está implantado e foi visualizado pela última vez há menos de uma hora.
* Desconectado: o Hybrid Runbook Worker está implantado e foi visualizado pela última vez há mais de uma hora.
* Não configurado: O Hybrid Runbook Worker não foi encontrado ou não concluiu a implantação.

> [!NOTE]
> Pode haver um pequeno atraso entre o que o portal do Azure mostra e o estado atual de um computador.

Este artigo descreve como executar a solução de problemas em computadores do Azure no portal do Azure e em computadores não Azure no [cenário offline](#troubleshoot-offline).

> [!NOTE]
> O script de solução de problemas atualmente não roteia o tráfego por um servidor proxy se houver um configurado.

## <a name="start-the-troubleshooter"></a>Iniciar a solução de problemas

Para computadores do Azure, selecione o link **Solução de Problemas** na coluna **Preparação do Agente de Atualização** no portal para abrir a página Solucionar Problemas do Agente de Atualização. Para computadores não Azure, o link levará você para este artigo. Para solucionar problemas de um computador não Azure, confira as instruções na seção "Solucionar problemas offline".

![Página de lista da VM](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> As verificações exigem que a VM esteja em execução. Se a VM não estiver em execução, **Iniciar a VM** será exibido.

Na página Solucionar problemas de atualização de agente, selecione **Executar Verificações** para iniciar a solução de problemas. A solução de problemas usa [Executar comando](../../virtual-machines/linux/run-command.md) para executar um script no computador para verificar as dependências. Quando a solução de problemas é concluída, ela retorna o resultado das verificações.

![Página Solucionar problemas](../media/update-agent-issues-linux/troubleshoot-page.png)

Quando as verificações forem concluídas, os resultados serão retornados na janela. As seções de verificação fornecem informações sobre o que cada verificação está procurando.

![Página Atualizar verificações de agente](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A verificação do sistema operacional verifica se o Hybrid Runbook Worker está executando um dos sistemas operacionais a seguir.

|Sistema operacional  |Observações  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux devem ter acesso a um repositório de atualização. O patch baseado em classificação requer que "yum" retorne dados de segurança que o CentOS não tem.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Os agentes do Linux devem ter acesso a um repositório de atualização.         |

## <a name="monitoring-agent-service-health-checks"></a>Monitoramento das verificações de integridade de serviço do agente

### <a name="log-analytics-agent"></a>Agente do Log Analytics

Essa verificação garante que o agente do Log Analytics para Linux esteja instalado. Para obter instruções sobre como instalar, consulte [Instalar o agente para Linux](../../azure-monitor/vm/quick-collect-linux-computer.md#install-the-agent-for-linux).

### <a name="log-analytics-agent-status"></a>Status do agente do Log Analytics

Essa verificação garante que o agente do Log Analytics para Linux esteja em execução. Se o agente não estiver em execução, será possível executar o comando a seguir para tentar reiniciá-lo. Para saber mais sobre como solucionar problemas do agente, confira [Linux – Solucionar problemas do Hybrid Runbook Worker](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Hospedagem múltipla

Essa verificação determina se o agente está se comunicando com vários workspaces. O Gerenciamento de Atualizações não dá suporte a hospedagem múltipla.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Isso verifica se o agente do Log Analytics para Linux tem o pacote do Hybrid Runbook Worker. Este pacote é necessário para o Gerenciamento de Atualizações trabalhar. Para saber mais, confira [O agente do Log Analytics para Linux não está em execução](hybrid-runbook-worker.md#oms-agent-not-running).

Gerenciamento de Atualizações baixa pacotes do Hybrid Runbook Worker do ponto de extremidade de operações. Portanto, se o Hybrid Runbook Worker não estiver em execução e a verificação do [ponto de extremidade de operações](#operations-endpoint) falhar, a atualização poderá falhar.

### <a name="hybrid-runbook-worker-status"></a>Status do Hybrid Runbook Worker

Essa verificação garante que o Hybrid Runbook Worker esteja em execução no computador. Os processos no exemplo abaixo deverão estar presentes se o Hybrid Runbook Worker estiver sendo executado corretamente.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="general-internet-connectivity"></a>Conectividade com a Internet geral

Essa verificação garante que o computador tenha acesso à internet.

### <a name="registration-endpoint"></a>Ponto de extremidade de registro

Essa verificação determina se o Hybrid Runbook Worker pode se comunicar corretamente com a Automação do Azure no workspace do Log Analytics.

As configurações de proxy e firewall devem permitir que o agente do Hybrid Runbook Worker se comunique com o ponto de extremidade de registro. Para obter uma lista de endereços e portas a serem abertas, consulte [Planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Ponto de extremidade de operações

Essa verificação determina se o agente do Log Analytics pode se comunicar corretamente com o Serviço de Dados de Runtime do Trabalho.

As configurações de proxy e firewall devem permitir que o agente do Hybrid Runbook Worker se comunique com o Serviço de Dados de Runtime do Trabalho. Para obter uma lista de endereços e portas a serem abertas, consulte [Planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Ponto de extremidade do Log Analytics 1

Essa verificação verifica se o seu computador tem acesso aos pontos de extremidade necessários para o agente do Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Ponto de extremidade do Log Analytics 2

Essa verificação verifica se o seu computador tem acesso aos pontos de extremidade necessários para o agente do Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Ponto de extremidade do Log Analytics 3

Essa verificação verifica se o seu computador tem acesso aos pontos de extremidade necessários para o agente do Log Analytics.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Solucionar problemas offline

Você pode usar a solução de problemas offline em um Hybrid Runbook Worker executando o script localmente. O script Python, [UM_Linux_Troubleshooter_Offline. py](https://github.com/Azure/updatemanagement/blob/main/UM_Linux_Troubleshooter_Offline.py), pode ser encontrado no github. Um exemplo da saída desse script é mostrado no seguinte exemplo:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Próximas etapas

[Solucionar problemas do Hybrid Runbook Worker](hybrid-runbook-worker.md).
