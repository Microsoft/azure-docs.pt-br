---
title: Início Rápido do Azure – Fazer backup de uma VM com o PowerShell
description: Saiba como fazer backup de máquinas virtuais com o Azure PowerShell
author: dcurwin
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: e7a343e257a926f010b52f9833d7acb321c8aeb6
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639470"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Fazer backup de uma máquina virtual no Azure com o PowerShell

O módulo de [AZ do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) é usado para criar e gerenciar recursos do Azure a partir da linha de comando ou em scripts. 

O [Backup do Azure](backup-overview.md) faz backup de aplicativos e computadores locais e VMs do Azure. Este artigo mostra como fazer backup de uma VM do Azure com o módulo de AZ. Como alternativa, é possível fazer backup de uma VM usando a [CLI do Azure](quick-backup-vm-cli.md) ou no [portal do Azure](quick-backup-vm-portal.md).

Este início rápido habilita o backup em uma VM do Azure existente. Se você precisar criar uma máquina virtual, poderá [criar uma máquina virtual com o Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Este início rápido requer o módulo de AZ do Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Entrar e registrar

1. Faça logon na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

    ```powershell
    Connect-AzAccount
    ```
2. Na primeira vez em que você usar o Backup do Azure, registre o provedor do Serviço de Recuperação do Azure na assinatura com [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider), conforme a seguir:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um [cofre dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) é um contêiner lógico que armazena dados de backup para recursos protegidos, como VMs do Azure. Quando um trabalho de backup executa, ele cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Você pode usar um desses pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Ao criar o cofre:

- Para o grupo de recursos e a localização, especifique o grupo de recursos e a localização da VM da qual você quer fazer backup.
- Se você usou este [script de exemplo](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) para criar a VM, o grupo de recursos é **myResourceGroup**, a VM é ***myVM** e os recursos estão na região **WestEurope**.
- O Backup do Azure manipula automaticamente o armazenamento para os dados de backup. Por padrão, o cofre usa [GRS (Armazenamento com Redundância Geográfica)](../storage/common/storage-redundancy-grs.md). A redundância geográfica garante que os dados de backup sejam replicados para uma região do Azure secundária, a centenas de quilômetros de distância da região primária.

Agora, crie um cofre:


1. Use o [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) para criar o cofre:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Defina o contexto do cofre com [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext), conforme a seguir:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Altere a configuração de redundância de armazenamento (LRS/GRS) do cofre com [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty), da seguinte maneira:
    
    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```
    > [!NOTE]
    > A Redundância do Armazenamento só poderá ser modificada se não houver itens de backup protegidos no cofre.

## <a name="enable-backup-for-an-azure-vm"></a>Habilitar o backup para uma VM do Azure

Você habilita o backup de uma VM do Azure e especifica uma política de backup.

- A política define quando os backups serão executados e por quanto tempo os pontos de recuperação criados pelos backups deverão ser retidos.
- A política de proteção padrão executa um backup uma vez por dia para a VM e mantém por 30 dias os pontos de recuperação criados. É possível usar essa política padrão para proteger rapidamente a VM. 

Habilite o backup conforme a seguir:

1. Primeiro, defina a política padrão com [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Habilite o backup da VM com [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Especifique a política, o grupo de recursos e o nome da VM.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```


## <a name="start-a-backup-job"></a>Iniciar um trabalho de backup

Os backups são executados de acordo com o agendamento especificado na política de backup. Também é possível executar um backup ad hoc:

- O primeiro trabalho de backup inicial criará um ponto de recuperação completo.
- Após o backup inicial, cada trabalho de backup criará pontos de recuperação incrementais.
- Os pontos de recuperação incrementais são eficientes em termos de armazenamento e de tempo, já que eles transferem somente as alterações feitas desde o último backup.

Para executar um backup ad hoc, use o [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem). 
- Você especifica um contêiner no cofre que contém os dados de backup com [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Cada máquina virtual a ter o backup feito é tratada como um item. Para iniciar um trabalho de backup, você obtém informações sobre a VM com [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Execute um trabalho de backup ad hoc da seguinte maneira:

1. Especifique o contêiner, obtenha informações da VM e execute o backup.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Talvez seja necessário aguardar até 20 minutos, pois o primeiro trabalho de backup criará um ponto de recuperação completo. Monitore o trabalho, conforme descrito no próximo procedimento.


## <a name="monitor-the-backup-job"></a>Monitorar o trabalho de backup

1. Execute [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) para monitorar o status do trabalho.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```
    A saída é semelhante ao exemplo a seguir, que mostra o trabalho como **InProgress**:

    ```
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Quando o status do trabalho estiver **Concluído**, a VM estará protegida e terá um ponto de recuperação completo armazenado.


## <a name="clean-up-the-deployment"></a>Limpar a implantação

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
