---
title: Suporte à camada de arquivo morto (versão prévia)
description: Saiba mais sobre o suporte de camada de arquivo morto para o backup do Azure
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 322bc9d7e2160cc9156c793859b9fda833b3df09
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563966"
---
# <a name="archive-tier-support-preview"></a>Suporte à camada de arquivo morto (versão prévia)

Os clientes dependem do backup do Azure para armazenar dados de backup, incluindo seus dados de backup de redefinição de Long-Term (EPD) com necessidades de retenção sendo definidas pelas regras de conformidade da organização. Na maioria dos casos, os dados de backup mais antigos raramente são acessados e são armazenados apenas para necessidades de conformidade.

O backup do Azure dá suporte ao backup de pontos de retenção de longo prazo na camada de arquivo morto, além dos instantâneos e da camada Standard.

## <a name="scope-for-preview"></a>Escopo para visualização

Cargas de trabalho com suporte:

- Máquinas virtuais do Azure
  - Somente pontos de recuperação mensais e anuais. Não há suporte para pontos de recuperação diários e semanais.
  - Idade >= 3 meses na camada Vault-Standard
  - >de retenção restantes = 6 meses
  - Nenhuma dependência diária e semanal ativa
- SQL Server em máquinas virtuais do Azure
  - Somente pontos de recuperação completos. Não há suporte para logs e diferenciais.
  - Idade >= 45 dias na camada Vault-Standard
  - >de retenção restantes = 6 meses
  - Sem dependências

Clientes com suporte:

- O recurso é fornecido usando o PowerShell

