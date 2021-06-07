---
title: Restaurar arquivos do Azure com o PowerShell
description: Neste artigo, saiba como restaurar os arquivos do Azure usando o serviço de backup do Azure e o PowerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 60c9848e12de80bcafe4553a9e8f3e27e8876d41
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021377"
---
# <a name="restore-azure-files-with-powershell"></a>Restaurar arquivos do Azure com o PowerShell

Este artigo explica como restaurar um compartilhamento de arquivos inteiro, ou arquivos específicos, de um ponto de restauração criado pelo serviço de [backup do Azure](backup-overview.md) usando Azure PowerShell.

Você pode restaurar um compartilhamento de arquivos inteiro ou arquivos específicos no compartilhamento. Você pode restaurar para o local original ou para um local alternativo.

> [!WARNING]
> Verifique se a versão do PowerShell é atualizada para a versão mínima para ' AZ. Recoveryservices 2.6.0 ' para backups AFS. Para obter mais informações, consulte [a seção](backup-azure-afs-automation.md#important-notice-backup-item-identification) contorno do requisito para essa alteração.

>[!NOTE]
>O backup do Azure agora dá suporte à restauração de vários arquivos ou pastas para o local original ou alternativo usando o PowerShell. Consulte [esta seção](#restore-multiple-files-or-folders-to-original-or-alternate-location) do documento para saber como.

## <a name="fetch-recovery-points"></a>Buscar pontos de recuperação

Use [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) para listar todos os pontos de recuperação para o item de backup.

No script a seguir:

* A variável **$RP** é uma matriz de pontos de recuperação para o item de backup selecionado dos últimos sete dias.
* A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice **0**.
* Use a indexação de matriz padrão do PowerShell para selecionar o ponto de recuperação.
* No exemplo, **$rp[0]** seleciona o ponto de recuperação mais recente.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"
$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID
$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0] | fl
```

A saída é semelhante à seguinte.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

Depois que o ponto de recuperação relevante é selecionado, você restaura o compartilhamento de arquivos ou o arquivo para o local original ou para um local alternativo.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Restaurar um compartilhamento de arquivos do Azure para um local alternativo

Use o [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) para restaurar para o ponto de recuperação selecionado. Especifique esses parâmetros para identificar o local alternativo:

* **TargetStorageAccountName**: a conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **TargetFileShareName**: os compartilhamentos de arquivos dentro da conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **TargetFolder**: a pasta sob o compartilhamento de arquivos para o qual os dados são restaurados. Se for para restaurar o conteúdo do backup em uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **ResolveConflict**: instrução se houver um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

Execute o cmdlet com os parâmetros da seguinte maneira:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

O comando retorna um trabalho com uma ID a ser rastreada, como no exemplo a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Restaurar um arquivo do Azure para um local alternativo

Use o [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) para restaurar para o ponto de recuperação selecionado. Especifique esses parâmetros para identificar o local alternativo e para identificar exclusivamente o arquivo que você deseja restaurar.

* **TargetStorageAccountName**: a conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **TargetFileShareName**: os compartilhamentos de arquivos dentro da conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **TargetFolder**: a pasta sob o compartilhamento de arquivos para o qual os dados são restaurados. Se for para restaurar o conteúdo do backup em uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **SourceFilePath**: o caminho absoluto do arquivo a ser restaurado no compartilhamento de arquivos, como uma cadeia de caracteres. Esse caminho é o mesmo que foi usado no cmdlet **Get-AzStorageFile** do PowerShell.
* **SourceFileType**: se um diretório ou um arquivo está selecionado. Aceita **diretório** ou **arquivo**.
* **ResolveConflict**: instrução se houver um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

Os parâmetros adicionais (SourceFilePath e SourceFileType) estão relacionados somente ao arquivo individual que você deseja restaurar.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Esse comando retorna um trabalho com uma ID a ser rastreada, conforme mostrado na seção anterior.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Restaurar arquivos e compartilhamentos de arquivos do Azure para o local original

Ao restaurar para um local original, você não precisa especificar parâmetros relacionados ao destino e ao destino. Somente **ResolveConflict** deve ser fornecido.

### <a name="overwrite-an-azure-file-share"></a>Substituir um compartilhamento de arquivos do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>Substituir um arquivo do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Restaurar vários arquivos ou pastas para o local original ou alternativo

Use o comando [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) passando o caminho de todos os arquivos ou pastas que você deseja restaurar como um valor para o parâmetro **MultipleSourceFilePath** .

### <a name="restore-multiple-files"></a>Restaurar vários arquivos

No script a seguir, estamos tentando restaurar os arquivos de *FileSharePage.png* e *MyTestFile.txt* .

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>Restaurar vários diretórios

No script a seguir, estamos tentando restaurar o *zrs1_restore* e *restaurar* os diretórios.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("Restore","zrs1_restore")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType Directory -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

A saída será semelhante ao seguinte:

```output
WorkloadName         Operation         Status          StartTime                EndTime       JobID
------------         ---------         ------          ---------                -------       -----
azurefiles           Restore           InProgress      4/5/2020 8:01:24 AM                    cd36abc3-0242-44b1-9964-0a9102b74d57
```

Se você quiser restaurar vários arquivos ou pastas para o local alternativo, use os scripts acima especificando os valores de parâmetro relacionados ao local de destino, conforme explicado acima em [restaurar um arquivo do Azure para um local alternativo](#restore-an-azure-file-to-an-alternate-location).

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre como](restore-afs.md) restaurar arquivos do Azure no portal do Azure.
