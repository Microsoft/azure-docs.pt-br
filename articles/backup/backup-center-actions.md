---
title: Executar ações usando o centro de backup
description: Este artigo explica como executar ações usando o centro de backup
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8c21475e5a52cdce7e38bbeb9d00df3c3ac3a752
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506422"
---
# <a name="perform-actions-using-backup-center"></a>Executar ações usando o centro de backup

O centro de backup permite que você execute as principais ações relacionadas ao backup por meio de uma interface central, sem a necessidade de navegar para um cofre individual. Algumas ações que podem ser executadas por meio do centro de backup são:

* Configurar o backup para as suas fontes de dados
* Restaurar uma instância de backup
* Criar um novo cofre
* Criar uma nova política de backup
* Disparar um backup sob demanda para uma instância de backup
* Parar o backup de uma instância de backup

## <a name="supported-scenarios"></a>Cenários com suporte

* O centro de backup tem suporte no momento para backup de VM do Azure, do SQL em VM do Azure, do SAP HANA em VM do Azure, de Arquivos do Azure e de Banco de Dados do Azure para PostgreSQL Server.
* Confira a [matriz de suporte](backup-center-support-matrix.md) para obter uma lista detalhada de cenários com e sem suporte.

## <a name="configure-backup"></a>Configurar backup

Se estiver fazendo backup de VMs do Azure, SQL em VMs do Azure, SAP HANA em VMs do Azure ou Arquivos do Azure, você deverá usar um cofre dos Serviços de Recuperação. Se estiver fazendo backup de Bancos de Dados do Azure para PostgreSQL Server, você deverá usar um cofre de backup. 

Dependendo do tipo de fonte de dados da qual você deseja fazer backup, siga as instruções apropriadas, conforme descrito abaixo.

### <a name="configure-backup-to-a-recovery-services-vault"></a>Configurar o backup para um cofre dos Serviços de Recuperação

