---
title: Fazer backup de cargas de trabalho do SQL Server no Azure Stack
description: Neste artigo, saiba como configurar o servidor de Backup do Microsoft Azure (MABS) para proteger SQL Server bancos de dados no Azure Stack.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 80de7913b010fca69c3703e423109f2ede653590
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91332807"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Fazer backup do SQL Server no Azure Stack

Use este artigo para configurar o MABS (Servidor de Backup do Microsoft Azure) para proteger bancos de dados do SQL Server no Azure Stack.

O gerenciamento de backup do banco de dados SQL Server no Azure e a recuperação do Azure envolvem três etapas:

1. Criar uma política de backup para proteger os bancos de dados do SQL Server
2. Criar cópias de backup sob demanda.
3. Recuperar o banco de dados dos discos e do Microsoft Azure

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

* Se você tiver um banco de dados com arquivos em um compartilhamento de arquivo remoto, a proteção falhará com a ID do Erro 104. O MABS não dá suporte à proteção para SQL Server dados em um compartilhamento de arquivos remoto.
* O MABS não pode proteger bancos de dados armazenados em compartilhamentos SMB remotos.
* Verifique se as [réplicas do grupo de disponibilidade estão configuradas como somente leitura](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server).
* Você deve adicionar explicitamente a conta do sistema **NTAuthority\System** ao grupo Sysadmin em SQL Server.
* Ao fazer a recuperação de um banco de dados parcialmente independente em um local alternativo, verifique se a instância SQL de destino tem o recurso [Banco de Dados Independentes](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable) habilitado.
* Ao fazer a recuperação de um banco de dados de fluxo de arquivos em um local alternativo, verifique se a instância SQL de destino tem o recurso [Banco de dados de fluxo de arquivos](/sql/relational-databases/blob/enable-and-configure-filestream) habilitado.
* Proteção para o SQL Server AlwaysOn:
  * O MABS detecta grupos de disponibilidade ao executar a consulta na criação do grupo de proteção.
  * O MABS detecta um failover e continua a proteção do banco de dados.
  * O MABS dá suporte a configurações de cluster multissite para uma instância do SQL Server.
* Quando você protege bancos de dados que usam o recurso AlwaysOn, o MABS tem as seguintes limitações:
  * O MABS honrará a política de backup para grupos de disponibilidade definidos no SQL Server com base nas preferências de backup, da seguinte maneira:
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
  * O SQL Server 2014 adicionou um novo recurso para a criação de um [banco de dados para o SQL Server local no Armazenamento de Blobs do Microsoft Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). MABS não pode ser usado para proteger essa configuração.
  * Há alguns problemas conhecidos com a preferência de backup "preferir secundário" para a opção AlwaysOn do SQL. O MABS sempre faz um backup do secundário. Se nenhum secundário puder ser encontrado, o backup falhará.

## <a name="before-you-start"></a>Antes de começar

