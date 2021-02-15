---
title: Automação de trabalho com trabalhos do SQL Agent no Azure SQL Instância Gerenciada
description: Opções de automação para executar scripts Transact-SQL (T-SQL) no Azure SQL Instância Gerenciada
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 02/01/2021
ms.openlocfilehash: beb82f8435aea817a074ce83fddc6a5417b86c26
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416948"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>Automatizar tarefas de gerenciamento usando trabalhos do SQL Agent no Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Usando [SQL Server Agent](/sql/ssms/agent/sql-server-agent) em SQL Server e [SQL instância gerenciada](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md), você pode criar e agendar trabalhos que podem ser executados periodicamente em um ou vários bancos de dados para executar consultas Transact-SQL (T-SQL) e executar tarefas de manutenção. Este artigo introduziu o SQL Agent para SQL Instância Gerenciada.

> [!Note]
> O SQL Agent não está disponível no banco de dados SQL do Azure ou no Azure Synapse Analytics. Em vez disso, recomendamos [a automação de trabalhos com trabalhos elásticos](job-automation-overview.md).

### <a name="sql-agent-job-limitations-in-azure-sql-managed-instance"></a>Limitações de trabalho do SQL Agent na instância gerenciada do SQL do Azure

Vale a pena observar as diferenças entre o SQL Agent disponível em SQL Server e como parte do SQL Instância Gerenciada. Para obter mais informações sobre as diferenças de recursos com suporte entre o SQL Server e o SQL Instância Gerenciada, consulte [diferenças de T-SQL do Azure SQL instância gerenciada do SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent). 

Alguns dos recursos do SQL Agent que estão disponíveis no SQL Server não têm suporte no SQL Instância Gerenciada:

- As configurações do agente SQL são somente leitura. 
    - O procedimento armazenado do sistema `sp_set_agent_properties` não tem suporte no SQL instância gerenciada.
- Atualmente, não há suporte para habilitar/desabilitar o SQL Agent no SQL Instância Gerenciada. O SQL Agent sempre está em execução.
- Há suporte parcial para notificações:
  - Não há suporte para pager.
  - Não há suporte a NetSend.
  - Não há suporte para alertas.
- Não há suporte para proxies.
- Não há suporte para Eventlog.
- Não há suporte para o gatilho de agenda de trabalho com base em uma CPU ociosa.

## <a name="when-to-use-sql-agent-jobs"></a>Quando usar trabalhos do SQL Agent 

Há vários cenários em que você pode usar trabalhos do SQL Agent:

- Automatizar tarefas de gerenciamento e, em seguida, agendá-las para serem executadas a todo dia da semana, após horas etc.
  - Implantar alterações de esquema, gerenciamento de credenciais, coleta de dados de desempenho ou coleta de telemetria do locatário (cliente).
  - Atualizar dados de referência (informações comuns a todos os bancos de dados), carregar dados do armazenamento de blobs do Azure.
  - Tarefas comuns de manutenção, incluindo DBCC CHECKDB, para garantir a integridade dos dados ou a manutenção do índice para melhorar o desempenho da consulta. Configure trabalhos para serem executados em um conjunto de bancos de dados de modo recorrente, por exemplo, fora dos horários de pico.
  - Coletar resultados de consulta de um conjunto de bancos de dados em uma tabela central em uma base contínua. Consultas de desempenho podem ser executadas continuamente e configuradas para disparar tarefas adicionais a serem executadas.
- Coletar dados para relatórios
  - Agregue dados de uma coleção de bancos de dados em uma tabela de destino único.
  - Executar consultas de processamento de dados mais longas em um grande conjunto de bancos de dados, por exemplo, a coleta de telemetria do cliente. Resultados são coletados em uma única tabela de destino para análise posterior.
- Movimentações de dados
  - Crie trabalhos que replicam as alterações feitas em seus bancos de dados para outros bancos de dados ou colete atualizações feitas em bancos de dados remotos e aplique o que foi alterado no banco de dados.
  - Crie trabalhos que carregam dados de ou para seus bancos de dados usando o SSIS (SQL Server Integration Services).

