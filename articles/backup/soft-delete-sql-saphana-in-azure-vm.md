---
title: Exclusão reversível para SQL Server na VM do Azure e SAP HANA em cargas de trabalho de VM do Azure
description: Saiba como a exclusão reversível para o SQL Server na VM do Azure e SAP HANA em cargas de trabalho de VM do Azure torna os backups mais seguros.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2a442997d426ff0bf4c74b0b45f7657cc0593b82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254288"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Exclusão reversível para SQL Server na VM do Azure e SAP HANA em cargas de trabalho de VM do Azure

O backup do Azure agora fornece a exclusão reversível para o SQL Server na VM do Azure e SAP HANA em cargas de trabalho de VM do Azure. Isso é além do [cenário de exclusão reversível de máquina virtual do Azure](soft-delete-virtual-machines.md)já compatível.

A [exclusão reversível](backup-azure-security-feature-cloud.md) é um recurso de segurança para ajudar a proteger os dados de backup mesmo após a exclusão. Com a exclusão reversível, mesmo que um ator mal-intencionado exclua o backup de um banco de dados (ou, por acaso, o backup de um dado seja excluído), os dados de backup serão mantidos por 14 dias adicionais. Isso permite a recuperação desse item de backup sem perda de dados. Essa retenção adicional de 14 dias dos dados de backup no estado de "exclusão reversível" não incorre em nenhum custo para o cliente.

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>Exclusão reversível para SQL Server na VM do Azure usando portal do Azure

>[!NOTE]
>Essas instruções também se aplicam a SAP HANA na VM do Azure.

1. Para excluir os dados de backup de um banco de dado em um SQL Server, o backup deve ser interrompido. Na portal do Azure, vá para o cofre dos serviços de recuperação, vá para o item de backup e escolha **parar backup**.

   ![Parar backup](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. Na janela a seguir, você terá a opção de excluir ou reter os dados de backup. Se você escolher **excluir dados de backup**, o backup do banco de dado não será excluído permanentemente. Em vez disso, os dados de backup serão mantidos por 14 dias no estado de exclusão reversível. A exclusão é adiada até o 15º dia com emails de alerta regulares nos primeiros, 12 e 15 dias informando sobre o estado de backup do banco de dados para o usuário.

   ![Excluir dados de backup](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. Durante esses 14 dias, no cofre dos serviços de recuperação, o item com exclusão reversível será exibido com um ícone vermelho de "exclusão reversível" ao lado dele.

   ![Itens com exclusão reversível](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. Para restaurar o BD com exclusão reversível, primeiro ele deve ser restaurado. Para restaurar, escolha o banco de BD com exclusão reversível e, em seguida, selecione a opção **restaurar**.

   ![Restaurar banco de dados](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   Uma janela será exibida avisando que, se a exclusão for escolhida, todos os pontos de restauração do banco de dados serão restaurados e estarão disponíveis para a execução de uma operação de restauração. O item de backup será retido em um estado "parar proteção com dados de retenção" com backups pausados e dados de backup retidos para sempre, sem nenhuma política de backup em vigor.

   ![Remover aviso](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. Neste ponto, você também pode restaurar os dados selecionando **restaurar** para o item de backup excluído reversível escolhido.

   ![Restaurar VM](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. Depois que o processo de reinicialização for concluído, o status retornará "parar backup com reter dados" e você poderá escolher **retomar backup**. A operação **retomar backup** retorna o item de backup no estado ativo, associado a uma política de backup selecionada pelo usuário que define os agendamentos de backup e retenção.

   ![Retomar backup](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>Exclusão reversível para SQL Server na VM usando Azure PowerShell

>[!NOTE]
>A versão AZ. Recoveryservices necessária para usar a exclusão reversível usando Azure PowerShell é o mínimo de 2.2.0. Use `Install-Module -Name Az.RecoveryServices -Force` para obter a versão mais recente.

A sequência de etapas para usar Azure PowerShell é a mesma do portal do Azure, descrita acima.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Excluir o item de backup usando Azure PowerShell

Exclua o item de backup usando o cmdlet [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) do PowerShell.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

O **deletestate** do item de backup será alterado de não **excluído** para **ToBeDeleted**. Os dados de backup serão mantidos por 14 dias. Se você quiser reverter a operação de exclusão, a ação desfazer-excluir deverá ser executada.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Desfazendo a operação de exclusão usando Azure PowerShell

Primeiro, busque o item de backup relevante que está no estado de exclusão reversível (ou seja, prestes a ser excluído).

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

Em seguida, execute a operação de desfazer exclusão usando o cmdlet do PowerShell [Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) .

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

O **deletestate** do item de backup será revertido para **excluído**. Mas a proteção ainda está parada. Retome o backup para reabilitar a proteção.

## <a name="how-to-disable-soft-delete"></a>Como desabilitar a exclusão reversível

Não é recomendável desabilitar esse recurso. A única circunstância em que você deve considerar a desabilitação da exclusão reversível é se você planeja mover seus itens protegidos para um novo cofre e não pode aguardar os 14 dias necessários antes de excluir e proteger novamente (como em um ambiente de teste). Para obter instruções sobre como desabilitar a exclusão reversível, consulte [Habilitando e desabilitando a exclusão reversível](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Próximas etapas

- Leia as [perguntas](backup-azure-security-feature-cloud.md#frequently-asked-questions) frequentes sobre a exclusão reversível
- Leia sobre todos os [recursos de segurança no backup do Azure](security-overview.md)
