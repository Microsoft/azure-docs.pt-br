---
title: Solucionar problemas de backup de compartilhamento de arquivos do Azure
description: Este artigo tem informações sobre a solução de problemas que ocorrem ao proteger seus compartilhamentos de arquivos no Azure.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4908b8ed97bad43d9d24427660a8691ee43d7eaf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89376971"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Solucionar problemas ao fazer backup de compartilhamentos de arquivos do Azure

Este artigo fornece informações de solução de problemas para resolver os problemas que você chega ao configurar o backup ou a restauração de compartilhamentos de arquivos do Azure usando o serviço de backup do Azure.

## <a name="common-configuration-issues"></a>Problemas comuns de configuração

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Não foi possível localizar minha conta de armazenamento para configurar o backup para o compartilhamento de arquivos do Azure

- Aguarde até a descoberta ser concluída.
- Verifique se algum compartilhamento de arquivos na conta de armazenamento já está protegido com outro cofre dos serviços de recuperação.

  >[!NOTE]
  >Todos os compartilhamentos de arquivos em uma conta de armazenamento podem ser protegidos somente em um cofre dos Serviços de Recuperação. Você pode usar [esse script](scripts/backup-powershell-script-find-recovery-services-vault.md) para localizar o cofre dos serviços de recuperação em que sua conta de armazenamento está registrada.

- Verifique se o compartilhamento de arquivos não está presente em nenhuma das contas de armazenamento sem suporte. Você pode consultar a [matriz de suporte para o backup do compartilhamento de arquivos do Azure](azure-file-share-support-matrix.md) para localizar as contas de armazenamento com suporte.
- Verifique se o comprimento combinado do nome da conta de armazenamento e o nome do grupo de recursos não excedem 84 caracteres no caso de novas contas de armazenamento e 77 caracteres no caso de contas de armazenamento clássicas.
- Verifique as configurações de firewall da conta de armazenamento para garantir que a opção de permitir que os serviços confiáveis da Microsoft acessem a conta de armazenamento esteja habilitada.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Erro no portal declara que a descoberta de contas de armazenamento falhou

Se você tiver uma assinatura de parceiro (habilitada para CSP), ignore o erro. Se sua assinatura não estiver habilitada para CSP e suas contas de armazenamento não puderem ser descobertas, contate o suporte.

### <a name="selected-storage-account-validation-or-registration-failed"></a>Falha na validação ou no registro da conta de armazenamento selecionada

Repita o registro. Se o problema persistir, contate o Suporte.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Não foi possível listar ou localizar compartilhamentos de arquivos na conta de armazenamento selecionada

- Verifique se a conta de armazenamento existe no grupo de recursos e não foi excluída ou movida após a última validação ou o registro no cofre.
- Verifique se o compartilhamento de arquivos que você está procurando proteger não foi excluído.
- Verifique se a conta de armazenamento é uma conta de armazenamento com suporte para backup de compartilhamento de arquivos. Você pode consultar a [matriz de suporte para o backup do compartilhamento de arquivos do Azure](azure-file-share-support-matrix.md) para localizar as contas de armazenamento com suporte.
- Verifique se o compartilhamento de arquivos já está protegido no mesmo cofre dos serviços de recuperação.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>A configuração do compartilhamento de arquivos de backup (ou a configuração da política de proteção) está falhando

- Tente a configuração novamente para ver se o problema persiste.
- Verifique se o compartilhamento de arquivos que você deseja proteger não foi excluído.
- Se você estiver tentando proteger vários compartilhamentos de arquivos ao mesmo tempo e alguns dos compartilhamentos de arquivos estiverem falhando, tente configurar o backup para os compartilhamentos de arquivos com falha novamente.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Não é possível excluir o cofre dos serviços de recuperação após desproteger um compartilhamento de arquivos

Na portal do Azure, abra seu **cofre**  >  **infraestrutura de backup**  >  **contas de armazenamento**. Selecione **Cancelar registro** para remover as contas de armazenamento do cofre dos serviços de recuperação.

>[!NOTE]
>Um cofre dos serviços de recuperação só pode ser excluído após o cancelamento do registro de todas as contas de armazenamento registradas no cofre.

## <a name="common-backup-or-restore-errors"></a>Erros comuns de backup ou restauração

>[!NOTE]
>Consulte [este documento](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) para garantir que você tenha permissões suficientes para executar operações de backup ou restauração.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound-a operação falhou porque o compartilhamento de arquivos não foi encontrado

Código de erro: FileShareNotFound

Mensagem de erro: falha na operação porque o compartilhamento de arquivos não foi encontrado

Verifique se o compartilhamento de arquivos que você está tentando proteger não foi excluído.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable-conta de armazenamento não encontrada ou sem suporte

Código de erro: UserErrorFileShareEndpointUnreachable

