---
title: Fazer backup de cargas de trabalho do SQL Server no Azure Stack
description: Usar o Servidor de Backup do Azure para proteger uma carga de trabalho do SQL Server no Azure Stack.
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: adigan
ms.openlocfilehash: 11d03a9c5cc81b915f48bc66f5a0e5ab034662ed
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465149"
---
# <a name="back-up-sql-server-on-stack"></a>Fazer backup do SQL Server no Stack
Use este artigo para configurar o MABS (Servidor de Backup do Microsoft Azure) para proteger bancos de dados do SQL Server no Azure Stack.

O gerenciamento de backup do banco de dados SQL Server no Azure e a recuperação do Azure envolvem três etapas:

1. Criar uma política de backup para proteger os bancos de dados do SQL Server
2. Criar cópias de backup sob demanda.
3. Recuperar o banco de dados dos discos e do Microsoft Azure

## <a name="before-you-start"></a>Antes de iniciar

[Instalar e preparar o Servidor de Backup do Azure](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Criar política de backup para proteger bancos de dados SQL Server no Azure
1. Na interface de usuário do Servidor de Backup do Azure, clique no workspace **Proteção**.

2. Na faixa de opções da ferramenta, clique em **Novo** para criar um novo grupo de proteção.

    ![Criar Grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)

    O Servidor de Backup do Azure inicia o assistente de Grupo de proteção, que orienta você na criação de um **Grupo de proteção**. Clique em **Avançar**.

3. Na tela **Selecionar tipo de grupo de proteção**, selecione **Servidores**.

    ![Selecionar o tipo de Grupo de Proteção - ‘Servidores’](./media/backup-azure-backup-sql/pg-servers.png)

4. Na tela **Selecionar membros do grupo**, a lista de membros disponíveis exibe as várias fontes de dados. Clique em **+** para expandir uma pasta e revelar as subpastas. Clique na caixa de seleção para selecionar um item.

    ![Selecione o banco de dados SQL](./media/backup-azure-backup-sql/pg-databases.png)

    Todos os itens selecionados são exibidos na lista de membros Selecionados. Após selecionar os servidores ou bancos de dados que deseja proteger, clique em **Avançar**.

5. Na tela **Selecionar método de proteção de dados**, forneça um nome para o grupo de proteção e marque a caixa de seleção **Desejo proteção online**.

    ![Método de proteção de dados – disco de curto prazo e Azure Online](./media/backup-azure-backup-sql/pg-name.png)

6. Na tela **Especificar objetivos de curto prazo**, inclua as entradas necessárias para criar pontos de backup no disco e clique em **Avançar**.

    No exemplo, o **Intervalo de retenção** é de **5 dias**, a **Frequência de sincronização** é de uma vez a cada **15 minutos**, que é a frequência de backup. **Backup Completo Expresso** é definido como **20h**.

    ![Objetivos de curto prazo](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > No exemplo mostrado, às 20h, todos os dias, um ponto de backup é criado transferindo os dados modificados do ponto de backup às 20h do dia anterior. Esse processo é chamado de **Backup Completo Expresso**. Os logs de transação são sincronizados a cada 15 minutos. Se precisar recuperar o banco de dados às 21h, o ponto será criado com base nos logs do último ponto de backup completo expresso (20h, nesse caso).
   >
   >

7. Na tela **Examinar alocação de disco**, verifique o espaço geral de armazenamento disponível e o possível espaço em disco. Clique em **Avançar**.

8. Em **Escolher método de criação de réplica**, escolha como criar seu primeiro ponto de recuperação. É possível transferir o backup inicial manualmente (fora da rede) para evitar o congestionamento de largura de banda ou pela rede. Se optar por esperar para transferir o primeiro backup, será possível especificar o tempo da transferência inicial. Clique em **Avançar**.

    ![Método de replicação inicial](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia de backup inicial exige a transferência de toda a fonte de dados (banco de dados do SQL Server) do servidor de produção (computador do SQL Server) para o Servidor de Backup do Azure. Esses dados podem ser grandes e transferir os dados pela rede pode exceder a largura de banda. Por esse motivo, você pode optar por transferir o backup inicial: **Manualmente** (usando mídia removível) para evitar congestionamento de largura de banda ou **Automaticamente pela rede** (em um horário especificado).

    Quando o backup inicial for concluído, os backups restantes serão backups incrementais na cópia de backup inicial. Os backups incrementais tendem a ser pequenos e são facilmente transferidos pela rede.

9. Escolha quando deseja que a verificação de consistência seja executada e clique em **Avançar**.

    ![Verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    O Servidor de Backup do Azure realiza uma verificação de consistência sobre a integridade do ponto de backup. O Servidor de Backup do Azure calcula a soma de verificação do arquivo de backup no servidor de produção (computador do SQL Server neste cenário) e os dados incluídos no backup para esse arquivo. Havendo um conflito, presume-se que o arquivo incluído no backup no Servidor de Backup do Azure está corrompido. O Servidor de Backup do Azure corrige os dados do backup enviando os blocos correspondentes à incompatibilidade da soma de verificação. Como as verificações de consistência requerem desempenho intenso, é possível agendar a verificação de consistência ou executá-la automaticamente.

10. Para especificar a proteção online das fontes de dados, selecione os bancos de dados a serem protegidos no Azure e clique em **Avançar**.

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
13. Clique em **Avançar** e selecione a opção apropriada para transferir a cópia do backup inicial para o Azure. Você pode escolher **automaticamente pela rede**

14. Depois de examinar os detalhes da política na tela **Resumo**, clique em **Criar grupo** para concluir o fluxo de trabalho. É possível clicar em **Fechar** e monitorar o andamento do trabalho no workspace Monitoramento.

    ![Criação de grupo de proteção em andamento](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Backup sob demanda de um banco de dados SQL Server
Embora as etapas anteriores tenham criado uma política de backup, um "ponto de recuperação" é criado somente quando ocorre o primeiro backup. Em vez de esperar o Agendador ser ativado, as etapas a seguir irão disparar a criação de um ponto de recuperação manualmente.

1. Aguarde até que o status do grupo de proteção mostre **OK** para o banco de dados antes de criar o ponto de recuperação.

    ![Membros do grupo de proteção](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Clique com o botão direito do mouse no banco de dados e selecione **Criar Ponto de Recuperação**.

    ![Criar ponto de recuperação online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Escolha **Proteção online** no menu suspenso e clique em **OK** para iniciar a criação de um ponto de recuperação no Azure.

    ![Criar Ponto de Recuperação](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Exiba o andamento do trabalho no workspace **Monitoramento**.

    ![Console de monitoramento](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar um banco de dados SQL Server no Azure
As seguintes etapas são necessárias para recuperar uma entidade protegida (banco de dados SQL Server) do Azure.

1. Abra o Console de gerenciamento do Servidor de Backup do Azure. Navegue até o workspace **Recuperação**, em que é possível ver os servidores protegidos. Procure o banco de dados necessário (nesse caso, ReportServer$MSDPM2012). Selecione um horário **Recuperação de** especificado como um ponto **Online**.

    ![Selecione um ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Clique com o botão direito do mouse no nome do banco de dados e clique em **Recuperar**.

    ![Recuperar do Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. O MABS mostra os detalhes do ponto de recuperação. Clique em **Avançar**. Para substituir o banco de dados, selecione o tipo de recuperação **Recuperar na instância original do SQL Server**. Clique em **Avançar**.

    ![Recuperar no local original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o MABS recupera o banco de dados para outra instância do SQL Server ou para uma pasta de rede autônoma.

4. Na tela **Especificar opções de recuperação** , você pode selecionar as opções de recuperação, como a limitação do uso da largura de banda de rede para restringir a largura de banda usada pela recuperação. Clique em **Avançar**.

5. Na tela **Resumo** , você vê todas as configurações de recuperação fornecidas até agora. Clique em **Recuperar**.

    O status de Recuperação mostra que o banco de dados está sendo recuperado. Você pode clicar em **Fechar** para fechar o assistente e exibir o andamento no workspace **Monitoramento**.

    ![Iniciar o processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Após a conclusão da recuperação, o banco de dados restaurado será consistente com o aplicativo.

## <a name="next-steps"></a>Próximas etapas

Consulte o artigo [Backup files and application](backup-mabs-files-applications-azure-stack.md) (Arquivos e aplicativo de backup).
Consulte o artigo [SharePoint no Azure de backup](backup-mabs-sharepoint-azure-stack.md).
