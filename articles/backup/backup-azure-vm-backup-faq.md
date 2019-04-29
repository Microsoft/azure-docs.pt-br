---
title: Perguntas frequentes sobre como fazer backup de VMs do Azure com o Backup do Azure
description: Respostas para perguntas comuns sobre como fazer backup de VMs do Azure com o Backup do Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sogup
ms.openlocfilehash: 9f233af316bd6022b93a7208bf3fae37e913e6af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848173"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Perguntas frequentes perguntas-fazer backup de VMs do Azure

Este artigo responde a perguntas comuns sobre como fazer backup de VMs do Azure com o [Backup do Azure](backup-introduction-to-azure-backup.md) service.


## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Quais imagens VM podem ser habilitadas para backup, quando eu criá-los?
Quando você cria uma VM, você pode habilitar o backup para VMs em execução [sistemas operacionais com suporte](backup-support-matrix-iaas.md#supported-backup-actions)
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>É o custo de backup incluído no custo da VM? 

Não. Custos de backup são separados dos custos da VM. Saiba mais sobre [preços de Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Quais permissões são necessárias para habilitar o backup de uma VM? 

Se você for um colaborador VM, você pode habilitar o backup na VM. Se você estiver usando uma função personalizada, você precisará das seguintes permissões para habilitar o backup na VM: 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Se seu Cofre de serviços de recuperação e a VM tiverem grupos de recursos diferentes, certifique-se de que ter permissões de gravação no grupo de recursos para o Cofre de serviços de recuperação.  


### <a name="what-azure-vms-can-you-back-up-using-azure-backup"></a>Quais VMs do Azure podem passar por backup usando o Backup do Azure?

Examine os [matriz de suporte](backup-support-matrix-iaas.md) para detalhes de suporte e limitações.

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>O trabalho de backup sob demanda usa o mesmo agendamento de retenção que os backups agendados?
Não. Especifique o período de retenção para um trabalho de backup sob demanda. Por padrão, ele é retido por 30 dias quando disparado de um portal.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Eu recentemente habilitei a Criptografia de Disco do Azure em algumas VMs. Meus backups continuarão a funcionar?
Forneça permissões para o Backup do Azure acessar o Cofre de chaves. Especifique as permissões no PowerShell, conforme descrito na seção **Habilitar backup** na documentação do [PowerShell do Backup do Azure](backup-azure-vms-automation.md).

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Migrei os discos de uma VM para os discos gerenciados. Meus backups continuarão a funcionar?
Sim, os backups funcionam perfeitamente. Não é necessário reconfigurar nada.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Por que não consigo ver minha VM no assistente de Backup de Configuração?
O assistente lista apenas as VMs na mesma região do cofre e que ainda não estão sendo submetidas a backup.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Minha VM está desligada. Será uma sob demanda ou um trabalho de backup agendado?
Sim. Os backups são executados quando um computador é desligado. O ponto de recuperação é marcado como consistente com a falha.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Posso cancelar um trabalho de backup em andamento?
Sim. Você poderá cancelar o trabalho de backup se ele estiver em um estado **Criar instantâneo**. Você não poderá cancelar um trabalho se a transferência de dados do instantâneo estiver em andamento.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprggeonumber-will-my-backups-continue-to-work"></a>Eu habilitei o bloqueio no grupo de recursos criado pelo serviço de Backup do Azure (ou seja `AzureBackupRG_<geo>_<number>`), meus backups continuarão a funcionar?
Se você bloquear o grupo de recursos criado pelo serviço de Backup do Azure, o backups começarão a falhar pois não há um limite máximo de 18 pontos de restauração.

Usuário precisa remover o bloqueio e limpar a coleção de pontos de restauração do grupo de recursos para tornar os futuros backups bem-sucedidos, [siga estas etapas](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) para remover a coleção de pontos de restauração.

### <a name="does-the-backup-policy-consider-daylight-saving-time-dst"></a>A política de backup leva em conta o horário de verão (DST)?
Não. A data e hora no computador local é local com o atual horário de verão aplicado. O tempo definido para backups agendados pode ser diferente do horário local devido ao horário de verão.

### <a name="how-many-data-disks-can-i-attach-to-a-vm-backed-up-by-azure-backup"></a>Quantos discos de dados eu posso anexar a uma VM para fazer backup pelo Backup do Azure?
O Backup do Azure pode fazer backup de VMs com até 16 discos. Suporte para 16 discos é fornecido na [Restauração Instantânea](backup-instant-restore-capability.md).

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>O backup do Azure oferece suporte ao disco gerenciado SSD Standard?
O Backup do Azure oferece suporte aos [discos gerenciados SSD Standard](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/). Discos gerenciados SSD fornecem um novo tipo de armazenamento durável para VMs do Azure. Suporte para discos gerenciados SSD é fornecido na [Restauração Instantânea](backup-instant-restore-capability.md).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Podemos fazer backup de uma VM com um disco ativado pelo Acelerador de Gravação?
Os instantâneos não podem ser criados no disco habilitado pelo Acelerador de Gravação. No entanto, o serviço de Backup do Azure pode excluir o disco habilitado pelo Acelerador de Gravação do backup. A exclusão de disco para VMs com discos habilitados pelo Acelerador de Gravação só tem suporte para assinaturas atualizadas para a Restauração Instantânea.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Tenho uma VM com discos do Acelerador de Gravação e do SAP HANA instalados. Como fazer backup?
O Backup do Azure não pode fazer backup do disco habilitado pelo Acelerador de Gravação, mas pode excluí-lo do backup. No entanto, o backup não fornecerá a consistência do banco de dados porque não são feitos backups de informações do disco habilitado pelo Acelerador de Gravação. Você pode fazer backup de discos com essa configuração se desejar o backup em disco do sistema operacional e o backup dos discos que não são habilitados pelo Acelerador de Gravação.

Estamos executando versão prévia privada para um backup do SAP HANA com um RPO de 15 minutos. Ele é criado de maneira semelhante ao backup do Banco de Dados do SQL e usa a interface backInt para soluções de terceiros certificadas pelo SAP HANA. Se você estiver interessado, envie um email `AskAzureBackupTeam@microsoft.com` com o assunto **Inscreva-se para visualização privada para o backup do SAP HANA em VMs do Azure**.


## <a name="restore"></a>Restaurar

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Como faço para decidir se quero restaurar somente a discos ou uma VM completa?
Pense na restauração da VM como uma opção de criação rápida para uma VM do Azure. Esta opção altera os nomes de disco, contêineres usados por discos, endereços IP públicos e os nomes de interface de rede. A alteração mantém recursos exclusivos quando uma VM é criada. A VM não é adicionada ao conjunto de disponibilidade.

Você pode usar a opção de disco de restauração se você quer:
  * Personalizar a VM que é criada. Por exemplo, altere o tamanho.
  * Adicione definições de configuração que não estavam lá no momento do backup.
  * Controlar a convenção de nomenclatura para os recursos que são criados.
  * Adicionar a VM a um grupo de disponibilidade.
  * Adicione qualquer outra configuração que deva ser configurada usando o PowerShell ou um modelo.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Posso restaurar backups de discos de VMs não gerenciadas após o upgrade dos meus discos para discos gerenciados?
Sim, você pode usar os backups feitos antes de migrar os discos de não gerenciados para gerenciados.
- Por padrão, um trabalho de VM de restauração cria uma VM não gerenciada.
- No entanto, você pode restaurar discos e usá-los para criar uma VM gerenciada.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Como faço para restaurar uma VM em um ponto de restauração antes que a VM seja migrada para os discos gerenciados?
Por padrão, o trabalho de VM de restauração cria uma VM com discos não gerenciados. Para criar uma VM com discos gerenciados:
1. [Restaure em discos não gerenciados](tutorial-restore-disk.md#restore-a-vm-disk).
2. [Converta os discos restaurados em discos gerenciados](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [Crie uma VM com discos gerenciados](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Saiba mais](backup-azure-vms-automation.md#restore-an-azure-vm) sobre como fazer isso no PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Posso restaurar a VM que foi excluída?
Sim. Mesmo se você excluir a VM, poderá acessar o item de backup correspondente no cofre e restaurar a partir de um ponto de recuperação.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Como restaurar uma VM nos mesmos conjuntos de disponibilidade?
Para a VM do Azure do Disco Gerenciado, a restauração dos conjuntos de disponibilidade é ativada ao fornecer uma opção no modelo durante a restauração como discos gerenciados. Este modelo tem o parâmetro de entrada chamado **Conjuntos de disponibilidade**.

### <a name="how-do-we-get-faster-restore-performances"></a>Como conseguimos desempenhos de restauração mais rápidos?
Para melhorar o desempenho de restauração, vamos mudar para a funcionalidade de [Restauração Instantânea](backup-instant-restore-capability.md).

## <a name="manage-vm-backups"></a>Gerenciar backups de VM

### <a name="what-happens-if-i-modify-a-backup-policy"></a>O que acontecerá se eu alterar minha política de backup?
O backup da VM é feito usando as configurações de retenção e agendamento na política nova ou modificada.

- Se a retenção for estendida, os pontos de recuperação existentes serão marcados e mantidos de acordo com a nova política.
- Se a retenção for reduzida, eles serão marcados para remoção no próximo trabalho de limpeza e subsequentemente excluídos.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Como faço para mover uma VM com backup feito pelo Backup do Azure para um grupo de recursos diferente?

1. Pare temporariamente o backup e mantenha os dados de backup.
2. Mova a VM para o grupo de recursos de destino.
3. Backup habilitado novamente no mesmo ou novo cofre.

Você pode restaurar a VM a partir dos pontos de restauração disponíveis que foram criados antes da operação de migração.