## <a name="sql-agent-jobs-in-azure-sql-managed-instance"></a>Trabalhos do SQL Agent na instância gerenciada do SQL do Azure

Os trabalhos do SQL Agent são executados pelo serviço SQL Agent que continua a ser usado para automação de tarefas no SQL Server e no SQL Instância Gerenciada. 

Os trabalhos do SQL Agent são uma série especificada de scripts T-SQL com relação ao seu banco de dados. Use trabalhos para definir uma tarefa administrativa que pode ser executada uma ou mais vezes e monitorada quanto a êxito ou falha. 

Um trabalho pode ser executado em um servidor local ou em vários servidores remotos. Os trabalhos do SQL Agent são um componente de Mecanismo de Banco de Dados interno executado no serviço SQL Instância Gerenciada.

Há vários conceitos importantes em Trabalhos do SQL Agent:

- **Etapas de trabalho** conjunto de uma ou mais etapas que devem ser executadas dentro do trabalho. Para cada etapa de trabalho, é possível definir a estratégia de repetição e a ação que deverá acontecer se a etapa de trabalho tiver êxito ou falhar.
- **Agendas** definem quando o trabalho deve ser executado.
- **Notificações** permitem que você defina regras que serão usadas para notificar operadores por email após a conclusão do trabalho.

### <a name="sql-agent-job-steps"></a>Etapas de trabalho do SQL Agent

As etapas do Trabalho do SQL Agent são sequências de ações que o SQL Agent deve executar. Cada etapa tem a seguinte etapa que deverá ser executada se a etapa tiver êxito ou falhar, número de repetições em caso de falha.

O SQL Agent permite que você crie diferentes tipos de etapas de trabalho, como etapas de trabalho Transact-SQL que executam um único lote Transact-SQL no banco de dados ou as etapas de comando/PowerShell do sistema operacional que podem executar script de sistema operacional personalizado, [etapas de trabalho do SSIS](/azure/data-factory/how-to-invoke-ssis-package-managed-instance-agent) que permitem carregar dados usando o tempo de execução do [](../managed-instance/replication-transactional-overview.md)

> [!Note]
> Para obter mais informações sobre como aproveitar o Integration Runtime do Azure SSIS com o SSISDB hospedado pelo SQL Instância Gerenciada do Azure, consulte [usar o Azure sql instância gerenciada com SQL Server Integration Services (SSIS) no Azure data Factory](/../azure/data-factory/how-to-use-sql-managed-instance-with-ir.md).

A [replicação transacional](../managed-instance/replication-transactional-overview.md) pode replicar as alterações de suas tabelas em outros bancos de dados no Azure SQL instância gerenciada, banco de dados SQL do azure ou SQL Server. Para obter informações, consulte [Configurar a replicação no Azure SQL instância gerenciada](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md). 

Outros tipos de etapas de trabalho não têm suporte no momento no SQL Instância Gerenciada, incluindo:

- Não há suporte para a etapa de trabalho de replicação de mesclagem.
- Não há suporte para leitor de fila.
- O Analysis Services não é suportado
 
### <a name="sql-agent-job-schedules"></a>Agendas de trabalho do SQL Agent

Uma agenda especifica quando um trabalho é executado. Mais de um trabalho pode ser executado na mesma agenda e mais de uma agenda pode ser aplicada ao mesmo trabalho.

Uma agenda pode definir as condições a seguir para a hora em que um trabalho é executado:

- Sempre que SQL Server Agent é iniciado. O trabalho é ativado após cada failover.
- Uma vez, em uma data e hora específicas, que é útil para a execução atrasada de algum trabalho.
- Em uma agenda recorrente.

> [!Note]
> O SQL Instância Gerenciada atualmente não permite que você inicie um trabalho quando a CPU está ociosa.

### <a name="sql-agent-job-notifications"></a>Notificações de trabalho do SQL Agent

Os trabalhos do SQL Agent permitem que você receba notificações quando o trabalho é concluído com êxito ou com falha. É possível receber a notificações por email.

