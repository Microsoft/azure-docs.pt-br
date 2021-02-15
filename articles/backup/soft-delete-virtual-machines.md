---
title: Exclusão temporária para máquinas virtuais
description: Saiba como a exclusão reversível para máquinas virtuais torna os backups mais seguros.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: a8b70d4c8240d096c19e5a8d7449921557b8896c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89022234"
---
# <a name="soft-delete-for-virtual-machines"></a>Exclusão temporária para máquinas virtuais

A exclusão reversível para VMs protege os backups de suas VMs contra exclusão não intencional. Mesmo depois que os backups são excluídos, eles são preservados no estado de exclusão reversível por 14 dias adicionais.

> [!NOTE]
> A exclusão reversível só protege os dados de backup excluídos. Se uma VM for excluída sem um backup, o recurso de exclusão reversível não preservará os dados. Todos os recursos devem ser protegidos com o backup do Azure para garantir a resiliência completa.
>

## <a name="supported-regions"></a>Regiões com suporte

A exclusão reversível atualmente é suportada no EUA Central ocidental, Ásia Oriental, Canadá central, leste do Canadá, França central, sul da França, Coreia central, Coreia do Sul, Sul do Reino Unido, Oeste do Reino Unido, leste da Austrália, leste do Japão, Europa Setentrional, oeste dos EUA, oeste da Índia, Índia central, oeste da Índia, leste dos Estados Unidos, leste dos EUA 2 , Norte da Suíça, Oeste da Suíça, oeste da Noruega, leste da Noruega e todas as regiões nacionais.

## <a name="soft-delete-for-vms-using-azure-portal"></a>Exclusão reversível para VMs usando portal do Azure

1. Para excluir os dados de backup de uma VM, o backup deve ser interrompido. Na portal do Azure, vá para o cofre dos serviços de recuperação, clique com o botão direito do mouse no item de backup e escolha **parar backup**.

   ![Captura de tela de portal do Azure itens de backup](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. Na janela a seguir, você terá a opção de excluir ou reter os dados de backup. Se você escolher **excluir dados de backup** e **parar o backup**, o backup da VM não será excluído permanentemente. Em vez disso, os dados de backup serão mantidos por 14 dias no estado de exclusão reversível. Se **excluir dados de backup** for escolhido, um alerta de email de exclusão será enviado para a ID de email configurada informando ao usuário que 14 dias permanecem de retenção estendida para dados de backup. Além disso, um alerta por email é enviado no dia de 12 dias informando que há mais dois dias restantes para ressuscitar os dados excluídos. A exclusão é adiada até o dia 15, quando a exclusão permanente ocorre e um alerta de email final é enviado informando sobre a exclusão permanente dos dados.

   ![Captura de tela de portal do Azure, parar o backup Screen](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Durante esses 14 dias, no cofre dos serviços de recuperação, a VM com exclusão reversível será exibida com um ícone vermelho de "exclusão reversível" ao lado dele.

   ![Captura de tela de portal do Azure, VM no estado de exclusão reversível](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Se algum item de backup excluído por software estiver presente no cofre, o cofre não poderá ser excluído nesse momento. Tente excluir o cofre depois que os itens de backup forem excluídos permanentemente e não houver itens no estado de exclusão reversível no cofre.

4. Para restaurar a VM com exclusão reversível, primeiro ela deve ser restaurada. Para restaurar, escolha a VM com exclusão reversível e, em seguida, selecione a opção **restaurar**.

   ![Captura de tela de portal do Azure, restaurar VM](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Uma janela será exibida avisando que, se a exclusão for escolhida, todos os pontos de restauração da VM serão restaurados e estarão disponíveis para a execução de uma operação de restauração. A VM será retida em um estado "parar proteção com retenção de dados" com backups pausados e dados de backup retidos para sempre, sem nenhuma política de backup em vigor.

   ![Captura de tela de portal do Azure, confirmar restaurar VM](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Neste ponto, você também pode restaurar a VM selecionando **restaurar VM** do ponto de restauração escolhido.  

   ![Captura de tela de portal do Azure, opção restaurar VM](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > O coletor de lixo só executará e limpará os pontos de recuperação expirados depois que o usuário executar a operação de **retomada de backup** .

5. Depois que o processo de reinicialização for concluído, o status retornará "parar backup com reter dados" e você poderá escolher **retomar backup**. A operação **retomar backup** retorna o item de backup no estado ativo, associado a uma política de backup selecionada pelo usuário que define os agendamentos de backup e retenção.

   ![Captura de tela de portal do Azure, opção retomar backup](./media/backup-azure-security-feature-cloud/resume-backup.png)

## <a name="soft-delete-for-vms-using-azure-powershell"></a>Exclusão reversível para VMs usando Azure PowerShell

> [!IMPORTANT]
> A versão AZ. Recoveryservices necessária para usar a exclusão reversível usando Azure PowerShell é o mínimo de 2.2.0. Use ```Install-Module -Name Az.RecoveryServices -Force``` para obter a versão mais recente.

Conforme descrito acima para portal do Azure, a sequência de etapas é a mesma ao usar o Azure PowerShell também.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Excluir o item de backup usando Azure PowerShell

Exclua o item de backup usando o cmdlet [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) do PowerShell.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

O ' Deletestate ' do item de backup será alterado de ' candeleted ' para ' ToBeDeleted '. Os dados de backup serão mantidos por 14 dias. Se você quiser reverter a operação de exclusão, a ação desfazer-excluir deverá ser executada.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Desfazendo a operação de exclusão usando Azure PowerShell

Primeiro, busque o item de backup relevante que está no estado de exclusão reversível (ou seja, prestes a ser excluído).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Em seguida, execute a operação de desfazer exclusão usando o cmdlet do PowerShell [Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) .

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

O ' Deletestate ' do item de backup será revertido para ' candeleted '. Mas a proteção ainda está parada. [Retome o backup](./backup-azure-vms-automation.md#change-policy-for-backup-items) para reabilitar a proteção.

## <a name="soft-delete-for-vms-using-rest-api"></a>Exclusão reversível para VMs usando a API REST

- Exclua os backups usando a API REST, conforme mencionado [aqui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Se você quiser desfazer essas operações de exclusão, consulte as etapas mencionadas [aqui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion).

## <a name="how-to-disable-soft-delete"></a>Como desabilitar a exclusão reversível

Não é recomendável desabilitar esse recurso. A única circunstância em que você deve considerar a desabilitação da exclusão reversível é se você planeja mover seus itens protegidos para um novo cofre e não pode aguardar os 14 dias necessários antes de excluir e proteger novamente (como em um ambiente de teste). Para obter instruções sobre como desabilitar a exclusão reversível, consulte [Habilitando e desabilitando a exclusão reversível](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Próximas etapas

- Leia as [perguntas](backup-azure-security-feature-cloud.md#frequently-asked-questions) frequentes sobre a exclusão reversível
- Leia sobre todos os [recursos de segurança no backup do Azure](security-overview.md)