Mensagem de erro: conta de armazenamento não encontrada ou sem suporte

- Verifique se a conta de armazenamento existe no grupo de recursos e se não foi excluída ou removida do grupo de recursos após a última validação.

- Verifique se a conta de armazenamento é uma conta de armazenamento com suporte para backup de compartilhamento de arquivos.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached-você atingiu o limite máximo de instantâneos para este compartilhamento de arquivos; Você poderá levar mais uma vez as antigas expirarem

Código de erro: AFSMaxSnapshotReached

Mensagem de erro: você atingiu o limite máximo de instantâneos para este compartilhamento de arquivos; Você poderá levar mais uma vez as antigas expirarem.

- Esse erro pode ocorrer quando você cria vários backups sob demanda para um compartilhamento de arquivos.
- Há um limite de 200 instantâneos por compartilhamento de arquivos, incluindo aqueles feitos pelo backup do Azure. Os backups (ou instantâneos) agendados mais antigos são limpos automaticamente. Os backups (ou instantâneos) sob demanda devem ser excluídos se o limite máximo for atingido.

Exclua os backups sob demanda (instantâneos de compartilhamento de arquivo do Azure) no portal de Arquivos do Azure.

>[!NOTE]
> os pontos de recuperação são perdidos quando você exclui os instantâneos criados pelo Backup do Azure.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound-a operação falhou porque a conta de armazenamento especificada não existe mais

Código de erro: UserErrorStorageAccountNotFound

Mensagem de erro: a operação falhou porque a conta de armazenamento especificada não existe mais.

Verifique se a conta de armazenamento ainda existe e não foi excluída.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound-os detalhes da conta de armazenamento fornecidos estão incorretos

Código de erro: UserErrorDTSStorageAccountNotFound

Mensagem de erro: os detalhes da conta de armazenamento fornecidos estão incorretos.

Verifique se a conta de armazenamento ainda existe e não foi excluída.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound-o grupo de recursos não existe

Código de erro: UserErrorResourceGroupNotFound

Mensagem de erro: o grupo de recursos não existe

Selecione um grupo de recursos existente ou crie um grupo de recursos.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest-um trabalho de backup já está em andamento para este compartilhamento de arquivos

Código de erro: ParallelSnapshotRequest

Mensagem de erro: um trabalho de backup já está em andamento para este compartilhamento de arquivos.

- O backup de compartilhamento de arquivos não dá suporte a solicitações de instantâneo paralelo no mesmo compartilhamento de arquivos.

- Aguarde até que o trabalho de backup existente seja concluído e tente novamente. Se você não encontrar um trabalho de backup no cofre dos serviços de recuperação, verifique outros cofres dos serviços de recuperação na mesma assinatura.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling-falha no backup ou na restauração do compartilhamento de arquivos devido à limitação do serviço de armazenamento. Isso pode ocorrer porque o serviço de armazenamento está ocupado processando outras solicitações para a conta de armazenamento determinada

Código de erro: FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling

Mensagem de erro: falha no backup ou na restauração do compartilhamento de arquivos devido à limitação do serviço de armazenamento. Isso pode ser devido ao serviço de armazenamento estar ocupado processando outras solicitações para determinada conta de armazenamento.

Tente a operação de backup/restauração posteriormente.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound-compartilhamento de arquivos de destino não encontrado

Código de erro: TargetFileShareNotFound

Mensagem de erro: compartilhamento de arquivos de destino não encontrado.

- Verifique se a conta de armazenamento selecionada existe e se o compartilhamento de arquivos de destino não foi excluído.

- Verifique se a conta de armazenamento é uma conta de armazenamento com suporte para backup de compartilhamento de arquivos.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked-falha nos trabalhos de backup ou restauração devido à conta de armazenamento estar em estado bloqueado

Código de erro: UserErrorStorageAccountIsLocked

Mensagem de erro: falha nos trabalhos de backup ou restauração devido à conta de armazenamento estar em estado bloqueado.

Remova o bloqueio na conta de armazenamento ou use o **bloqueio de exclusão** em vez do bloqueio de **leitura** e repita a operação de backup ou restauração.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached-Recovery falhou porque o número de arquivos com falha é maior que o limite

Código de erro: DataTransferServiceCoFLimitReached

Mensagem de erro: falha na recuperação porque o número de arquivos com falha é maior que o limite.

- Os motivos da falha de recuperação são listados em um arquivo (caminho fornecido nos detalhes do trabalho). Resolva as falhas e repita a operação de restauração somente para os arquivos com falha.

- Motivos comuns para falhas de restauração de arquivo:

  - arquivos que falharam estão em uso no momento
  - um diretório com o mesmo nome que o arquivo com falha existe no diretório pai.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover-Recovery falhou, pois nenhum arquivo pôde ser recuperado

