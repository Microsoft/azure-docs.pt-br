---
title: Backup e restauração de disco seletivo para máquinas virtuais do Azure
description: Neste artigo, saiba mais sobre o backup e a restauração de disco seletivo usando a solução de backup de máquina virtual do Azure.
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 38ead1591bf2ecadc8bfca5875ac1fa3e69d56ef
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806380"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Backup e restauração de disco seletivo para máquinas virtuais do Azure

O backup do Azure dá suporte ao backup de todos os discos (sistema operacional e dados) em uma VM juntas usando a solução de backup de máquina virtual. Agora, usando a funcionalidade de backup e restauração dos discos seletivos, você pode fazer backup de um subconjunto dos discos de dados em uma VM. Isso fornece uma solução eficiente e econômica para suas necessidades de backup e restauração. Cada ponto de recuperação contém apenas os discos que são incluídos na operação de backup. Isso permite ainda que você tenha um subconjunto de discos restaurados do ponto de recuperação fornecido durante a operação de restauração. Isso se aplica a ambas as restaurações de instantâneos e do cofre.

## <a name="scenarios"></a>Cenários

Essa solução é útil principalmente nos seguintes cenários:

1. Se você tiver dados críticos para fazer backup em apenas um disco ou em um subconjunto dos discos e não quiser fazer backup do restante dos discos anexados a uma VM para minimizar os custos de armazenamento de backup.
2. Se você tiver outras soluções de backup para parte de sua VM ou dados. Por exemplo, se você fizer o backup de seus bancos de dados ou de suas funções usando uma solução de backup de carga de trabalho diferente e quiser usar o backup de nível de VM do Azure para o restante dos dados ou discos para criar um sistema eficiente e robusto usando os melhores recursos disponíveis.

Usando o PowerShell ou CLI do Azure, você pode configurar o backup de disco seletivo da VM do Azure.  Usando um script, você pode incluir ou excluir discos de dados usando seus números de LUN.  Atualmente, a capacidade de configurar o backup de discos seletivos por meio do portal do Azure é limitada à opção **somente disco do so de backup** . Portanto, você pode configurar o backup de sua VM do Azure com o disco do sistema operacional e excluir todos os discos de dados anexados a ele.

>[!NOTE]
> O disco do sistema operacional é, por padrão, adicionado ao backup da VM e não pode ser excluído.

## <a name="using-azure-cli"></a>Usando a CLI do Azure

Verifique se você está usando AZ CLI versão 2.0.80 ou superior. Você pode obter a versão da CLI com este comando:

```azurecli
az --version
```

Entre na ID da assinatura onde reside o cofre dos serviços de recuperação e a VM:

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>Somente o nome do **resourcegroup** (não o objeto) correspondente ao cofre é necessário em cada comando abaixo.

### <a name="configure-backup-with-azure-cli"></a>Configurar o backup com o CLI do Azure

Durante a operação de configuração de proteção, você precisa especificar a configuração de lista de discos com um parâmetro de exclusão de **inclusão**  /   , fornecendo os números de LUN dos discos a serem incluídos ou excluídos no backup.

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

Se a VM não estiver no mesmo grupo de recursos que o cofre, o **resourcegroup** se referirá ao grupo de recursos em que o cofre foi criado. Em vez do nome da VM, forneça a ID da VM, conforme indicado abaixo.

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id --output tsv) --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>Modificar a proteção para as VMs já submetidas a backup com CLI do Azure

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Fazer backup somente do disco do sistema operacional durante a configuração de backup com o CLI do Azure

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Fazer backup somente do disco do sistema operacional durante a proteção de modificação com o CLI do Azure

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Restaurar discos com CLI do Azure

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Restaurar somente disco do sistema operacional com CLI do Azure

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Obter item protegido para obter detalhes de exclusão de disco com CLI do Azure

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Há um parâmetro **diskExclusionProperties** adicional adicionado ao item protegido, conforme mostrado abaixo:

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Obter trabalho de backup com CLI do Azure

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

Esse comando ajuda a obter os detalhes dos discos de backup e excluídos, conforme mostrado abaixo:

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>Listar pontos de recuperação com CLI do Azure

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

Isso fornece as informações do número de discos anexados e de backup na VM.

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Obter ponto de recuperação com CLI do Azure

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

