---
title: Executar ações usando o centro de backup
description: Este artigo explica como executar ações usando o centro de backup
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8c21475e5a52cdce7e38bbeb9d00df3c3ac3a752
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506422"
---
# <a name="perform-actions-using-backup-center"></a>Executar ações usando o centro de backup

O centro de backup permite que você execute as principais ações relacionadas ao backup de uma interface central sem a necessidade de navegar para um cofre individual. Algumas ações que podem ser executadas no centro de backup são:

* Configurar o backup para suas fontes de fonte
* Restaurar uma instância de backup
* Criar um novo cofre
* Criar uma nova política de backup
* Disparar um backup sob demanda para uma instância de backup
* Parar o backup de uma instância de backup

## <a name="supported-scenarios"></a>Cenários com suporte

* O centro de backup tem suporte no momento para backup de VM do Azure, SQL no backup de VM do Azure, SAP HANA no backup de VM do Azure, backup de arquivos do Azure e banco de dados do Azure para backup do servidor PostgreSQL.
* Consulte a [matriz de suporte](backup-center-support-matrix.md) para obter uma lista detalhada de cenários com e sem suporte.

## <a name="configure-backup"></a>Configurar backup

Se você estiver fazendo backup de VMs do Azure, SQL em VMs do Azure, SAP HANA em VMs do Azure ou arquivos do Azure, você deve usar um cofre dos serviços de recuperação. Se você estiver fazendo backup de bancos de dados do Azure para o servidor PostgreSQL, deverá usar um cofre de backup. 

Dependendo do tipo de fonte de origem que você deseja fazer backup, siga as instruções apropriadas, conforme descrito abaixo.

### <a name="configure-backup-to-a-recovery-services-vault"></a>Configurar o backup para um cofre dos serviços de recuperação

