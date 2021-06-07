---
title: Restaurar Managed Disks do Azure via Azure PowerShell
description: Saiba como restaurar Managed Disks do Azure usando o Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 0ddf552947c39692ea01d0dea7e67f147d754fcc
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630317"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Restaurar Managed Disks do Azure usando Azure PowerShell

Este artigo explica como restaurar [Managed disks do Azure](../virtual-machines/managed-disks-overview.md) de um ponto de restauração criado pelo backup do Azure.

Atualmente, a opção de recuperação de Original-Location (OLR) de restauração substituindo o disco de origem existente do qual os backups foram feitos não tem suporte. Você pode restaurar de um ponto de recuperação para criar um novo disco no mesmo grupo de recursos do disco de origem do qual os backups foram feitos ou em qualquer outro grupo de recursos. Isso é conhecido como ALR (recuperação de Alternate-Location) e isso ajuda a manter o disco de origem e o disco restaurado (novo).

Neste artigo, você aprenderá a:

- Restaurar para criar um novo disco

- Acompanhar o status da operação de restauração

Iremos nos referir a um cofre de backup existente "TestBkpVault" no grupo de recursos "testBkpVaultRG" nos exemplos

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Restaurar para criar um novo disco

### <a name="setting-up-permissions"></a>Configurando as permissões

O cofre de backup usa identidade gerenciada para acessar outros recursos do Azure. Para restaurar do backup, a identidade gerenciada do cofre de backup requer um conjunto de permissões no grupo de recursos em que o disco deve ser restaurado.

O cofre de backup usa uma identidade gerenciada atribuída pelo sistema, que é restrita a um por recurso e está vinculada ao ciclo de vida deste recurso. Você pode conceder permissões para a identidade gerenciada usando o controle de acesso baseado em função do Azure (RBAC do Azure). A identidade gerenciada é uma entidade de serviço de um tipo especial que pode ser usada apenas com recursos do Azure. Saiba mais sobre [identidades gerenciadas](../active-directory/managed-identities-azure-resources/overview.md).

Atribua as permissões relevantes para a identidade gerenciada atribuída pelo sistema do cofre no grupo de recursos de destino em que os discos serão restaurados/criados conforme mencionado [aqui](restore-managed-disks.md#restore-to-create-a-new-disk).

### <a name="fetching-the-relevant-recovery-point"></a>Buscando o ponto de recuperação relevante

Busque todas as instâncias usando o comando [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) e identifique a instância relevante.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Você também pode usar **AZ. Resourcegraph** e o comando [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) para pesquisar entre instâncias em muitos cofres e assinaturas.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

Depois que a instância for identificada, busque o ponto de recuperação relevante.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>Preparando a solicitação de restauração

Construa a ID do ARM do novo disco a ser criado com o grupo de recursos de destino, ao qual as permissões foram atribuídas, conforme detalhado [acima](#setting-up-permissions), e o nome do disco necessário. Por exemplo, um disco pode ser nomeado **PSTestDisk2** em um grupo de recursos **targetrg** com uma assinatura diferente.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Use o comando [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) para preparar a solicitação de restauração com todos os detalhes relevantes.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>Disparar a restauração

Use o comando [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) para disparar a restauração com a solicitação preparada acima.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Trabalho de acompanhamento

Acompanhe todos os trabalhos usando o comando [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) . Você pode listar todos os trabalhos e buscar um detalhe de trabalho específico.

Você também pode usar **AZ. ResourceGraph** para acompanhar todos os trabalhos em todos os cofres de backup. Use o comando [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) para obter o trabalho relevante, que pode estar em qualquer cofre de backup.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Próximas etapas

- [FAQ do backup em disco do Azure](disk-backup-faq.md)