>[!NOTE]
>O suporte de camada de arquivo para VMs do Azure e SQL Server em VMs do Azure está em visualização pública limitada com inscrições limitadas. Para se inscrever para obter suporte para arquivamento, use este [link](https://aka.ms/ArchivePreviewInterestForm).

## <a name="get-started-with-powershell"></a>Introdução ao PowerShell

1. Execute o seguinte comando no PowerShell:
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.0.0-preview -AllowPrerelease -force
    ```

1. Conecte-se ao Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
1. Entre em sua assinatura:

   `Set-AzContext -Subscription "SubscriptionName"`

1. Obtenha o cofre:

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. Obter a lista de itens de backup:

    `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM/AzureWorkload" -WorkloadType "AzureVM/MSSQL"`

1. Obter o item de backup.

    - Para máquinas virtuais do Azure:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - Para SQL Server em máquinas virtuais do Azure:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

## <a name="use-powershell"></a>Usar o PowerShell

### <a name="check-archivable-recovery-points"></a>Verificar pontos de recuperação arquiváveis

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm  -IsReadyForMove $true -TargetTier VaultArchive
```

Isso listará todos os pontos de recuperação associados a um item de backup específico que estão prontos para serem movidos para o arquivo morto.

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>Verifique por que um ponto de recuperação não pode ser movido para o arquivo morto

```azurepowershell
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Em que `$rp[0]` é o ponto de recuperação para o qual você deseja verificar por que ele não é arquivável.

Saída de exemplo:

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Verificar o conjunto recomendado de pontos arquiváveis (somente para VMs do Azure)

Os pontos de recuperação associados a uma máquina virtual são incrementais por natureza. Quando um ponto de recuperação específico é movido para o arquivo morto, ele é convertido em um backup completo e, em seguida, movido para o arquivo morto. Portanto, a economia de custo associada à mudança para o arquivamento depende da rotatividade da fonte de dados.

Portanto, o backup do Azure surgiu com um conjunto recomendado de pontos de recuperação que podem resultar em economia de custos se forem movidos juntos.

>[!NOTE]
>A economia de custos depende de vários motivos e pode não ser a mesma para duas instâncias.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>Mover para o arquivo morto

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[2] -SourceTier VaultStandard -DestinationTier VaultArchive
```

Esse comando move um ponto de recuperação arquivável para arquivo morto. Ele retorna um trabalho que pode ser usado para controlar a operação de movimentação tanto do portal quanto do PowerShell.

### <a name="view-archived-recovery-points"></a>Exibir pontos de recuperação arquivados

Esse comando retorna todos os pontos de recuperação arquivados.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive
```

### <a name="restore-with-powershell"></a>Restaurar com o PowerShell

Para pontos de recuperação no arquivamento, o backup do Azure fornece uma metodologia de restauração integrada.

A restauração integrada é um processo de duas etapas. A primeira etapa envolve reidratar os pontos de recuperação armazenados no arquivo morto e armazená-los temporariamente na camada padrão do cofre por uma duração (também conhecida como a duração do reidratação), variando de um período de 10 a 30 dias. O padrão é 15 dias. Há duas prioridades diferentes de reidratação – padrão e alta prioridade. Saiba mais sobre a [prioridade reidratação](../storage/blobs/storage-blob-rehydration.md#rehydrate-an-archived-blob-to-an-online-tier).

>[!NOTE]
>
>- A duração de reidratação uma vez selecionada não pode ser alterada e os pontos de recuperação de resalimentação permanecem na camada Standard para a duração de reidratação.
>- A etapa adicional de reidratação incorre em custos.

Para obter mais informações sobre os vários métodos de restauração para máquinas virtuais do Azure, consulte [restaurar uma VM do Azure com o PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

Para restaurar SQL Server, siga [estas etapas](backup-azure-sql-automation.md#restore-sql-dbs). Os parâmetros adicionais necessários são **RehydrationPriority** e **RehydrationDuration**.

### <a name="view-jobs-from-powershell"></a>Exibir trabalhos do PowerShell

Para exibir os trabalhos de movimentação e restauração, use o seguinte cmdlet do PowerShell:

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>Usar o portal

### <a name="check-archived-recovery-point"></a>Verificar ponto de recuperação Arquivado

Agora você pode exibir todos os pontos de recuperação que foram movidos para o arquivo morto.

![Todos os pontos de recuperação.](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>Restaurar no portal

Para os pontos de recuperação que foram movidos para o arquivo morto, a restauração exige que você adicione os parâmetros de duração de reidratação e prioridade de reidratação.

![Restaurar no Portal.](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>Exibir trabalhos no portal

![Exibir trabalhos no Portal.](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>Modificar proteção

Há duas maneiras pelas quais você pode modificar a proteção de uma fonte de fontes:

- Modificar uma política existente
- Protegendo a fonte de fontes com uma nova política

Em ambos os casos, a nova política é aplicada a todos os pontos de recuperação mais antigos, que estão na camada Standard e os na camada de arquivo morto. Assim, os pontos de recuperação mais antigos poderão ser excluídos se houver uma alteração na política.

Quando os pontos de recuperação são movidos para o arquivo morto, eles estão sujeitos a um período de exclusão antecipada de 180 dias. Os encargos são rateado. Se um ponto de recuperação que não permaneceu no arquivamento por 180 dias for excluído, ele incorrerá em custo equivalente a 180 menos o número de dias gasto na camada Standard.

Os pontos de recuperação que não permaneceram no arquivamento por um mínimo de seis meses incorrerão em um custo de exclusão antecipada na exclusão.

## <a name="stop-protection-and-delete-data"></a>Interromper a proteção e excluir dados

Interromper a proteção e excluir dados exclui todos os pontos de recuperação. Para pontos de recuperação no arquivo que não permaneceram por uma duração de 180 dias na camada de arquivamento, a exclusão de pontos de recuperação levará ao custo de exclusão antecipada.

## <a name="error-codes-and-troubleshooting-steps"></a>Códigos de erro e etapas de solução de problemas

Há vários códigos de erro que surgem quando um ponto de recuperação não pode ser movido para o arquivo morto.

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Mensagem de erro** -o tipo de Recovery-Point não está qualificado para movimentação de arquivo

**Descrição** – esse código de erro é mostrado quando o tipo de ponto de recuperação selecionado não está qualificado para ser movido para o arquivo morto.

**Ação recomendada** – Verifique a qualificação do ponto de recuperação [aqui](#scope-for-preview)

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Mensagem de erro** -Recovery-Point ter dependências ativas para restauração não está qualificada para movimentação de arquivo

**Descrição –** O ponto de recuperação selecionado tem dependências ativas e, portanto, não pode ser movido para o arquivo morto.

**Ação recomendada** – Verifique a qualificação do ponto de recuperação [aqui](#scope-for-preview)

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**Mensagem de erro** -Recovery-Point não está qualificada para movimentação de arquivo como o ciclo de vida gasto no cofre-a camada padrão é menor do que o mínimo necessário

**Descrição** – o ponto de recuperação deve permanecer na camada Standard por um mínimo de três meses para máquinas virtuais do azure e 45 dias para SQL Server em máquinas virtuais do Azure

**Ação recomendada** – Verifique a qualificação do ponto de recuperação [aqui](#scope-for-preview)

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Mensagem de erro** -Recovery-Point o ciclo de vida restante é menor do que o mínimo necessário.

**Descrição** – o ciclo de vida mínimo necessário para um ponto de recuperação para a qualificação de movimentação de arquivamento é de seis meses.

**Ação recomendada** – Verifique a qualificação do ponto de recuperação [aqui](#scope-for-preview)

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Mensagem de erro** -Recovery-Point não está qualificado para movimentação de arquivo, pois já foi movido para a camada de arquivo morto

**Descrição** – o ponto de recuperação selecionado já está no arquivo morto. Portanto, não está qualificado para ser movido para o arquivo morto.

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Mensagem de erro** -o tipo DataSource não é elegível para a API de recomendação.

**Descrição** – a API de recomendação só é aplicável a máquinas virtuais do Azure. Não é aplicável para o tipo de fonte de tipos selecionado.

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**Mensagem de erro** -o ponto de recuperação já está em resalimentado. Reidratação não é permitido neste RP.

**Descrição** – o ponto de recuperação selecionado já foi realimentado.

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Mensagem de erro** -o ponto de recuperação não está qualificado para movimentação de arquivo.

**Descrição** – o ponto de recuperação selecionado não está qualificado para movimentação de arquivo.

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

 **Mensagem** de erro-o ponto de recuperação de arquivamento não é alimentado. Tente restaurar novamente após a reidratação ser concluída no RP do arquivo.

**Descrição** – o ponto de recuperação não é alimentado. Tente restaurar depois de reidratar o ponto de recuperação.

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

 **Mensagem** de erro-reidratação tem suporte apenas para pontos de recuperação de arquivo-reidratação tem suporte apenas para pontos de recuperação de arquivo

**Descrição** – reidratação não é permitido para o ponto de recuperação selecionado.

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**Mensagem de erro** – reidratação já está In-Progress para o ponto de recuperação de arquivamento.

**Descrição** – o reidratação para o ponto de recuperação selecionado já está em andamento.

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Mensagem de erro** -o ponto de recuperação não pode ser movido para a camada de arquivo devido à duração de retenção insuficiente especificada na política

**Ação recomendada** -atualize a política no item protegido com a configuração de retenção apropriada e tente novamente.

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**Mensagem de erro** -ainda estamos determinando se esse ponto de recuperação pode ser movido.

**Descrição** – a prontidão de movimentação do ponto de recuperação ainda deve ser determinada.

**Ação recomendada** -Verifique novamente depois de esperar por algum tempo.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>O que acontecerá em arquivar pontos de recuperação se eu parar a proteção e reter os dados?

O ponto de recuperação permanecerá no arquivo para sempre. Para obter mais informações, consulte [impacto de parar proteção em pontos de recuperação](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points).

## <a name="next-steps"></a>Próximas etapas

- [Preços do Backup do Azure](azure-backup-pricing.md)