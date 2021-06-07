---
title: Solucionando problemas de falhas de backup no backup em disco do Azure
description: Saiba como solucionar problemas de falhas de backup no backup em disco do Azure
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: f6e9bcbbd618cf820ce91f8a3092d5be4aa045cb
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107237"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup"></a>Solucionando problemas de falhas de backup no backup em disco do Azure

Este artigo fornece informações de solução de problemas de backup e restauração enfrentados com o disco do Azure. Para obter mais informações sobre a disponibilidade da região de [backup em disco do Azure](disk-backup-overview.md) , cenários e limitações com suporte, consulte a [matriz de suporte](disk-backup-support-matrix.md).

## <a name="common-issues-faced-with-azure-disk-backup"></a>Problemas comuns enfrentados com o backup em disco do Azure

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>Código de erro: UserErrorSnapshotRGSubscriptionMismatch

Mensagem de erro: assinatura inválida selecionada para armazenamento de dados de instantâneo

Ação recomendada: discos e instantâneos de disco são armazenados na mesma assinatura. Você pode escolher qualquer grupo de recursos para armazenar os instantâneos de disco na assinatura. Selecione a mesma assinatura do disco de origem. Para obter mais informações, consulte a [matriz de suporte](disk-backup-support-matrix.md).

### <a name="error-code-usererrorsnapshotrgnotfound"></a>Código de erro: UserErrorSnapshotRGNotFound

Mensagem de erro: não foi possível executar a operação porque o grupo de recursos do repositório de dados de instantâneo não existe.