Se ele ainda não estiver habilitado, primeiro você precisará configurar [o recurso Database Mail](/sql/relational-databases/database-mail/database-mail) no SQL do Azure instância gerenciada:

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

Como um exercício de exemplo, configure a conta de email que será usada para enviar as notificações por email. Atribua a conta ao perfil de email chamado `AzureManagedInstance_dbmail_profile` . Para enviar emails usando trabalhos do SQL Agent no SQL Instância Gerenciada, deve haver um perfil que deve ser chamado `AzureManagedInstance_dbmail_profile` . Caso contrário, o SQL Instância Gerenciada não poderá enviar emails por meio do SQL Agent. Consulte o exemplo a seguir:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

Teste a configuração de Database Mail por meio do T-SQL usando o procedimento armazenado do sistema [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) :

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

É possível notificar o operador de que algo aconteceu com seus trabalhos do SQL Agent. Um operador define informações de contato para um indivíduo responsável pela manutenção de uma ou mais instâncias na Instância Gerenciada de SQL. Algumas vezes, as responsabilidades do operador são atribuídas a um indivíduo.

Em sistemas com várias instâncias na Instância Gerenciada de SQL ou no SQL Server, muitos indivíduos podem compartilhar as responsabilidades do operador. Um operador não contém informações de segurança nem define uma entidade de segurança. O ideal é que um operador não seja um indivíduo cujas responsabilidades possam ser alteradas, mas um grupo de distribuição de email.   

Você pode [criar operadores](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql) usando o SQL Server Management Studio (SSMS) ou o script TRANSACT-SQL mostrado no exemplo a seguir:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

Confirme o êxito ou a falha do email por meio do [log de Database Mail](/sql/relational-databases/database-mail/database-mail-log-and-audits) no SSMS.

Em seguida, você pode [modificar qualquer trabalho do SQL Agent](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql) e atribuir operadores que serão notificados por email se o trabalho for concluído, falhar ou conseguir usar o SSMS ou o seguinte script TRANSACT-SQL:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>Histórico de trabalhos do SQL Agent

O Azure SQL Instância Gerenciada atualmente não permite que você altere as propriedades do SQL Agent porque elas são armazenadas nos valores de registro subjacentes. Isso significa que as opções para ajustar a política de retenção de agente para registros de histórico de trabalho são fixas no padrão de registros de total de 1000 e registros de histórico de 100 máximos por trabalho.

### <a name="sql-agent-fixed-database-role-membership"></a>Associação de função de banco de dados fixa do SQL Agent

Se os usuários vinculados a logons não-sysadmin forem adicionados a qualquer uma das três funções de banco de dados fixas do SQL Agent no banco de dados do sistema msdb, existe um problema no qual as permissões de execução explícitas precisam ser concedidas aos procedimentos armazenados mestres para que esses logons funcionem. Se este erro for encontrado, a mensagem de erro "A permissão EXECUTE foi negada no objeto <object_name> (Microsoft SQL Server, Erro: 229)" será exibida. 

Depois de adicionar usuários a uma função de banco de dados fixa do SQL Agent (SQLAgentUserRole, SQLAgentReaderRole ou SQLAgentOperatorRole) no msdb, para cada um dos logons do usuário adicionados a essas funções, execute o script T-SQL abaixo para conceder explicitamente permissões de execução aos procedimentos armazenados do sistema listados. Este exemplo pressupõe que o nome de usuário e o nome de logon sejam os mesmos. 

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>Saiba mais

- [O que é Instância Gerenciada de SQL do Azure?](../managed-instance/sql-managed-instance-paas-overview.md)
- [O que há de novo no banco de dados SQL do Azure & SQL Instância Gerenciada?](../../azure-sql/database/doc-changes-updates-release-notes.md?tabs=managed-instance)
- [Diferenças de T-SQL do Azure SQL Instância Gerenciada do SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [Comparação de recursos: banco de dados SQL do Azure e Azure SQL Instância Gerenciada](../../azure-sql/database/features-comparison.md)