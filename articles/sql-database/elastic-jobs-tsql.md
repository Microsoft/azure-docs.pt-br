---
title: Criar e gerenciar trabalhos de Banco de Dados Elástico SQL do Azure usando o T-SQL (Transact-SQL) | Microsoft Docs
description: Execute scripts em vários bancos de dados com o agente do Trabalho de Banco de Dados Elástico usando o T-SQL (Transact-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 683297e32c40f73c64dc40b18f279d92e2396e8d
ms.sourcegitcommit: 3107874d7559ea975e4d55ae33cdf45f4b5485e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67568284"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Usar o T-SQL (Transact-SQL) para criar e gerenciar trabalhos de Banco de Dados Elástico

Este artigo fornece muitos cenários de exemplo para começar a trabalhar com trabalhos elásticos usando o T-SQL.

Os exemplos usam os [procedimentos armazenados](#job-stored-procedures) e as [exibições](#job-views) disponíveis no [*banco de dados de trabalhos*](sql-database-job-automation-overview.md#job-database).

O T-SQL (Transact-SQL) é usado para criar, configurar, executar e gerenciar trabalhos. O agente de trabalho elástico não é compatível com o T-SQL, portanto, você precisa primeiro criar um *agente de trabalho elástico* usando o portal ou o [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Criar uma credencial para a execução do trabalho

A credencial é usada para conectar-se a seus bancos de dados de destino para a execução do script. A credencial precisa de permissões apropriadas, nos bancos de dados especificados pelo grupo de destino, para executar o script com êxito. Ao usar um servidor e/ou um membro do grupo de destino do pool, é altamente recomendado criar uma credencial mestre a ser usada para atualizar a credencial antes de expansão do servidor e/ou do pool no momento da execução do trabalho. A credencial no escopo do banco de dados é criada no banco de dados do agente de trabalho. A mesma credencial precisa ser usada para *Criar um logon* e *Criar um usuário do logon para conceder as permissões do banco de dados de logon* nos bancos de dados de destino.


```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO
```

## <a name="create-a-target-group-servers"></a>Criar um grupo de destino (servidores)

O exemplo a seguir mostra como executar um trabalho em todos os bancos de dados em um servidor.  
Conecte-se ao [*banco de dados de trabalhos*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:


```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```


## <a name="exclude-an-individual-database"></a>Excluir um banco de dados individual

O exemplo a seguir mostra como executar um trabalho em todos os bancos de dados em um servidor do Banco de Dados SQL, com exceção do banco de dados denominado *MappingDB*.  
Conecte-se ao [*banco de dados de trabalhos*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name='server2.database.windows.net'
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```


## <a name="create-a-target-group-pools"></a>Criar um grupo de destino (pools)

O exemplo a seguir mostra como direcionar a todos os bancos de dados em um ou mais pools elásticos.  
Conecte-se ao [*banco de dados de trabalhos*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```


## <a name="deploy-new-schema-to-many-databases"></a>Implantar o novo esquema para vários bancos de dados

O exemplo a seguir mostra como implantar o novo esquema em todos os bancos de dados.  
Conecte-se ao [*banco de dados de trabalhos*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:


```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables 
            WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```


## <a name="data-collection-using-built-in-parameters"></a>Coleta de dados usando parâmetros internos

Em muitos cenários de coleta de dados, pode ser útil incluir algumas dessas variáveis de script para ajudar no pós-processamento dos resultados do trabalho.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Por exemplo, para agrupar todos os resultados da mesma execução de trabalho, use o *$(job_execution_id)* , como é mostrado no comando a seguir:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Como monitorar o desempenho do banco de dados

O exemplo a seguir cria um novo trabalho para coletar dados de desempenho de vários bancos de dados.

Por padrão, o agente de trabalho procurará criar a tabela para armazenar os resultados retornados. Como resultado, o login associado à credencial usada para a credencial de saída precisará ter permissões suficientes para executar isso. Se você quiser criar manualmente a tabela com antecedência, ela precisará ter as seguintes propriedades:
1. Colunas com o nome correto e tipos de dados para o conjunto de resultados.
2. Coluna adicional para internal_execution_id com o tipo de dados de uniqueidentifier.
3. Um índice não clusterizado chamado `IX_<TableName>_Internal_Execution_ID` na coluna internal_execution_id.

Conecte-se ao [*banco de dados de trabalhos*](sql-database-job-automation-overview.md#job-database) e execute os seguintes comandos:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>Exibir definições de trabalho

O exemplo a seguir mostra como exibir as definições de trabalho atuais.  
Conecte-se ao [*banco de dados de trabalhos*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j 
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```


## <a name="begin-ad-hoc-execution-of-a-job"></a>Iniciar a execução ad hoc de um trabalho

O exemplo a seguir mostra como iniciar um trabalho imediatamente.  
Conecte-se ao [*banco de dados de trabalhos*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```


## <a name="schedule-execution-of-a-job"></a>Agendar a execução de um trabalho

O exemplo a seguir mostra como agendar um trabalho para execução futura.  
Conecte-se ao [*banco de dados de trabalhos*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Monitorar o status de execução do trabalho

O exemplo a seguir mostra como exibir detalhes do status de execução de todos os trabalhos.  
Conecte-se ao [*banco de dados de trabalhos*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>Cancelar um trabalho

O exemplo a seguir mostra como cancelar um trabalho.  
Conecte-se ao [*banco de dados de trabalhos*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions 
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```


## <a name="delete-old-job-history"></a>Excluir o histórico de trabalhos antigo

O exemplo a seguir mostra como excluir o histórico de trabalhos antes de uma data específica.  
Conecte-se ao [*banco de dados de trabalhos*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Excluir um trabalho e todo o seu histórico de trabalhos

O exemplo a seguir mostra como excluir um trabalho e todo o histórico de trabalhos relacionado.  
Conecte-se ao [*banco de dados de trabalhos*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Procedimentos armazenados de trabalho

Os seguintes procedimentos armazenados ficam no [banco de dados de trabalhos](sql-database-job-automation-overview.md#job-database).



|Procedimento armazenado  |DESCRIÇÃO  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Adiciona um novo trabalho.    |
|[sp_update_job](#sp_update_job)    |      Atualiza um trabalho existente.   |
|[sp_delete_job](#sp_delete_job)     |      Excluir um trabalho existente.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Adiciona uma etapa a um trabalho.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Atualiza uma etapa de trabalho.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Exclui uma etapa de trabalho.    |
|[sp_start_job](#sp_start_job)    |  Inicia a execução de um trabalho.       |
|[sp_stop_job](#sp_stop_job)     |     Interrompe a execução de um trabalho.   |
|[sp_add_target_group](#sp_add_target_group)    |     Adiciona um grupo de destino.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Exclui um grupo de destino.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Adiciona um banco de dados ou um grupo de bancos de dados a um grupo de destino.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Remove um membro do grupo de destino de um grupo de destino.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Remove os registros de histórico de um trabalho.     |





### <a name="sp_add_job"></a>sp_add_job

Adiciona um novo trabalho. 
  
#### <a name="syntax"></a>Sintaxe  
  

```sql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
    [ , [ @job_id = ] job_id OUTPUT ]
```

  
#### <a name="arguments"></a>Argumentos  

[ **\@job_name =** ] 'job_name'  
O nome do trabalho. O nome precisa ser exclusivo e não pode conter o caractere de porcentagem (%). job_name é nvarchar(128), sem nenhum padrão.

[ **\@description =** ] 'description'  
A descrição do trabalho. a descrição é nvarchar(512), com um padrão NULL. Se a descrição for omitida, uma cadeia de caracteres vazia será usada.

[  **\@habilitado =** ] habilitado  
Se a agenda do trabalho está habilitada. Habilitado é bit, com o padrão 0 (desabilitado). Se for 0, o trabalho não estará habilitado e não será executado de acordo com sua agenda. No entanto, ele poderá ser executado manualmente. Se for 1, o trabalho será executado de acordo com sua agenda e também poderá ser executado manualmente.

[  **\@schedule_interval_type =** ] schedule_interval_type  
O valor indica quando o trabalho será executado. schedule_interval_type é nvarchar(50), com o padrão Once e pode ser um dos seguintes valores:
- 'Once',
- 'Minutes',
- 'Hours',
- 'Days',
- 'Weeks',
- 'Months'

[  **\@schedule_interval_count =** ] schedule_interval_count  
O número de períodos de schedule_interval_count que devem ocorrer entre cada execução do trabalho. schedule_interval_count é um int, com o padrão 1. O valor precisa ser maior ou igual a 1.

[  **\@schedule_start_time =** ] schedule_start_time  
A data na qual a execução do trabalho pode começar. schedule_start_time é DATETIME2, com o padrão 0001-01-01 00:00:00.0000000.

[ **\@schedule_end_time =** ] schedule_end_time  
A data na qual a execução do trabalho pode parar. schedule_end_time é DATETIME2, com o padrão 9999-12-31 11:59:59.0000000. 

[  **\@job_id =** ] job_id saída  
O número de identificação do trabalho atribuído ao trabalho se criado com êxito. job_id é uma variável de saída do tipo uniqueidentifier.

#### <a name="return-code-values"></a>Valores do código de retorno

0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Comentários
sp_add_job precisa ser executado por meio do banco de dados do agente de trabalho especificado durante a criação do agente de trabalho.
Depois que sp_add_job for executado para adicionar um trabalho, sp_add_jobstep poderá ser usado para adicionar etapas que executam as atividades para o trabalho. O número de versão inicial do trabalho é 0, que será incrementado para 1 quando a primeira etapa for adicionada.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Elas restringem um usuário a poder apenas monitorar trabalhos. É possível conceder ao usuário a seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:

- jobs_reader

Para obter detalhes sobre as permissões dessas funções, confira a seção Permissão neste documento. Somente os membros da função sysadmin podem usar esse procedimento armazenado para editar os atributos dos trabalhos pertencentes a outros usuários.

### <a name="sp_update_job"></a>sp_update_job

Atualiza um trabalho existente.

#### <a name="syntax"></a>Sintaxe

```sql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
    [ , [ @new_name = ] 'new_name' ]
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
```

#### <a name="arguments"></a>Argumentos
[ **\@job_name =** ] 'job_name'  
O nome do trabalho a ser atualizado. job_name é nvarchar(128).

[ **\@new_name =** ] 'new_name'  
O novo nome do trabalho. new_name é nvarchar(128).

[ **\@description =** ] 'description'  
A descrição do trabalho. description é nvarchar(512).

[  **\@habilitado =** ] habilitado  
Especifica se a agenda do trabalho está habilitada (1) ou não habilitada (0). Habilitada é bit.

[ **\@schedule_interval_type=** ] schedule_interval_type  
O valor indica quando o trabalho será executado. schedule_interval_type é nvarchar(50) e pode ser um dos valores a seguir:

- 'Once',
- 'Minutes',
- 'Hours',
- 'Days',
- 'Weeks',
- 'Months'

[  **\@schedule_interval_count =** ] schedule_interval_count  
O número de períodos de schedule_interval_count que devem ocorrer entre cada execução do trabalho. schedule_interval_count é um int, com o padrão 1. O valor precisa ser maior ou igual a 1.

[ **\@schedule_start_time=** ] schedule_start_time  
A data na qual a execução do trabalho pode começar. schedule_start_time é DATETIME2, com o padrão 0001-01-01 00:00:00.0000000.

[ **\@schedule_end_time=** ] schedule_end_time  
A data na qual a execução do trabalho pode parar. schedule_end_time é DATETIME2, com o padrão 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Valores do código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Comentários
Depois que sp_add_job for executado para adicionar um trabalho, sp_add_jobstep poderá ser usado para adicionar etapas que executam as atividades para o trabalho. O número de versão inicial do trabalho é 0, que será incrementado para 1 quando a primeira etapa for adicionada.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Elas restringem um usuário a poder apenas monitorar trabalhos. É possível conceder ao usuário a seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, confira a seção Permissão neste documento. Somente os membros da função sysadmin podem usar esse procedimento armazenado para editar os atributos dos trabalhos pertencentes a outros usuários.



### <a name="sp_delete_job"></a>sp_delete_job

Excluir um trabalho existente.

#### <a name="syntax"></a>Sintaxe

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumentos
[ **\@job_name =** ] 'job_name'  
O nome do trabalho a ser excluído. job_name é nvarchar(128).

[ **\@force =** ] force  
Especifica se deve ocorrer a exclusão se o trabalho tiver execuções em andamento e o cancelamento de todas as execuções em andamento (1) ou falhar se alguma execução de trabalho estiver em andamento (0). force é bit.

#### <a name="return-code-values"></a>Valores do código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Comentários
O histórico de trabalhos é excluído automaticamente quando um trabalho é excluído.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Elas restringem um usuário a poder apenas monitorar trabalhos. É possível conceder ao usuário a seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, confira a seção Permissão neste documento. Somente os membros da função sysadmin podem usar esse procedimento armazenado para editar os atributos dos trabalhos pertencentes a outros usuários.



### <a name="sp_add_jobstep"></a>sp_add_jobstep

Adiciona uma etapa a um trabalho.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] step_name ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]   
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumentos

[ **\@job_name =** ] 'job_name'  
O nome do trabalho ao qual a etapa deve ser adicionada. job_name é nvarchar(128).

[  **\@step_id =** ] step_id  
O número de identificação de sequência da etapa de trabalho. Os números de identificação de etapa começam em 1 e são incrementados sem lacunas. Se uma etapa existente já tiver essa ID, essa etapa e todas as próximas etapas terão suas ID incrementadas para que essa nova etapa possa ser inserida na sequência. Se não for especificada, a step_id será atribuída automaticamente à última na sequência de etapas. step_id é um int.

[  **\@step_name =** ] step_name  
O nome da etapa. Precisa ser especificado, exceto para a primeira etapa de um trabalho que (para facilitar) tem o nome padrão 'JobStep'. step_name é nvarchar (128).

[ **\@command_type =** ] 'command_type'  
O tipo de comando executado pela etapa de trabalho. command_type é nvarchar(50), com o valor padrão TSql, o que significa que o valor do parâmetro @command_type é um script T-SQL.

Se especificado, o valor precisa ser TSql.

[ **\@command_source =** ] 'command_source'  
O tipo do local em que o comando é armazenado. command_source é nvarchar(50), com o valor padrão Inline, o que significa que o valor do parâmetro @command_source é o texto literal do comando.

Se especificado, o valor precisa ser Inline.

[ **\@command =** ] 'command'  
O comando precisa ser um script T-SQL válido que será executado por essa etapa de trabalho. command é nvarchar (max), com o padrão NULL.

[ **\@credential_name =** ] 'credential_name'  
O nome da credencial no escopo do banco de dados armazenada neste banco de dados de controle de trabalhos que é usado para conectar-se a cada um dos bancos de dados de destino dentro do grupo de destino quando essa etapa é executada. credential_name é nvarchar(128).

[ **\@target_group_name =** ] 'target-group_name'  
O nome do grupo de destino que contém os bancos de dados de destino nos quais a etapa de trabalho será executada. target_group_name é nvarchar(128).

[  **\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
O atraso antes da primeira tentativa de repetição, se a etapa de trabalho falhar na tentativa de execução inicial. initial_retry_interval_seconds é um int, com o valor padrão 1.

[  **\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
O atraso máximo entre as tentativas repetição. Se o atraso entre as repetições puder aumentar mais que esse valor, ele será limitado a esse valor. maximum_retry_interval_seconds é um int, com o valor padrão 120.

[ **\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
O multiplicador a ser aplicado ao atraso de repetição se várias tentativas de execução de etapa de trabalho falharem. Por exemplo, se a primeira repetição tiver um atraso de 5 segundos e o multiplicador de retirada for 2.0, a segunda repetição terá um atraso de 10 segundos e a terceira, de 20 segundos. retry_interval_backoff_multiplier é real, com o valor padrão 2.0.

[ **\@retry_attempts =** ] retry_attempts  
O número de vezes que a execução deverá ser repetida quando a tentativa inicial falhar. Por exemplo, se o valor de retry_attempts for 10, haverá 1 tentativa inicial e 10 tentativas de repetição, resultando no total de 11 tentativas. Se a tentativa de repetição final falhar, a execução do trabalho será encerrada com um ciclo de vida Com Falha. retry_attempts é um int, com o valor padrão 10.

[ **\@step_timeout_seconds =** ] step_timeout_seconds  
O período de tempo máximo permitido para a execução da etapa. Se esse tempo for excedido, a execução do trabalho será encerrada com um ciclo de vida igual a TimedOut. step_timeout_seconds é um int, com o valor padrão de 43.200 segundos (12 horas).

[ **\@output_type =** ] 'output_type'  
Se não for nulo, o tipo de destino no qual o primeiro conjunto de resultados do comando será gravado. output_type é nvarchar(50), com o padrão NULL.

Se especificado, o valor precisará ser SqlDatabase.

[ **\@output_credential_name =** ] 'output_credential_name'  
Se não for nulo, o nome da credencial no escopo do banco de dados que é usada para conectar-se ao banco de dados de destino de saída. Precisará ser especificado se output_type for igual a SqlDatabase. output_credential_name é nvarchar(128), com o valor padrão NULL.

[ **\@output_subscription_id =** ] 'output_subscription_id'  
Precisa de descrição.

[ **\@output_resource_group_name =** ] 'output_resource_group_name'  
Precisa de descrição.

[ **\@output_server_name =** ] 'output_server_name'  
Se não for nulo, será o nome DNS totalmente qualificado do servidor que contém o banco de dados de destino de saída. Precisará ser especificado se output_type for igual a SqlDatabase. output_server_name é nvarchar(256), com o padrão NULL.

[ **\@output_database_name =** ] 'output_database_name'  
Se não for nulo, será o nome do banco de dados que contém a tabela de destino de saída. Precisará ser especificado se output_type for igual a SqlDatabase. output_database_name é nvarchar(128), com o padrão NULL.

[ **\@output_schema_name =** ] 'output_schema_name'  
Se não for nulo, será o nome do esquema SQL que contém a tabela de destino de saída. Se output_type for SqlDatabase, o valor padrão será dbo. output_schema_name é nvarchar(128).

[ **\@output_table_name =** ] 'output_table_name'  
Se não for nulo, o nome da tabela na qual o primeiro conjunto de resultados do comando será gravado. Se a tabela ainda não existir, ela será criada com base no esquema de conjunto de resultados retornado. Precisará ser especificado se output_type for igual a SqlDatabase. output_table_name é nvarchar(128), com o valor padrão NULL.

[  **\@job_version =** ] job_version saída  
Parâmetro de saída que será atribuído ao novo número de versão do trabalho. job_version é um int.

[  **\@max_parallelism =** ] max_parallelism saída  
O nível máximo de paralelismo por pool elástico. Se estiver definido, a execução da etapa de trabalho será restrita apenas a esse número máximo de bancos de dados por pool elástico. Isso se aplica a cada pool elástico que está incluído diretamente no grupo de destino ou está dentro de um servidor incluído no grupo de destino. max_parallelism é um int.


#### <a name="return-code-values"></a>Valores do código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Comentários
Quando sp_add_jobstep é bem-sucedido, o número de versão atual do trabalho é incrementado. Na próxima vez em que o trabalho for executado, a nova versão será usada. Se o trabalho estiver em execução no momento, essa execução não conterá a nova etapa.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Elas restringem um usuário a poder apenas monitorar trabalhos. É possível conceder ao usuário a seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:  

- jobs_reader

Para obter detalhes sobre as permissões dessas funções, confira a seção Permissão neste documento. Somente os membros da função sysadmin podem usar esse procedimento armazenado para editar os atributos dos trabalhos pertencentes a outros usuários.



### <a name="sp_update_jobstep"></a>sp_update_jobstep

Atualiza uma etapa de trabalho.

#### <a name="syntax"></a>Sintaxe

```sql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @new_id = ] new_id ]   
     [ , [ @new_name = ] 'new_name' ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumentos
[ **\@job_name =** ] 'job_name'  
O nome do trabalho ao qual a etapa pertence. job_name é nvarchar(128).

[  **\@step_id =** ] step_id  
O número de identificação da etapa de trabalho a ser modificada. step_id ou step_name precisa ser especificado. step_id é um int.

[  **\@step_name =** ] 'step_name'  
O nome da etapa a ser modificada. step_id ou step_name precisa ser especificado. step_name é nvarchar (128).

[ **\@new_id =** ] new_id  
O novo número de identificação de sequência da etapa de trabalho. Os números de identificação de etapa começam em 1 e são incrementados sem lacunas. Se uma etapa for reordenada, as outras etapas serão renumeradas automaticamente.

[ **\@new_name =** ] 'new_name'  
O novo nome da etapa. new_name é nvarchar(128).

[ **\@command_type =** ] 'command_type'  
O tipo de comando executado pela etapa de trabalho. command_type é nvarchar(50), com o valor padrão TSql, o que significa que o valor do parâmetro @command_type é um script T-SQL.

Se especificado, o valor precisa ser TSql.

[ **\@command_source =** ] 'command_source'  
O tipo do local em que o comando é armazenado. command_source é nvarchar(50), com o valor padrão Inline, o que significa que o valor do parâmetro @command_source é o texto literal do comando.

Se especificado, o valor precisa ser Inline.

[ **\@command =** ] 'command'  
Os comandos precisam ser um script T-SQL válido que será executado por essa etapa de trabalho. command é nvarchar (max), com o padrão NULL.

[ **\@credential_name =** ] 'credential_name'  
O nome da credencial no escopo do banco de dados armazenada neste banco de dados de controle de trabalhos que é usado para conectar-se a cada um dos bancos de dados de destino dentro do grupo de destino quando essa etapa é executada. credential_name é nvarchar(128).

[ **\@target_group_name =** ] 'target-group_name'  
O nome do grupo de destino que contém os bancos de dados de destino nos quais a etapa de trabalho será executada. target_group_name é nvarchar(128).

[  **\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
O atraso antes da primeira tentativa de repetição, se a etapa de trabalho falhar na tentativa de execução inicial. initial_retry_interval_seconds é um int, com o valor padrão 1.

[  **\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
O atraso máximo entre as tentativas repetição. Se o atraso entre as repetições puder aumentar mais que esse valor, ele será limitado a esse valor. maximum_retry_interval_seconds é um int, com o valor padrão 120.

[ **\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
O multiplicador a ser aplicado ao atraso de repetição se várias tentativas de execução de etapa de trabalho falharem. Por exemplo, se a primeira repetição tiver um atraso de 5 segundos e o multiplicador de retirada for 2.0, a segunda repetição terá um atraso de 10 segundos e a terceira, de 20 segundos. retry_interval_backoff_multiplier é real, com o valor padrão 2.0.

[ **\@retry_attempts =** ] retry_attempts  
O número de vezes que a execução deverá ser repetida quando a tentativa inicial falhar. Por exemplo, se o valor de retry_attempts for 10, haverá 1 tentativa inicial e 10 tentativas de repetição, resultando no total de 11 tentativas. Se a tentativa de repetição final falhar, a execução do trabalho será encerrada com um ciclo de vida Com Falha. retry_attempts é um int, com o valor padrão 10.

[ **\@step_timeout_seconds =** ] step_timeout_seconds  
O período de tempo máximo permitido para a execução da etapa. Se esse tempo for excedido, a execução do trabalho será encerrada com um ciclo de vida igual a TimedOut. step_timeout_seconds é um int, com o valor padrão de 43.200 segundos (12 horas).

[ **\@output_type =** ] 'output_type'  
Se não for nulo, o tipo de destino no qual o primeiro conjunto de resultados do comando será gravado. Para redefinir o valor de output_type novamente para NULL, defina o valor deste parâmetro como '' (cadeia de caracteres vazia). output_type é nvarchar(50), com o padrão NULL.

Se especificado, o valor precisará ser SqlDatabase.

[ **\@output_credential_name =** ] 'output_credential_name'  
Se não for nulo, o nome da credencial no escopo do banco de dados que é usada para conectar-se ao banco de dados de destino de saída. Precisará ser especificado se output_type for igual a SqlDatabase. Para redefinir o valor de output_credential_name novamente como NULL, defina o valor deste parâmetro como '' (cadeia de caracteres vazia). output_credential_name é nvarchar(128), com o valor padrão NULL.

[ **\@output_server_name =** ] 'output_server_name'  
Se não for nulo, será o nome DNS totalmente qualificado do servidor que contém o banco de dados de destino de saída. Precisará ser especificado se output_type for igual a SqlDatabase. Para redefinir o valor de output_server_name novamente como NULL, defina o valor deste parâmetro como '' (cadeia de caracteres vazia). output_server_name é nvarchar(256), com o padrão NULL.

[ **\@output_database_name =** ] 'output_database_name'  
Se não for nulo, será o nome do banco de dados que contém a tabela de destino de saída. Precisará ser especificado se output_type for igual a SqlDatabase. Para redefinir o valor de output_database_name novamente como NULL, defina o valor deste parâmetro como '' (cadeia de caracteres vazia). output_database_name é nvarchar(128), com o padrão NULL.

[ **\@output_schema_name =** ] 'output_schema_name'  
Se não for nulo, será o nome do esquema SQL que contém a tabela de destino de saída. Se output_type for SqlDatabase, o valor padrão será dbo. Para redefinir o valor de output_schema_name novamente como NULL, defina o valor deste parâmetro como '' (cadeia de caracteres vazia). output_schema_name é nvarchar(128).

[ **\@output_table_name =** ] 'output_table_name'  
Se não for nulo, o nome da tabela na qual o primeiro conjunto de resultados do comando será gravado. Se a tabela ainda não existir, ela será criada com base no esquema de conjunto de resultados retornado. Precisará ser especificado se output_type for igual a SqlDatabase. Para redefinir o valor de output_server_name novamente como NULL, defina o valor deste parâmetro como '' (cadeia de caracteres vazia). output_table_name é nvarchar(128), com o valor padrão NULL.

[  **\@job_version =** ] job_version saída  
Parâmetro de saída que será atribuído ao novo número de versão do trabalho. job_version é um int.

[  **\@max_parallelism =** ] max_parallelism saída  
O nível máximo de paralelismo por pool elástico. Se estiver definido, a execução da etapa de trabalho será restrita apenas a esse número máximo de bancos de dados por pool elástico. Isso se aplica a cada pool elástico que está incluído diretamente no grupo de destino ou está dentro de um servidor incluído no grupo de destino. Para redefinir o valor de max_parallelism novamente como nulo, defina o valor deste parâmetro como -1. max_parallelism é um int.


#### <a name="return-code-values"></a>Valores do código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Comentários
As execuções do trabalho em andamento não serão afetadas. Quando sp_update_jobstep for bem-sucedida, o número de versão do trabalho será incrementado. Na próxima vez em que o trabalho for executado, a nova versão será usada.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Elas restringem um usuário a poder apenas monitorar trabalhos. É possível conceder ao usuário a seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:

- jobs_reader

Para obter detalhes sobre as permissões dessas funções, confira a seção Permissão neste documento. Somente os membros da função sysadmin podem usar esse procedimento armazenado para editar os atributos dos trabalhos pertencentes a outros usuários




### <a name="sp_delete_jobstep"></a>sp_delete_jobstep

Remove uma etapa de trabalho de um trabalho.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumentos
[ **\@job_name =** ] 'job_name'  
O nome do trabalho do qual a etapa será removida. job_name é nvarchar(128), sem nenhum padrão.

[  **\@step_id =** ] step_id  
O número de identificação da etapa de trabalho a ser excluída. step_id ou step_name precisa ser especificado. step_id é um int.

[  **\@step_name =** ] 'step_name'  
O nome da etapa a ser excluída. step_id ou step_name precisa ser especificado. step_name é nvarchar (128).

[  **\@job_version =** ] job_version saída  
Parâmetro de saída que será atribuído ao novo número de versão do trabalho. job_version é um int.

#### <a name="return-code-values"></a>Valores do código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Comentários
As execuções do trabalho em andamento não serão afetadas. Quando sp_update_jobstep for bem-sucedida, o número de versão do trabalho será incrementado. Na próxima vez em que o trabalho for executado, a nova versão será usada.

As outras etapas de trabalho serão renumeradas automaticamente para preencher a lacuna deixada pela etapa de trabalho excluída.
 
#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Elas restringem um usuário a poder apenas monitorar trabalhos. É possível conceder ao usuário a seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, confira a seção Permissão neste documento. Somente os membros da função sysadmin podem usar esse procedimento armazenado para editar os atributos dos trabalhos pertencentes a outros usuários.






### <a name="sp_start_job"></a>sp_start_job

Inicia a execução de um trabalho.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumentos
[ **\@job_name =** ] 'job_name'  
O nome do trabalho do qual a etapa será removida. job_name é nvarchar(128), sem nenhum padrão.

[  **\@job_execution_id =** ] job_execution_id saída  
Parâmetro de saída que será atribuído à ID da execução do trabalho. job_version é um uniqueidentifier.

#### <a name="return-code-values"></a>Valores do código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Comentários
Nenhuma.
 
#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Elas restringem um usuário a poder apenas monitorar trabalhos. É possível conceder ao usuário a seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, confira a seção Permissão neste documento. Somente os membros da função sysadmin podem usar esse procedimento armazenado para editar os atributos dos trabalhos pertencentes a outros usuários.

### <a name="sp_stop_job"></a>sp_stop_job

Interrompe a execução de um trabalho.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumentos
[ **\@job_execution_id =** ] job_execution_id  
O número de identificação da execução de trabalho a ser interrompida. job_execution_id é um uniqueidentifier, com o padrão NULL.

#### <a name="return-code-values"></a>Valores do código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Comentários
Nenhuma.
 
#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Elas restringem um usuário a poder apenas monitorar trabalhos. É possível conceder ao usuário a seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, confira a seção Permissão neste documento. Somente os membros da função sysadmin podem usar esse procedimento armazenado para editar os atributos dos trabalhos pertencentes a outros usuários.


### <a name="sp_add_target_group"></a>sp_add_target_group

Adiciona um grupo de destino.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumentos
[ **\@target_group_name =** ] 'target_group_name'  
O nome do grupo de destino a ser criado. target_group_name é nvarchar(128), sem nenhum padrão.

[  **\@target_group_id =** ] target_group_id o destino de saída agrupar o número de identificação atribuído ao trabalho se criado com êxito. target_group_id é uma variável de saída do tipo uniqueidentifier, com o padrão NULL.

#### <a name="return-code-values"></a>Valores do código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Comentários
Os grupos de destino fornecem uma maneira fácil de direcionar a um trabalho em uma coleção de bancos de dados.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Elas restringem um usuário a poder apenas monitorar trabalhos. É possível conceder ao usuário a seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, confira a seção Permissão neste documento. Somente os membros da função sysadmin podem usar esse procedimento armazenado para editar os atributos dos trabalhos pertencentes a outros usuários.

### <a name="sp_delete_target_group"></a>sp_delete_target_group

Exclui um grupo de destino.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumentos
[ **\@target_group_name =** ] 'target_group_name'  
O nome do grupo de destino a ser excluído. target_group_name é nvarchar(128), sem nenhum padrão.

#### <a name="return-code-values"></a>Valores do código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Comentários
Nenhuma.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Elas restringem um usuário a poder apenas monitorar trabalhos. É possível conceder ao usuário a seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, confira a seção Permissão neste documento. Somente os membros da função sysadmin podem usar esse procedimento armazenado para editar os atributos dos trabalhos pertencentes a outros usuários.

### <a name="sp_add_target_group_member"></a>sp_add_target_group_member

Adiciona um banco de dados ou um grupo de bancos de dados a um grupo de destino.

#### <a name="syntax"></a>Sintaxe

```sql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]   
        [ , [ @target_type = ] 'target_type' ]   
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]   
        [ , [ @server_name = ] 'server_name' ]   
        [ , [ @database_name = ] 'database_name' ]   
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]   
        [ , [ @shard_map_name = ] 'shard_map_name' ]   
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Argumentos
[ **\@target_group_name =** ] 'target_group_name'  
O nome do grupo de destino ao qual o membro será adicionado. target_group_name é nvarchar(128), sem nenhum padrão.

[ **\@membership_type =** ] 'membership_type'  
Especifica se o membro do grupo de destino será incluído ou excluído. target_group_name é nvarchar(128), com o padrão ‘Include’. Os valores válidos para target_group_name são 'Include' ou 'Exclude'.

[ **\@target_type =** ] 'target_type'  
O tipo do banco de dados ou da coleção de bancos de dados de destino, incluindo todos os bancos de dados em um servidor, todos os bancos de dados em um pool elástico, todos os bancos de dados em um mapa de fragmentos ou em um banco de dados individual. target_type é nvarchar(128), sem nenhum padrão. Os valores válidos para target_type são 'SqlServer', 'SqlElasticPool', 'SqlDatabase' ou 'SqlShardMap'. 

[ **\@refresh_credential_name =** ] 'refresh_credential_name'  
O nome do servidor do Banco de Dados SQL. refresh_credential_name é nvarchar(128), sem nenhum padrão.

[ **\@server_name =** ] 'server_name'  
O nome do servidor do Banco de Dados SQL que deve ser adicionado ao grupo de destino especificado. server_name deverá ser especificado quando target_type for ‘SqlServer’. server_name é nvarchar(128), sem nenhum padrão.

[ **\@database_name =** ] 'database_name'  
O nome do banco de dados que deve ser adicionado ao grupo de destino especificado. database_name deverá ser especificado quando target_type for ‘SqlDatabase’. database_name é nvarchar(128), sem nenhum padrão.

[ **\@elastic_pool_name =** ] 'elastic_pool_name'  
O nome do pool elástico que deve ser adicionado ao grupo de destino especificado. elastic_pool_name deverá ser especificado quando target_type for ‘SqlElasticPool’. elastic_pool_name é nvarchar(128), sem nenhum padrão.

[ **\@shard_map_name =** ] 'shard_map_name'  
O nome do pool do mapa de fragmentos que deve ser adicionado ao grupo de destino especificado. elastic_pool_name deverá ser especificado quando target_type for ‘SqlSqlShardMap’. shard_map_name é nvarchar(128), sem nenhum padrão.

[  **\@target_id =** ] target_group_id saída  
O número de identificação de destino atribuído ao membro do grupo de destino, se tiver sido criado, adicionado ao grupo de destino. target_id é uma variável de saída do tipo uniqueidentifier, com o padrão NULL.
Valores do código de retorno 0 (sucesso) ou 1 (falha)

#### <a name="remarks"></a>Comentários
Um trabalho é executado em todos os bancos de dados individuais em um servidor do Banco de Dados SQL ou em um pool elástico no momento da execução, quando um servidor do Banco de Dados SQL ou pool elástico é incluído no grupo de destino.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Elas restringem um usuário a poder apenas monitorar trabalhos. É possível conceder ao usuário a seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, confira a seção Permissão neste documento. Somente os membros da função sysadmin podem usar esse procedimento armazenado para editar os atributos dos trabalhos pertencentes a outros usuários.

#### <a name="examples"></a>Exemplos
O exemplo a seguir adiciona todos os bancos de dados nos servidores London e NewYork ao grupo Servidores que Mantêm Informações de Clientes. Você precisa se conectar ao banco de dados de trabalhos especificado ao criar o agente de trabalho, neste caso, o ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

Remove um membro do grupo de destino de um grupo de destino.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



Arguments [ @target_group_name = ] 'target_group_name'  
O nome do grupo de destino do qual o membro do grupo de destino deve ser removido. target_group_name é nvarchar(128), sem nenhum padrão.

[ @target_id = ] target_id  
 O número de identificação de destino atribuído ao membro do grupo de destino a ser removido. target_id é um uniqueidentifier, com o padrão NULL.

#### <a name="return-code-values"></a>Valores do código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Comentários
Os grupos de destino fornecem uma maneira fácil de direcionar a um trabalho em uma coleção de bancos de dados.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Elas restringem um usuário a poder apenas monitorar trabalhos. É possível conceder ao usuário a seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, confira a seção Permissão neste documento. Somente os membros da função sysadmin podem usar esse procedimento armazenado para editar os atributos dos trabalhos pertencentes a outros usuários.

#### <a name="examples"></a>Exemplos
O exemplo a seguir remove o servidor London do grupo Servidores que Mantêm Informações de Clientes. Você precisa se conectar ao banco de dados de trabalhos especificado ao criar o agente de trabalho, neste caso, o ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Remove os registros de histórico de um trabalho.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumentos
[ **\@job_name =** ] 'job_name'  
O nome do trabalho cujos registros de histórico devem ser excluídos. job_name é nvarchar(128), com o padrão NULL. É necessário especificar job_id ou job_name, mas não ambos.

[ **\@job_id =** ] job_id  
 O número de identificação do trabalho cujos registros devem ser excluídos. job_id é um uniqueidentifier, com o padrão NULL. É necessário especificar job_id ou job_name, mas não ambos.

[ **\@oldest_date =** ] oldest_date  
 O registro mais antigo a ser retido no histórico. oldest_date é DATETIME2, com o padrão NULL. Quando oldest_date é especificado, sp_purge_jobhistory remove somente os registros que são mais antigos do que o valor especificado.

#### <a name="return-code-values"></a>Valores do código de retorno
0 (êxito) ou 1 (falha) Os grupos de Destino de Comentários oferecem uma maneira fácil de direcionar a um trabalho em uma coleção de bancos de dados.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Elas restringem um usuário a poder apenas monitorar trabalhos. É possível conceder ao usuário a seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, confira a seção Permissão neste documento. Somente os membros da função sysadmin podem usar esse procedimento armazenado para editar os atributos dos trabalhos pertencentes a outros usuários.

#### <a name="examples"></a>Exemplos
O exemplo a seguir adiciona todos os bancos de dados nos servidores London e NewYork ao grupo Servidores que Mantêm Informações de Clientes. Você precisa se conectar ao banco de dados de trabalhos especificado ao criar o agente de trabalho, neste caso, o ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Exibições de trabalho

As seguintes exibições estão disponíveis no [banco de dados de trabalhos](sql-database-job-automation-overview.md#job-database).


|Visualizar  |DESCRIÇÃO  |
|---------|---------|
|[jobs_executions](#jobs_executions-view)     |  Mostra o histórico de execução de trabalho.      |
|[jobs](#jobs-view)     |   Mostra todos os trabalhos.      |
|[job_versions](#job_versions-view)     |   Mostra todas as versões de trabalhos.      |
|[jobsteps](#jobsteps-view)     |     Mostra todas as etapas na versão atual de cada trabalho.    |
|[jobstep_versions](#jobstep_versions-view)     |     Mostra todas as etapas em todas as versões de cada trabalho.    |
|[target_groups](#target_groups-view)     |      Mostra todos os grupos de destino.   |
|[target_group_members](#target_groups_members-view)     |   Mostra todos os membros de todos os grupos de destino.      |


### <a name="jobs_executions-view"></a>jobs_executions view

[jobs].[jobs_executions]

Mostra o histórico de execução de trabalho.


|Nome da coluna|   Tipo de dados   |DESCRIÇÃO|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  ID exclusiva de uma instância de uma execução de trabalho.
|**job_name**   |nvarchar(128)  |Nome do trabalho.
|**job_id** |uniqueidentifier|  ID exclusiva do trabalho.
|**job_version**    |int    |Versão do trabalho (atualizada automaticamente sempre que o trabalho é modificado).
|**step_id**    |int|   Identificador exclusivo (para este trabalho) da etapa. NULL indica que esta é a execução do trabalho pai.
|**is_active**| bit |Indica se as informações estão ativas ou inativas. 1 indica trabalhos ativos e 0 indica inativos.
|**lifecycle**| nvarchar (50)|Valor que indica o status do trabalho: ‘Created’, ‘In Progress’, ‘Failed’, ‘Succeeded’, ‘Skipped’, 'SucceededWithSkipped’|
|**create_time**|   Datetime2 (7)|   Data e hora em que o trabalho foi criado.
|**start_time** |Datetime2 (7)|  Data e hora em que a execução do trabalho foi iniciada. NULL se o trabalho ainda não foi executado.
|**end_time**|  Datetime2 (7)    |Data e hora em que a execução do trabalho foi concluída. NULL se o trabalho ainda não foi executado ou se a execução ainda não foi concluída.
|**current_attempts**   |int    |Número de vezes que a etapa foi repetida. O trabalho pai será 0, as execuções de trabalho filho serão 1 ou maior com base na política de execução.
|**current_attempt_start_time** |Datetime2 (7)|  Data e hora em que a execução do trabalho foi iniciada. NULL indica que esta é a execução do trabalho pai.
|**last_message**   |nvarchar(max)| Mensagem de histórico do trabalho ou da etapa. 
|**target_type**|   nvarchar(128)   |Tipo de banco de dados ou de coleção de bancos de dados de destino, incluindo todos os bancos de dados em um servidor, todos os bancos de dados em um pool elástico ou em um banco de dados. Os valores válidos para target_type são 'SqlServer', 'SqlElasticPool' ou 'SqlDatabase'. NULL indica que esta é a execução do trabalho pai.
|**target_id**  |uniqueidentifier|  A ID exclusiva do membro do grupo de destino.  NULL indica que esta é a execução do trabalho pai.
|**target_group_name**  |nvarchar(128)  |Nome do grupo de destino. NULL indica que esta é a execução do trabalho pai.
|**target_server_name**|    nvarchar(256)|  Nome do servidor do Banco de Dados SQL contido no grupo de destino. Especificado somente se target_type for 'SqlServer'. NULL indica que esta é a execução do trabalho pai.
|**target_database_name**   |nvarchar(128)| Nome do banco de dados contido no grupo de destino. Especificado somente quando target_type for 'SqlDatabase'. NULL indica que esta é a execução do trabalho pai.


### <a name="jobs-view"></a>Exibição jobs

[jobs].[jobs]

Mostra todos os trabalhos.

|Nome da coluna|   Tipo de dados|  DESCRIÇÃO|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Nome do trabalho.|
|**job_id**|    uniqueidentifier    |ID exclusiva do trabalho.|
|**job_version**    |int    |Versão do trabalho (atualizada automaticamente sempre que o trabalho é modificado).|
|**description**    |nvarchar(512)| Descrição do trabalho. enabled bit – Indica se o trabalho está habilitado ou desabilitado. 1 indica trabalhos habilitados e 0 indica trabalhos desabilitados.|
|**schedule_interval_type** |nvarchar (50)   |Valor que indica quando o trabalho deve ser executado:'Once', 'Minutes', 'Hours', 'Days', 'Weeks', 'Months'
|**schedule_interval_count**|   int|    O número de períodos de schedule_interval_type que devem ocorrer entre cada execução do trabalho.|
|**schedule_start_time**    |Datetime2 (7)|  Data e hora em que a última execução do trabalho foi iniciada.|
|**schedule_end_time**| Datetime2 (7)|   Data e hora em que a execução do trabalho foi concluída pela última vez.|


### <a name="job_versions-view"></a>modo de exibição job_versions

[jobs].[job_versions]

Mostra todas as versões de trabalhos.

|Nome da coluna|   Tipo de dados|  DESCRIÇÃO|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Nome do trabalho.|
|**job_id**|    uniqueidentifier    |ID exclusiva do trabalho.|
|**job_version**    |int    |Versão do trabalho (atualizada automaticamente sempre que o trabalho é modificado).|


### <a name="jobsteps-view"></a>Exibição jobsteps

[jobs].[jobsteps]

Mostra todas as etapas na versão atual de cada trabalho.

|Nome da coluna    |Tipo de dados| DESCRIÇÃO|
|------|------|-------|
|**job_name**   |nvarchar(128)| Nome do trabalho.|
|**job_id** |uniqueidentifier   |ID exclusiva do trabalho.|
|**job_version**|   int|    Versão do trabalho (atualizada automaticamente sempre que o trabalho é modificado).|
|**step_id**    |int    |Identificador exclusivo (para este trabalho) da etapa.|
|**step_name**  |nvarchar(128)  |Nome exclusivo (para este trabalho) da etapa.|
|**command_type**   |nvarchar (50)   |Tipo de comando a ser executado na etapa de trabalho. Para v1, o valor precisa ser igual e configurado como ‘TSql’ por padrão.|
|**command_source** |nvarchar (50)|  Local do comando. Para v1, ‘Inline’ é o padrão e o único valor aceito.|
|**command**|   nvarchar(max)|  Os comandos a serem executados pelos trabalhos elásticos por meio de command_type.|
|**credential_name**|   nvarchar(128)   |O nome da credencial no escopo do banco de dados usada para a execução do trabalho.|
|**target_group_name**| nvarchar(128)   |Nome do grupo de destino.|
|**target_group_id**|   uniqueidentifier|   ID exclusiva do grupo de destino.|
|**initial_retry_interval_seconds**|    int |O atraso antes da primeira tentativa de repetição. O valor padrão é 1.|
|**maximum_retry_interval_seconds** |int|   O atraso máximo entre as tentativas repetição. Se o atraso entre as repetições puder aumentar mais que esse valor, ele será limitado a esse valor. O valor padrão é 120.|
|**retry_interval_backoff_multiplier**  |real|  O multiplicador a ser aplicado ao atraso de repetição se várias tentativas de execução de etapa de trabalho falharem. O valor padrão é 2.0.|
|**retry_attempts** |int|   O número de tentativas de repetição que devem ocorrer se esta etapa falhar. O padrão é 10, que indica nenhuma tentativa de repetição.|
|**step_timeout_seconds**   |int|   O período de tempo em minutos entre as tentativas de repetição. O padrão é 0, que indica um intervalo de 0 minuto.|
|**output_type**    |nvarchar(11)|  Local do comando. Na versão prévia atual, 'Inline' é o padrão e o único valor aceito.|
|**output_credential_name**|    nvarchar(128)   |O nome das credenciais a serem usadas para conectar-se ao servidor de destino para armazenar o conjunto de resultados.|
|**output_subscription_id**|    uniqueidentifier|   ID exclusiva da assinatura do servidor/banco de dados de destino do conjunto de resultados da execução da consulta.|
|**output_resource_group_name** |nvarchar(128)| Nome do grupo de recursos no qual o servidor de destino reside.|
|**output_server_name**|    nvarchar(256)   |Nome do servidor de destino do conjunto de resultados.|
|**output_database_name**   |nvarchar(128)| Nome do banco de dados de destino do conjunto de resultados.|
|**output_schema_name** |nvarchar(max)| Nome do esquema de destino. O padrão será dbo se não estiver especificado.|
|**output_table_name**| nvarchar(max)|  Nome da tabela para armazenar o conjunto de resultados da consulta. A tabela será criada automaticamente com base no esquema do conjunto de resultados se ela ainda não existir. O esquema precisa corresponder ao esquema do conjunto de resultados.|
|**max_parallelism**|   int|    O número máximo de bancos de dados por pool elástico em que a etapa de trabalho será executada de cada vez. O padrão é NULL, o que significa sem limite. |


### <a name="jobstep_versions-view"></a>modo de exibição jobstep_versions

[jobs].[jobstep_versions]

Mostra todas as etapas em todas as versões de cada trabalho. O esquema é idêntico ao de [jobsteps](#jobsteps-view).

### <a name="target_groups-view"></a>target_groups view

[jobs].[target_groups]

Lista todos os grupos de destino.

|Nome da coluna|Tipo de dados| DESCRIÇÃO|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |O nome do grupo de destino, uma coleção de bancos de dados. 
|**target_group_id**    |uniqueidentifier   |ID exclusiva do grupo de destino.

### <a name="target_groups_members-view"></a>target_groups_members view

[jobs].[target_groups_members]

Mostra todos os membros de todos os grupos de destino.

|Nome da coluna|Tipo de dados| DESCRIÇÃO|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128|O nome do grupo de destino, uma coleção de bancos de dados. |
|**target_group_id**    |uniqueidentifier   |ID exclusiva do grupo de destino.|
|**membership_type**    |int|   Especifica se o membro do grupo de destino está incluído ou excluído do grupo de destino. Os valores válidos para target_group_name são 'Include' ou 'Exclude'.|
|**target_type**    |nvarchar(128)| Tipo de banco de dados ou de coleção de bancos de dados de destino, incluindo todos os bancos de dados em um servidor, todos os bancos de dados em um pool elástico ou em um banco de dados. Os valores válidos para target_type são 'SqlServer', 'SqlElasticPool', 'SqlDatabase' ou 'SqlShardMap'.|
|**target_id**  |uniqueidentifier|  A ID exclusiva do membro do grupo de destino.|
|**refresh_credential_name**    |nvarchar(128)  |O nome da credencial no escopo do banco de dados usada para conectar-se ao membro do grupo de destino.|
|**subscription_id**    |uniqueidentifier|  ID exclusiva da assinatura.|
|**resource_group_name**    |nvarchar(128)| Nome do grupo de recursos no qual o membro do grupo de destino reside.|
|**server_name**    |nvarchar(128)  |Nome do servidor do Banco de Dados SQL contido no grupo de destino. Especificado somente se target_type for 'SqlServer'. |
|**database_name**  |nvarchar(128)  |Nome do banco de dados contido no grupo de destino. Especificado somente quando target_type for 'SqlDatabase'.|
|**elastic_pool_name**  |nvarchar(128)| Nome do pool elástico contido no grupo de destino. Especificado somente quando target_type for 'SqlElasticPool'.|
|**shard_map_name** |nvarchar(128)| Nome do mapa de fragmentos contido no grupo de destino. Especificado somente quando target_type for 'SqlShardMap'.|


## <a name="resources"></a>Recursos

 - ![Ícone de link do tópico](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "Ícone de link do tópico") [Convenções de sintaxe Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>Próximas etapas

- [Criar e gerenciar trabalhos elásticos usando o PowerShell](elastic-jobs-powershell.md)
- [Autorização e permissões no SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
