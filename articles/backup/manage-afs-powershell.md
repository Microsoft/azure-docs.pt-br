---
title: Gerenciar backups de compartilhamento de arquivos do Azure com o PowerShell
description: Saiba como usar o PowerShell para gerenciar e monitorar compartilhamentos de arquivos do Azure submetidos a backup pelo serviço de backup do Azure.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e2f07e56fb9a8715b1b53165ab5f4b45b4e20ccb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89000219"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Gerenciar backups de compartilhamento de arquivos do Azure com o PowerShell

Este artigo descreve como usar Azure PowerShell para gerenciar e monitorar os compartilhamentos de arquivos do Azure cujo backup é feito pelo serviço de backup do Azure.

> [!WARNING]
> Verifique se a versão do PowerShell é atualizada para a versão mínima para ' AZ. Recoveryservices 2.6.0 ' para backups AFS. Para obter mais detalhes, consulte [a seção](backup-azure-afs-automation.md#important-notice-backup-item-identification) contorno do requisito para essa alteração.

## <a name="modify-the-protection-policy"></a>Modificar a política de proteção

Para alterar a política usada para fazer backup do compartilhamento de arquivos do Azure, use [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Especifique o item de backup relevante e a nova política de backup.

O exemplo a seguir altera a política de proteção de **testAzureFS** de **dailyafs** para **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Acompanhar tarefas de backup e restauração

As operações de backup e restauração sob demanda retornam um trabalho junto com uma ID, conforme mostrado quando você [executa um backup sob demanda](backup-azure-afs-automation.md#trigger-an-on-demand-backup). Use o cmdlet [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) para acompanhar o progresso e os detalhes do trabalho.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>Interromper a proteção em um compartilhamento de arquivo

Há duas maneiras de interromper a proteção de compartilhamentos de arquivos do Azure:

* Parar todos os trabalhos de backup futuros e *excluir* todos os pontos de recuperação
* Parar todos os trabalhos de backup futuros, mas *deixar* os pontos de recuperação

Pode haver um custo associado à saída dos pontos de recuperação no armazenamento, pois os instantâneos subjacentes criados pelo backup do Azure serão mantidos. No entanto, o benefício de deixar os pontos de recuperação é que você pode restaurar o compartilhamento de arquivos posteriormente, se desejar. Para obter informações sobre o custo de deixar os pontos de recuperação, confira os [detalhes de preços](https://azure.microsoft.com/pricing/details/storage/files/). Se você optar por excluir todos os pontos de recuperação, não poderá restaurar o compartilhamento de arquivos.

## <a name="stop-protection-and-retain-recovery-points"></a>Interromper a proteção e manter os pontos de recuperação

Para interromper a proteção enquanto mantém os dados, use o cmdlet [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) .

O exemplo a seguir interrompe a proteção para o compartilhamento de arquivos *afsfileshare* , mas retém todos os pontos de recuperação:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

O atributo ID do trabalho na saída corresponde à ID do trabalho que é criado pelo serviço de backup para a operação "parar proteção". Para acompanhar o status do trabalho, use o cmdlet [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) .

## <a name="stop-protection-without-retaining-recovery-points"></a>Interromper a proteção sem reter pontos de recuperação

Para interromper a proteção sem reter pontos de recuperação, use o cmdlet [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) e adicione o parâmetro **-RemoveRecoveryPoints** .

O exemplo a seguir interrompe a proteção para o compartilhamento de arquivos *afsfileshare* sem reter os pontos de recuperação:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>Próximas etapas

[Saiba como](manage-afs-backup.md) gerenciar backups de compartilhamento de arquivos do Azure no portal do Azure.
