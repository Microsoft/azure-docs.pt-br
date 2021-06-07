---
title: Solucionar problemas de extensão de VM do Azure Log Analytics
description: Descreva os sintomas, as causas e a resolução dos problemas mais comuns com a extensão de VM do Log Analytics para VMs do Azure para Windows e Linux.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: 33bdff8d516929e7f615f3a7f8bd77463eea0c64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040134"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Solucionar problemas da extensão da VM do Log Analytics no Azure Monitor
Este artigo fornece ajuda para solucionar erros que você pode encontrar com a extensão de VM do Log Analytics para máquinas virtuais Windows e Linux em execução no Microsoft Azure e sugere soluções possíveis para resolvê-los.

Para verificar o status da extensão, execute as seguintes etapas no portal do Azure.

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **máquinas virtuais**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Máquinas virtuais**.
3. Na lista de máquinas virtuais, localize e selecione-a.
3. Na máquina virtual, clique em **Extensões**.
4. Na lista, verifique se a extensão de Log Analytics está habilitada ou não.  Para o Linux, o agente é listado como **OMSAgentforLinux** e, para o Windows, o agente é listado como **MicrosoftMonitoringAgent**.

   ![Exibição da Extensão de VM](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Clique na extensão para exibir detalhes. 

   ![Detalhes da Extensão da VM](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Solucionando problemas da extensão da VM do Windows no Azure

Se a extensão da VM *Microsoft Monitoring Agent* não está instalando ou reportando, você pode executar as etapas a seguir para solucionar o problema.

1. Verifique se o agente de VM do Azure está instalado e funcionando corretamente usando as etapas em [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Você também pode examinar o arquivo de log do agente de VM `C:\WindowsAzure\logs\WaAppAgent.log`
   * Se o log não existir, isso significará que o agente de VM não está instalado.
   * [Instalar o agente de VM do Azure](../vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Examinar os arquivos de log de extensão de VM do Microsoft Monitoring Agent em `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Certificar-se de que a máquina virtual pode executar scripts do PowerShell
4. Certificar-se de que as permissões em C:\Windows\temp não foram alteradas
5. Exibir o status do Microsoft Monitoring Agent, digitando o seguinte em uma janela do PowerShell com privilégios elevados na máquina virtual `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Examinar os arquivos de log de instalação do Microsoft Monitoring Agent em `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Para obter mais informações, consulte [Solucionando problemas em extensões do Windows](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Solucionando problemas da extensão da VM do Linux
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Se a extensão da VM *Agente do Log Analytics para Linux* não estiver instalando ou reportando, você pode executar as etapas a seguir para solucionar o problema.

1. Se o status da extensão for *Desconhecido*, verifique se o agente de VM do Azure está instalado e funcionando corretamente, examinando o arquivo de log do agente de VM `/var/log/waagent.log`
   * Se o log não existir, isso significará que o agente de VM não está instalado.
   * [Instalar o Agente de VM do Azure em VMs Linux](../../virtual-machines/extensions/agent-linux.md#installation)
2. Para outros status não íntegros, examine o Agente do Log Analytics para arquivos de log de extensão de VMs do Linux em `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` e `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Se o status da extensão estiver íntegro mas os dados não estiverem sendo carregados, examine o Agente do Log Analytics para arquivos de log do Linux em `/var/opt/microsoft/omsagent/log/omsagent.log`

Para obter mais informações, consulte [Solucionando problemas em extensões do Linux](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Próximas etapas

Para mais orientações sobre solução de problemas relacionados ao agente do Log Analytics para Linux hospedado em computadores fora do Azure, consulte [Solucionar problemas do Agente do Linux do Azure Log Analytics](../agents/agent-linux-troubleshoot.md).