Código de erro: DataTransferServiceAllFilesFailedToRecover

Mensagem de erro: falha na recuperação, pois nenhum arquivo pôde ser recuperado.

- Os motivos da falha de recuperação são listados em um arquivo (caminho fornecido nos detalhes do trabalho). Aborde as falhas e repita as operações de restauração apenas para os arquivos com falha.

- Motivos comuns para falhas de restauração de arquivo:

  - arquivos que falharam estão em uso no momento
  - um diretório com o mesmo nome que o arquivo com falha existe no diretório pai.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>Falha na restauração de UserErrorDTSSourceUriNotValid porque um dos arquivos na origem não existe

Código de erro: DataTransferServiceSourceUriNotValid

Mensagem de erro: falha na restauração porque um dos arquivos na origem não existe.

- Os itens selecionados não estão presentes nos dados de ponto de recuperação. Para recuperar os arquivos, forneça a lista correta de arquivos.
- O instantâneo de compartilhamento de arquivos que corresponde ao ponto de recuperação é excluído manualmente. Selecione um ponto de recuperação diferente e repita a operação de restauração.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked-um trabalho de recuperação está em processo para o mesmo destino

Código de erro: UserErrorDTSDestLocked

Mensagem de erro: um trabalho de recuperação está em processo para o mesmo destino.

- O backup de compartilhamento de arquivos não dá suporte à recuperação paralela para o mesmo compartilhamento de arquivos de destino.

- Aguarde até que recuperação existente seja concluída e tente novamente. Se você não encontrar um trabalho de recuperação no cofre dos serviços de recuperação, verifique outros cofres dos serviços de recuperação na mesma assinatura.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>Falha na operação UserErrorTargetFileShareFull-Restore porque o compartilhamento de arquivos de destino está cheio

Código de erro: UserErrorTargetFileShareFull

Mensagem de erro: falha na operação de restauração porque o compartilhamento de arquivos de destino está cheio.

Aumente a cota de tamanho do compartilhamento de arquivos de destino para acomodar os dados de restauração e repita a operação de restauração.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient-o compartilhamento de arquivos de destino não tem cota de tamanho de armazenamento suficiente para restauração

Código de erro: UserErrorTargetFileShareQuotaNotSufficient

Mensagem de erro: o compartilhamento de arquivos de destino não tem cota de tamanho de armazenamento suficiente para restauração

Aumente a cota de tamanho do compartilhamento de arquivos de destino para acomodar os dados de restauração e repita a operação

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>Sincronização de Arquivos operação de PreRestoreFailed falhou porque ocorreu um erro ao executar operações de pré-restauração em Sincronização de Arquivos recursos de serviço associados ao compartilhamento de arquivos de destino

Código de erro: Sincronização de Arquivos PreRestoreFailed

Mensagem de erro: falha na operação de restauração porque ocorreu um erro ao executar operações de pré-restauração em Sincronização de Arquivos recursos de serviço associados ao compartilhamento de arquivos de destino.

Tente restaurar os dados em um momento posterior. Se o problema persistir, contate o Suporte da Microsoft.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>A detecção de alteração do serviço de Sincronização de Arquivos do Azure de AzureFileSyncChangeDetectionInProgress está em andamento para o compartilhamento de arquivos de destino. A detecção de alteração foi disparada por uma restauração anterior para o compartilhamento de arquivos de destino

Código de erro: AzureFileSyncChangeDetectionInProgress

Mensagem de erro: a detecção de alteração do serviço de Sincronização de Arquivos do Azure está em andamento para o compartilhamento de arquivos de destino. A detecção de alteração foi disparada por uma restauração anterior para o compartilhamento de arquivos de destino.

Use um compartilhamento de arquivos de destino diferente. Como alternativa, você pode aguardar a conclusão da detecção de alteração do serviço de Sincronização de Arquivos do Azure para o compartilhamento de arquivos de destino antes de tentar novamente a restauração.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored-um ou mais arquivos não puderam ser recuperados com êxito. Para obter mais informações, verifique a lista de arquivos com falha no caminho fornecido acima

Código de erro: UserErrorAFSRecoverySomeFilesNotRestored

Mensagem de erro: um ou mais arquivos não puderam ser recuperados com êxito. Para obter mais informações, verifique a lista de arquivos com falha no caminho fornecido acima.

- Os motivos da falha de recuperação são listados no arquivo (caminho fornecido nos detalhes do trabalho). Resolva os motivos e repita a operação de restauração somente para os arquivos com falha.
- Motivos comuns para falhas de restauração de arquivo:

  - arquivos que falharam estão em uso no momento
  - um diretório com o mesmo nome que o arquivo com falha existe no diretório pai.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound-instantâneo de compartilhamento de arquivos do Azure correspondente ao ponto de recuperação não pode ser encontrado

Código de erro: UserErrorAFSSourceSnapshotNotFound