1. Navegue até o centro de backup e selecione **+ backup** na parte superior da guia **visão geral** .

    ![Visão geral do centro de backup](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. Selecione o tipo de fonte de origem que você deseja fazer backup.

    ![Selecionar DataSource para configurar o backup da VM](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Escolha um cofre dos serviços de recuperação e selecione **continuar**. Isso leva você para a experiência de configuração de backup que é idêntica à que pode ser acessada por um cofre dos serviços de recuperação. [Saiba mais sobre como configurar o backup para máquinas virtuais do Azure com um cofre dos serviços de recuperação](tutorial-backup-vm-at-scale.md).

### <a name="configure-backup-to-a-backup-vault"></a>Configurar o backup para um cofre de backup

1. Navegue até o centro de backup e selecione **+ backup** na parte superior da guia **visão geral** .
2. Selecione o tipo de fonte de dados do qual você deseja fazer backup (servidor do Azure para PostgreSQL neste caso).

    ![Selecione DataSource para configurar o banco de dados do Azure para backup do servidor PostgreSQL](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. Selecione **Continuar**. Isso leva você à experiência de configuração de backup que é idêntica à que pode ser acessada por um cofre de backup. [Saiba mais sobre como configurar o backup do banco de dados do Azure para o servidor PostgreSQL com um cofre de backup](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases).

## <a name="restore-a-backup-instance"></a>Restaurar uma instância de backup

Dependendo do tipo de fonte de origem que você deseja restaurar, siga as instruções apropriadas, conforme descrito abaixo.

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>Se você estiver restaurando de um cofre dos serviços de recuperação

1. Navegue até o centro de backup e selecione **restaurar** na parte superior da guia **visão geral** .

    ![Visão geral do centro de backup para restaurar a VM](./media/backup-center-actions/backup-center-overview-restore.png)

2. Selecione o tipo de fonte de origem que você deseja restaurar.

    ![Selecionar fonte de origem para restauração da VM](./media/backup-center-actions/restore-select-datasource-vm.png)

3. Escolha uma instância de backup e selecione **continuar**. Isso leva você para a experiência de configurações de restauração que é idêntica à que pode ser acessada por um cofre de serviços de recuperação. [Saiba mais sobre como restaurar uma máquina virtual do Azure com um cofre dos serviços de recuperação](backup-azure-arm-restore-vms.md#before-you-start).

### <a name="if-youre-restoring-from-a-backup-vault"></a>Se você estiver restaurando de um cofre de backup

1. Navegue até o centro de backup e selecione **restaurar** na parte superior da guia **visão geral** .
2. Selecione o tipo de fonte de dados que você deseja restaurar (servidor do Azure para PostgreSQL neste caso).

    ![Selecionar DataSource para banco de dados do Azure para restauração do servidor PostgreSQL](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. Escolha uma instância de backup e selecione **continuar**. Isso leva você para a experiência de configurações de restauração que é idêntica à que pode ser acessada por um cofre de serviços de recuperação. [Saiba mais sobre como restaurar um servidor de banco de dados do Azure para PostgreSQL com um cofre de backup](backup-azure-database-postgresql.md#restore).

## <a name="create-a-new-vault"></a>Criar um novo cofre

Você pode criar um novo cofre navegando até o centro de backup e selecionando **+ Vault** na parte superior da guia **visão geral** .

![Criar cofre](./media/backup-center-actions/backup-center-create-vault.png)

* [Saiba mais sobre como criar um cofre dos serviços de recuperação](backup-create-rs-vault.md)
* [Saiba mais sobre como criar um cofre de backup](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>Criar uma nova política de backup

Dependendo do tipo de fonte de origem que você deseja fazer backup, siga as instruções apropriadas descritas abaixo.

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>Se você estiver fazendo backup em um cofre dos serviços de recuperação

1. Navegue até o centro de backup e selecione **+ política** na parte superior da guia **visão geral** .

    ![Visão geral do centro de backup para a política de backup](./media/backup-center-actions/backup-center-overview-policy.png)

2. Selecione o tipo de fonte de origem que você deseja fazer backup.

    ![Selecionar fonte de origem para a política de backup de VM](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Escolha um cofre dos serviços de recuperação e selecione **continuar**. Isso leva você à experiência de criação de política que é idêntica à acessível por meio de um cofre dos serviços de recuperação. [Saiba mais sobre como criar uma nova política de backup para a máquina virtual do Azure com um cofre dos serviços de recuperação](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

### <a name="if-youre-backing-up-to-a-backup-vault"></a>Se você estiver fazendo backup em um cofre de backup

1. Navegue até o centro de backup e selecione **+ política** na parte superior da guia **visão geral** .
2. Selecione o tipo de fonte de dados do qual você deseja fazer backup (servidor do Azure para PostgreSQL neste caso).

    ![Selecionar DataSource para política para o banco de dados do Azure para backup do servidor PostgreSQL](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. Selecione **Continuar**. Isso leva você à experiência de criação de política que é idêntica à acessível por meio de um cofre de backup. [Saiba mais sobre como criar uma nova política de backup com um cofre de backup](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>Executar um backup sob demanda para uma instância de backup

O centro de backup permite que você pesquise instâncias de backup em seu espaço de backup e execute operações de backup sob demanda.

Para disparar um backup sob demanda, navegue até o centro de backup e selecione o item de menu **instâncias de backup** . Selecionar isso permite exibir detalhes de todas as instâncias de backup às quais você tem acesso. Você pode pesquisar a instância de backup da qual deseja fazer backup. Clicar com o botão direito do mouse em um item na grade abre uma lista de ações disponíveis. Selecione a opção **fazer backup agora** para executar um backup sob demanda.

![Backup sob demanda](./media/backup-center-actions/backup-center-on-demand-backup.png)

[Saiba mais sobre como executar backups sob demanda para máquinas virtuais do Azure](backup-azure-manage-vms.md#run-an-on-demand-backup).

[Saiba mais sobre como executar backups sob demanda para o servidor do banco de dados do Azure para PostgreSQL](backup-azure-database-postgresql.md#on-demand-backup).

## <a name="stop-backup-for-a-backup-instance"></a>Parar o backup de uma instância de backup

Há cenários em que você talvez queira interromper o backup para uma instância de backup, como quando o backup do recurso subjacente não existe mais.

Para disparar um backup sob demanda, navegue até o centro de backup e selecione o item de menu **instâncias de backup** . Selecione isso permite exibir detalhes de todas as instâncias de backup às quais você tem acesso. Você pode pesquisar a instância de backup da qual deseja fazer backup. Clicar com o botão direito do mouse em um item na grade abre uma lista de ações disponíveis. Selecione a opção **parar backup** para interromper o backup da instância de backup.

![Parar a proteção](./media/backup-center-actions/backup-center-stop-protection.png)

[Saiba mais sobre como parar o backup de máquinas virtuais do Azure](backup-azure-manage-vms.md#stop-protecting-a-vm).

[Saiba mais sobre como interromper o backup do banco de dados do Azure para servidor PostgreSQL](backup-azure-database-postgresql.md#stop-protection)

## <a name="next-steps"></a>Próximas etapas

* [Monitorar e operar backups](backup-center-monitor-operate.md)
* [Governar seu espaço de backup](backup-center-govern-environment.md)
* [Obtenha informações sobre seus backups](backup-center-obtain-insights.md)