1. Navegue até o centro de backup e selecione **+ Backup**, na parte superior da guia **Visão geral**.

    ![Visão geral do centro de backup](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. Selecione o tipo de fonte de dados da qual você deseja fazer backup.

    ![Selecionar a fonte de dados para configurar o backup de VM](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Escolha um cofre dos Serviços de Recuperação e selecione **Prosseguir**. Isso leva você a uma experiência de configuração de backup que é idêntica àquela que pode ser alcançada por meio de um cofre dos Serviços de Recuperação. [Saiba mais sobre como configurar o backup de máquinas virtuais do Azure com um cofre dos Serviços de Recuperação](tutorial-backup-vm-at-scale.md).

### <a name="configure-backup-to-a-backup-vault"></a>Configurar o backup para um cofre de backup

1. Navegue até o centro de backup e selecione **+ Backup**, na parte superior da guia **Visão geral**.
2. Selecione o tipo de fonte de dados do qual você deseja fazer backup (Banco de Dados do Azure para PostgreSQL Server, nesse caso).

    ![Selecione a fonte de dados para configurar o backup do Banco de Dados do Azure para PostgreSQL Server](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. Selecione **Continuar**. Isso leva você a uma experiência de configuração de backup que é idêntica àquela que pode ser alcançada por meio de um cofre de backup. [Saiba mais sobre como configurar o backup do Banco de Dados do Azure para PostgreSQL Server com um cofre de backup](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases).

## <a name="restore-a-backup-instance"></a>Restaurar uma instância de backup

Dependendo do tipo de fonte de dados que você deseja restaurar, siga as instruções apropriadas, conforme descrito abaixo.

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>Se você estiver restaurando de um cofre dos Serviços de Recuperação

1. Navegue até o centro de backup e selecione **Restaurar**, na parte superior da guia **Visão geral**.

    ![Visão geral do centro de backup para restaurar VM](./media/backup-center-actions/backup-center-overview-restore.png)

2. Selecione o tipo de fonte de dados que você deseja restaurar.

    ![Selecionar a fonte de dados para restauração da VM](./media/backup-center-actions/restore-select-datasource-vm.png)

3. Escolha uma instância de backup e selecione **Prosseguir**. Isso leva você a uma experiência de configurações de restauração que é idêntica àquela que pode ser alcançada por meio de um cofre dos Serviços de Recuperação. [Saiba mais sobre como restaurar uma Máquina Virtual do Azure com um cofre dos Serviços de Recuperação](backup-azure-arm-restore-vms.md#before-you-start).

### <a name="if-youre-restoring-from-a-backup-vault"></a>Se você estiver restaurando por meio de um cofre de backup

1. Navegue até o centro de backup e selecione **Restaurar**, na parte superior da guia **Visão geral**.
2. Selecione o tipo de fonte de dados que você deseja restaurar (Banco de Dados do Azure para PostgreSQL Server, nesse caso).

    ![Selecionar a fonte de dados para a restauração do Banco de Dados do Azure para PostgreSQL Server](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. Escolha uma instância de backup e selecione **Prosseguir**. Isso leva você a uma experiência de configurações de restauração que é idêntica àquela que pode ser alcançada por meio de um cofre dos Serviços de Recuperação. [Saiba mais sobre como restaurar um Banco de Dados do Azure para PostgreSQL Server com um cofre de backup](backup-azure-database-postgresql.md#restore).

## <a name="create-a-new-vault"></a>Criar um novo cofre

Você pode criar um cofre navegando até o centro de backup e selecionando **+ Cofre**, na parte superior da guia **Visão geral**.

![Criar cofre](./media/backup-center-actions/backup-center-create-vault.png)

* [Saiba mais sobre como criar um cofre dos Serviços de Recuperação](backup-create-rs-vault.md)
* [Saiba mais sobre como criar um cofre de backup](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>Criar uma nova política de backup

Dependendo do tipo de fonte de dados da qual você deseja fazer backup, siga as instruções apropriadas descritas abaixo.

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>Se você estiver fazendo backup para um cofre dos Serviços de Recuperação

1. Navegue até o centro de backup e selecione **+ Política**, na parte superior da guia **Visão geral**.

    ![Visão geral do centro de backup para a política de backup](./media/backup-center-actions/backup-center-overview-policy.png)

2. Selecione o tipo de fonte de dados da qual você deseja fazer backup.

    ![Selecionar a fonte de dados da política para backup de VM](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Escolha um cofre dos Serviços de Recuperação e selecione **Prosseguir**. Isso leva você a uma experiência de criação de política que é idêntica àquela que pode ser alcançada por meio de um cofre dos Serviços de Recuperação. [Saiba mais sobre como criar uma política de backup para uma Máquina Virtual do Azure com um cofre dos Serviços de Recuperação](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

### <a name="if-youre-backing-up-to-a-backup-vault"></a>Se você estiver fazendo backup para um cofre de backup

1. Navegue até o centro de backup e selecione **+ Política**, na parte superior da guia **Visão geral**.
2. Selecione o tipo de fonte de dados do qual você deseja fazer backup (Banco de Dados do Azure para PostgreSQL Server, nesse caso).

    ![Selecionar a fonte de dados para a política de backup do Banco de Dados do Azure para PostgreSQL Server](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. Selecione **Continuar**. Isso leva você a uma experiência de criação de política que é idêntica àquela que pode ser alcançada por meio de um cofre de backup. [Saiba mais sobre como criar uma política de backup com um cofre de backup](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>Executar um backup sob demanda para uma instância de backup

O centro de backup permite que você pesquise por instâncias de backup em sua propriedade de backup e execute operações de backup sob demanda.

Para disparar um backup sob demanda, navegue até o centro de backup e selecione o item de menu **Instâncias de backup**. Selecionar esse item de menu permitirá exibir detalhes sobre todas as instâncias de backup às quais você tem acesso. Você pode pesquisar pela instância de backup da qual deseja fazer backup. Clicar com o botão direito do mouse em um item da grade abre uma lista de ações disponíveis. Selecione a opção **Fazer backup agora** para executar um backup sob demanda.

![Backup sob demanda](./media/backup-center-actions/backup-center-on-demand-backup.png)

[Saiba mais sobre como executar backups sob demanda para Máquinas Virtuais do Azure](backup-azure-manage-vms.md#run-an-on-demand-backup).

[Saiba mais sobre como executar backups sob demanda para Banco de Dados do Azure para PostgreSQL Server](backup-azure-database-postgresql.md#on-demand-backup).

## <a name="stop-backup-for-a-backup-instance"></a>Parar o backup de uma instância de backup

Há cenários em que você talvez queira parar o backup de uma instância de backup, como quando o recurso subjacente cujo backup está sendo feito não existe mais.

Para disparar um backup sob demanda, navegue até o centro de backup e selecione o item de menu **Instâncias de backup**. Selecionar esse item de menu permitirá exibir detalhes sobre todas as instâncias de backup às quais você tem acesso. Você pode pesquisar pela instância de backup da qual deseja fazer backup. Clicar com o botão direito do mouse em um item da grade abre uma lista de ações disponíveis. Selecione a opção **Parar Backup** para interromper o backup da instância de backup.

![Parar a proteção](./media/backup-center-actions/backup-center-stop-protection.png)

[Saiba mais sobre como parar o backup de Máquinas Virtuais do Azure](backup-azure-manage-vms.md#stop-protecting-a-vm).

[Saiba mais sobre como parar o backup do Banco de Dados do Azure para PostgreSQL Server](backup-azure-database-postgresql.md#stop-protection)

## <a name="next-steps"></a>Próximas etapas

* [Monitorar e operar backups](backup-center-monitor-operate.md)
* [Administrar sua propriedade de backup](backup-center-govern-environment.md)
* [Obter insights sobre seus backups](backup-center-obtain-insights.md)