Mensagem de erro: não foi possível encontrar o instantâneo de compartilhamento de arquivos do Azure correspondente ao ponto de recuperação

- Verifique se o instantâneo de compartilhamento de arquivos, correspondente ao ponto de recuperação que você está tentando usar para recuperação, ainda existe.

  >[!NOTE]
  >Se você excluir um instantâneo de compartilhamento de arquivos que foi criado pelo backup do Azure, os pontos de recuperação correspondentes se tornarão inutilizáveis. Recomendamos não excluir instantâneos para garantir a recuperação garantida.

- Tente selecionar outro ponto de restauração para recuperar os dados.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget-outro trabalho de restauração está em andamento no mesmo compartilhamento de arquivos de destino

Código de erro: UserErrorAnotherRestoreInProgressOnSameTarget

Mensagem de erro: outro trabalho de restauração está em andamento no mesmo compartilhamento de arquivos de destino

Use um compartilhamento de arquivos de destino diferente. Como alternativa, você pode cancelar ou aguardar a conclusão da outra restauração.

## <a name="common-modify-policy-errors"></a>Erros comuns de política de modificação

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation-outra operação de configuração de proteção está em andamento para este item

Código de erro: BMSUserErrorConflictingProtectionOperation

Mensagem de erro: outra operação de configuração de proteção está em andamento para este item.

Aguarde a conclusão da operação de modificação anterior da política e tente novamente mais tarde.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked-outra operação está em andamento no item selecionado

Código de erro: BMSUserErrorObjectLocked

Mensagem de erro: outra operação está em andamento no item selecionado.

Aguarde a conclusão da outra operação em andamento e tente novamente mais tarde.

## <a name="common-soft-delete-related-errors"></a>Erros relacionados à exclusão reversível comum

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState-este ponto de restauração não está disponível porque o instantâneo associado a esse ponto está em um compartilhamento de arquivos que está no estado de exclusão reversível

Código de erro: UserErrorRestoreAFSInSoftDeleteState

Mensagem de erro: esse ponto de restauração não está disponível, pois o instantâneo associado a esse ponto está em um compartilhamento de arquivo que está em estado de exclusão reversível.

Não é possível executar uma operação de restauração quando o compartilhamento de arquivos está no estado de exclusão reversível. Desexclua o compartilhamento de arquivos do portal do arquivo ou use o [script undelete](scripts/backup-powershell-script-undelete-file-share.md) e tente restaurar.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState-os pontos de restauração listados não estão disponíveis, pois o compartilhamento de arquivos associado que contém os instantâneos do ponto de restauração foi excluído permanentemente

Código de erro: UserErrorRestoreAFSInDeleteState

Mensagem de erro: os pontos de restauração listados não estão disponíveis, pois o compartilhamento de arquivos associado que contém os instantâneos do ponto de restauração foi excluído permanentemente.

Verifique se o compartilhamento de arquivos com backup foi excluído. Se ele estava em um estado de exclusão reversível, verifique se o período de retenção de exclusão flexível está acima e se não foi recuperado novamente. Em qualquer um desses casos, você perderá todos os instantâneos permanentemente e não poderá recuperar os dados.

>[!NOTE]
> Recomendamos que você não exclua o compartilhamento de arquivos de backup ou, se ele estiver no estado de exclusão reversível, não exclua antes de o período de retenção de exclusão reversível terminar, para evitar a perda de todos os pontos de restauração.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState-backup falhou porque o compartilhamento de arquivos do Azure está em estado de exclusão reversível

Código de erro: UserErrorBackupAFSInSoftDeleteState

Mensagem de erro: falha de backup porque o compartilhamento de arquivos do Azure está em estado de exclusão reversível

Restaurar o compartilhamento de arquivos do **portal de arquivos** ou usar o [script de reexclusão](scripts/backup-powershell-script-undelete-file-share.md) para continuar o backup e impedir a exclusão permanente de dados.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState-backup falhou porque o compartilhamento de arquivos do Azure associado é excluído permanentemente

Código de erro: UserErrorBackupAFSInDeleteState

Mensagem de erro: falha de backup porque o compartilhamento de arquivos do Azure associado é excluído permanentemente

Verifique se o compartilhamento de arquivos com backup é excluído permanentemente. Em caso afirmativo, pare o backup do compartilhamento de arquivos para evitar falhas de backup repetidas. Para saber como interromper a proteção, consulte [interromper a proteção para o compartilhamento de arquivos do Azure](./manage-afs-backup.md#stop-protection-on-a-file-share)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o backup de compartilhamentos de arquivos do Azure, confira:

- [Backup de compartilhamentos de arquivos do Azure](backup-afs.md)
- [Perguntas frequentes sobre backup de compartilhamentos de arquivos do Azure](backup-azure-files-faq.md)
