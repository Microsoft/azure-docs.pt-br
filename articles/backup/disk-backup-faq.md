---
title: Perguntas frequentes sobre o backup em disco do Azure
description: Obtenha respostas para perguntas frequentes sobre o backup em disco do Azure
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 3ef18a7d178075194e24889477768583f05f0cdd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734555"
---
# <a name="frequently-asked-questions-about-azure-disk-backup-in-preview"></a>Perguntas frequentes sobre o backup em disco do Azure (em versão prévia)

>[!IMPORTANT]
>O backup em disco do Azure está em versão prévia sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para disponibilidade de região, consulte a [matriz de suporte](disk-backup-support-matrix.md).
>
>[Preencha este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) para se inscrever na versão prévia.

Este artigo responde a perguntas frequentes sobre o backup em disco do Azure. Para obter mais informações sobre a disponibilidade da região de [backup em disco do Azure](disk-backup-overview.md) , cenários e limitações com suporte, consulte a [matriz de suporte](disk-backup-support-matrix.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="can-i-back-up-the-disk-using-the-azure-disk-backup-solution-if-the-same-disk-is-backed-up-using-azure-virtual-machine-backup"></a>Posso fazer backup do disco usando a solução de backup em disco do Azure se for feito o backup do mesmo disco usando o backup de máquina virtual do Azure?

O backup do Azure oferece suporte lado a lado para backup de disco gerenciado usando o backup em disco e as soluções de [backup de VM do Azure](backup-azure-vms-introduction.md) . Isso é útil quando você precisa de um backup consistente do aplicativo uma vez por dia de máquinas virtuais e também backups mais frequentes do disco do sistema operacional ou de um disco de dados específico, que são consistente com a falha sem afetar o desempenho do aplicativo de produção.

### <a name="how-do-i-find-the-snapshot-resource-group-that-i-used-to-configure-backup-for-a-disk"></a>Como fazer localizar o grupo de recursos de instantâneo que usei para configurar o backup de um disco?

Na tela de **instância de backup** , você pode encontrar o campo grupo de recursos de instantâneo na seção **Essentials** . Você pode pesquisar e selecionar a instância de backup do disco correspondente no centro de backup ou no cofre de backup.

![Campo de grupo de recursos de instantâneo](./media/disk-backup-faq/snapshot-resource-group.png)

### <a name="what-is-a-snapshot-resource-group"></a>O que é um grupo de recursos de instantâneo?

O backup em disco do Azure oferece backup de camada operacional para o disco gerenciado. Ou seja, os instantâneos criados durante as operações de backup agendadas e sob demanda são armazenados em um grupo de recursos em sua assinatura. O backup do Azure oferece restauração instantânea porque os instantâneos incrementais são armazenados em sua assinatura. Esse grupo de recursos é conhecido como o grupo de recursos de instantâneo. Para obter mais informações, consulte [Configurar backup](backup-managed-disks.md#configure-backup).

### <a name="why-must-the-snapshot-resource-group-be-in-same-subscription-as-that-of-the-disk-being-backed-up"></a>Por que o grupo de recursos de instantâneo deve estar na mesma assinatura que o backup do disco?

Você não pode criar um instantâneo incremental para um disco específico fora da assinatura desse disco. Portanto, escolha o grupo de recursos na mesma assinatura do disco do qual será feito o backup. Saiba mais sobre o [instantâneo incremental](../virtual-machines/disks-incremental-snapshots.md#restrictions) para discos gerenciados.

### <a name="why-do-i-need-to-provide-role-assignments-to-be-able-to-configure-backups-perform-scheduled-and-on-demand-backups-and-restore-operations"></a>Por que preciso fornecer atribuições de função para poder configurar backups, executar backups agendados e sob demanda e operações de restauração?

O backup em disco do Azure usa a abordagem de privilégios mínimos para descobrir, proteger e restaurar os discos gerenciados em suas assinaturas. Para conseguir isso, o backup do Azure usa a identidade gerenciada do [cofre de backup](backup-vault-overview.md) para acessar outros recursos do Azure. Uma identidade gerenciada atribuída pelo sistema é restrita a uma por recurso e está vinculada ao ciclo de vida deste recurso. Você pode conceder permissões para a identidade gerenciada usando o controle de acesso baseado em função do Azure (RBAC do Azure). A identidade gerenciada é uma entidade de serviço de um tipo especial que pode ser usada apenas com recursos do Azure. Saiba mais sobre as [identidades gerenciadas](../active-directory/managed-identities-azure-resources/overview.md). Por padrão, o cofre de backup não terá permissão para acessar o disco do qual será feito backup, criar instantâneos periódicos, excluir instantâneos após o período de retenção e restaurar um disco do backup. Ao conceder explicitamente atribuições de função à identidade gerenciada do cofre de backup, você está no controle do gerenciamento de permissões para os recursos nas assinaturas.

### <a name="why-does-backup-policy-limit-the-retention-duration"></a>Por que a política de backup limita a duração da retenção?

O backup em disco do Azure usa instantâneos incrementais, que são limitados a 200 instantâneos por disco. Para permitir que você faça backups sob demanda além dos backups agendados, a política de backup limita o total de backups a 180. Saiba mais sobre [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md#restrictions) para discos gerenciados.

### <a name="how-does-the-hourly-and-daily-backup-frequency-work-in-the-backup-policy"></a>Como funciona a frequência de backup por hora e diária na política de backup?

O backup em disco do Azure oferece vários backups por dia. Se você precisar de backups mais frequentes, escolha a frequência de backup por **hora** . Os backups são agendados com base no intervalo de **tempo** selecionado. Por exemplo, se você selecionar a **cada 4 horas**, os backups serão feitos a aproximadamente a cada 4 horas para que os backups sejam distribuídos igualmente ao longo do dia. Se uma vez por dia o backup for suficiente, escolha a frequência de backup **diária** . Na frequência de backup diário, você pode especificar a hora do dia em que os backups serão feitos. É importante observar que a hora do dia indica a hora de início do backup e não a hora em que o backup foi concluído. O tempo necessário para concluir a operação de backup depende de vários fatores, incluindo a taxa de rotatividade entre backups consecutivos. No entanto, o backup em disco do Azure é um backup sem agente que usa [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md) que não afetam o desempenho do aplicativo de produção.

### <a name="why-does-the-backup-vaults-redundancy-setting-not-apply-to-the-backups-stored-in-operational-tier-the-snapshot-resource-group"></a>Por que a configuração de redundância do cofre de backup não se aplica aos backups armazenados na camada operacional (o grupo de recursos de instantâneo)?

O backup do Azure usa [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md#restrictions) de discos gerenciados que armazenam apenas as alterações delta em discos desde o último instantâneo no armazenamento HDD Standard, independentemente do tipo de armazenamento do disco pai. Para obter mais confiabilidade, os instantâneos incrementais são armazenados no armazenamento com redundância de zona (ZRS) por padrão em regiões que dão suporte a ZRS. Atualmente, o backup em disco do Azure dá suporte a backups operacionais de discos gerenciados que não copiam os backups no armazenamento do cofre de backup. Portanto, a configuração de redundância de armazenamento de backup do cofre de backup não se aplica aos pontos de recuperação.

### <a name="can-i-use-backup-center-to-configure-backups-and-manage-backup-instances-for-azure-disks"></a>Posso usar o centro de backup para configurar backups e gerenciar instâncias de backup para discos do Azure?

Sim, o backup em disco do Azure é integrado ao [centro de backup](backup-center-overview.md), que fornece uma **única experiência de gerenciamento unificada** no Azure para que as empresas administrem, monitorem, operem e analisem backups em escala. Você também pode usar o cofre de backup para fazer backup, restaurar e gerenciar as instâncias de backup protegidas no cofre.

### <a name="why-do-i-need-to-create-a-backup-vault-and-not-use-a-recovery-services-vault"></a>Por que preciso criar um cofre de backup e não usar um cofre dos serviços de recuperação?

Um cofre de backup é uma entidade de armazenamento no Azure que hospeda dados de backup para determinadas cargas de trabalho mais recentes às quais o backup do Azure dá suporte. Você pode usar os cofres de backup para manter os dados de backup de vários serviços do Azure, como os servidores do Azure Database para PostgreSQL, os discos do Azure e as cargas de trabalho mais recentes para as quais o backup do Azure dará suporte. Os cofres de backup facilitam a organização dos dados de backup, minimizando a sobrecarga de gerenciamento. Consulte [cofres de backup](./backup-vault-overview.md) para saber mais.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-subscriptions"></a>É possível fazer backup do disco e o cofre de backup estar em assinaturas diferentes?

Sim, o disco de origem gerenciada para backup e o cofre de backup podem estar em assinaturas diferentes.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-regions"></a>É possível fazer backup do disco e o cofre de backup estar em regiões diferentes?

Não, atualmente o disco gerenciado por origem para backup e o cofre de backup devem estar na mesma região.

### <a name="can-i-restore-a-disk-into-a-different-subscription"></a>Posso restaurar um disco em uma assinatura diferente?

Sim, você pode restaurar o disco em uma assinatura diferente daquela do disco gerenciado por origem do qual o backup é feito.

### <a name="can-i-back-up-multiple-disks-together"></a>Posso fazer backup de vários discos juntos?

Não, não há suporte para instantâneos pontuais de vários discos anexados a uma máquina virtual. Para obter mais informações, consulte [Configurar backup](backup-managed-disks.md#configure-backup) e para saber mais sobre limitações, consulte a [matriz de suporte](disk-backup-support-matrix.md).

### <a name="what-are-my-options-to-back-up-disks-across-multiple-subscriptions"></a>Quais são minhas opções para fazer backup de discos em várias assinaturas?

Atualmente, o uso do portal do Azure para configurar o backup de discos é limitado a um máximo de 20 discos da mesma assinatura.

### <a name="what-is-a-target-resource-group"></a>O que é um grupo de recursos de destino?

Durante uma operação de restauração, você pode escolher a assinatura e um grupo de recursos no qual deseja restaurar o disco. O backup do Azure criará novos discos do ponto de recuperação no grupo de recursos selecionado. Isso é conhecido como um grupo de recursos de destino. Observe que a identidade gerenciada do cofre de backup requer que a atribuição de função no grupo de recursos de destino seja capaz de executar a operação de restauração com êxito. Para obter mais informações, consulte a [documentação de restauração](restore-managed-disks.md).

### <a name="what-are-the-permissions-used-by-azure-backup-during-backup-and-restore-operation"></a>Quais são as permissões usadas pelo backup do Azure durante a operação de backup e restauração?

A seguir estão as ações usadas na função de **leitor de backup em disco** atribuída no **disco** para fazer backup:

"Microsoft. Compute/disks/Read"

"Microsoft. Compute/disks/beginGetAccess/Action"

A seguir estão as ações usadas na função **colaborador de instantâneo de disco** atribuída no grupo de recursos de **instantâneo**:

"Microsoft. Compute/instantâneos/excluir"

"Microsoft. Compute/Snapshots/Write"

"Microsoft. Compute/Snapshots/Read"

"Microsoft. Storage/storageAccounts/Write"

"Microsoft. Storage/storageAccounts/Read"

"Microsoft. Storage/storageAccounts/Delete"

"Microsoft. Resources/subscriptions/resourceGroups/Read"

"Microsoft. Storage/storageAccounts/listkeys/Action"

"Microsoft. Compute/Snapshots/beginGetAccess/Action"

"Microsoft. Compute/Snapshots/endGetAccess/Action"

"Microsoft. Compute/disks/beginGetAccess/Action"

A seguir estão as ações usadas na função de **operador de restauração de disco** atribuída no grupo de recursos de **destino**:

"Microsoft. Compute/disks/Write"

"Microsoft. Compute/disks/Read"

"Microsoft. Resources/subscriptions/resourceGroups/Read"

>[!NOTE]
>As permissões nessas funções podem ser alteradas no futuro, com base nos recursos que estão sendo adicionados pelo serviço de backup do Azure.

## <a name="next-steps"></a>Próximas etapas

- [Matriz de suporte de Backup de Disco do Azure](disk-backup-support-matrix.md)