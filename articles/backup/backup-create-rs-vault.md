---
title: Criar e configurar cofres dos serviços de recuperação
description: Neste artigo, saiba como criar e configurar cofres de serviços de recuperação que armazenam os backups e os pontos de recuperação. Saiba como usar a restauração entre regiões para restaurar em uma região secundária.
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions
ms.openlocfilehash: 1a20cd2b1245febea5fd18a9f6fe6e7a7bb6f04b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716747"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Criar e configurar um cofre dos serviços de recuperação

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Definir redundância de armazenamento

O backup do Azure manipula automaticamente o armazenamento para o cofre. Você precisa especificar como esse armazenamento é replicado.

> [!NOTE]
> A alteração do **tipo de replicação de armazenamento** (com redundância local/geograficamente) para um cofre dos serviços de recuperação deve ser feita antes de configurar backups no cofre. Depois de configurar o backup, a opção para modificar é desabilitada.
>
>- Se você ainda não tiver configurado o backup, [siga estas etapas](#set-storage-redundancy) para revisar e modificar as configurações.
>- Se você já configurou o backup e deve passar de GRS para LRS, [revise essas soluções alternativas](#how-to-change-from-grs-to-lrs-after-configuring-backup).

1. No painel **cofres dos serviços de recuperação** , selecione o novo cofre. Na seção **configurações** , selecione  **Propriedades**.
1. Em **Propriedades**, em **configuração de backup**, selecione **Atualizar**.

1. Selecione o tipo de replicação de armazenamento e selecione **salvar**.

     ![Definir a configuração de armazenamento para o novo cofre](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Recomendamos que, se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup primário, continue a usar a configuração padrão **com redundância geográfica** .
   - Se você não usar o Azure como um ponto de extremidade de armazenamento de backup principal, escolha **Localmente redundante**, que reduz os custos de armazenamento do Azure.
   - Saiba mais sobre a redundância [geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) e [local](../storage/common/storage-redundancy.md#locally-redundant-storage) .
   - Se você precisar de disponibilidade de dados sem tempo de inatividade em uma região, garantindo a residência de dados e, em seguida, escolha [armazenamento com redundância de zona](../storage/common/storage-redundancy.md#zone-redundant-storage).

>[!NOTE]
>As configurações de replicação de armazenamento para o cofre não são relevantes para o backup do compartilhamento de arquivos do Azure, pois a solução atual é baseada em instantâneo e não há dados transferidos para o cofre. Os instantâneos são armazenados na mesma conta de armazenamento que o compartilhamento de arquivos de backup.

## <a name="set-cross-region-restore"></a>Definir restauração entre regiões

A opção de restauração de **CRR (restauração entre regiões)** permite que você restaure dados em uma [região emparelhada do Azure](../best-practices-availability-paired-regions.md)e secundária.

Ele dá suporte às seguintes fontes de origem:

- VMs do Azure (disponibilidade geral)
- Bancos de dados SQL hospedados em VMs do Azure (versão prévia)
- SAP HANA bancos de dados hospedados em VMs do Azure (versão prévia)

Usar a restauração entre regiões permite que você:

- realizar análises quando houver um requisito de auditoria ou de conformidade
- restaurar os dados se houver um desastre na região primária

Ao restaurar uma VM, você pode restaurar a VM ou seu disco. Se você estiver restaurando de bancos de dados SQL/SAP HANA hospedados em VMs do Azure, poderá restaurar bancos de dados ou seus arquivos.

Para escolher esse recurso, selecione **habilitar restauração entre regiões** no painel **configuração de backup** .

Como esse processo está no nível de armazenamento, há [implicações de preços](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Antes de começar:
>
>- Examine a [matriz de suporte](backup-support-matrix.md#cross-region-restore) para obter uma lista de tipos e regiões gerenciados com suporte.
>- O recurso de CRR (restauração entre regiões) para VMs do Azure agora está em disponibilidade geral em todas as regiões públicas do Azure.
>- A restauração entre regiões para bancos de dados SQL e SAP HANA está em versão prévia em todas as regiões públicas do Azure.
>- A CRR é um recurso de consentimento de nível de cofre para qualquer cofre GRS (desativado por padrão).
>- Depois de aceitar, pode levar até 48 horas para que os itens de backup estejam disponíveis em regiões secundárias.
>- Atualmente, a CRR para VMs do Azure tem suporte apenas para VMs do Azure do Azure Resource Manager. Não haverá suporte para VMs clássicas do Azure.  Quando tipos de gerenciamento adicionais dão suporte à CRR, eles serão registrados **automaticamente** .
>- Atualmente, a restauração entre regiões **não pode ser revertida de volta** para grs ou LRS quando a proteção é iniciada pela primeira vez.
>- Atualmente, o [RPO](azure-backup-glossary.md#rpo-recovery-point-objective) de região secundária é de até 12 horas a partir da região primária, embora a replicação de [armazenamento com REDUNDÂNCIA geográfica (ra-grs) de acesso de leitura](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) seja de 15 minutos.

### <a name="configure-cross-region-restore"></a>Configurar a restauração entre regiões

Um cofre criado com redundância GRS inclui a opção de configurar o recurso de restauração entre regiões. Cada cofre do GRS terá um banner, que será vinculado à documentação. Para configurar a CRR para o cofre, vá para o painel configuração de backup, que contém a opção para habilitar esse recurso.

 ![Faixa de configuração de backup](./media/backup-azure-arm-restore-vms/banner.png)

1. No portal, acesse o cofre dos serviços de recuperação > **Propriedades** (em **configurações**).
1. Em **configuração de backup**, selecione **Atualizar**.
1. Selecione **habilitar a restauração entre regiões neste cofre** para habilitar a funcionalidade.

   ![Habilitar restauração entre regiões](./media/backup-azure-arm-restore-vms/backup-configuration.png)

Consulte estes artigos para obter mais informações sobre backup e restauração com CRR:

- [Restauração entre regiões para VMs do Azure](backup-azure-arm-restore-vms.md#cross-region-restore)
- [Restauração entre regiões para bancos de dados SQL](restore-sql-database-azure-vm.md#cross-region-restore)
- [Restauração entre regiões para bancos de dados SAP HANA](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>Definir configurações de criptografia

Por padrão, os dados no cofre dos serviços de recuperação são criptografados usando chaves gerenciadas por plataforma. Nenhuma ação explícita é necessária do seu fim para habilitar essa criptografia e ela se aplica a todas as cargas de trabalho cujo backup está sendo feito em seu cofre dos serviços de recuperação.  Você pode optar por trazer sua própria chave para criptografar os dados de backup neste cofre. Isso é conhecido como chaves gerenciadas pelo cliente. Se você deseja criptografar dados de backup usando sua própria chave, a chave de criptografia deve ser especificada antes que qualquer item seja protegido a esse cofre. Depois de habilitar a criptografia com a chave, ela não poderá ser revertida.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Configurando um cofre para criptografar usando chaves gerenciadas pelo cliente

Para configurar seu cofre para criptografar com chaves gerenciadas pelo cliente, essas etapas devem ser seguidas nesta ordem:

1. Habilitar identidade gerenciada para seu cofre de serviços de recuperação

1. Atribua permissões ao cofre para acessar a chave de criptografia no Azure Key Vault

1. Habilitar a proteção de exclusão e limpeza reversível no Azure Key Vault

1. Atribuir a chave de criptografia ao cofre dos serviços de recuperação

As instruções para cada uma dessas etapas podem ser encontradas [neste artigo](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys).

## <a name="modifying-default-settings"></a>Modificando configurações padrão

É altamente recomendável examinar as configurações padrão de **tipo de Replicação de Armazenamento** e **Configurações de segurança** antes de configurar backups no cofre.

- O **tipo de replicação de armazenamento** por padrão é definido como com **redundância geográfica** (GRS). Depois de configurar o backup, a opção para modificar é desabilitada.
  - Se você ainda não tiver configurado o backup, [siga estas etapas](#set-storage-redundancy) para revisar e modificar as configurações.
  - Se você já configurou o backup e deve passar de GRS para LRS, [revise essas soluções alternativas](#how-to-change-from-grs-to-lrs-after-configuring-backup).

- A **exclusão reversível** por padrão é **habilitada** em cofres recém-criados para proteger dados de backup de exclusões acidentais ou mal-intencionadas. [Siga estas etapas](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) para examinar e modificar as configurações.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Como alterar de GRS para LRS depois de configurar o backup

Antes de decidir migrar do GRS para o armazenamento com redundância local (LRS), examine as compensações entre custos menores e maior durabilidade dos dados que se adaptam ao seu cenário. Se você precisar mover de GRS para LRS, terá duas opções. Eles dependem de seus requisitos de negócios para manter os dados de backup:

- [Não é necessário preservar os dados de backup anteriores](#dont-need-to-preserve-previous-backed-up-data)
- [Deve preservar os dados anteriores de backup](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Não é necessário preservar os dados de backup anteriores

Para proteger as cargas de trabalho em um novo cofre do LRS, a proteção e os dados atuais precisarão ser excluídos no cofre do GRS e os backups serão configurados novamente.

>[!WARNING]
>A operação a seguir é destrutiva e não pode ser desfeita. Todos os dados de backup e itens de backup associados ao servidor protegido serão excluídos permanentemente. Continue com cuidado.

Pare e exclua a proteção atual no cofre do GRS:

1. Desabilite a exclusão reversível nas propriedades do cofre GRS. Siga [estas etapas](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) para desabilitar a exclusão reversível.

1. Interrompa a proteção e exclua backups do cofre GRS existente. No menu do painel do cofre, selecione **itens de backup**. Os itens listados aqui que precisam ser movidos para o cofre LRS devem ser removidos junto com seus dados de backup. Consulte como [excluir itens protegidos na nuvem](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) e [excluir itens protegidos localmente](backup-azure-delete-vault.md#delete-protected-items-on-premises).

1. Se estiver planejando mover os AFS (compartilhamentos de arquivos do Azure), servidores SQL ou servidores SAP HANA, você também precisará cancelar seu registro. No menu do painel do cofre, selecione **infraestrutura de backup**. Consulte como [cancelar o registro do SQL Server](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance), [cancelar o registro de uma conta de armazenamento associada aos compartilhamentos de arquivos do Azure](manage-afs-backup.md#unregister-a-storage-account)e [cancelar o registro de uma instância de SAP Hana](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

1. Depois que eles forem removidos do cofre do GRS, continue a configurar os backups para sua carga de trabalho no novo cofre LRS.

#### <a name="must-preserve-previous-backed-up-data"></a>Deve preservar os dados anteriores de backup

Se você precisar manter os dados protegidos atuais no cofre do GRS e continuar a proteção em um novo cofre LRS, haverá opções limitadas para algumas das cargas de trabalho:

- Para MARS, você pode [parar a proteção com reter dados](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) e registrar o agente no novo cofre do lRS.

  - O serviço de backup do Azure continuará a reter todos os pontos de recuperação existentes do cofre do GRS.
  - Você precisará pagar para manter os pontos de recuperação no cofre do GRS.
  - Você poderá restaurar os dados de backup somente para pontos de recuperação não expirados no cofre do GRS.
  - Será necessário criar uma nova réplica inicial dos dados no cofre do LRS.

- Para uma VM do Azure, você pode [parar a proteção com reter dados](backup-azure-manage-vms.md#stop-protecting-a-vm) para a VM no cofre do grs, mover a VM para outro grupo de recursos e, em seguida, proteger a VM no cofre do lRS. Consulte as [diretrizes e limitações](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) para mover uma VM para outro grupo de recursos.

  Uma VM pode ser protegida em apenas um cofre por vez. No entanto, a VM no novo grupo de recursos pode ser protegida no cofre do LRS, pois ela é considerada uma VM diferente.

  - O serviço de backup do Azure manterá os pontos de recuperação cujo backup foi feito no cofre do GRS.
  - Você precisará pagar para manter os pontos de recuperação no cofre do GRS (consulte [preços de backup do Azure](azure-backup-pricing.md) para obter detalhes).
  - Você poderá restaurar a VM, se necessário, do cofre do GRS.
  - O primeiro backup no cofre LRS da VM no novo recurso será uma réplica inicial.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](backup-azure-recovery-services-vault-overview.md) Cofres dos serviços de recuperação.
[Saiba mais](backup-azure-delete-vault.md) Excluir cofres dos serviços de recuperação.