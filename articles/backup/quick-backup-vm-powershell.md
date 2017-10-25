---
title: "Início Rápido do Azure - Fazer backup de uma VM com o PowerShell | Microsoft Docs"
description: "Saiba como fazer backup de máquinas virtuais com o Azure PowerShell"
services: virtual-machines-windows, azure-backup
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: virtual-machines-windows, azure-backup
ms.devlang: azurecli
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/18/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3ca0e9d905e23e25b57b46454399ad12e2890d52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Fazer backup de uma máquina virtual no Azure com o PowerShell
O módulo do Azure PowerShell é usado para criar e gerenciar recursos do Azure da linha de comando do ou em scripts. Você pode proteger seus dados fazendo backups em intervalos regulares. O Backup do Azure cria pontos de recuperação que são armazenados em cofres de recuperação com redundância geográfica. Este artigo fornece detalhes sobre como fazer backup de uma VM (máquina virtual) com o módulo do Azure PowerShell. Você também pode executar essas etapas com a [CLI do Azure ](quick-backup-vm-cli.md) ou o [portal do Azure](quick-backup-vm-portal.md).

Este início rápido habilita o backup em uma VM do Azure existente. Se você precisar criar uma máquina virtual, poderá [criar uma máquina virtual com o Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Este início rápido requer o módulo Azure PowerShell versão 4.4 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela.

```powershell
Login-AzureRmAccount
```

Na primeira vez que você usar o Backup do Azure, deverá registrar o provedor de serviços de recuperação do Azure em sua assinatura com [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

```powershell
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
```


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação
Um cofre dos Serviços de Recuperação é um contêiner lógico que armazena os dados de backup para cada recurso protegido, como VMs do Azure. Quando o trabalho de backup para um recurso protegido é executado, ele cria um ponto de recuperação no cofre dos Serviços de Recuperação. Você pode usar um desses pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Crie um Cofre de Serviços de Recuperação com [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault). Especifique o mesmo grupo de recursos e o local da VM que você deseja proteger. Se você tiver usado o [script de exemplo](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) para criar sua VM, o grupo de recursos será chamado de *myResourceGroup*, a VM será chamada de *myVM* e os recursos estarão no local *WestEurope*.

```powershell
New-AzureRmRecoveryServicesVault `
    -ResourceGroupName "myResourceGroup" `
    -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
```

Por padrão, o cofre é definido para o armazenamento com redundância geográfica. Para proteger ainda mais seus dados, esse nível de redundância de armazenamento garante que os dados de backup sejam replicados para uma região secundária do Azure a centenas de milhas de distância da região primária.

Para usar o cofre com as etapas restantes, defina o contexto de cofre com [Set-AzureRmRecoveryServicesVaultContext](/powershell/module/AzureRM.RecoveryServices/Set-AzureRmRecoveryServicesVaultContext)

```powershell
Get-AzureRmRecoveryServicesVault `
    -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesVaultContext
```


## <a name="enable-backup-for-an-azure-vm"></a>Habilitar o backup para uma VM do Azure
Criar e usar políticas para definir quando um trabalho de backup é executado e por quanto tempo os pontos de recuperação serão armazenados. A política de proteção padrão executa um trabalho de backup a cada dia e retém pontos de recuperação por 30 dias. Você pode usar esses valores de política padrão para proteger rapidamente sua VM. Primeiro, defina a política padrão com [Get-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupProtectionPolicy):

```powershell
$policy = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "DefaultPolicy"
```

Para habilitar a proteção do backup para uma VM, use [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/AzureRM.RecoveryServices.Backup/Enable-AzureRmRecoveryServicesBackupProtection). Especifique a política a ser usada, o grupo de recursos e a VM a serem protegidos:

```powershell
Enable-AzureRmRecoveryServicesBackupProtection `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Policy $policy
```


## <a name="start-a-backup-job"></a>Iniciar um trabalho de backup
Para iniciar um backup agora em vez de aguardar a política padrão executar o trabalho no horário agendado, use [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem). O primeiro trabalho de backup cria um ponto de recuperação completa. Cada trabalho de backup depois que o backup inicial cria pontos de recuperação incremental. Os pontos de recuperação incrementais são eficientes em termos de armazenamento e de tempo, já que eles transferem somente as alterações feitas desde o último backup.

No conjunto de comandos a seguir, especifique um contêiner no cofre dos Serviços de Recuperação que contém os dados de backup com [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer). Cada máquina virtual a ter o backup feito é tratada como um item. Para iniciar um trabalho de backup, obtenha informações sobre o item de VM com [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupItem).

```powershell
$backupcontainer = Get-AzureRmRecoveryServicesBackupContainer `
    -ContainerType "AzureVM" `
    -FriendlyName "myVM"

$item = Get-AzureRmRecoveryServicesBackupItem `
    -Container $backupcontainer `
    -WorkloadType "AzureVM"

Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Como o primeiro trabalho de backup cria um ponto de recuperação completa, o processo pode levar até 20 minutos.


## <a name="monitor-the-backup-job"></a>Monitorar o trabalho de backup
Para monitorar o status de trabalhos de backup, use [Get-AzureRmRecoveryservicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob):

```powershell
Get-AzureRmRecoveryservicesBackupJob
```

O resultado é semelhante ao exemplo a seguir, que mostra que o trabalho de backup está **InProgress**:

```
WorkloadName   Operation         Status       StartTime              EndTime                JobID
------------   ---------         ------       ---------              -------                -----
myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
```

Quando o *Status* do trabalho de backup relatar *Concluído*, sua VM será protegida com os Serviços de Recuperação e terá um ponto de recuperação completo armazenado.


## <a name="clean-up-deployment"></a>Limpar implantação
Quando não for mais necessária, você poderá desabilitar a proteção na máquina virtual, remover os pontos de restauração e o cofre dos Serviços de Recuperação, então excluir o grupo de recursos e recursos associados de VM. Se você tiver usado uma VM existente, poderá ignorar o último cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para deixar o grupo de recursos e a VM no local.

Se você pretende examinar um tutorial de Backup que explique como restaurar dados para sua VM, ignore as etapas desta seção e vá para [Próximas etapas](#next-steps). 

```powershell
Disable-AzureRmRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzureRmRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzureRmRecoveryServicesVault -Vault $vault
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criou um cofre dos Serviços de Recuperação, habilitou a proteção em uma máquina virtual e criou o ponto de recuperação inicial. Para saber mais sobre os Serviços de Recuperação e o Backup do Azure, prossiga para os tutoriais.

> [!div class="nextstepaction"]
> [Fazer backup de várias VMs do Azure](./tutorial-backup-vm-at-scale.md)