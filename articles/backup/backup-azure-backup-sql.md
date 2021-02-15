---
title: Fazer backup do SQL Server no Azure como uma carga de trabalho do DPM
description: Uma introdução ao backup de bancos de dados SQL Server usando o serviço de backup do Azure
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 592a51051a0d02a6c1d491db0fe559e2e62babb2
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327042"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Fazer backup do SQL Server no Azure como uma carga de trabalho do DPM

Este artigo orienta você pelas etapas de configuração para fazer backup de bancos de dados SQL Server usando o backup do Azure.

Para fazer backup de bancos de dados do SQL Server no Azure é necessária uma conta do Azure. Se você não tiver uma, poderá criar uma conta gratuita em apenas alguns minutos. Para obter mais informações, consulte [criar sua conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

Para fazer backup de um banco de dados SQL Server no Azure e recuperá-lo do Azure:

1. Crie uma política de backup para proteger SQL Server bancos de dados no Azure.
1. Crie cópias de backup sob demanda no Azure.
1. Recuperar o banco de dados do Azure.

>[!NOTE]
>O DPM 2019 UR2 dá suporte a SQL Server FCI (instâncias de cluster de failover) usando CSV (volumes compartilhados do cluster).<br><br>
>A proteção da [instância de cluster de failover SQL Server com espaços de armazenamento diretos no Azure](../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)  e [SQL Server instância de cluster de failover com o Azure Shared disks](../azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md) tem suporte com esse recurso. O servidor DPM deve ser implantado na máquina virtual do Azure para proteger a instância do SQL FCI implantada em VMs do Azure. 

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

* Se você tiver um banco de dados com arquivos em um compartilhamento de arquivo remoto, a proteção falhará com a ID do Erro 104. O DPM não dá suporte à proteção para SQL Server dados em um compartilhamento de arquivos remoto.
* O DPM não pode proteger bancos de dados armazenados em compartilhamentos SMB remotos.
* Verifique se as [réplicas do grupo de disponibilidade estão configuradas como somente leitura](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server).
* Você deve adicionar explicitamente a conta do sistema **NTAuthority\System** ao grupo Sysadmin em SQL Server.
* Ao fazer a recuperação de um banco de dados parcialmente independente em um local alternativo, verifique se a instância SQL de destino tem o recurso [Banco de Dados Independentes](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable) habilitado.
* Ao fazer a recuperação de um banco de dados de fluxo de arquivos em um local alternativo, verifique se a instância SQL de destino tem o recurso [Banco de dados de fluxo de arquivos](/sql/relational-databases/blob/enable-and-configure-filestream) habilitado.
* Proteção para o SQL Server AlwaysOn:
  * O DPM detecta os Grupos de Disponibilidade ao executar a consulta na criação do grupo de proteção.
  * O DPM detecta um failover e continua a proteção do banco de dados.
  * O DPM oferece suporte a configurações de cluster multissite para uma instância do SQL Server.
* Ao proteger bancos de dados que usam o recurso AlwaysOn, o DPM apresenta as seguintes limitações:
  * O DPM honrará a política de backup para grupos de disponibilidade definidos no SQL Server com base nas preferências de backup, da seguinte maneira:
    * Preferir secundária — Os backups devem ocorrer em uma réplica secundária, exceto quando a réplica primária for a única réplica online. Se houver várias réplicas secundárias disponíveis, o nó com a prioridade de backup mais alta será selecionado para backup. SE apenas a réplica primária estiver disponível, o backup deverá ocorrer na réplica primária.
    * Somente secundária — O backup não deve ser executado na réplica primária. Se a réplica primária for a única online, o backup não deverá ocorrer.
    * Primária — Os backups devem ocorrer sempre na réplica primária.
    * Qualquer réplica — Os backups podem ocorrer em qualquer uma das réplicas de disponibilidade no grupo de disponibilidade. O nó cujo backup será feito será baseado nas prioridades de backup de cada um dos nós.
  * Observe o seguinte:
    * Os backups podem ocorrer de qualquer réplica legível, ou seja, primário, secundário síncrono, secundário assíncrono.
    * Se qualquer réplica for excluída do backup, por exemplo, **excluir réplica** está habilitado ou marcado como não legível, essa réplica não será selecionada para backup em nenhuma das opções.
    * Se várias réplicas estiverem disponíveis e legíveis, o nó com a prioridade de backup mais alta será selecionado para backup.
    * Se o backup falhar no nó selecionado, a operação de backup falhará.
    * Não há suporte para a recuperação para o local original.
* Problemas de backup do SQL Server 2014 ou superior:
  * O SQL Server 2014 adicionou um novo recurso para a criação de um [banco de dados para o SQL Server local no Armazenamento de Blobs do Microsoft Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). O DPM não pode ser usado para proteger essa configuração.
  * Há alguns problemas conhecidos com a preferência de backup "preferir secundário" para a opção AlwaysOn do SQL. O DPM sempre faz um backup do secundário. Se nenhum secundário puder ser encontrado, o backup falhará.

## <a name="before-you-start"></a>Antes de começar

Antes de começar, verifique se você atende aos [pré-requisitos](backup-azure-dpm-introduction.md#prerequisites-and-limitations) para usar o backup do Azure para proteger as cargas de trabalho. Aqui estão algumas das tarefas de pré-requisito:

* Crie um cofre de backup.
* Baixe as credenciais do cofre.
* Instale o agente de backup do Azure.
* Registre o servidor no cofre.

## <a name="create-a-backup-policy"></a>Criar uma política de backup

Para proteger SQL Server bancos de dados no Azure, primeiro crie uma política de backup:

1. No servidor Data Protection Manager (DPM), selecione o espaço de trabalho **proteção** .
1. Selecione **novo** para criar um grupo de proteção.

    ![Criar um grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)
1. Na página inicial, examine as diretrizes sobre como criar um grupo de proteção. Em seguida, selecione **Avançar**.
1. Selecione **servidores**.

    ![Selecione o tipo de grupo de proteção servidores](./media/backup-azure-backup-sql/pg-servers.png)
1. Expanda a máquina virtual SQL Server em que os bancos de dados que você deseja fazer backup estão localizados. Você vê as fontes de dados cujo backup pode ser feito a partir desse servidor. Expanda **todos os compartilhamentos SQL** e selecione os bancos de dados que você deseja fazer backup. Neste exemplo, selecionamos ReportServer $ MSDPM2012 e ReportServer $ MSDPM2012TempDB. Em seguida, selecione **Avançar**.

    ![Selecionar um banco de dados SQL Server](./media/backup-azure-backup-sql/pg-databases.png)
1. Nomeie o grupo de proteção e selecione **desejo proteção online**.

    ![Escolha um método de proteção de dados – proteção de disco de curto prazo ou proteção online do Azure](./media/backup-azure-backup-sql/pg-name.png)
1. Na página **especificar metas de Short-Term** , inclua as entradas necessárias para criar pontos de backup para o disco.

    Neste exemplo, o **período de retenção** é definido como *5 dias*. A **frequência de sincronização** de backup é definida como uma vez a cada *15 minutos*. O **backup completo expresso** é definido como *8:00 PM*.

    ![Configurar objetivos de curto prazo para proteção de backup](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Neste exemplo, um ponto de backup é criado às 8:00 PM todos os dias. Os dados que foram modificados desde o ponto de backup de 8:00 PM do dia anterior são transferidos. Esse processo é chamado de **Backup Completo Expresso**. Embora os logs de transações sejam sincronizados a cada 15 minutos, se for necessário recuperar o banco de dados às 9:00 PM, o ponto será criado repetindo os logs do último ponto de backup completo expresso, que é 8:00 PM neste exemplo.
   >
   >

1. Selecione **Avançar**. O DPM mostra o espaço de armazenamento geral disponível. Ele também mostra a possível utilização do espaço em disco.

    ![Configurar alocação de disco](./media/backup-azure-backup-sql/pg-storage.png)

    Por padrão, o DPM cria um volume por fonte (banco de dados SQL Server). O volume é usado para a cópia de backup inicial. Nessa configuração, o Gerenciador de discos lógicos (LDM) limita a proteção do DPM a 300 fontes de dados (SQL Server bancos de dados). Para contornar essa limitação, selecione **Colocalizar dados no Pool de Armazenamento DPM**. Se você usar essa opção, o DPM usará um único volume para várias fontes de dados. Essa configuração permite que o DPM Proteja até 2.000 bancos de dados SQL Server.

    Se você selecionar **aumentar os volumes automaticamente**, o DPM poderá considerar o aumento do volume de backup à medida que os dados de produção crescem. Se você não selecionar **aumentar os volumes automaticamente**, o DPM limitará o armazenamento de backup para as fontes de dados no grupo de proteção.

1. Se você for um administrador, poderá optar por transferir esse backup inicial **automaticamente pela rede** e escolher a hora da transferência. Ou opte por transferir **manualmente** o backup. Em seguida, selecione **Avançar**.

    ![Escolher um método de criação de réplica](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia de backup inicial requer a transferência de toda a fonte de dados (SQL Server banco de dado). Os dados de backup são movidos do servidor de produção (SQL Server computador) para o servidor DPM. Se esse backup for grande, a transferência dos dados pela rede poderá causar o congestionamento da largura de banda. Por esse motivo, os administradores podem optar por usar a mídia removível para transferir o backup inicial **manualmente**. Ou podem transferir os dados **automaticamente pela rede** em um horário especificado.

    Após a conclusão do backup inicial, os backups continuam incrementalmente na cópia de backup inicial. Os backups incrementais tendem a ser pequenos e são facilmente transferidos pela rede.

1. Escolha quando executar uma verificação de consistência. Em seguida, selecione **Avançar**.

    ![Escolher quando executar uma verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    O DPM pode executar uma verificação de consistência na integridade do ponto de backup. Ele calcula a soma de verificação do arquivo de backup no servidor de produção (o SQL Server computador neste exemplo) e os dados de backup para esse arquivo no DPM. Se a verificação encontrar um conflito, o arquivo de backup no DPM será considerado corrompido. O DPM corrige os dados dos quais foi feito backup enviando os blocos que correspondem à incompatibilidade da soma de verificação. Como a verificação de consistência é uma operação com uso intensivo de desempenho, os administradores podem optar por agendar a verificação de consistência ou executá-la automaticamente.

1. Selecione as fontes de dados a serem protegidas no Azure. Em seguida, selecione **Avançar**.

    ![Selecionar fontes de dados para proteger no Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Se você for um administrador, poderá escolher os agendamentos de backup e as políticas de retenção que atendam às políticas da sua organização.

    ![Escolher agendas e políticas de retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, os backups são feitos diariamente às 12:00 PM e 8:00.

    > [!TIP]
    > Para recuperação rápida, mantenha alguns pontos de recuperação de curto prazo em seu disco. Esses pontos de recuperação são usados para recuperação operacional. O Azure serve como um bom local externo, fornecendo SLAs maiores e disponibilidade garantida.
    >
    > Use o DPM para agendar backups do Azure após o término dos backups do disco local. Quando você segue essa prática, o backup em disco mais recente é copiado para o Azure.
    >

1. Escolha o agendamento de política de retenção. Para obter mais informações sobre como a política de retenção funciona, consulte [usar o backup do Azure para substituir sua infraestrutura de fita](backup-azure-backup-cloud-as-tape.md).

    ![Escolher uma política de retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * Os backups são feitos diariamente às 12:00 PM e 8:00. Eles são mantidos por 180 dias.
    * O backup em sábado às 12:00 PM é mantido por 104 semanas.
    * O backup do último sábado do mês às 12:00 PM é mantido por 60 meses.
    * O backup do último sábado de março às 12:00 PM é mantido por 10 anos.

    Depois de escolher uma política de retenção, selecione **Avançar**.

1. Escolha como transferir a cópia de backup inicial para o Azure.

    * A opção **automaticamente pela rede** segue o agendamento de backup para transferir os dados para o Azure.
    * Para obter mais informações sobre o **backup offline**, consulte [visão geral do backup offline](offline-backup-overview.md).

    Depois de escolher um mecanismo de transferência, selecione **Avançar**.

1. Na página **Resumo** , examine os detalhes da política. Em seguida, selecione **Criar grupo**. Você pode selecionar **fechar** e observar o andamento do trabalho no espaço de trabalho **monitoramento** .

    ![O progresso da criação do grupo de proteção](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Criar cópias de backup sob demanda de um banco de dados SQL Server

Um ponto de recuperação é criado quando o primeiro backup ocorre. Em vez de aguardar a execução da agenda, você pode disparar manualmente a criação de um ponto de recuperação:

1. No grupo de proteção, verifique se o status do banco de dados é **OK**.

    ![Um grupo de proteção, mostrando o status do banco de dados](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Clique com o botão direito do mouse no banco de dados e selecione **criar ponto de recuperação**.

    ![Escolha para criar um ponto de recuperação online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. No menu suspenso, selecione **proteção online**. Em seguida, selecione **OK** para iniciar a criação de um ponto de recuperação no Azure.

    ![Começar a criar um ponto de recuperação no Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Você pode exibir o andamento do trabalho no espaço de trabalho **monitoramento** .

    ![Exibir o andamento do trabalho no console de monitoramento](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar um banco de dados SQL Server no Azure

Para recuperar uma entidade protegida, como um banco de dados SQL Server, no Azure:

1. Abra o console de gerenciamento do servidor DPM. Vá para o espaço de trabalho de **recuperação** para ver os servidores que o DPM faz backup. Selecione o banco de dados (neste exemplo, ReportServer $ MSDPM2012). Selecione um **tempo de recuperação** que termine com **online**.

    ![Selecionar um ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Clique com o botão direito do mouse no nome do banco de dados e selecione **recuperar**.

    ![Recuperar um banco de dados do Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. O DPM mostra os detalhes do ponto de recuperação. Selecione **Avançar**. Para substituir o banco de dados, selecione o tipo de recuperação **Recuperar na instância original do SQL Server**. Em seguida, selecione **Avançar**.

    ![Recuperar um banco de dados em seu local original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o DPM permite que o banco de dados seja recuperado para outra instância do SQL Server ou para uma pasta de rede autônoma.
1. Na página **especificar opções de recuperação** , você pode selecionar as opções de recuperação. Por exemplo, você pode escolher a **limitação do uso de largura de banda da rede** para limitar a largura de banda usada pela recuperação. Em seguida, selecione **Avançar**.
1. Na página **Resumo** , você verá a configuração de recuperação atual. Selecione **recuperar**.

    O status de recuperação mostra o banco de dados que está sendo recuperado. Você pode selecionar **fechar** para fechar o assistente e exibir o progresso no espaço de trabalho **monitoramento** .

    ![Iniciar o processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Quando a recuperação for concluída, o banco de dados restaurado será consistente com o aplicativo.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [perguntas frequentes sobre o backup do Azure](backup-azure-backup-faq.md).