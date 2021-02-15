---
title: Exclusão reversível para o backup do Azure
description: Saiba como usar recursos de segurança no backup do Azure para tornar os backups mais seguros.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: da473b1d886ec2fe95a7baae76b09aff38fb3cd7
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95254012"
---
# <a name="soft-delete-for-azure-backup"></a>Exclusão reversível para o backup do Azure

Preocupações sobre problemas de segurança, como malware, ransomware e invasão, estão aumentando. Esses problemas de segurança podem ser dispendiosos em termos de dinheiro e dados. Para se proteger contra tais ataques, o backup do Azure agora fornece recursos de segurança para ajudar a proteger os dados de backup mesmo após a exclusão.

Um desses recursos é exclusão reversível. Com a exclusão reversível, mesmo que um ator mal-intencionado exclua um backup (ou os dados de backup sejam excluídos acidentalmente), os dados de backup são mantidos por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. Os 14 dias adicionais de retenção para dados de backup no estado de "exclusão reversível" não incorrem em nenhum custo para você.

A proteção de exclusão reversível está disponível para esses serviços:

- [Exclusão reversível para máquinas virtuais do Azure](soft-delete-virtual-machines.md)
- [Exclusão reversível para SQL Server na VM do Azure e exclusão reversível para SAP HANA em cargas de trabalho de VM do Azure](soft-delete-sql-saphana-in-azure-vm.md)

Este gráfico de fluxo mostra as diferentes etapas e Estados de um item de backup quando a exclusão reversível está habilitada:

![Ciclo de vida do item de backup excluído por software](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>Habilitando e desabilitando a exclusão reversível

A exclusão reversível é habilitada por padrão em cofres recém-criados para proteger dados de backup de exclusões acidentais ou mal-intencionadas.  Não é recomendável desabilitar esse recurso. A única circunstância em que você deve considerar a desabilitação da exclusão reversível é se você planeja mover seus itens protegidos para um novo cofre e não pode aguardar os 14 dias necessários antes de excluir e proteger novamente (como em um ambiente de teste). Somente o proprietário do cofre pode desabilitar esse recurso. Se você desabilitar esse recurso, todas as exclusões futuras de itens protegidos resultarão na remoção imediata, sem a capacidade de restaurar. Os dados de backup que existem no estado de exclusão reversível antes de desabilitar esse recurso permanecerão no estado de exclusão reversível pelo período de 14 dias. Se você quiser excluí-las permanentemente imediatamente, será necessário restaurar e excluí-las novamente para que sejam excluídas permanentemente.

É importante lembrar que, quando a exclusão reversível está desabilitada, o recurso é desabilitado para todos os tipos de cargas de trabalho. Por exemplo, não é possível desabilitar a exclusão reversível somente para bancos de SAP HANA ou SQL Server, mantendo-o habilitado para máquinas virtuais no mesmo cofre. Você pode criar cofres separados para o controle granular.

### <a name="disabling-soft-delete-using-azure-portal"></a>Desabilitando a exclusão reversível usando o portal do Azure

Para desabilitar a exclusão reversível, siga estas etapas:

1. Na portal do Azure, acesse seu cofre e vá para **configurações**  ->  **Propriedades**.
2. No painel Propriedades, selecione **configurações de segurança**  ->  **atualização**.  
3. No painel configurações de segurança, em **exclusão reversível**, selecione **desabilitar**.

![Desabilitar exclusão reversível](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Desabilitando a exclusão reversível usando o Azure PowerShell

> [!IMPORTANT]
> A versão AZ. Recoveryservices necessária para usar a exclusão reversível usando Azure PowerShell é o mínimo de 2.2.0. Use ```Install-Module -Name Az.RecoveryServices -Force``` para obter a versão mais recente.

Para desabilitar, use o cmdlet do PowerShell [set-AzRecoveryServicesVaultBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) .

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Desabilitando a exclusão reversível usando a API REST

Para desabilitar a funcionalidade de exclusão reversível usando a API REST, consulte as etapas mencionadas [aqui](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Excluindo permanentemente itens de backup com exclusão reversível

Dados de backup em estado de exclusão reversível antes de desabilitar esse recurso, permanecerão no estado de exclusão reversível. Se você quiser excluí-las permanentemente, desexclua-as e exclua-as novamente para que sejam excluídas permanentemente.

### <a name="using-azure-portal"></a>Usando o Portal do Azure

Siga estas etapas:

1. Siga as etapas para [desabilitar a exclusão reversível](#enabling-and-disabling-soft-delete).

2. No portal do Azure, acesse seu cofre, vá para **itens de backup** e escolha o item com exclusão reversível.

   ![Escolher item com exclusão reversível](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Selecione a opção **restaurar**.

   ![Escolha restaurar](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Uma janela será exibida. Selecione **restaurar**.

   ![Selecione restaurar](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Escolha **excluir dados de backup** para excluir permanentemente os dados de backup.

   ![Escolha excluir dados de backup](./media/backup-azure-manage-vms/delete-backup-button.png)

6. Digite o nome do item de backup para confirmar que você deseja excluir os pontos de recuperação.

   ![Digite o nome do item de backup](./media/backup-azure-manage-vms/delete-backup-data.png)

7. Para excluir os dados de backup do item, selecione **excluir**. Uma mensagem de notificação permite que você saiba que os dados de backup foram excluídos.

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Se os itens foram excluídos antes de a exclusão reversível ter sido desabilitada, eles estarão em um estado de exclusão reversível. Para excluí-los imediatamente, a operação de exclusão precisa ser revertida e, em seguida, executada novamente.

Identifique os itens que estão no estado de exclusão reversível.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Em seguida, inverta a operação de exclusão que foi executada quando a exclusão reversível foi habilitada.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Como a exclusão reversível agora está desabilitada, a operação de exclusão resultará na remoção imediata dos dados de backup.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Usando a API REST

Se os itens foram excluídos antes de a exclusão reversível ter sido desabilitada, eles estarão em um estado de exclusão reversível. Para excluí-los imediatamente, a operação de exclusão precisa ser revertida e, em seguida, executada novamente.

1. Primeiro, desfaça as operações de exclusão com as etapas mencionadas [aqui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion).
2. Em seguida, desabilite a funcionalidade de exclusão reversível usando a API REST usando as etapas mencionadas [aqui](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).
3. Em seguida, exclua os backups usando a API REST, conforme mencionado [aqui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>É necessário habilitar o recurso de exclusão reversível em todos os cofres?

Não, ela é interna e habilitada por padrão para todos os cofres dos serviços de recuperação.

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>Posso configurar o número de dias pelos quais meus dados serão retidos no estado de exclusão reversível após a conclusão da operação de exclusão?

Não, ele é corrigido para 14 dias de retenção adicional após a operação de exclusão.

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>É necessário pagar o custo dessa retenção adicional de 14 dias?

Não, essa retenção adicional de 14 dias vem sem custo como parte da funcionalidade de exclusão reversível.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Posso executar uma operação de restauração quando meus dados estiverem no estado de exclusão reversível?

Não, você precisa restaurar o recurso de exclusão reversível a fim de restaurá-lo. A operação de desfazer exclusão levará o recurso de volta para o **estado parar proteção com manter dados** , em que você pode restaurar para qualquer ponto no tempo. O coletor de lixo permanece pausado nesse estado.

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Meus instantâneos seguem o mesmo ciclo de vida dos meus pontos de recuperação no cofre?

Sim.

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Como posso disparar os backups agendados novamente para um recurso excluído de forma reversível?

A exclusão seguida de uma operação de retomada irá proteger o recurso novamente. A operação de retomada associa uma política de backup para disparar os backups agendados com o período de retenção selecionado. Além disso, o coletor de lixo é executado assim que a operação de retomada é concluída. Se você quiser executar uma restauração de um ponto de recuperação após sua data de expiração, é aconselhável fazer isso antes de disparar a operação de retomada.

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Posso excluir meu cofre se houver itens com exclusão reversível no cofre?

O cofre dos serviços de recuperação não poderá ser excluído se houver itens de backup em estado de exclusão reversível no cofre. Os itens excluídos por software são excluídos permanentemente 14 dias após a operação de exclusão. Se você não puder esperar por 14 dias, [desabilite a exclusão reversível](#enabling-and-disabling-soft-delete), exclua os itens com exclusão reversível e exclua-os novamente para ser excluído permanentemente. Depois de garantir que não há itens protegidos e nenhum item com exclusão reversível, o cofre pode ser excluído.  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Posso excluir os dados anteriores ao período de exclusão reversível de 14 dias após a exclusão?

Não. Não é possível forçar a exclusão dos itens excluídos por software. Eles são excluídos automaticamente após 14 dias. Esse recurso de segurança é habilitado para proteger os dados de backup de exclusões acidentais ou mal-intencionadas.  Você deve aguardar por 14 dias antes de executar qualquer outra ação no item.  Os itens excluídos por software não serão cobrados.  Se você precisar proteger novamente os itens marcados para exclusão reversível dentro de 14 dias em um novo cofre, entre em contato com o suporte da Microsoft.

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>As operações de exclusão reversível podem ser executadas no PowerShell ou na CLI?

As operações de exclusão reversível podem ser executadas usando o PowerShell. Atualmente, não há suporte para a CLI.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos recursos de segurança no Backup do Azure](security-overview.md)