Ação recomendada: Crie o grupo de recursos e forneça as permissões necessárias nele. Para obter mais informações, consulte [Configurar backup](backup-managed-disks.md#configure-backup).

### <a name="error-code-usererrormanageddisknotfound"></a>Código de erro: UserErrorManagedDiskNotFound

Mensagem de erro: não foi possível executar a operação porque o disco gerenciado não existe mais.

Ação recomendada: os backups continuarão a falhar, pois o disco de origem pode ser excluído ou movido para um local diferente. Use o ponto de restauração existente para restaurar o disco se ele for excluído por engano. Se o disco for movido para um local diferente, configure o backup para o disco.

### <a name="error-code-usererrornotenoughpermissionondisk"></a>Código de erro: UserErrorNotEnoughPermissionOnDisk

Mensagem de erro: o serviço de backup do Azure requer permissões adicionais no disco para executar esta operação.

Ação recomendada: conceda à identidade gerenciada do cofre de backup as permissões apropriadas no disco. Consulte [a documentação](backup-managed-disks.md) para entender quais permissões são exigidas pela identidade gerenciada do cofre de backup e como fornecê-las.

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>Código de erro: UserErrorNotEnoughPermissionOnSnapshotRG

Mensagem de erro: o serviço de backup do Azure requer permissões adicionais no grupo de recursos de armazenamento de dados de instantâneo para executar esta operação.

Ação recomendada: conceda à identidade gerenciada do cofre de backup as permissões apropriadas no grupo de recursos de armazenamento de dados de instantâneo. O grupo de recursos de armazenamento de dados de instantâneo é o local onde os instantâneos de disco são armazenados. Consulte [a documentação](backup-managed-disks.md) para entender qual é o grupo de recursos, quais permissões são necessárias para a identidade gerenciada do cofre de backup e como fornecê-lo.

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>Código de erro: UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

Mensagem de erro: disco inválido ou o serviço de backup do Azure requer permissões adicionais no disco para executar esta operação

Ação recomendada: os backups continuarão a falhar, pois o disco de origem pode ser excluído ou movido para um local diferente. Use o ponto de restauração existente para restaurar o disco se ele for excluído por engano. Se o disco for movido para um local diferente, configure o backup para o disco. Se o disco não for excluído ou movido, conceda à identidade gerenciada do cofre de backup as permissões apropriadas no disco. Consulte [a documentação](backup-managed-disks.md) para entender quais permissões são exigidas pela identidade gerenciada do cofre de backup e como fornecê-las.

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>Código de erro: UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

Mensagem de erro: não foi possível executar a operação porque o grupo de recursos do repositório de dados de instantâneo não existe mais. Ou o serviço de backup do Azure requer permissões adicionais no grupo de recursos de armazenamento de dados de instantâneo para realizar essa operação.

Ação recomendada: Crie um grupo de recursos e conceda à identidade gerenciada do cofre de backup as permissões apropriadas no grupo de recursos de armazenamento de dados de instantâneo. O grupo de recursos de armazenamento de dados de instantâneo é o local onde os instantâneos de disco são armazenados. Consulte [a documentação](backup-managed-disks.md) para entender o que é o grupo de recursos, quais permissões são necessárias para a identidade gerenciada do cofre de backup e como fornecê-lo.

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>Código de erro: UserErrorDiskBackupAuthorizationFailed

Mensagem de erro: a identidade gerenciada do cofre de backup não tem as permissões necessárias para executar esta operação.

Ação recomendada: conceda à identidade gerenciada do cofre de backup as permissões apropriadas no disco para fazer backup e no grupo de recursos de armazenamento de dados de instantâneo em que os instantâneos são armazenados. Consulte [a documentação](backup-managed-disks.md) para entender quais permissões são exigidas pela identidade gerenciada do cofre de backup e como fornecê-las.

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>Código de erro: UserErrorSnapshotRGOrMSIPermissionsNotPresent

Mensagem de erro: não foi possível executar a operação porque o grupo de recursos do repositório de dados de instantâneo não existe mais. Ou, o serviço de backup do Azure requer permissões adicionais no grupo de recursos de armazenamento de dados de instantâneo para realizar essa operação.

Ação recomendada: Crie o grupo de recursos e conceda à identidade gerenciada do cofre de backup as permissões apropriadas no grupo de recursos de armazenamento de dados de instantâneo. O grupo de recursos de armazenamento de dados de instantâneo é o local onde os instantâneos são armazenados. Consulte [a documentação](backup-managed-disks.md) para entender o que é o grupo de recursos, quais permissões são necessárias para a identidade gerenciada do cofre de backup e como fornecê-lo.

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>Código de erro: UserErrorOperationalStoreParametersNotProvided

Mensagem de erro: não foi possível executar a operação porque o parâmetro do grupo de recursos do repositório de dados de instantâneo não foi fornecido

Ação recomendada: forneça o parâmetro do grupo de recursos do armazenamento de dados do instantâneo. O grupo de recursos de armazenamento de dados de instantâneo é o local onde os instantâneos de disco são armazenados. Para obter mais informações, consulte [a documentação](backup-managed-disks.md).

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>Código de erro: UserErrorInvalidOperationalStoreResourceGroup

Mensagem de erro: o grupo de recursos do repositório de dados de instantâneo fornecido é inválido

Ação recomendada: forneça um grupo de recursos válido para o armazenamento de dados de instantâneo. O grupo de recursos de armazenamento de dados de instantâneo é o local onde os instantâneos de disco são armazenados. Para obter mais informações, consulte [a documentação](backup-managed-disks.md).

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>Código de erro: UserErrorDiskBackupDiskTypeNotSupported

Mensagem de erro: tipo de disco sem suporte

Ação recomendada: consulte [a matriz de suporte](disk-backup-support-matrix.md) em cenários e limitações sem suporte.

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>Código de erro: UserErrorSameNameDiskAlreadyExists

Mensagem de erro: não foi possível restaurar porque já existe um disco com o mesmo nome no grupo de recursos de destino selecionado

Ação recomendada: forneça um nome de disco diferente para restauração. Para obter mais informações, consulte [restaurar Managed disks do Azure](restore-managed-disks.md).

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>Código de erro: UserErrorRestoreTargetRGNotFound

Mensagem de erro: falha na operação porque o grupo de recursos de destino não existe.

Ação recomendada: forneça um grupo de recursos válido para restaurar. Para obter mais informações, consulte [restaurar Managed disks do Azure](restore-managed-disks.md).

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>Código de erro: UserErrorNotEnoughPermissionOnTargetRG

Mensagem de erro: o serviço de backup do Azure requer permissões adicionais no grupo de recursos de destino para executar esta operação.

Ação recomendada: conceda à identidade gerenciada do cofre de backup as permissões apropriadas no grupo de recursos de destino. O grupo de recursos de destino é o local selecionado onde o disco deve ser restaurado. Consulte a [documentação de restauração](restore-managed-disks.md) para entender quais permissões são necessárias para a identidade gerenciada do cofre de backup e como fornecê-las.

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>Código de erro: UserErrorSubscriptionDiskQuotaLimitReached

Mensagem de erro: a operação falhou porque o limite máximo de cota de disco foi atingido na assinatura.

Ação recomendada: consulte a [assinatura do Azure e os limites de serviço e a documentação da cota](../azure-resource-manager/management/azure-subscription-service-limits.md) ou entre em contato com suporte da Microsoft para obter mais diretrizes.

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>Código de erro: UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

Mensagem de erro: falha na operação porque o grupo de recursos de destino não existe. Ou o serviço de backup do Azure requer permissões adicionais no grupo de recursos de destino para executar esta operação.

Ação recomendada: forneça um grupo de recursos válido para restaurar e conceda à identidade gerenciada do cofre de backup as permissões apropriadas no grupo de recursos de destino. O grupo de recursos de destino é o local selecionado onde o disco deve ser restaurado. Consulte a [documentação de restauração](restore-managed-disks.md) para entender quais permissões são necessárias para a identidade gerenciada do cofre de backup e como fornecê-las.

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>Código de erro: UserErrorDESKeyVaultKeyDisabled

Mensagem de erro: a chave do Key Vault usada para o conjunto de criptografia de disco não está no estado habilitado.

Ação recomendada: habilite a chave do Key Vault usada para o conjunto de criptografia de disco. Consulte as limitações na matriz de [suporte](disk-backup-support-matrix.md).

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>Código de erro: UserErrorMSIPermissionsNotPresentOnDES

Mensagem de erro: o serviço de backup do Azure precisa de permissão para acessar o conjunto de criptografia de disco usado com o disco.

Ação recomendada: forneça acesso de leitor à identidade gerenciada do cofre de backup para o conjunto de criptografia de disco (DES).

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>Código de erro: UserErrorDESKeyVaultKeyNotAvailable

Mensagem de erro: a chave do Key Vault usada para o conjunto de criptografia de disco não está disponível.

Ação recomendada: Verifique se a chave do Key Vault usada para o conjunto de criptografia de disco não está desabilitada ou excluída.

### <a name="error-code-usererrordisksnapshotnotfound"></a>Código de erro: UserErrorDiskSnapshotNotFound

Mensagem de erro: o instantâneo do disco para este ponto de restauração foi excluído.

Ação recomendada: os instantâneos são armazenados no grupo de recursos de armazenamento de dados de instantâneo em sua assinatura. É possível que o instantâneo relacionado ao ponto de restauração selecionado possa ter sido excluído ou movido desse grupo de recursos. Considere usar outro ponto de recuperação para restaurar. Além disso, siga as diretrizes recomendadas para escolher o grupo de recursos de instantâneo mencionado na [documentação de restauração](restore-managed-disks.md).

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>Código de erro: UserErrorSnapshotMetadataNotFound

Mensagem de erro: os metadados de instantâneo de disco para este ponto de restauração foram excluídos

Ação recomendada: considere usar outro ponto de recuperação para restaurar. Para obter mais informações, consulte a [documentação de restauração](restore-managed-disks.md).

### <a name="error-code-backupagentpluginhostvalidateprotectionerror"></a>Código de erro: BackupAgentPluginHostValidateProtectionError

Mensagem de erro: o backup em disco ainda não está disponível na região do cofre de backup sob a qual a proteção de configuração está sendo tentada.

Ação recomendada: o cofre de backup deve estar em uma região com suporte. Para disponibilidade de região, consulte a [matriz de suporte](disk-backup-support-matrix.md).

### <a name="error-code-usererrordppdatasourcealreadyhasbackupinstance"></a>Código de erro: UserErrorDppDatasourceAlreadyHasBackupInstance

Mensagem de erro: o disco que você está tentando configurar o backup já está sendo protegido. O disco já está associado a uma instância de backup em um cofre de backup.

Ação recomendada: este disco já está associado a uma instância de backup em um cofre de backup. Se você quiser proteger novamente esse disco, exclua a instância de backup do cofre de backup no qual ele está protegido e proteja novamente o disco em qualquer outro cofre.

### <a name="error-code-usererrordppdatasourcealreadyprotected"></a>Código de erro: UserErrorDppDatasourceAlreadyProtected

Mensagem de erro: o disco que você está tentando configurar o backup já está sendo protegido. O disco já está associado a uma instância de backup em um cofre de backup.

Ação recomendada: este disco já está associado a uma instância de backup em um cofre de backup. Se você quiser proteger novamente esse disco, exclua a instância de backup do cofre de backup no qual ele está protegido e proteja novamente o disco em qualquer outro cofre.

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>Código de erro: UserErrorMaxConcurrentOperationLimitReached

Mensagem de erro: não é possível iniciar a operação porque o número máximo de backups simultâneos permitidos foi atingido.

Ação recomendada: Aguarde até que o backup anterior em execução seja concluído.

## <a name="next-steps"></a>Próximas etapas

- [Matriz de suporte de Backup de Disco do Azure](disk-backup-support-matrix.md)