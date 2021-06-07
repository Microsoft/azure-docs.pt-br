---
title: Gerenciamento de extensão de VM com servidores habilitados para Arc do Azure
description: Os servidores habilitados para Arc do Azure podem gerenciar a implantação de extensões de máquina virtual que fornecem tarefas de automação e configuração pós-implantação com VMs não Azure.
ms.date: 03/22/2021
ms.topic: conceptual
ms.openlocfilehash: 9af2700f036352188e646188485285482ee70c69
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799574"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Gerenciamento de extensão da máquina virtual com servidores habilitados para Azure Arc

As extensões de VM (máquina virtual) são pequenos aplicativos que fornecem tarefas de configuração e automação de pós-implantação em VMs do Azure. Por exemplo, se uma máquina virtual exigir instalação de software, proteção antivírus ou para executar um script nele, uma extensão de VM poderá ser usada.

Os servidores habilitados para Arc do Azure permitem implantar extensões de VM do Azure em VMs não Azure Windows e Linux, simplificando o gerenciamento da sua máquina híbrida por meio de seu ciclo de vida. As extensões de VM podem ser gerenciadas usando os seguintes métodos em seus computadores híbridos ou servidores gerenciados por servidores habilitados para Arc:

- O [Portal do Azure](manage-vm-extensions-portal.md)
- A [CLI do Azure](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Modelos do Azure [Resource Manager](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Principais benefícios

O suporte à extensão de VM dos servidores habilitados para Arc do Azure oferece os seguintes benefícios principais:

- Coletar dados de log para análise com [logs em Azure monitor](../../azure-monitor/logs/data-platform-logs.md) habilitando a extensão de VM do agente log Analytics. Isso é útil para fazer análises complexas em dados de diferentes tipos de fontes.

- Com o [Azure monitor para VMs](../../azure-monitor/vm/vminsights-overview.md), o analisa o desempenho de suas VMs do Windows e do Linux e monitora seus processos e dependências em outros recursos e processos externos. Isso é obtido por meio da habilitação do agente de Log Analytics e das extensões de VM do agente de dependência.

- Baixe e execute scripts em computadores conectados híbridos usando a extensão de script personalizado. Essa extensão é útil para a configuração de pós-implantação, instalação de software ou qualquer outra configuração ou tarefas de gerenciamento.

- Atualização automática de certificados armazenados em um [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="availability"></a>Disponibilidade

A funcionalidade de extensão de VM está disponível somente na lista de [regiões com suporte](overview.md#supported-regions). Certifique-se de carregar seu computador em uma dessas regiões.

## <a name="extensions"></a>Extensões

Nesta versão, damos suporte às seguintes extensões de VM em computadores Windows e Linux.

Para saber mais sobre o pacote do agente do computador conectado do Azure e detalhes sobre o componente do agente de extensão, consulte [visão geral do agente](agent-overview.md#agent-component-details).

> [!NOTE]
> Recentemente, o suporte para a extensão de VM DSC foi removido para servidores habilitados para Arc. Como alternativa, é recomendável usar a extensão de script personalizado para gerenciar a configuração pós-implantação de seu servidor ou computador.

### <a name="windows-extensions"></a>Extensões do Windows

|Extensão |Publicador |Type |Informações adicionais |
|----------|----------|-----|-----------------------|
|Scanner de vulnerabilidade integrada do Azure defender |Qualys |WindowsAgent.AzureSecurityCenter |[Solução de avaliação de vulnerabilidade integrada do Azure defender para Azure e computadores híbridos](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Extensão de Script personalizado |Microsoft.Compute | CustomScriptExtension |[Extensão de script personalizado do Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|Agente do Log Analytics |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[Log Analytics extensão de VM para Windows](../../virtual-machines/extensions/oms-windows.md)|
|Azure Monitor para VMs (insights) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [Extensão da máquina virtual do agente de dependência para Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Sincronização de certificado Azure Key Vault | Microsoft. Azure. Key. Vault |KeyVaultForWindows | [Extensão da máquina virtual de Key Vault para Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|Agente do Azure Monitor |Microsoft. Azure. monitor |AzureMonitorWindowsAgent |[Instalar o agente de Azure Monitor (versão prévia)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Extensões do Linux

|Extensão |Publicador |Type |Informações adicionais |
|----------|----------|-----|-----------------------|
|Scanner de vulnerabilidade integrada do Azure defender |Qualys |LinuxAgent.AzureSecurityCenter |[Solução de avaliação de vulnerabilidade integrada do Azure defender para Azure e computadores híbridos](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Extensão de Script personalizado |Microsoft. Azure. Extensions |CustomScript |[Extensão de script personalizado do Linux versão 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Agente do Log Analytics |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[Log Analytics extensão de VM para Linux](../../virtual-machines/extensions/oms-linux.md) |
|Azure Monitor para VMs (insights) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[Extensão da máquina virtual do agente de dependência para Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Sincronização de certificado Azure Key Vault | Microsoft. Azure. Key. Vault |KeyVaultForLinux | [Extensão da máquina virtual de Key Vault para Linux](../../virtual-machines/extensions/key-vault-linux.md) |
|Agente do Azure Monitor |Microsoft. Azure. monitor |AzureMonitorLinuxAgent |[Instalar o agente de Azure Monitor (versão prévia)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>Pré-requisitos

Esse recurso depende dos seguintes provedores de recursos do Azure em sua assinatura:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Se eles ainda não estiverem registrados, siga as etapas em [registrar provedores de recursos do Azure](agent-overview.md#register-azure-resource-providers).

Certifique-se de examinar a documentação de cada extensão de VM referenciada na tabela anterior para entender se ela tem requisitos de rede ou de sistema. Isso pode ajudá-lo a evitar problemas de conectividade com um serviço ou recurso do Azure que dependa dessa extensão de VM.

### <a name="log-analytics-vm-extension"></a>Log Analytics extensão de VM

A extensão de VM do agente de Log Analytics para Linux requer o Python 2. x instalado no computador de destino.

### <a name="azure-key-vault-vm-extension-preview"></a>Extensão de VM Azure Key Vault (versão prévia)

A extensão de VM Key Vault (versão prévia) não dá suporte aos seguintes sistemas operacionais Linux:

- CentOS Linux 7 (x64)
- RHEL (Red Hat Enterprise Linux) 7 (x64)
- Amazon Linux 2 (x64)

A implantação da extensão de VM Key Vault (visualização) só tem suporte usando:

- A CLI do Azure
- O Azure PowerShell
- Modelo do Azure Resource Manager

Antes de implantar a extensão, você precisará concluir o seguinte:

1. [Crie um cofre e um certificado](../../key-vault/certificates/quick-create-portal.md) (autoassinado ou importação).

2. Conceda ao Azure Arc acesso do servidor habilitado para o segredo do certificado. Se você estiver usando a [visualização do RBAC](../../key-vault/general/rbac-guide.md), procure o nome do recurso de arco do Azure e atribua a ele a função de **usuário Key Vault segredos (versão prévia)** . Se você estiver usando a [política de acesso Key Vault](../../key-vault/general/assign-access-policy-portal.md), atribua permissões de **Get** de segredo à identidade atribuída do sistema do recurso de arco do Azure.

### <a name="connected-machine-agent"></a>Agente do Connected Machine

Verifique se seu computador corresponde às [versões com suporte](agent-overview.md#supported-operating-systems) do sistema operacional Windows e Linux para o agente do computador conectado do Azure.

A versão mínima do agente do computador conectado que tem suporte com esse recurso no Windows e no Linux é a versão 1,0.

Para atualizar seu computador para a versão do agente necessária, consulte [Atualizar agente](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Próximas etapas

Você pode implantar, gerenciar e remover extensões de VM usando o [CLI do Azure](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md), dos modelos [portal do Azure](manage-vm-extensions-portal.md)ou [Azure Resource Manager](manage-vm-extensions-template.md).
