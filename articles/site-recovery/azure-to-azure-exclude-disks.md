---
title: Exclua os discos de VM do Azure da replicação com Azure Site Recovery e Azure PowerShell
description: Saiba como excluir discos de máquinas virtuais do Azure durante Azure Site Recovery usando Azure PowerShell.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: a21460279420c46b11c43615ae5ecc7bfa81de4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135814"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Excluir discos da replicação do PowerShell de VMs do Azure

Este artigo descreve como excluir discos ao replicar VMs do Azure. Você pode excluir discos para otimizar a largura de banda de replicação consumida ou os recursos do lado de destino que esses discos usam. Atualmente, essa funcionalidade está disponível apenas por meio de Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de compreender a [arquitetura e os componentes de recuperação de desastre](azure-to-azure-architecture.md).
- Examine os [requisitos de suporte](azure-to-azure-support-matrix.md) de todos os componentes.
- Verifique se você tem o módulo "AZ" do AzureRm PowerShell. Para instalar ou atualizar o PowerShell, consulte [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps).
- Certifique-se de que você criou um cofre de serviços de recuperação e máquinas virtuais protegidas pelo menos uma vez. Se você ainda não fez essas coisas, siga o processo em [Configurar a recuperação de desastre para máquinas virtuais do Azure usando Azure PowerShell](azure-to-azure-powershell.md).
- Se você estiver procurando informações sobre como adicionar discos a uma VM do Azure habilitada para replicação, [Leia este artigo](azure-to-azure-enable-replication-added-disk.md).

## <a name="why-exclude-disks-from-replication"></a>Por que excluir discos da replicação
Talvez seja necessário excluir discos da replicação porque:

- Sua máquina virtual atingiu [Azure site Recovery limites para replicar taxas de alteração de dados](./azure-to-azure-support-matrix.md).

- Os dados que estão sendo rotatividade no disco excluído não são importantes ou não precisam ser replicados.

- Você deseja salvar os recursos de armazenamento e rede não replicando os dados.

## <a name="how-to-exclude-disks-from-replication"></a>Como excluir discos da replicação

Em nosso exemplo, replicamos uma máquina virtual que tem um sistema operacional e três discos de dados que estão na região leste dos EUA para a região oeste dos EUA 2. O nome da máquina virtual é *AzureDemoVM*. Excluímos o disco 1 e mantemos os discos 2 e 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Obter detalhes das máquinas virtuais a serem replicadas

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Obtenha detalhes sobre os discos da máquina virtual. Essas informações serão usadas posteriormente quando você iniciar a replicação da VM.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replicar uma máquina virtual do Azure

Para o exemplo a seguir, presumimos que você já tenha uma conta de armazenamento em cache, uma política de replicação e mapeamentos. Se você não tiver essas coisas, siga o processo em [Configurar a recuperação de desastre para máquinas virtuais do Azure usando Azure PowerShell](azure-to-azure-powershell.md).

Replique uma máquina virtual do Azure com *discos gerenciados*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication.

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Quando a operação de replicação de inicialização for concluída com sucesso, os dados da VM serão replicados para a região de recuperação.

Você pode ir para a portal do Azure e ver as VMs replicadas em "itens replicados".

O processo de replicação começa propagando uma cópia dos discos de replicação da máquina virtual na região de recuperação. Essa fase é chamada de fase de replicação inicial.

Após a conclusão da replicação inicial, a replicação passa para a fase de sincronização diferencial. Neste ponto, a máquina virtual está protegida. Selecione a máquina virtual protegida para ver se algum disco foi excluído.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [executar um failover de teste](site-recovery-test-failover-to-azure.md).
