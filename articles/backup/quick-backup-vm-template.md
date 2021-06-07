---
title: Início Rápido – Backup de VM do modelo do Resource Manager
description: Saiba como fazer backup de máquinas virtuais com o modelo do Azure Resource Manager
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: e64aa10d8ddadd367d04b2b480770a99f3ece1dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88826524"
---
# <a name="quickstart-back-up-a-virtual-machine-in-azure-with-an-arm-template"></a>Início Rápido: Fazer backup de uma máquina virtual no Azure com um modelo do ARM

O [Backup do Azure](backup-overview.md) faz backup de aplicativos e computadores locais e VMs do Azure. Este artigo mostra como fazer backup de uma VM do Azure com o modelo do ARM (Azure Resource Manager) e o Azure PowerShell. Este guia de início rápido concentra-se no processo de implantação de um modelo do ARM para criar um cofre dos Serviços de Recuperação. Para obter mais informações sobre como desenvolver modelos do ARM, confira a [documentação do Azure Resource Manager](../azure-resource-manager/index.yml) e a [referência de modelo](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Um [cofre dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) é um contêiner lógico que armazena dados de backup para recursos protegidos, como VMs do Azure. Quando um trabalho de backup executa, ele cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Você pode usar um desses pontos de recuperação para restaurar dados para um determinado ponto no tempo. Como alternativa, é possível fazer backup de uma VM usando o [Azure PowerShell](./quick-backup-vm-powershell.md), a [CLI do Azure](quick-backup-vm-cli.md) ou no [portal do Azure](quick-backup-vm-portal.md).

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-create-vm-and-configure-backup%2Fazuredeploy.json)

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Este modelo permite que você implante a VM do Windows simples e o cofre dos Serviços de Recuperação configurados com o DefaultPolicy for Protection.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json":::

Os recursos definidos no modelo são:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/vaults**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>Implantar o modelo

Para implantar o modelo, selecione **Experimentar** para abrir o Azure Cloud Shell e, em seguida, cole o script do PowerShell a seguir na janela do shell. Para colar o código, clique com o botão direito do mouse na janela do shell e, em seguida, selecione **Colar**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (for example, centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

Neste início rápido, o Azure PowerShell é usado para implantar o modelo do ARM. O [Portal do Azure](../azure-resource-manager/templates/deploy-portal.md), a [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md) e a [API Rest](../azure-resource-manager/templates/deploy-rest.md) também podem ser usados para implantar modelos.

## <a name="validate-the-deployment"></a>Validar a implantação

### <a name="start-a-backup-job"></a>Iniciar um trabalho de backup

O modelo cria uma VM e ativa a VM novamente. Após implantar o modelo, você precisa iniciar um trabalho de backup. Para saber mais, confira [Iniciar um trabalho de backup](./quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>Monitorar o trabalho de backup

Para monitorar o trabalho de backup, confira [Monitorar o trabalho de backup](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais fazer backup da VM, poderá limpá-la.

- Se você quiser experimentar a restauração da VM, ignore a limpeza.
- Se você usou uma VM existente, poderá ignorar o cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) final para deixar o grupo de recursos e a VM implementados.

Desabilite a proteção, remova os pontos de restauração e o cofre. Em seguida, exclua o grupo de recursos e os recursos de VM associados, da seguinte maneira:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um cofre dos Serviços de Recuperação, habilitou a proteção em uma VM e criou o ponto de recuperação inicial.

- [Saiba como](tutorial-backup-vm-at-scale.md) fazer backup de VMs no portal do Azure.
- [Saiba como](tutorial-restore-disk.md) restaurar rapidamente uma VM
- [Saiba como](../azure-resource-manager/templates/template-tutorial-create-first-template.md) criar modelos do ARM.
