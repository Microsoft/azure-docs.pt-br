---
title: Fazer backup de uma VM SQL Server no painel VM
description: Neste artigo, saiba como fazer backup de bancos de dados do SQL Server em máquinas virtuais do Azure por meio do painel VM.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 4f4ea202ee96e93a621c8dd0025c9ebc8b8d445d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88891650"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>Fazer backup de um SQL Server no painel de VM

Este artigo explica como fazer backup de SQL Server em execução em VMs do Azure com o serviço de [backup do Azure](backup-overview.md) . Você pode fazer backup de SQL Server VMs usando dois métodos:

- SQL Server única VM do Azure: as instruções neste artigo descrevem como fazer backup de uma VM SQL Server diretamente da exibição da VM.
- Várias VMs SQL Server do Azure: você pode configurar um cofre de serviços de recuperação e configurar o backup para várias VMs. Siga as instruções neste [artigo](backup-sql-server-database-azure-vms.md) para esse cenário.

## <a name="before-you-start"></a>Antes de começar

1. Verifique seu ambiente com a [matriz de suporte](sql-support-matrix.md).
2. Obtenha uma [visão geral](backup-azure-sql-database.md) do backup do Azure para SQL Server VM.
3. Verifique se a VM tem [conectividade de rede](backup-sql-server-database-azure-vms.md#establish-network-connectivity).

## <a name="configure-backup-on-the-sql-server"></a>Configurar o backup no SQL Server

Você pode habilitar o backup em sua VM SQL Server do painel **backup** na VM. Esse método faz duas coisas:

- Registra a VM do SQL com o serviço de backup do Azure para conceder acesso.
- Protege autoproteção de todas as instâncias de SQL Server em execução dentro da VM. Isso significa que a política de backup é aplicada a todos os bancos de dados existentes, bem como aos bancos de dados que serão adicionados a essas instâncias no futuro.

1. Selecione a faixa na parte superior da página para abrir a exibição de SQL Server Backup.

    ![SQL Server exibição de backup](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >Não consegue ver a faixa? A faixa só é exibida para as VMs SQL Server que são criadas usando as imagens do Azure Marketplace. Ele é exibido adicionalmente para as VMs que são protegidas com o backup de VM do Azure. Para outras imagens, você pode configurar o backup conforme explicado [aqui](backup-sql-server-database-azure-vms.md).

2. Insira o nome do cofre dos serviços de recuperação. Um cofre é uma entidade lógica para armazenar e gerenciar todos os seus backups. Se você criar um novo cofre:

    - Ele será criado na mesma assinatura e região que a VM SQL Server que você está protegendo.
    - Ele será criado com a configuração GRS (armazenamento com redundância geográfica) para todos os backups. Se você quiser alterar o tipo de redundância, deverá fazê-lo antes de proteger a VM. Para obter mais informações, consulte [este artigo](backup-create-rs-vault.md#set-storage-redundancy).

3. Escolha uma **política de backup**. Você pode escolher entre a política padrão ou outras políticas existentes que você criou no cofre. Se você quiser criar uma nova política, poderá consultar [Este artigo](backup-sql-server-database-azure-vms.md#create-a-backup-policy) para obter um guia passo a passo.

    ![Escolher uma política de backup](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. Selecione **Habilitar Backup**. A operação pode levar alguns minutos para ser concluída.

    ![Selecione habilitar backup](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. Quando a operação for concluída, você verá o **nome do cofre** na faixa.

    ![O nome do cofre aparece na faixa](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. Selecione a faixa para acessar a exibição do cofre, onde você pode ver todas as VMs registradas e seu status de proteção.

    ![Exibição de cofre com VMs registradas](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. Para imagens que não são do Marketplace, o registro pode ser bem-sucedido, mas **Configurar o backup** pode não ser disparado até que a extensão de backup do Azure receba permissão no SQL Server. Nesses casos, a coluna **prontidão para backup** lê **não está pronta**. Você precisa [atribuir as permissões apropriadas](backup-azure-sql-database.md#set-vm-permissions) manualmente para imagens que não são do Marketplace, para que a configuração de backup possa ser disparada.

    ![A preparação do backup não está pronta](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. Para operações adicionais ou monitoramento que você precisa fazer na VM de backup SQL Server, vá para o cofre dos serviços de recuperação correspondente. Vá para **itens de backup** para ver todos os bancos de dados dos quais foi feito backup nesse cofre e acione operações como backup e restauração sob demanda. Da mesma forma, acesse **trabalhos de backup** para [monitorar](manage-monitor-sql-database-backup.md) trabalhos correspondentes a operações como configurar a proteção, o backup e a restauração.

    ![Consulte backup de bancos de dados em itens de backup](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>O backup não é configurado automaticamente em qualquer uma das novas instâncias de SQL Server que possam ser adicionadas posteriormente à VM protegida. Para configurar o backup nas instâncias adicionadas recentemente, você precisará acessar o cofre em que a VM está registrada e seguir as etapas listadas [aqui](backup-sql-server-database-azure-vms.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como:

- [Restaurar backups de bancos de dados do SQL Server](restore-sql-database-azure-vm.md)
- [Gerenciar backups de bancos de dados do SQL Server](manage-monitor-sql-database-backup.md)
