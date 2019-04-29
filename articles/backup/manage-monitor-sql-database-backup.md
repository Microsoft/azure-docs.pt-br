---
title: Gerenciar e monitorar bancos de dados do SQL Server em uma VM do Azure que é feito backup pelo Backup do Azure | Microsoft Docs
description: Este artigo descreve como gerenciar e monitorar bancos de dados do SQL Server em execução em uma VM do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: ea5495867d5f453db014e000e01d533d049dc628
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60849253"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gerenciar e monitorar backup de bancos de dados do SQL Server

Este artigo descreve as tarefas comuns para gerenciamento e monitoramento de bancos de dados do SQL Server que estiver executando em uma máquina virtual do Azure (VM) e que passam por backup nos serviços de recuperação de Backup do Azure do cofre o [Backup do Azure](backup-overview.md) service. Você aprenderá a monitorar trabalhos e alertas, interromper e retomar a proteção do banco de dados, executar trabalhos de backup e cancelar o registro de uma VM a partir de backups.

Se você ainda não tiver configurado a backups para seus bancos de dados do SQL Server, consulte [fazer backup de bancos de dados do SQL Server em VMs do Azure](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorar trabalhos de backup manuais no portal

O Backup do Azure mostra todos os trabalhos disparados manualmente na **trabalhos de Backup** portal. Os trabalhos que você vê nessa descoberta de banco de dados do portal de inclusão e registrando e backup e restaurar operações.

![O portal de trabalhos de Backup](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> O **trabalhos de Backup** portal não mostra os trabalhos de backup agendados. Use o SQL Server Management Studio para monitorar trabalhos de backup agendados, conforme descrito na próxima seção.
>

Para obter detalhes sobre cenários de monitoramento, acesse [monitoramento no Portal do Azure](backup-azure-monitoring-built-in-monitor.md) e [monitoramento usando o Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).  


## <a name="view-backup-alerts"></a>Exibir alertas de backup

Uma vez que os backups de log ocorrem a cada 15 minutos, monitorar os trabalhos de backup pode ser entediante. O Backup do Azure facilita o monitoramento por meio do envio de alertas por email. Alertas de email são:

- Acionado para todas as falhas de backup.
- Consolidados no nível do banco de dados por código de erro.
- Enviadas somente para a primeira falha de backup do banco de dados.

Para monitorar alertas de backup do banco de dados:

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No painel de cofres, selecione **Alertas e Eventos**.

   ![Selecionar Alertas e Eventos](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. Em **Alertas e Eventos**, selecione **Alertas de Backup**.

   ![Selecionar Alertas de Backup](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Interromper a proteção para um banco de dados do SQL Server

Você pode interromper o backup de um banco de dados do SQL Server de duas maneiras:

* Interromper todos os trabalhos de backup futuros e excluir todos os pontos de recuperação.
* Interrompa todos os trabalhos de backup futuros e deixar os pontos de recuperação intacto.

Se você optar por deixar os pontos de recuperação, lembre-se esses detalhes:

* Todos os pontos de recuperação permanecerão intactos para sempre, remoção de todos os deverá parar em Parar proteção com reter dados.
* Você será cobrado para a instância protegida e o armazenamento consumido. Para obter mais informações, consulte [preços de Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).
* Se você excluir uma fonte de dados sem interromper os backups, novos backups falhará.

Para interromper a proteção para um banco de dados:

1. No painel do cofre, selecione **itens de Backup**.

2. Sob **tipo de gerenciamento de Backup**, selecione **SQL em VM do Azure**.

    ![Selecionar SQL na VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selecione o banco de dados para o qual você deseja interromper a proteção.

    ![Selecionar o banco de dados para interromper a proteção](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. No menu de banco de dados, selecione **Parar backup**.

    ![Selecionar Parar Backup](./media/backup-azure-sql-database/stop-db-button.png)


5. Sobre o **parar Backup** menu, selecione se deseja reter ou excluir dados. Se você desejar, forneça um motivo e o comentário.

    ![Reter ou excluir os dados no menu parar Backup](./media/backup-azure-sql-database/stop-backup-button.png)

6. Selecione **parar backup**.


## <a name="resume-protection-for-a-sql-database"></a>Retomar a proteção para um banco de dados SQL

Quando você interromper a proteção para o banco de dados SQL, se você selecionar o **reter dados de Backup** opção, você pode retomar a proteção mais tarde. Se você não mantiver os dados de backup, não é possível retomar a proteção.

Para retomar a proteção para um banco de dados SQL:

1. Abra o item de backup e selecione **retomar backup**.

    ![Selecionar Retomar backup para retomar a proteção do banco de dados](./media/backup-azure-sql-database/resume-backup-button.png)

2. No menu **Política de backup**, selecione uma política e, em seguida, selecione **Salvar**.

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

Você pode executar diferentes tipos de backups sob demanda:

* Backup completo
* Backup completo somente de cópia
* Backup diferencial
* Backup de log

Enquanto você precisa especificar a duração de retenção de backup completo somente cópia, o período de retenção para outros tipos de backup é automaticamente definido como 30 dias da hora atual. <br/>
Para obter mais informações, consulte [tipos de backup do SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Cancelar o registro de uma instância do SQL Server

Cancelar o registro de uma instância do SQL Server depois de desabilitar a proteção, mas antes de excluir o cofre:

1. No painel do cofre, sob **Manage**, selecione **infraestrutura de Backup**.  

   ![Selecionar Infraestrutura de Backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Em **Servidores de Gerenciamento**, selecione **Servidores Protegidos**.

   ![Selecionar Servidores Protegidos](./media/backup-azure-sql-database/protected-servers.png)

3. Em **Servidores Protegidos**, selecione o servidor para cancelar o registro. Para excluir o cofre, você deve cancelar o registro de todos os servidores.

4. O servidor protegido com o botão direito e selecione **excluir**.

   ![Selecione Excluir](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Registrar novamente a extensão de VM do SQL Server

Às vezes, a extensão de carga de trabalho na VM pode ser afetada por um motivo ou outro. Nesses casos, todas as operações acionadas na VM começam a falhar. Em seguida, você talvez precise registrar novamente a extensão na VM. **Registrar novamente** operação reinstalará a extensão de backup de carga de trabalho na VM para a continuidade das operações.  <br>

É recomendável usar essa opção com cuidado; Quando disparado em uma VM com uma extensão já íntegra, esta operação fará com que a extensão para obter reiniciado. Isso pode resultar em todos os trabalhos em andamento falhe. . Verifique para um ou mais dos [sintomas](backup-sql-server-azure-troubleshoot.md#symptoms) antes de disparar a operação registrar novamente.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [solucionar problemas de backups em um banco de dados do SQL Server](backup-sql-server-azure-troubleshoot.md).