Cada ponto de recuperação tem as informações dos discos incluídos e excluídos:

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Remover configurações de exclusão de disco e obter item protegido com CLI do Azure

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Ao executar esses comandos, você verá `"diskExclusionProperties": null` .

## <a name="using-powershell"></a>Usar o PowerShell

Verifique se você está usando Azure PowerShell versão 3.7.0 ou superior.

Durante a operação de configuração de proteção, você precisa especificar a configuração da lista de discos com um parâmetro de inclusão/exclusão, fornecendo os números de LUN dos discos a serem incluídos ou excluídos no backup.

### <a name="enable-backup-with-powershell"></a>Habilitar backup com o PowerShell

Por exemplo:

```azurepowershell
$disks = ("0","1")
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "rg-p-recovery_vaults" -Name "rsv-p-servers"
Get-AzRecoveryServicesBackupProtectionPolicy
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "P-Servers"
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -InclusionDisksList $disks -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExclusionDisksList $disks -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>Fazer backup somente do disco do sistema operacional durante a configuração de backup com o PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>Obter o objeto de item de backup a ser passado em modificar proteção com o PowerShell

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $Vault.ID -FriendlyName "V2VM"
```

Você precisa passar o objeto de **$Item** obtido acima para o parâmetro **– Item** nos cmdlets a seguir.

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>Modificar a proteção de VMs já submetidas a backup com o PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>Fazer backup somente do disco do sistema operacional durante a proteção de modificação com o PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>Redefinir a configuração de exclusão de disco com o PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ResetExclusionSettings -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>Restaurar discos seletivos com o PowerShell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [Strings]
```

### <a name="restore-only-os-disk-with-powershell"></a>Restaurar somente o disco do sistema operacional com o PowerShell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Usando o portal do Azure

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

Usando o portal do Azure, você pode exibir os discos incluídos e excluídos no painel detalhes de backup da VM e no painel detalhes do trabalho de backup.  Durante a restauração, ao selecionar o ponto de recuperação do qual restaurar, você pode exibir os discos de backup nesse ponto de recuperação.

Aqui você pode exibir os discos incluídos e excluídos para uma máquina virtual no portal do painel de detalhes de backup da VM:

![Exibir discos incluídos e excluídos do painel de detalhes de backup](./media/selective-disk-backup-restore/backup-details.png)

Aqui você pode exibir os discos incluídos e excluídos em um backup do painel de detalhes do trabalho:

![Exibir discos incluídos e excluídos do painel de detalhes do trabalho](./media/selective-disk-backup-restore/job-details.png)

Aqui você pode exibir os discos de backup durante a restauração, quando você seleciona o ponto de recuperação do qual restaurar:

![Exibir discos de backup durante a restauração](./media/selective-disk-backup-restore/during-restore.png)

A configuração da experiência de backup de discos seletivos para uma VM por meio da portal do Azure é limitada à opção **somente disco do so de backup** . Para usar o backup de discos seletivos em uma VM já incluída em backup ou para inclusão ou exclusão avançada de discos de dados específicos de uma VM, use o PowerShell ou o CLI do Azure.

>[!NOTE]
>Se os dados se estendem pelos discos, verifique se todos os discos dependentes estão incluídos no backup. Se você não fizer backup de todos os discos dependentes em um volume, durante a restauração, o volume que consiste em alguns discos não submetidos a backup não será criado.

### <a name="backup-os-disk-only-in-the-azure-portal"></a>Disco do so de backup somente no portal do Azure

Ao habilitar o backup usando portal do Azure, você pode escolher a opção **somente disco do so de backup** . Portanto, você pode configurar o backup de sua VM do Azure com o disco do sistema operacional e excluir todos os discos de dados anexados a ele.

![Configurar o backup somente para o disco do sistema operacional](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="using-azure-rest-api"></a>Usando a API REST do Azure

Você pode configurar o backup de VM do Azure com alguns discos selecionados ou pode modificar a proteção de uma VM existente para incluir/excluir alguns discos, conforme documentado [aqui](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup).

## <a name="selective-disk-restore"></a>Restauração de disco seletivo

A restauração de disco seletivo é uma funcionalidade adicional que você obtém ao habilitar o recurso de backup de discos seletivos. Com essa funcionalidade, você pode restaurar discos seletivos de todos os discos de backup em um ponto de recuperação. É mais eficiente e ajuda a economizar tempo em cenários em que você sabe quais discos precisam ser restaurados.

- O disco do sistema operacional é incluído por padrão no backup e na restauração da VM e não pode ser excluído.
- A restauração de disco seletivo tem suporte apenas para pontos de recuperação criados após a habilitação do recurso de exclusão de disco.
- Backups com a configuração de exclusão de disco **no** dão suporte apenas à opção de **restauração de disco** . Neste caso, não há suporte para a **restauração de VM** ou para substituir opções de restauração **existentes** .

![A opção de restaurar VM e substituir existente não está disponível durante a operação de restauração](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>Limitações

A funcionalidade de backup de discos seletivos não tem suporte para máquinas virtuais clássicas e máquinas virtuais criptografadas. Portanto, as VMs do Azure que são criptografadas com Azure Disk Encryption (ADE) usando o BitLocker para criptografia da VM do Windows e o recurso DM-cript para VMs do Linux não têm suporte.

As opções de restauração para **criar uma nova VM** e **substituir existentes** não têm suporte para a VM para a qual a funcionalidade de backup de discos seletivos está habilitada.

Atualmente, o backup de VM do Azure não dá suporte a VMs com ultra discos ou discos compartilhados anexados a eles. O backup de disco seletivo não pode ser usado nesses casos, o que exclui o disco e o backup da VM.

## <a name="billing"></a>Cobrança

O backup de máquina virtual do Azure segue o modelo de preços existente, explicado em detalhes [aqui](https://azure.microsoft.com/pricing/details/backup/).

O **custo da instância protegida (PI)** é calculado para o disco do sistema operacional somente se você optar por fazer backup usando a opção **somente disco do sistema operacional** .  Se você configurar o backup e selecionar pelo menos um disco de dados, o custo de PI será calculado para todos os discos anexados à VM. O **custo de armazenamento de backup** é calculado com base apenas nos discos incluídos e, portanto, você tem de economizar no custo de armazenamento. O **custo do instantâneo** é sempre calculado para todos os discos na VM (os discos incluídos e excluídos).

Se você tiver escolhido o recurso de CRR (restauração entre regiões), o [Preço da CRR](https://azure.microsoft.com/pricing/details/backup/) se aplicará ao custo de armazenamento de backup depois de excluir o disco.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-is-protected-instance-pi-cost-calculated-for-only-os-disk-backup-in-windows-and-linux"></a>Como o custo de instância protegida (PI) é calculado para apenas o backup de disco do so no Windows e no Linux?

O custo de PI é calculado com base no tamanho real (usado) da VM.

- Para o Windows: o cálculo de espaço usado se baseia na unidade que armazena o sistema operacional (que geralmente é C:).
- Para o Linux: o cálculo de espaço usado se baseia no dispositivo em que o sistema de arquivos raiz (/) está montado.

### <a name="i-have-configured-only-os-disk-backup-why-is-the-snapshot-happening-for-all-the-disks"></a>Configurei apenas o backup de disco do so, por que o instantâneo ocorre em todos os discos?

Os recursos de backup de disco seletivo permitem economizar no custo de armazenamento do cofre de backup, protegendo os discos incluídos que fazem parte do backup. No entanto, o instantâneo é tomado para todos os discos que estão anexados à VM. Portanto, o custo do instantâneo é sempre calculado para todos os discos na VM (os discos incluídos e excluídos). Para obter mais informações, consulte [cobrança](#billing).

### <a name="i-cant-configure-backup-for-the-azure-virtual-machine-by-excluding-ultra-disk-or-shared-disks-attached-to-the-vm"></a>Não consigo configurar o backup para a máquina virtual do Azure, excluindo discos de ultra disco ou compartilhados anexados à VM

O recurso de backup de disco seletivo é um recurso fornecido sobre a solução de backup de máquina virtual do Azure. Atualmente, o backup de VM do Azure não dá suporte a VMs com discos de ultra ou de disco compartilhado anexados a eles.

## <a name="next-steps"></a>Próximas etapas

- [Matriz de suporte para backup de VM do Azure](backup-support-matrix-iaas.md)
- [Perguntas frequentes-fazer backup de VMs do Azure](backup-azure-vm-backup-faq.yml)
