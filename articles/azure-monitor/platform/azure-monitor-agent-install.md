---
title: Instalar o agente de Azure Monitor
description: Opções para instalar o agente de Azure Monitor (AMA) em máquinas virtuais do Azure e servidores habilitados para Arc do Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 893a04f5acd48cf1e6f34033c06a758492e70abf
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516716"
---
# <a name="install-the-azure-monitor-agent-preview"></a>Instalar o agente de Azure Monitor (versão prévia)
Este artigo fornece as diferentes opções disponíveis no momento para instalar o [agente de Azure monitor](azure-monitor-agent-overview.md) em máquinas virtuais do Azure e em servidores habilitados para Arc do Azure e também as opções para criar [associações com regras de coleta de dados](data-collection-rule-azure-monitor-agent.md) que definem quais dados o agente deve coletar.

## <a name="prerequisites"></a>Pré-requisitos
Os pré-requisitos a seguir são necessários antes de instalar o agente de Azure Monitor.

- A [identidade do sistema gerenciado](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) deve ser habilitada em máquinas virtuais do Azure. Isso não é necessário para os servidores habilitados para Arc do Azure. A identidade do sistema será habilitada automaticamente se o agente for instalado como parte do processo de [criação e atribuição de uma regra de coleta de dados usando o portal do Azure](#install-with-azure-portal).
- A [marca de serviço AzureResourceManager](../../virtual-network/service-tags-overview.md) deve ser habilitada na rede virtual para a máquina virtual.

## <a name="virtual-machine-extension-details"></a>Detalhes da extensão da máquina virtual
O agente de Azure Monitor é implementado como uma [extensão de VM do Azure](../../virtual-machines/extensions/overview.md) com os detalhes na tabela a seguir. Ele pode ser instalado usando qualquer um dos métodos para instalar extensões de máquina virtual, incluindo as descritas neste artigo.

| Propriedade | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft. Azure. monitor  | Microsoft. Azure. monitor |
| Tipo      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |


## <a name="install-with-azure-portal"></a>Instalação com o portal do Azure
Para instalar o agente de Azure Monitor usando o portal do Azure, siga o processo para [criar uma regra de coleta de dados](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) no portal do Azure. Isso permite que você associe a regra de coleta de dados a uma ou mais máquinas virtuais do Azure ou a servidores habilitados para Arc do Azure. O agente será instalado em qualquer uma dessas máquinas virtuais que ainda não o possuem.


## <a name="install-with-resource-manager-template"></a>Instalar com o modelo do Resource Manager
Você pode usar modelos do Resource Manager para instalar o agente de Azure Monitor em máquinas virtuais do Azure e em servidores habilitados para Arc do Azure e para criar uma associação com regras de coleta de dados. Você deve criar qualquer regra de coleta de dados antes de criar a associação.

Obtenha modelos de exemplo para instalar o agente e criar a associação a partir do seguinte: 

- [Modelo para instalar o agente de Azure Monitor (Azure e Azure ARC)](../samples/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Modelo para criar associação com a regra de coleta de dados](../samples/resource-manager-data-collection-rules.md)

Instale os modelos usando [qualquer método de implantação para modelos do Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md) , como os comandos a seguir.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>Instalar com o PowerShell
Você pode instalar o agente de Azure Monitor em máquinas virtuais do Azure e em servidores habilitados para Arc do Azure usando o comando do PowerShell para adicionar uma extensão de máquina virtual. 

### <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure
Use os comandos do PowerShell a seguir para instalar o agente de Azure Monitor em máquinas virtuais do Azure.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
---

### <a name="azure-arc-enabled-servers"></a>Servidores habilitados para Azure Arc
Use os comandos do PowerShell a seguir para instalar os servidores habilitados para Arc do Azure Monitor Agent.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>CLI do Azure
Você pode instalar o agente de Azure Monitor em máquinas virtuais do Azure e em servidores habilitados para Arc do Azure usando o comando CLI do Azure para adicionar uma extensão de máquina virtual. 

### <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure
Use os comandos da CLI a seguir para instalar o agente de Azure Monitor em máquinas virtuais do Azure.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Servidores habilitados para Azure Arc
Use os comandos da CLI a seguir para instalar os servidores habilitados para Arc do Azure Monitor Agent.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>Próximas etapas

- [Crie uma regra de coleta de dados](data-collection-rule-azure-monitor-agent.md) para coletar dados do agente e enviá-los para Azure monitor.
