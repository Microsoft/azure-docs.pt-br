---
title: Backup do Azure para cargas de trabalho do SQL Server usando o Servidor de Backup do Azure
description: Uma introdução ao backup de bancos de dados do SQL Server usando o Servidor de Backup do Azure
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: kasinh
ms.openlocfilehash: 72b2368979f0c9e546e1c7ef7fc462bf1d64c947
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782048"
---
# <a name="back-up-sql-server-to-azure-with-azure-backup-server"></a>Fazer backup do SQL Server no Azure com o Servidor de Backup do Azure
Este artigo guia você pelas etapas de configuração de backup de bancos de dados do SQL Server usando o MABS (Servidor de Backup do Microsoft Azure).

O gerenciamento de backup do banco de dados SQL Server no Azure e a recuperação do Azure envolvem três etapas:

1. Crie uma política de backup para proteger bancos de dados do SQL Server no Azure.
2. Criar cópias de backup sob demanda no Azure.
3. Recuperar o banco de dados do Azure.

## <a name="before-you-start"></a>Antes de começar
Antes de começar, verifique se você [instalou e preparou o Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Criar política de backup para proteger bancos de dados SQL Server no Azure
1. Na interface de usuário do Servidor de Backup do Azure, clique no workspace **Proteção**.
2. Na faixa de opções da ferramenta, clique em **Novo** para criar um novo grupo de proteção.

    ![Criar grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)
3. O MABS mostra a tela inicial com a orientação sobre como criar um **Grupo de Proteção**. Clique em **Avançar**.
4. Selecione **Servidores**.

    ![Selecionar o tipo de Grupo de Proteção - ‘Servidores’](./media/backup-azure-backup-sql/pg-servers.png)
5. Expanda o computador do SQL Server em que os bancos de dados a serem incluídos no backup estão presentes. O MABS mostra várias fontes de dados cujo backup pode vir desse servidor. Expanda **Todos os Compartilhamentos de SQL** e selecione os bancos de dados (neste caso, selecionamos ReportServer$MSDPM2012 e ReportServer$MSDPM2012TempDB) para fazer backup. Clique em **Avançar**.

    ![Selecione o banco de dados SQL](./media/backup-azure-backup-sql/pg-databases.png)
6. Forneça um nome para o grupo de proteção e marque a caixa de seleção **Desejo proteção online** .

    ![Método de proteção de dados: disco de curto prazo e Azure Online](./media/backup-azure-backup-sql/pg-name.png)
7. Na tela **Especificar Objetivos de Curto Prazo** , inclua as entradas necessárias para criar pontos de backup em disco.

    Aqui vemos que o **intervalo de retenção** está definido como *5 dias* e a **Frequência de sincronização** está definida como uma vez cada *15 minutos*, que é a frequência na qual o backup é feito. **Backup Completo Expresso** é definido como *20h*.

    ![Objetivos de curto prazo](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Às 20h (de acordo com a entrada da tela), um ponto de backup é criado diariamente transferindo os dados que foram modificados do ponto de backup das 20h do dia anterior. Esse processo é chamado de **Backup Completo Expresso**. Enquanto os logs de transição são sincronizados a cada 15 minutos, se houver a necessidade de recuperar o banco de dados às 9h, o ponto será criado reproduzindo novamente os logs do último ponto de backup completo expresso (20h, neste caso).
   >
   >

8. Clique em **Avançar**

    O MABS mostra o espaço de armazenamento geral disponível e a possível utilização do espaço de disco.

    ![Alocação de disco](./media/backup-azure-backup-sql/pg-storage.png)

    Por padrão, o MABS cria um volume por fonte de dados (banco de dados do SQL Server), que é usado para a cópia de backup inicial. Usando essa abordagem, o LDM (Gerenciador de Discos Lógicos) limita a proteção do MABS a 300 fontes de dados (bancos de dados do SQL Server). Para contornar essa limitação, selecione a opção **Colocalizar dados no Pool de Armazenamento do DPM**. Se você usar essa opção, o MABS usará um único volume de várias fontes de dados, o que permite que o MABS proteja até 2000 bancos de dados SQL.

    Se a opção **Aumentar os volumes automaticamente** estiver selecionada, o MABS poderá considerar o aumento do volume de backup conforme os dados de produção aumentarem. Se a opção **Aumentar os volumes automaticamente** não estiver selecionada, o MABS limitará o armazenamento de backup usado para as fontes de dados no grupo de proteção.
9. Os administradores recebem a opção de transferir este backup inicial manualmente (fora da rede) para evitar o congestionamento de largura de banda ou pela rede. Eles também podem configurar a hora em que a transferência inicial pode acontecer. Clique em **Avançar**.

    ![Método de replicação inicial](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia de backup inicial exige a transferência de toda a fonte de dados (banco de dados do SQL Server) do servidor de produção (computador do SQL Server) para o MABS. Esses dados podem ser grandes e transferir os dados pela rede pode exceder a largura de banda. Por esse motivo, os administradores podem optar por transferir o backup inicial: **Manualmente** (usando mídia removível) para evitar congestionamento de largura de banda ou **Automaticamente pela rede** (em um horário especificado).

    Quando o backup inicial for concluído, os backups restantes serão backups incrementais na cópia de backup inicial. Os backups incrementais tendem a ser pequenos e são facilmente transferidos pela rede.
10. Escolha quando deseja que a verificação de consistência seja executada e clique em **Avançar**.

    ![Verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    O MABS pode realizar uma verificação de consistência para confirmar a integridade do ponto de backup. Ele calcula a soma de verificação do arquivo de backup no servidor de produção (o computador do SQL Server neste cenário) e os dados incluídos no backup para esse arquivo no MABS. Em caso de conflito, supõe-se que o arquivo de backup no MABS esteja corrompido. O MABS corrige os dados do backup enviando os blocos correspondentes à incompatibilidade da soma de verificação. Como a verificação de consistência é uma operação com desempenho intenso, os administradores têm a opção de agendá-la ou de executá-la automaticamente.
11. Para especificar a proteção online das fontes de dados, selecione os bancos de dados a serem protegidos no Azure e clique em **Avançar**.

    ![Selecionar fontes de dados](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Os administradores podem escolher agendamentos de backup e políticas de retenção que atendam às políticas da organização.

    ![Agendamento e retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, os backups são feitos uma vez por dia às 12h e às 20h (parte inferior da tela)

    > [!NOTE]
    > É uma prática recomendada ter alguns pontos de recuperação de curto prazo em disco para recuperação rápida. Esses pontos de recuperação são usados para "recuperação operacional". O Azure serve como um bom local fora do site com SLAs e garantia de disponibilidade superiores.
    >
    >

    **Melhor prática**: verifique se os Backups do Azure estão agendados após a conclusão de backups em disco local usando o DPM. Isso permite que o último backup de disco seja copiado para o Azure.

13. Escolha o agendamento de política de retenção. Os detalhes sobre como funciona a política de retenção são fornecidos no artigo [Usar o Backup do Azure para substituir a infraestrutura de fita](backup-azure-backup-cloud-as-tape.md).

    ![Política de retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * Os backups são feitos uma vez por dia às 12h e às 20h (parte inferior da tela) e são mantidos por 180 dias.
    * O backup do sábado às 12h é retido por 104 semanas
    * O backup do último sábado às 12h é retido por 60 meses
    * O backup do último sábado de março às 12h é retido por 10 anos
14. Clique em **Avançar** e selecione a opção apropriada para transferir a cópia do backup inicial para o Azure. Você pode escolher **automaticamente pela rede** ou **Backup Offline**.

    * **Automaticamente pela rede** transfere os dados de backup para o Azure de acordo com o agendamento escolhido para backup.
    * Como o **Backup Offline** funciona é explicado no [Fluxo de trabalho de backup offline no Backup do Azure](backup-azure-backup-import-export.md).

    Escolha o mecanismo de transferência relevante para enviar a cópia de backup inicial para o Azure e clique em **Avançar**.
15. Depois de examinar os detalhes da política na tela **Resumo**, clique no botão **Criar grupo** para concluir o fluxo de trabalho. Você pode clicar no botão **Fechar** e monitorar o andamento do trabalho no workspace Monitoramento.

    ![Criação de grupo de proteção em andamento](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Backup sob demanda de um banco de dados SQL Server
Embora as etapas anteriores tenham criado uma política de backup, um "ponto de recuperação" é criado somente quando ocorre o primeiro backup. Em vez de esperar o Agendador ser ativado, as etapas a seguir irão disparar a criação de um ponto de recuperação manualmente.

1. Aguarde até que o status do grupo de proteção mostre **OK** para o banco de dados antes de criar o ponto de recuperação.

    ![Membros do grupo de proteção](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Clique com o botão direito do mouse no banco de dados e selecione **Criar Ponto de Recuperação**.

    ![Criar ponto de recuperação online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Escolha **Proteção Online** no menu suspenso e clique em **OK**. Isso inicia a criação de um ponto de recuperação no Azure.

    ![Criar Ponto de Recuperação](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Você pode exibir o andamento do trabalho no workspace **Monitoramento**, em que encontrará um trabalho em andamento como o mostrado na figura a seguir.

    ![Console de monitoramento](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar um banco de dados SQL Server no Azure
As seguintes etapas são necessárias para recuperar uma entidade protegida (banco de dados SQL Server) do Azure.

1. Abra o Console de gerenciamento do servidor DPM. Navegue até o workspace **Recuperação**, onde é possível ver os servidores incluídos no backup pelo DPM. Procure o banco de dados necessário (nesse caso, ReportServer$MSDPM2012). Selecione uma hora para **Recuperar de** que termine com **Online**.

    ![Selecione um ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Clique com o botão direito do mouse no nome do banco de dados e clique em **Recuperar**.

    ![Recuperar do Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. O DPM mostra os detalhes do ponto de recuperação. Clique em **Avançar**. Para substituir o banco de dados, selecione o tipo de recuperação **Recuperar na instância original do SQL Server**. Clique em **Avançar**.

    ![Recuperar no local original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o DPM permite a recuperação do banco de dados para outra instância do SQL Server ou em uma pasta de rede autônoma.
4. Na tela **Especificar opções de recuperação** , você pode selecionar as opções de recuperação, como a limitação do uso da largura de banda de rede para restringir a largura de banda usada pela recuperação. Clique em **Avançar**.
5. Na tela **Resumo** , você vê todas as configurações de recuperação fornecidas até agora. Clique em **Recuperar**.

    O status de Recuperação mostra que o banco de dados está sendo recuperado. Você pode clicar em **Fechar** para fechar o assistente e exibir o andamento no workspace **Monitoramento**.

    ![Iniciar o processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Após a conclusão da recuperação, o banco de dados restaurado será consistente com o aplicativo.

### <a name="next-steps"></a>Próximas etapas:
•    [Perguntas frequentes sobre o Backup do Azure](backup-azure-backup-faq.md)
