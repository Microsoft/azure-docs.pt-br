---
title: Gerenciar e monitorar bancos de SQL Server em uma VM do Azure
description: Este artigo descreve como gerenciar e monitorar SQL Server bancos de dados que estão em execução em uma VM do Azure.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: e37e6fc211b34b7e427b66db374a705faafd25f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97858715"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gerenciar e monitorar backup de bancos de dados do SQL Server

Este artigo descreve as tarefas comuns para gerenciar e monitorar SQL Server bancos de dados que estão em execução em uma VM (máquina virtual) do Azure e cujo backup é feito em um cofre dos serviços de recuperação de backup do Azure pelo serviço de [backup do Azure](backup-overview.md) . Você aprenderá como monitorar trabalhos e alertas, parar e retomar a proteção de banco de dados, executar trabalhos de backup e cancelar o registro de uma VM de backups.

Se você ainda não tiver configurado backups para seus bancos de dados SQL Server, consulte [fazer backup de bancos de dados SQL Server em VMs do Azure](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs-in-the-portal"></a>Monitorar trabalhos de backup no portal

O backup do Azure mostra todas as operações agendadas e sob demanda em **trabalhos de backup** no portal, exceto os backups de log agendados, pois eles podem ser muito frequentes. Os trabalhos que você vê neste portal incluem descoberta e registro de banco de dados, configurar backup e operações de backup e restauração.

![O portal de trabalhos de backup](./media/backup-azure-sql-database/sql-backup-jobs-list.png)

Para obter detalhes sobre cenários de monitoramento, acesse [monitoramento na portal do Azure](backup-azure-monitoring-built-in-monitor.md) e [monitoramento usando Azure monitor](backup-azure-monitoring-use-azuremonitor.md).  

## <a name="view-backup-alerts"></a>Exibir alertas de backup

Uma vez que os backups de log ocorrem a cada 15 minutos, monitorar os trabalhos de backup pode ser entediante. O backup do Azure facilita o monitoramento enviando alertas de email. Os alertas de email são:

- Disparados para todas as falhas de backup.
- Consolidados no nível do banco de dados por código de erro.
- Enviado somente para a primeira falha de backup de um banco de dados.

Para monitorar alertas de backup de banco de dados:

1. Entre no [portal do Azure](https://portal.azure.com).

2. No painel do cofre, selecione **Alertas de Backup**.

   ![Selecionar Alertas de Backup](./media/backup-azure-sql-database/sql-backup-alerts-list.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Interromper a proteção para um banco de dados do SQL Server

Você pode interromper o backup de um banco de dados do SQL Server de duas maneiras:

- Interromper todos os trabalhos de backup futuros e excluir todos os pontos de recuperação.
- Pare todos os trabalhos de backup futuros e deixe os pontos de recuperação intactos.

Se você optar por deixar os pontos de recuperação, tenha em mente estes detalhes:

- Todos os pontos de recuperação permanecerão intactos para sempre e toda a remoção será interrompida ao parar a proteção com os dados de retenção.
- Você será cobrado pela instância protegida e pelo armazenamento consumido. Para saber mais, confira [Preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).
- Se você excluir uma fonte de dados sem interromper os backups, os novos backups falharão. Os pontos de recuperação antigos expirarão de acordo com a política, mas o ponto de recuperação mais recente sempre será mantido até que você interrompa os backups e exclua os dados.

Para interromper a proteção para um banco de dados:

1. No painel do cofre, selecione **Itens de Backup**.

2. Em **tipo de gerenciamento de backup**, selecione **SQL na VM do Azure**.

    ![Selecionar SQL na VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selecione o banco de dados para o qual você deseja interromper a proteção.

    ![Selecionar o banco de dados para interromper a proteção](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. No menu de banco de dados, selecione **Parar backup**.

    ![Selecionar Parar Backup](./media/backup-azure-sql-database/stop-db-button.png)

5. No menu **Parar Backup**, selecione se deseja reter ou excluir dados. Se quiser, insira um motivo e um comentário.

    ![Reter ou excluir dados no menu parar backup](./media/backup-azure-sql-database/stop-backup-button.png)

6. Selecionar **Parar Backup**.

> [!NOTE]
>
>Para obter mais informações sobre a opção excluir dados, consulte as perguntas frequentes abaixo:
>
>- [Se eu excluir um banco de dados de uma instância protegida automaticamente, o que acontecerá com os backups?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Se eu parar a operação de backup de um banco de dados autoprotegido, qual será seu comportamento?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Retomar a proteção para um banco de dados SQL

Ao interromper a proteção para o banco de dados SQL, se você selecionar a opção **reter backup data** , poderá posteriormente continuar a proteção. Se você não mantiver os dados de backup, não poderá retomar a proteção.

Para retomar a proteção de um banco de dados SQL:

1. Abra o item de backup e selecione **Retomar o backup**.

    ![Selecionar Retomar backup para retomar a proteção do banco de dados](./media/backup-azure-sql-database/resume-backup-button.png)

2. No menu **Política de backup**, selecione uma política e, em seguida, selecione **Salvar**.

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

Você pode executar diferentes tipos de backups sob demanda:

- Backup completo
- Backup completo somente de cópia
- Backup diferencial
- Backup de log

Embora você precise especificar a duração da retenção para backup completo somente cópia, o período de retenção para backup completo sob demanda será definido automaticamente como 45 dias a partir da hora atual.

Para obter mais informações, consulte [SQL Server tipos de backup](backup-architecture.md#sql-server-backup-types).

## <a name="modify-policy"></a>Modificar a política

Modifique a política para alterar a frequência de backup ou o período de retenção.

> [!NOTE]
> Qualquer alteração no período de retenção será aplicada de forma retrospectiva a todos os pontos de recuperação mais antigos além dos novos.

No painel do cofre, vá para **gerenciar**  >  **políticas de backup** e escolha a política que você deseja editar.

  ![Gerenciar política de backup](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Modificar política de backup](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

A modificação da política afetará todos os itens de backup associados e disparará os trabalhos de **configuração de proteção** correspondentes.

### <a name="inconsistent-policy"></a>Política inconsistente

Às vezes, uma operação de modificação de política pode levar a uma versão de política **inconsistente** para alguns itens de backup. Isso ocorre quando ocorre falha no trabalho de **configuração de proteção** correspondente para o item de backup depois que uma operação de modificação de política é disparada. Ele aparece da seguinte maneira na exibição do item de backup:

  ![Política inconsistente](./media/backup-azure-sql-database/inconsistent-policy.png)

Você pode corrigir a versão da política para todos os itens impactados em um único clique:

  ![Corrigir a política inconsistente](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>Cancelar o registro de uma instância do SQL Server

Cancele o registro de uma instância de SQL Server depois de desabilitar a proteção, mas antes de excluir o cofre:

1. No painel de cofres, em **Gerenciar**, selecione **Infraestrutura de Backup**.  

   ![Selecionar Infraestrutura de Backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Em **Servidores de Gerenciamento**, selecione **Servidores Protegidos**.

   ![Selecionar Servidores Protegidos](./media/backup-azure-sql-database/protected-servers.png)

3. Em **Servidores Protegidos**, selecione o servidor para cancelar o registro. Para excluir o cofre, você deve cancelar o registro de todos os servidores.

4. Clique com o botão direito do mouse no servidor protegido e selecione **Cancelar registro**.

   ![Selecione Excluir](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Registrar novamente a extensão na VM SQL Server

Às vezes, a extensão de carga de trabalho na VM pode ser afetada por um motivo ou outra. Nesses casos, todas as operações disparadas na VM começarão a falhar. Você pode precisar registrar novamente a extensão na VM. A operação de **novo registro** reinstala a extensão de backup de carga de trabalho na VM para que as operações continuem. Você pode encontrar essa opção em **infraestrutura de backup** no cofre dos serviços de recuperação.

![Servidores protegidos em infraestrutura de backup](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

Use esta opção com cuidado. Quando disparado em uma VM com uma extensão já íntegra, essa operação fará com que a extensão seja reiniciada. Isso pode causar falha em todos os trabalhos em andamento. Verifique se há um ou mais dos [sintomas](backup-sql-server-azure-troubleshoot.md#re-registration-failures) antes de acionar a operação de novo registro.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [solucionar problemas de backups em um banco de dados SQL Server](backup-sql-server-azure-troubleshoot.md).
