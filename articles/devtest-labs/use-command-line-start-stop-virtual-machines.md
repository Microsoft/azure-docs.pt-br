---
title: Use ferramentas de linha de comando para iniciar e parar VMs Azure DevTest Labs
description: Saiba como usar as ferramentas de linha de comando para iniciar e parar máquinas virtuais no Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2ddc1620cf86fa203b2f0e31359f9fd262df8916
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499536"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Usar ferramentas de linha de comando para iniciar e parar Azure DevTest Labs máquinas virtuais
Este artigo mostra como usar Azure PowerShell ou CLI do Azure para iniciar ou parar máquinas virtuais em um laboratório no Azure DevTest Labs. Você pode criar scripts do PowerShell/CLI para automatizar essas operações. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral
Azure DevTest Labs é uma maneira de criar ambientes de desenvolvimento/teste rápidos, fáceis e enxutos. Ele permite que você gerencie custos, provisione rapidamente VMs e minimize o desperdício.  Há recursos internos no portal do Azure que permitem que você configure VMs em um laboratório para iniciar e parar automaticamente em horários específicos. 

No entanto, em alguns cenários, talvez você queira automatizar a inicialização e a interrupção de VMs de scripts do PowerShell/CLI. Ele oferece alguma flexibilidade com a inicialização e a interrupção de máquinas individuais a qualquer momento, em vez de horários específicos. Aqui estão algumas das situações em que a execução dessas tarefas usando scripts seria útil.

- Ao usar um aplicativo de três camadas como parte de um ambiente de teste, as camadas precisam ser iniciadas em uma sequência. 
- Desative uma VM quando um critério personalizado for atendido para economizar dinheiro. 
- Use-o como uma tarefa em um fluxo de trabalho de CI/CD para começar no início do fluxo, use as VMs como máquinas de compilação, máquinas de teste ou infraestrutura e, em seguida, interrompa as VMs quando o processo for concluído. Um exemplo disso seria a fábrica de imagens personalizada com Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> O script a seguir usa o módulo Azure PowerShell AZ. 

O script do PowerShell a seguir inicia uma VM em um laboratório. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) é o foco principal para esse script. O parâmetro **ResourceId** é a ID de recurso totalmente qualificada para a VM no laboratório. O parâmetro **Action** é onde as opções **Start** ou **Stop** são definidas dependendo do que é necessário.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>CLI do Azure
A [CLI do Azure](/cli/azure/get-started-with-azure-cli) é outra maneira de automatizar o início e a interrupção das VMs do DevTest Labs. CLI do Azure pode ser [instalado](/cli/azure/install-azure-cli) em diferentes sistemas operacionais. O script a seguir fornece comandos para iniciar e parar uma VM em um laboratório. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte artigo para usar o portal do Azure para executar essas operações: [reiniciar uma VM](devtest-lab-restart-vm.md).
