---
title: Restaurar bancos de dados SQL Server em uma VM do Azure
description: Este artigo descreve como restaurar SQL Server bancos de dados que estão em execução em uma VM do Azure e cujo backup é feito com o backup do Azure. Você também pode usar a restauração entre regiões para restaurar seus bancos de dados para uma região secundária.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 7dd8d8d54fa7d33bb4a0935357597d19dd2368c5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734395"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Restaurar bancos de dados do SQL Server em VMs do Azure

Este artigo descreve como restaurar um banco de dados SQL Server que está em execução em uma VM (máquina virtual) do Azure em que o backup do serviço [do Azure](backup-overview.md) foi feito em um cofre dos serviços de recuperação de backup do Azure.

Este artigo descreve como restaurar bancos de dados do SQL Server. Para obter mais informações, consulte [fazer backup de bancos de dados SQL Server em VMs do Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Restaurar para um momento ou um ponto de recuperação

O backup do Azure pode restaurar SQL Server bancos de dados que estão em execução em VMs do Azure da seguinte maneira:

- Restaurar para uma data ou hora específica (para o segundo) usando backups de log de transações. O backup do Azure determina automaticamente o backup diferencial completo apropriado e a cadeia de backups de log que são necessários para restaurar com base na hora selecionada.
- Restaure um backup completo ou diferencial específico para restaurar para um ponto de recuperação específico.

## <a name="restore-prerequisites"></a>Pré-requisitos de restauração

Antes de restaurar um banco de dados, observe o seguinte:

- Você pode restaurar o banco de dados para uma instância de um SQL Server na mesma região do Azure.
- O servidor de destino precisa ser registrado no mesmo cofre que a fonte.
- Se você tiver várias instâncias em execução em um servidor, todas as instâncias deverão estar ativas e em execução. Caso contrário, o servidor não aparecerá na lista de servidores de destino para os quais você deseja restaurar o banco de dados. Para obter mais informações, consulte [as etapas de solução de problemas](backup-sql-server-azure-troubleshoot.md#faulty-instance-in-a-vm-with-multiple-sql-server-instances).
- Para restaurar um banco de dados criptografado com TDE para outro SQL Server, primeiro você precisa [restaurar o certificado para o servidor de destino](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server).
- Bancos de dados habilitados para [CDC](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server) devem ser restaurados usando a opção [restaurar como arquivos](#restore-as-files) .
- Antes de restaurar o banco de dados "mestre", inicie a instância de SQL Server no modo de usuário único usando a opção de inicialização **-m AzureWorkloadBackup**.
  - O valor de **-m** é o nome do cliente.
  - Somente o nome do cliente especificado pode abrir a conexão.
- Para todos os bancos de dados do sistema (modelo, mestre, msdb), interrompa o serviço de SQL Server Agent antes de disparar a restauração.
- Feche todos os aplicativos que possam tentar fazer uma conexão com qualquer um desses bancos de dados.

## <a name="restore-a-database"></a>Restaurar um banco de dados

Para restaurar, você precisa das seguintes permissões:

- Permissões de **operador de backup** no cofre em que você está fazendo a restauração.
- Acesso de **Colaborador (gravação)** à VM de origem da qual será feito backup.
- Acesso de **Colaborador (gravação)** à VM de destino:
  - Se você estiver restaurando para a mesma VM, esta é a VM de origem.
  - Se você estiver restaurando para um local alternativo, essa será a nova VM de destino.

Restaure da seguinte maneira:

1. Abra o cofre no qual a VM do SQL Server está registrada.
2. No painel do cofre, em **Uso**, selecione **Itens de Backup**.
3. Em **Itens de Backup**, em **Tipo de Gerenciamento de Backup**, selecione **SQL na VM do Azure**.

    ![Selecionar SQL na VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selecione o banco de dados a ser restaurado.

    ![Selecionar o banco de dados a ser restaurado](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Examine o menu do banco de dados. Ele fornece informações sobre o backup do banco de dados, incluindo:

    - Os pontos de restauração mais antigo e mais recentes.
    - O status de backup de log das últimas 24 horas para bancos de dados que estão no modo de recuperação completa e bulk-logged e configurados para backups de log transacionais.

6. Selecione **Restaurar**.

    ![Selecione Restaurar](./media/backup-azure-sql-database/restore-db.png)

7. Em **Restaurar configuração**, especifique onde (ou como) restaurar os dados:
   - **Local alternativo**: restaure o banco de dados para um local alternativo e mantenha o banco de dados de origem original.
   - **Substituir Banco de Dados**: restaure os dados para a mesma instância do SQL Server que a fonte original. Essa opção substitui o banco de dados original.

        > [!IMPORTANT]
        > Se o banco de dados selecionado pertencer a um grupo de disponibilidade Always On, o SQL Server não permitirá que o banco de dados seja substituído. Somente **Localização Alternativa** está disponível.
        >
   - **Restaurar como arquivos**: em vez de restaurar como um banco de dados, restaure os arquivos de backup que podem ser recuperados como um banco de dados posteriormente em qualquer computador em que os arquivos estejam presentes usando SQL Server Management Studio.
     ![Menu Restaurar Configuração](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Restaurar para um local alternativo

1. No menu **configuração de restauração** , em **onde restaurar**, selecione **local alternativo**.
1. Selecione o nome do SQL Server e a instância na qual você deseja restaurar o banco de dados.
1. Na caixa **Nome do Banco de Dados Restaurado**, digite o nome do banco de dados de destino.
1. Se aplicável, selecione **Substituir se o BD com o mesmo nome já existir na instância do SQL selecionada**.
1. Selecione **ponto de restauração** e selecione se deseja [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time) ou para [restaurar para um ponto de recuperação específico](#restore-to-a-specific-restore-point).

    ![Selecionar ponto de restauração](./media/backup-azure-sql-database/select-restore-point.png)

    ![Restaurar para o ponto no tempo](./media/backup-azure-sql-database/restore-to-point-in-time.png)

1. No menu **Configuração Avançada**:

    - Se você quiser manter o banco de dados não operacional após a restauração, habilite **RESTORE WITH NORECOVERY**.
    - Se você quiser alterar o local de restauração no servidor de destino, insira novos caminhos de destino.

        ![Inserir caminhos de destino](./media/backup-azure-sql-database/target-paths.png)

1. Selecione **OK** para disparar a restauração. Acompanhe o progresso da restauração na área **notificações** ou acompanhe-a na exibição **trabalhos de backup** no cofre.

    > [!NOTE]
    > A restauração pontual está disponível apenas para backups de log de bancos de dados que estão no modo de recuperação completa e bulk-logged.

### <a name="restore-and-overwrite"></a>Restaurar e substituir

1. No menu **configuração de restauração** , em **onde restaurar**, selecione **substituir DB**  >  **OK**.

    ![Selecione Substituir Banco de Dados](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Em **selecionar ponto de restauração**, selecione **logs (ponto no tempo)** para [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione a **& total diferencial** para restaurar para um [ponto de recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração pontual está disponível apenas para backups de log de bancos de dados que estão no modo de recuperação completa e bulk-logged.

### <a name="restore-as-files"></a>Restaurar como arquivos

Para restaurar os dados de backup como arquivos. bak, em vez de em um banco de dados, escolha **restaurar como arquivos**. Depois que os arquivos são despejados em um caminho especificado, você pode pegar esses arquivos em qualquer computador em que deseja restaurá-los como um banco de dados. Como você pode mover esses arquivos para qualquer computador, agora é possível restaurar os dados entre assinaturas e regiões.

1. Em **onde e como restaurar**, selecione **restaurar como arquivos**.
1. Selecione o nome de SQL Server para o qual você deseja restaurar os arquivos de backup.
1. No **caminho de destino no servidor** , insira o caminho da pasta no servidor selecionado na etapa 2. Esse é o local onde o serviço irá despejar todos os arquivos de backup necessários. Normalmente, um caminho de compartilhamento de rede (ou caminho de um compartilhamento de arquivo do Azure montado quando especificado como o caminho de destino) permite o acesso mais fácil a esses arquivos por outros computadores na mesma rede ou com o mesmo compartilhamento de arquivo do Azure montado neles.<BR>

    >Para restaurar os arquivos de backup de banco de dados em um compartilhamento de arquivos do Azure montado na VM registrada de destino, verifique se NT AUTHORITY\SYSTEM tem acesso ao compartilhamento de arquivos. Você pode executar as etapas fornecidas abaixo para conceder as permissões de leitura/gravação para o AFS montado na VM:
    >
    >- Executar `PsExec -s cmd` para entrar no Shell NT AUTHORITY\SYSTEM
    >   - Execute `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - Verificar o acesso com `dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Disparar uma restauração como arquivos do cofre de backup para `\\<storageacct>.file.core.windows.net\<filesharename>` como o caminho<BR>
    Você pode baixar o PsExec na página do [Sysinternals](/sysinternals/downloads/psexec) .

1. Selecione **OK**.

    ![Selecione restaurar como arquivos](./media/backup-azure-sql-database/restore-as-files.png)

1. Selecione **ponto de restauração** e selecione se deseja [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time) ou para [restaurar para um ponto de recuperação específico](#restore-to-a-specific-restore-point).

1. Todos os arquivos de backup associados ao ponto de recuperação selecionado são despejados no caminho de destino. Você pode restaurar os arquivos como um banco de dados em qualquer computador em que estejam presentes usando SQL Server Management Studio.

    ![Arquivos de backup restaurados no caminho de destino](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar a um ponto específico no tempo

Se você tiver selecionado **Logs (Pontual)** como o tipo de restauração, faça o seguinte:

1. Em **restaurar data/hora**, abra o calendário. No calendário, as datas que têm pontos de recuperação são exibidas em negrito e a data atual é realçada.
1. Selecione uma data que tenha pontos de recuperação. Não é possível selecionar datas que não tenham pontos de recuperação.

    ![Abrir o calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Depois de você selecionar uma data, o gráfico de linha do tempo exibirá os pontos de recuperação disponíveis em um intervalo contínuo.
1. Especifique uma hora para a recuperação no grafo da linha do tempo ou selecione uma hora. Depois, selecione **OK**.

### <a name="restore-to-a-specific-restore-point"></a>Restaurar a um ponto de restauração específico

Se você tiver selecionado **Completo e Diferencial** como o tipo de restauração, faça o seguinte:

1. Selecione um ponto de recuperação da lista e selecione **OK** para concluir o procedimento de ponto de restauração.

    ![Escolha um ponto de recuperação completo](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > Por padrão, os pontos de recuperação dos últimos 30 dias são exibidos. Você pode exibir pontos de recuperação com mais de 30 dias selecionando **Filtrar** e selecionando um intervalo personalizado.

### <a name="restore-databases-with-large-number-of-files"></a>Restaurar bancos de dados com um grande número de arquivos

Se o tamanho total da cadeia de caracteres de arquivos em um banco de dados for maior que um [limite específico](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), o backup do Azure armazenará a lista de arquivos de banco de dados em um componente Pit diferente, para que você não possa definir o caminho de restauração de destino durante a operação de restauração. Em vez disso, os arquivos serão restaurados no caminho padrão do SQL.

  ![Restaurar banco de dados com arquivo grande](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="cross-region-restore"></a>Restauração Entre Regiões

Como uma das opções de restauração, a CRR (restauração entre regiões) permite que você restaure bancos de dados SQL hospedados em VMs do Azure em uma região secundária, que é uma região emparelhada do Azure.

Para carregar o recurso durante a versão prévia, leia a [seção antes de começar](./backup-create-rs-vault.md#set-cross-region-restore).

Para ver se a CRR está habilitada, siga as instruções em [Configurar a restauração entre regiões](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Exibir itens de backup na região secundária

Se a CRR estiver habilitada, você poderá exibir os itens de backup na região secundária.

1. No portal, vá para **cofre dos serviços de recuperação**  >  **itens de backup**.
1. Selecione **região secundária** para exibir os itens na região secundária.

>[!NOTE]
>Somente os tipos de gerenciamento de backup que dão suporte ao recurso de CRR serão mostrados na lista. Atualmente, é permitido apenas o suporte para a restauração de dados de região secundária em uma região secundária.

![Itens de backup na região secundária](./media/backup-azure-sql-database/backup-items-secondary-region.png)

![Bancos de dados na região secundária](./media/backup-azure-sql-database/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>Restaurar na região secundária

A experiência do usuário de restauração da região secundária será semelhante à experiência do usuário de restauração da região primária. Ao configurar detalhes no painel de configuração de restauração para configurar a restauração, você será solicitado a fornecer somente os parâmetros de região secundária.

![Onde e como restaurar](./media/backup-azure-sql-database/restore-secondary-region.png)

>[!NOTE]
>A rede virtual na região secundária precisa ser atribuída exclusivamente e não pode ser usada para outras VMs nesse grupo de recursos.

![Notificação de disparo de restauração em andamento](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>- Depois que a restauração é disparada e na fase de transferência de dados, o trabalho de restauração não pode ser cancelado.
>- As funções do Azure necessárias para restaurar na região secundária são as mesmas da região primária.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorando trabalhos de restauração de região secundária

1. No portal, acesse **cofre dos serviços de recuperação**  >  **trabalhos de backup**
1. Selecione **região secundária** para exibir os itens na região secundária.

    ![Trabalhos de backup filtrados](./media/backup-azure-sql-database/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>Próximas etapas

[Gerenciar e monitorar](manage-monitor-sql-database-backup.md) SQL Server bancos de dados que são submetidos a backup pelo backup do Azure.