[Instalar e preparar o Servidor de Backup do Azure](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Criar política de backup para proteger bancos de dados SQL Server no Azure

1. Na interface do usuário do Servidor de Backup do Azure, selecione o espaço de trabalho **proteção** .

2. Na faixa de opções da ferramenta, selecione **novo** para criar um novo grupo de proteção.

    ![Criar grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)

    O Servidor de Backup do Azure inicia o assistente de Grupo de proteção, que orienta você na criação de um **Grupo de proteção**. Selecione **Avançar**.

3. Na tela **Selecionar tipo de grupo de proteção**, selecione **Servidores**.

    ![Selecionar o tipo de Grupo de Proteção - ‘Servidores’](./media/backup-azure-backup-sql/pg-servers.png)

4. Na tela **Selecionar membros do grupo**, a lista de membros disponíveis exibe as várias fontes de dados. Selecione **+** para expandir uma pasta e revelar as subpastas. Marque a caixa de seleção para selecionar um item.

    ![Selecione o banco de dados SQL](./media/backup-azure-backup-sql/pg-databases.png)

    Todos os itens selecionados são exibidos na lista de membros Selecionados. Depois de selecionar os servidores ou bancos de dados que você deseja proteger, selecione **Avançar**.

5. Na tela **Selecionar método de proteção de dados**, forneça um nome para o grupo de proteção e marque a caixa de seleção **Desejo proteção online**.

    ![Método de proteção de dados – disco de curto prazo e Azure Online](./media/backup-azure-backup-sql/pg-name.png)

6. Na tela **especificar metas de Short-Term** , inclua as entradas necessárias para criar pontos de backup em disco e selecione **Avançar**.

    No exemplo, o **Intervalo de retenção** é de **5 dias**, a **Frequência de sincronização** é de uma vez a cada **15 minutos**, que é a frequência de backup. **Backup Completo Expresso** é definido como **20h**.

    ![Objetivos de curto prazo](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > No exemplo mostrado, às 20h, todos os dias, um ponto de backup é criado transferindo os dados modificados do ponto de backup às 20h do dia anterior. Esse processo é chamado de **Backup Completo Expresso**. Os logs de transação são sincronizados a cada 15 minutos. Se precisar recuperar o banco de dados às 21h, o ponto será criado com base nos logs do último ponto de backup completo expresso (20h, nesse caso).
   >
   >

7. Na tela **Examinar alocação de disco**, verifique o espaço geral de armazenamento disponível e o possível espaço em disco. Selecione **Avançar**.

8. Em **Escolher método de criação de réplica**, escolha como criar seu primeiro ponto de recuperação. É possível transferir o backup inicial manualmente (fora da rede) para evitar o congestionamento de largura de banda ou pela rede. Se optar por esperar para transferir o primeiro backup, será possível especificar o tempo da transferência inicial. Selecione **Avançar**.

    ![Método de replicação inicial](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia de backup inicial requer a transferência de toda a fonte de dados (banco de SQL Server) do servidor de produção (SQL Server computador) para Servidor de Backup do Azure. Esses dados podem ser grandes e transferir os dados pela rede pode exceder a largura de banda. Por esse motivo, é possível optar por transferir o backup inicial: **Manualmente** (usando mídia removível) para evitar o congestionamento de largura de banda ou **Automaticamente pela rede** (em um horário especificado).

    Quando o backup inicial for concluído, os backups restantes serão backups incrementais na cópia de backup inicial. Os backups incrementais tendem a ser pequenos e são facilmente transferidos pela rede.

9. Escolha quando deseja que a verificação de consistência seja executada e selecione **Avançar**.

    ![Verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    O Servidor de Backup do Azure realiza uma verificação de consistência sobre a integridade do ponto de backup. Servidor de Backup do Azure calcula a soma de verificação do arquivo de backup no servidor de produção (SQL Server computador neste cenário) e os dados de backup para esse arquivo. Se houver um conflito, supõe-se que o arquivo de backup em Servidor de Backup do Azure está corrompido. O Servidor de Backup do Azure corrige os dados do backup enviando os blocos correspondentes à incompatibilidade da soma de verificação. Como as verificações de consistência requerem desempenho intenso, é possível agendar a verificação de consistência ou executá-la automaticamente.

10. Para especificar a proteção online das fontes de dados, selecione os bancos que serão protegidos no Azure e selecione **Avançar**.

    ![Selecionar fontes de dados](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Escolha agendamentos de backup e políticas de retenção que atendam às políticas da organização.

    ![Agendamento e retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, os backups são feitos uma vez por dia às 12h e às 20h (parte inferior da tela)

    > [!NOTE]
    > É uma prática recomendada ter alguns pontos de recuperação de curto prazo em disco para recuperação rápida. Esses pontos de recuperação são usados para recuperação operacional. O Azure serve como um bom local fora do site com SLAs e garantia de disponibilidade superiores.
    >
    >

    **Melhor prática**: se você agendar backups para o Azure a serem iniciados após a conclusão dos backups de disco local, os backups de disco mais recentes sempre serão copiados para o Azure.

12. Escolha o agendamento de política de retenção. Os detalhes sobre como funciona a política de retenção são fornecidos no artigo [Usar o Backup do Azure para substituir a infraestrutura de fita](backup-azure-backup-cloud-as-tape.md).

    ![Política de retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * Os backups são feitos uma vez por dia às 12h e às 20h (parte inferior da tela) e são mantidos por 180 dias.
    * O backup do sábado às 12h é retido por 104 semanas
    * O backup do último sábado às 12h é retido por 60 meses
    * O backup do último sábado de março às 12h é retido por 10 anos
13. Selecione **Avançar** e selecione a opção apropriada para transferir a cópia de backup inicial para o Azure. Você pode escolher **automaticamente pela rede**

14. Depois de examinar os detalhes da política na tela **Resumo** , selecione **Criar grupo** para concluir o fluxo de trabalho. Você pode selecionar **fechar** e monitorar o andamento do trabalho no espaço de trabalho monitoramento.

    ![Criação de grupo de proteção em andamento](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Backup sob demanda de um banco de dados SQL Server

Embora as etapas anteriores tenham criado uma política de backup, um "ponto de recuperação" é criado somente quando ocorre o primeiro backup. Em vez de esperar o Agendador ser ativado, as etapas a seguir irão disparar a criação de um ponto de recuperação manualmente.

1. Aguarde até que o status do grupo de proteção mostre **OK** para o banco de dados antes de criar o ponto de recuperação.

    ![Membros do grupo de proteção](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Clique com o botão direito do mouse no banco de dados e selecione **Criar Ponto de Recuperação**.

    ![Criar ponto de recuperação online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Escolha **proteção online** no menu suspenso e selecione **OK** para iniciar a criação de um ponto de recuperação no Azure.

    ![Criar Ponto de Recuperação](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Exiba o andamento do trabalho no workspace **Monitoramento**.

    ![Console de monitoramento](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar um banco de dados SQL Server no Azure

As seguintes etapas são necessárias para recuperar uma entidade protegida (banco de dados SQL Server) do Azure.

1. Abra o Console de gerenciamento do Servidor de Backup do Azure. Navegue até o workspace **Recuperação**, em que é possível ver os servidores protegidos. Procure o banco de dados necessário (nesse caso, ReportServer$MSDPM2012). Selecione uma **recuperação a partir da** hora especificada como um ponto **online** .

    ![Selecione um ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Clique com o botão direito do mouse no nome do banco de dados e selecione **recuperar**.

    ![Recuperar do Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. O MABS mostra os detalhes do ponto de recuperação. Selecione **Avançar**. Para substituir o banco de dados, selecione o tipo de recuperação **Recuperar na instância original do SQL Server**. Selecione **Avançar**.

    ![Recuperar no local original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o MABS recupera o banco de dados para outra instância do SQL Server ou para uma pasta de rede autônoma.

4. Na tela **Especificar opções de recuperação** , você pode selecionar as opções de recuperação, como a limitação do uso da largura de banda de rede para restringir a largura de banda usada pela recuperação. Selecione **Avançar**.

5. Na tela **Resumo** , você vê todas as configurações de recuperação fornecidas até agora. Selecione **recuperar**.

    O status de Recuperação mostra que o banco de dados está sendo recuperado. Você pode selecionar **fechar** para fechar o assistente e exibir o progresso no espaço de trabalho **monitoramento** .

    ![Iniciar o processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Após a conclusão da recuperação, o banco de dados restaurado será consistente com o aplicativo.

## <a name="next-steps"></a>Próximas etapas

Consulte o artigo [Backup files and application](backup-mabs-files-applications-azure-stack.md) (Arquivos e aplicativo de backup).
Consulte o artigo [SharePoint no Azure de backup](backup-mabs-sharepoint-azure-stack.md).
