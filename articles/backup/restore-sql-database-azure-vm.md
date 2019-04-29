---
title: Use o Backup do Azure para restaurar bancos de dados do backup do SQL Server em uma VM do Azure | Microsoft Docs
description: Este artigo descreve como restaurar bancos de dados do SQL Server que estiver executando em uma VM do Azure e que é feito backup com o Backup do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: raynew
ms.openlocfilehash: 1712e46494796e563c26316b4f45d968872c304f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60781640"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Restaurar bancos de dados do SQL Server em VMs do Azure

Este artigo descreve como restaurar um banco de dados do SQL Server em execução em uma máquina virtual do Azure (VM) que o [Backup do Azure](backup-overview.md) serviço fez o backup em um cofre de serviços de recuperação de Backup do Azure.

Este artigo descreve como restaurar bancos de dados do SQL Server. Para obter mais informações, consulte [fazer backup de bancos de dados do SQL Server em VMs do Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Restaurar para uma hora ou um ponto de recuperação

O Backup do Azure pode restaurar bancos de dados do SQL Server que são executados em VMs do Azure da seguinte maneira:

- Restaure para uma determinada data ou hora (para o segundo) por meio de backups de log de transações. O Backup do Azure determina automaticamente o backup diferencial completo apropriado e a cadeia de backups de log que são necessários para restaurar com base no tempo selecionado.
- Restaure um backup completo ou diferencial específico para restaurar para um ponto de recuperação específico.


## <a name="prerequisites"></a>Pré-requisitos

Antes de restaurar um banco de dados, observe o seguinte:

- Você pode restaurar o banco de dados para uma instância de um SQL Server na mesma região do Azure.
- O servidor de destino precisa ser registrado no mesmo cofre que a fonte.
- Para restaurar um banco de dados criptografado com TDE para outro SQL Server, primeiro você precisa [restaurar o certificado para o servidor de destino](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Antes de restaurar o banco de dados "mestre", iniciar a instância do SQL Server no modo de usuário único usando a opção de inicialização **-m AzureWorkloadBackup**.
    - O valor para **-m** é o nome do cliente.
    - Somente o nome do cliente especificado pode abrir a conexão.
- Para todos os bancos de dados do sistema (modelo, master, msdb), pare o serviço do SQL Server Agent, antes de disparar a restauração.
- Feche os aplicativos que podem tentar fazer uma conexão a qualquer um desses bancos de dados.

## <a name="restore-a-database"></a>Restaurar um banco de dados

Para restaurar, você precisará das seguintes permissões:

* **Operador de backup** permissões no cofre em que você está fazendo a restauração.
* Acesso de **Colaborador (gravação)** à VM de origem da qual será feito backup.
* Acesso de **Colaborador (gravação)** à VM de destino:
    - Se você estiver restaurando para a mesma VM, isso é a VM de origem.
    - Se você estiver restaurando para um local alternativo, essa é a nova VM de destino.

Restaure da seguinte maneira:
1. Abra o cofre no qual a VM do SQL Server está registrada.
2. No painel do cofre, em **Uso**, selecione **Itens de Backup**.
3. Em **Itens de Backup**, em **Tipo de Gerenciamento de Backup**, selecione **SQL na VM do Azure**.

    ![Selecionar SQL na VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selecione o banco de dados a ser restaurado.

    ![Selecionar o banco de dados a ser restaurado](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Examine o menu do banco de dados. Ele fornece informações sobre o backup do banco de dados, incluindo:

    * Os pontos de restauração mais antigo e mais recentes.
    * O status do backup de log para as últimas 24 horas para bancos de dados que estão no modo de recuperação completa e bulk-logged e que são configurados para backups de log transacional.

6. Selecione **restaurar banco de dados**.

    ![Selecione Restaurar BD](./media/backup-azure-sql-database/restore-db-button.png)

7. Na **restauração da configuração**, especifique onde restaurar os dados:
   - **Local Alternativo**: Restaurar o banco de dados para um local alternativo e manter o banco de dados de origem original.
   - **Substituir BD**: Restaure os dados para a mesma instância do SQL Server que a fonte original. Essa opção substitui o banco de dados original.

     > [!Important]
     > Se o banco de dados selecionado pertencer a um grupo de disponibilidade Always On, o SQL Server não permitirá que o banco de dados seja substituído. Somente **Localização Alternativa** está disponível.
     >

     ![Menu Restaurar Configuração](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Restaurar para um local alternativo

1. No **restaurar configuração** menu, em **onde deseja restaurar**, selecione **local alternativo**.
2. Selecione o nome do SQL Server e a instância na qual você deseja restaurar o banco de dados.
3. Na caixa **Nome do Banco de Dados Restaurado**, digite o nome do banco de dados de destino.
4. Se aplicável, selecione **Substituir se o BD com o mesmo nome já existir na instância do SQL selecionada**.
5. Selecione **OK**.

    ![Forneça valores para o menu de Configuração da Restauração](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Na **Selecionar ponto de restauração**, selecione se deseja [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time) ou a [restaurar para um ponto de recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração point-in-time está disponível somente para backups de log para bancos de dados que estão no modo de recuperação completa e bulk-logged.

### <a name="restore-and-overwrite"></a>Restaurar e substituir

1. No **restaurar configuração** menu, em **onde deseja restaurar**, selecione **DB substituir** > **Okey**.

    ![Selecione Substituir Banco de Dados](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Na **Selecionar ponto de restauração**, selecione **Logs (pontuais)** para [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione **completo & diferencial** para restaurar para um [ponto de recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração point-in-time está disponível somente para backups de log para bancos de dados que estão no modo de recuperação completa e bulk-logged.

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar a um ponto específico no tempo

Se você tiver selecionado **Logs (Pontual)** como o tipo de restauração, faça o seguinte:

1.  Sob **data/hora de restauração**, abrir o calendário. No calendário, as datas que têm pontos de recuperação são exibidas em negrito, e a data atual é realçada.
1. Selecione uma data que tem pontos de recuperação. Não é possível selecionar datas que não têm nenhum ponto de recuperação.

    ![Abrir o calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Depois de você selecionar uma data, o gráfico de linha do tempo exibirá os pontos de recuperação disponíveis em um intervalo contínuo.
1. Especifique uma hora para a recuperação no gráfico de linha do tempo, ou selecione uma hora. Depois, selecione **OK**.

    ![Selecione uma hora de restauração](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. Sobre o **Advanced Configuration** menu, se você quiser manter o banco de dados não-operacional após a restauração, habilitar **restaurar com NORECOVERY**.
1. Se quiser alterar a localização de restauração no servidor de destino, insira um novo caminho de destino.
1. Selecione **OK**.

    ![Menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração.
1. Acompanhar o andamento de restauração na **notificações** área, ou controlá-la selecionando **trabalhos de restauração** no menu de banco de dados.

    ![Progresso do trabalho de restauração](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Restaurar a um ponto de restauração específico

Se você tiver selecionado **Completo e Diferencial** como o tipo de restauração, faça o seguinte:

1. Selecione um ponto de recuperação da lista e selecione **OK** para concluir o procedimento de ponto de restauração.

    ![Escolha um ponto de recuperação completo](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. Sobre o **Advanced Configuration** menu, se você quiser manter o banco de dados não-operacional após a restauração, habilitar **restaurar com NORECOVERY**.
1. Se quiser alterar a localização de restauração no servidor de destino, insira um novo caminho de destino.
1. Selecione **OK**.

    ![Menu de Configuração Avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração.
1. Acompanhar o andamento de restauração na **notificações** área, ou controlá-la selecionando **trabalhos de restauração** no menu de banco de dados.

    ![Progresso do trabalho de restauração](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>Próximas etapas

[Gerenciar e monitorar](manage-monitor-sql-database-backup.md) bancos de dados do SQL Server que passam por backup pelo Backup do Azure.
