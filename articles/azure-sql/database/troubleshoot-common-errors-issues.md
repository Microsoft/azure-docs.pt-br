---
title: Solucionar problemas comuns de conexão no Banco de Dados SQL do Azure
description: Fornece etapas para solucionar problemas de conexão do banco de dados SQL do Azure e resolver outros problemas específicos do banco de dados SQL do Azure ou do Azure SQL Instância Gerenciada
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019, sqldbrb=1
author: ramakoni1
ms.author: ramakoni
ms.reviewer: sstein,vanto
ms.date: 01/14/2021
ms.openlocfilehash: ec61f2c67576d6e144d8d4bb7e8ecaaa157db0a9
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233365"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Solucionar problemas de conectividade e outros erros com o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Você recebe mensagens de erro quando a conexão com o banco de dados SQL do Azure ou o Azure SQL Instância Gerenciada falha. Esses problemas de conexão podem ser causados pela reconfiguração, configurações de firewall, um tempo limite de conexão, informações de logon incorretas ou falha na aplicação de práticas recomendadas e diretrizes de design durante o processo de [design do aplicativo](develop-overview.md) . Além disso, se o limite máximo de alguns recursos do banco de dados SQL do Azure ou do SQL Instância Gerenciada for atingido, você não poderá mais se conectar.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Mensagens transitórias de erro de falha (40197, 40613 e outras)

O sistema Azure tem a capacidade de reconfigurar dinamicamente os servidores quando cargas de trabalho pesadas surgem no serviço do Banco de Dados SQL.  Esse comportamento dinâmico pode fazer com que o programa cliente perca sua conexão com o banco de dados ou instância. Essa variante de condição de erro é chamada de uma *falha transitória*. Eventos de reconfiguração do banco de dados ocorrem devido a um evento planejado (por exemplo, uma atualização de software) ou um evento não planejado (por exemplo, uma falha no processo ou balanceamento de carga). A maioria dos eventos de reconfiguração geralmente é de curta duração e deve ser concluída em, no máximo, de 60 segundos. No entanto, esses eventos ocasionalmente podem levar mais tempo para serem concluídos, como quando uma transação grande causa uma recuperação de execução longa. A tabela a seguir lista vários erros transitórios que os aplicativos podem receber ao se conectar ao banco de dados SQL

### <a name="list-of-transient-fault-error-codes"></a>Lista de códigos de erro transitórios

| Código do erro | Severidade | Descrição |
| ---:| ---:|:--- |
| 4060 |16 |Não é possível abrir o banco de dados "%.&#x2a;ls" solicitado pelo logon. Falha no logon. Para obter mais informações, consulte [erros 4000 a 4999](/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |O serviço encontrou um erro ao processar sua solicitação. Tente novamente. Código de erro %d.<br/><br/>Você recebe este erro quando o serviço fica inativo devido a upgrades de software ou hardware, falhas de hardware ou outros problemas de failover. O código de erro (%d) inserido na mensagem de erro 40197 fornece informações adicionais sobre o tipo de falha ou failover que ocorreu. Alguns exemplos de códigos que são inseridos na mensagem de erro 40197 são 40020, 40143, 40166 e 40540.<br/><br/>A reconexão automática conecta você a uma cópia íntegra do seu banco de dados. Seu aplicativo deve capturar o erro 40197, registrar o código de erro inserido (%d) na mensagem para solução do problema e tentar se reconectar ao Banco de Dados SQL até que os recursos estejam disponíveis e a conexão seja restabelecida. Para obter mais informações, consulte [erros transitórios](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |O serviço está ocupado. Repita a solicitação depois de 10 segundos. ID do incidente: %ls. Código: %d. Para obter mais informações, consulte: <br/>&bull;&nbsp; [Limites de recursos lógicos do SQL Server](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limites baseados em DTU para bancos de dados individuais](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites baseados em DTU para pools elásticos](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bancos de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para pools elásticos](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos do SQL instância gerenciada do Azure](../managed-instance/resource-limits.md).|
| 40613 |17 |O banco de dados “%.&#x2a;ls” no servidor “%.&#x2a;ls” não está disponível momento. Tente a conexão novamente mais tarde. Se o problema persistir, entre em contato com o atendimento ao cliente e forneça a ID de rastreamento da sessão “%.&#x2a;ls”.<br/><br/> Esse erro pode ocorrer se já houver uma conexão de administrador dedicada (DAC) existente estabelecida com o banco de dados. Para obter mais informações, consulte [erros transitórios](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Não é possível processar a solicitação. Não há recursos suficientes para processar a solicitação.<br/><br/>O serviço está ocupado. Tente fazer novamente a solicitação. Para obter mais informações, consulte: <br/>&bull;&nbsp; [Limites de recursos lógicos do SQL Server](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limites baseados em DTU para bancos de dados individuais](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites baseados em DTU para pools elásticos](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bancos de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para pools elásticos](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos do SQL instância gerenciada do Azure](../managed-instance/resource-limits.md). |
| 49919 |16 |Não é possível criar o processo ou atualizar a solicitação. Muitas operações de criação ou atualização em andamento para a assinatura "%ld".<br/><br/>O serviço está ocupado processando várias solicitações de criação ou atualização para a assinatura ou o servidor. As solicitações estão bloqueadas no momento para a otimização de recursos. Consulte [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) para obter as operações pendentes. Espere até que as solicitações pendentes de criação ou atualização sejam concluídas ou exclua uma das suas solicitações pendentes e tente a solicitação novamente mais tarde. Para obter mais informações, consulte: <br/>&bull;&nbsp; [Limites de recursos lógicos do SQL Server](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limites baseados em DTU para bancos de dados individuais](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites baseados em DTU para pools elásticos](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bancos de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para pools elásticos](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos do SQL instância gerenciada do Azure](../managed-instance/resource-limits.md). |
| 49920 |16 |Não é possível processar a solicitação. Muitas operações em andamento para assinatura "% ld".<br/><br/>O serviço está ocupado processando várias solicitações para essa assinatura. As solicitações estão bloqueadas no momento para a otimização de recursos. Consulte [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) para obter o status da operação. Espere até que as solicitações pendentes estejam concluídas ou exclua uma das suas solicitações pendentes e tente a solicitação novamente mais tarde. Para obter mais informações, consulte: <br/>&bull;&nbsp; [Limites de recursos lógicos do SQL Server](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limites baseados em DTU para bancos de dados individuais](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites baseados em DTU para pools elásticos](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bancos de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para pools elásticos](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos do SQL instância gerenciada do Azure](../managed-instance/resource-limits.md). |
| 4221 |16 |Falha de logon secundário de leitura devido a longa espera em 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING'. A réplica não está disponível para logon porque as versões de linha estão ausentes para as transações que estão em curso quando a réplica foi reciclada. O problema pode ser resolvido por reverter ou confirmar as transações ativas na réplica primária. Ocorrências dessa condição podem ser minimizadas, evitando transações de tempo de gravação no primário. |

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Etapas para resolver problemas de conectividade temporários

1. Confira o [Painel de Serviços do Microsoft Azure](https://azure.microsoft.com/status) quanto a quaisquer interrupções conhecidas que tenham ocorrido durante o tempo em que o erro foi relatado pelo aplicativo.
2. Os aplicativos que se conectam a um serviço de nuvem, como o banco de dados SQL do Azure, devem esperar eventos de reconfiguração periódicos e implementar a lógica de repetição para tratar esses erros em vez de erros de aplicativo identificando para os usuários
3. Conforme um banco de dados se aproxima dos limites de recursos, pode parecer que há um problema de conectividade temporário. Confira [Limites de recursos](resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached).
4. Se problemas de conectividade continuarem, se a duração pela qual o aplicativo encontra o erro exceder 60 segundos ou se você vir várias ocorrências do erro em um determinado dia, envie uma solicitação de suporte do Azure selecionando **Obter Suporte** no site de [Suporte do Azure](https://azure.microsoft.com/support/options) .

#### <a name="implementing-retry-logic"></a>Implementando a lógica de repetição

É altamente recomendável que seu programa cliente tem a lógica de repetição para que ele pôde restabelecer uma conexão após conceder, a hora de falhas transitórias para corrigir a mesmo.  É recomendável que você aguarde 5 segundos antes de sua primeira tentativa. Tentar novamente após um atraso menor que os de 5 segundos se sobrecarregaria no serviço de nuvem. Para cada tentativa subsequente, o atraso deverá aumentar exponencialmente, até um máximo de 60 segundos.

Para obter exemplos de código de lógica de repetição, consulte:

- [Conectar-se de forma resiliente ao SQL com ADO.NET](/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Conectar-se de forma resiliente ao SQL com PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Para obter informações adicionais sobre como tratar erros transitórios em seu aplicativo, examine [Solucionando problemas de erros de conexão transitórios para o banco de dados SQL](troubleshoot-common-connectivity-issues.md)

Uma discussão sobre o *período de bloqueio* para clientes que usam o ADO.net está disponível no [pooling de conexão (ADO.net)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-your-server"></a>Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o servidor

O problema ocorre se o aplicativo não puder se conectar ao servidor.

Para resolver esse problema, tente as etapas (na ordem apresentada) na seção [etapas para corrigir problemas comuns de conexão](#steps-to-fix-common-connection-issues) .

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>O servidor/instância não foi encontrado ou não estava acessível (erros 26, 40, 10053)

### <a name="error-26-error-locating-server-specified"></a>Erro 26: erro ao localizar o servidor especificado

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Erro 40: não foi possível abrir uma conexão com o servidor

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Erro 10053: ocorreu um erro no nível de transporte ao receber os resultados do servidor

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Esses problemas ocorrem se o aplicativo não puder se conectar ao servidor.

Para resolver esses problemas, tente as etapas (na ordem apresentada) na seção [etapas para corrigir problemas comuns de conexão](#steps-to-fix-common-connection-issues) .

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Não é possível se conectar ao servidor devido a problemas de firewall

### <a name="error-40615-cannot-connect-to--servername-"></a>Erro 40615: não é possível conectar-se ao < ServerName >

Para resolver esse problema, [defina as configurações de firewall no banco de dados SQL por meio do portal do Azure](firewall-configure.md).

### <a name="error-5-cannot-connect-to--servername-"></a>Erro 5: não é possível conectar-se ao < ServerName >

Para resolver esse problema, verifique se a porta 1433 está aberta para conexões de saída em todos os firewalls entre o cliente e a Internet.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Não é possível fazer logon no servidor (erros 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Falha de logon do usuário ' < nome de usuário > '

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Para resolver esse problema, entre em contato com o administrador de serviços para fornecer um nome de usuário e uma senha válidos.

Normalmente, o administrador de serviços pode usar as seguintes etapas para adicionar as credenciais de logon:

1. Faça logon no servidor usando SQL Server Management Studio (SSMS).
2. Execute a seguinte consulta SQL no banco de dados mestre para verificar se o nome de logon está desabilitado:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins;
   ```

3. Se o nome correspondente estiver desabilitado, habilite-o usando a instrução a seguir:

   ```sql
   ALTER LOGIN <User name> ENABLE;
   ```

4. Se o nome de usuário de logon do SQL não existir, edite e execute a seguinte consulta SQL para criar um novo logon do SQL:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = '<password, sysname, Change_Password>';
   GO
   ```

5. No Pesquisador de objetos do SSMS, expanda **bancos de dados**.
6. Selecione o banco de dados ao qual você deseja conceder a permissão de usuário.
7. Clique com o botão direito do mouse em **segurança** e selecione **novo**, **usuário**.
8. No script gerado com espaços reservados, edite e execute a seguinte consulta SQL:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>;
   GO

   -- Add user to the database owner role
   EXEC sp_addrolemember N'db_owner', N'<user_name, sysname, user_name>';
   GO
   ```

   Você também pode usar o `sp_addrolemember` para mapear usuários específicos para funções de banco de dados específicas.

   > [!NOTE]
   > No banco de dados SQL do Azure, considere a sintaxe [ALTER role](/sql/t-sql/statements/alter-role-transact-sql) mais recente para gerenciar a associação de função de banco de dados.  

Para obter mais informações, consulte [Gerenciando bancos de dados e logons no Azure SQL Database](./logins-create-manage.md).

## <a name="connection-timeout-expired-errors"></a>Erros de tempo limite de conexão expirados

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): tempo limite de conexão expirado

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): tempo limite expirado

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. Data. entidade. Core. EntityException: o provedor subjacente falhou ao abrir

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Não é possível se conectar ao nome do servidor < >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Essas exceções podem ocorrer devido a problemas de conexão ou de consulta. Para confirmar que esse erro é causado por problemas de conectividade, consulte [confirmar se um erro é causado por um problema de conectividade](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

Os tempos limite de conexão ocorrem porque o aplicativo não pode se conectar ao servidor. Para resolver esse problema, tente as etapas (na ordem apresentada) na seção [etapas para corrigir problemas comuns de conexão](#steps-to-fix-common-connection-issues) .

## <a name="resource-governance-errors"></a>Erros de governança de recursos

### <a name="error-10928-resource-id-d"></a>Erro 10928: ID do recurso:% d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Para contornar esse problema, tente um dos seguintes métodos:

- Verifique se há consultas de execução longa.

  > [!NOTE]
  > Essa é uma abordagem insuficiente que pode não resolver o problema. Para obter informações mais completas sobre como solucionar problemas de consultas de execução longa ou de bloqueio, consulte [entender e resolver problemas de bloqueio do banco de dados SQL do Azure](understand-resolve-blocking.md).

1. Execute a seguinte consulta SQL para verificar a exibição de [Sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para ver as solicitações de bloqueio:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

1. Determine o **buffer de entrada** para o bloqueador de cabeçalho usando a função de gerenciamento dinâmico [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) e a session_id da consulta incorreta, por exemplo:

   ```sql 
   SELECT * FROM sys.dm_exec_input_buffer (100,0);
   ```

1. Ajuste a consulta do bloqueador de cabeçalho.

Se o banco de dados atingir consistentemente seu limite, apesar de lidar com o bloqueio e consultas de longa execução, considere atualizar para uma edição com mais [edições](https://azure.microsoft.com/pricing/details/sql-database/)de recursos.

Para obter mais informações sobre limites de banco de dados, consulte  [limites de recursos do banco de dados SQL para servidores](./resource-limits-logical-server.md).

### <a name="error-10929-resource-id-1"></a>Erro 10929: ID do recurso: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Erro 40501: o serviço está ocupado no momento

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Esse é um erro de limitação do mecanismo, uma indicação de que os limites de recursos estão sendo excedidos.

Para obter mais informações sobre limites de recursos, consulte [limites de recursos lógicos do SQL Server](./resource-limits-logical-server.md).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Erro 40544: o banco de dados atingiu sua cota de tamanho

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Esse erro ocorre quando o banco de dados atingiu sua cota de tamanho.

As etapas a seguir podem ajudá-lo a solucionar o problema ou fornecer opções adicionais:

1. Verifique o tamanho atual do banco de dados usando o painel no portal do Azure.

   > [!NOTE]
   > Para identificar quais tabelas estão consumindo mais espaço e, portanto, candidatos potenciais para limpeza, execute a seguinte consulta SQL:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC;
   ```

2. Se o tamanho atual não exceder o tamanho máximo com suporte para sua edição, você poderá usar ALTER DATABASE para aumentar a configuração MAXSIZE.
3. Se o banco de dados já estiver além do tamanho máximo com suporte para sua edição, tente uma ou mais das seguintes etapas:

   - Executar atividades normais de limpeza do banco de dados. Por exemplo, limpe os dados indesejados usando truncar/excluir ou mover dados para fora usando SQL Server Integration Services (SSIS) ou o utilitário bcp (programa de cópia em massa).
   - Particione ou exclua dados, descarte índices ou consulte a documentação para conhecer as possíveis resoluções.
   - Para o dimensionamento de banco de dados, consulte [dimensionar recursos de banco de dados individual](./single-database-scale.md) e [dimensionar recursos de pool elástico](./elastic-pool-scale.md).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Erro 40549: a sessão foi encerrada porque você tem uma transação de execução longa

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Se você encontrar esse erro repetidamente, tente resolver o problema seguindo estas etapas:

1. Verifique a exibição de sys.dm_exec_requests para ver as sessões abertas que têm um valor alto para a coluna total_elapsed_time. Execute esta verificação executando o seguinte script SQL:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

2. Determine o **buffer de entrada** para o bloqueador de cabeçalho usando a função de gerenciamento dinâmico [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) e a session_id da consulta incorreta, por exemplo:

   ```sql 
   SELECT * FROM sys.dm_exec_input_buffer (100,0);
   ```

3. Ajuste a consulta.

    > [!Note]
    > Para obter mais informações sobre como solucionar problemas de bloqueio no banco de dados SQL do Azure, consulte [entender e resolver problemas de bloqueio do banco de dados SQL do Azure](understand-resolve-blocking.md).

Considere também o envio em lote de suas consultas. Para obter informações sobre o envio em lote, consulte [como usar o envio em lote para melhorar o desempenho do aplicativo do banco de dados SQL](../performance-improve-use-batching.md).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Erro 40551: a sessão foi encerrada devido ao uso excessivo de TEMPDB

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Para encontrar uma solução alternativa para esse problema, siga essas etapas:

1. Altere as consultas para reduzir o uso de espaço de tabela temporário.
2. Descartar objetos temporários depois que eles não forem mais necessários.
3. Truncar tabelas ou remover tabelas não usadas.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Erro 40552: a sessão foi encerrada devido ao uso excessivo de espaço do log de transações

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Para resolver esse problema, tente métodos a seguir:

- O problema pode ocorrer devido a operações de inserção, atualização ou exclusão.
Tente reduzir o número de linhas que são operadas imediatamente implementando em lote ou dividindo em várias transações menores.
- O problema pode ocorrer devido a operações de recompilação de índice. Para solucionar esse problema, verifique se o número de linhas afetadas na tabela * (tamanho médio do campo atualizado em bytes + 80) < 2 gigabytes (GB).

  > [!NOTE]
  > Para uma recompilação de índice, o tamanho médio do campo que é atualizado deve ser substituído pelo tamanho médio do índice.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Erro 40553: a sessão foi encerrada devido ao uso excessivo de memória

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Para contornar esse problema, tente otimizar a consulta.

Para obter um procedimento detalhado de solução de problemas, consulte a [minha consulta está funcionando bem na nuvem?](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabela de mensagens de erro adicionais de governança de recursos

| Código do erro | Severidade | Descrição |
| ---:| ---:|:--- |
| 10928 |20 |ID do recurso: %d. O limite de %s para o banco de dados é %d e foi atingido. Para saber mais, confira [Limites de recursos do Banco de Dados SQL para bancos de dados individuais em pool](resource-limits-logical-server.md).<br/><br/>A ID do recurso indica qual dos recursos atingiu o limite. Para threads de trabalho, a ID do recurso é igual a 1. Para sessões, a ID do recurso é igual a 2.<br/><br/>Para obter mais informações sobre esse erro e como resolvê-lo, consulte: <br/>&bull;&nbsp; [Limites de recursos lógicos do SQL Server](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limites baseados em DTU para bancos de dados individuais](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites baseados em DTU para pools elásticos](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bancos de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para pools elásticos](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos do SQL instância gerenciada do Azure](../managed-instance/resource-limits.md). |
| 10929 |20 |ID do recurso: %d. A garantia mínima de %s é %d, o limite máximo é %d e o uso atual do banco de dados é %d. No entanto, o servidor está muito ocupado para dar suporte a solicitações maiores que %d para este banco de dados. A ID do recurso indica qual dos recursos atingiu o limite. Para threads de trabalho, a ID do recurso é igual a 1. Para sessões, a ID do recurso é igual a 2. Para obter mais informações, consulte: <br/>&bull;&nbsp; [Limites de recursos lógicos do SQL Server](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limites baseados em DTU para bancos de dados individuais](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites baseados em DTU para pools elásticos](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bancos de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para pools elásticos](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos do SQL instância gerenciada do Azure](../managed-instance/resource-limits.md). <br/>Caso contrário, tente novamente mais tarde. |
| 40544 |20 |O banco de dados atingiu sua cota de tamanho. Particione ou exclua dados, descarte índices ou consulte a documentação para conhecer as possíveis resoluções. Para o dimensionamento de banco de dados, consulte [dimensionar recursos de banco de dados individual](single-database-scale.md) e [dimensionar recursos de pool elástico](elastic-pool-scale.md).|
| 40549 |16 |A sessão foi terminada porque você tem uma transação de longa execução. Tente encurtar a transação. Para obter informações sobre o envio em lote, consulte [como usar o envio em lote para melhorar o desempenho do aplicativo do banco de dados SQL](../performance-improve-use-batching.md).|
| 40550 |16 |A sessão foi terminada porque ela adquiriu muitos bloqueios. Tente ler ou modificar menos linhas em uma única transação. Para obter informações sobre o envio em lote, consulte [como usar o envio em lote para melhorar o desempenho do aplicativo do banco de dados SQL](../performance-improve-use-batching.md).|
| 40551 |16 |A sessão foi encerrada devido ao uso excessivo de `TEMPDB` . Tente modificar a consulta para reduzir o uso de espaço de tabela temporária.<br/><br/>Se você estiver usando objetos temporários, conserve espaço no banco de dados `TEMPDB` removendo objetos temporários depois que eles não forem mais necessários na sessão. Para obter mais informações sobre o uso de tempdb no banco de dados SQL, consulte [banco de dados tempdb no banco de dados SQL](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |A sessão foi terminada devido a uso excessivo de espaço de log de transação. Tente modificar menos linhas em uma única transação. Para obter informações sobre o envio em lote, consulte [como usar o envio em lote para melhorar o desempenho do aplicativo do banco de dados SQL](../performance-improve-use-batching.md).<br/><br/>Se você executar inserções em massa usando o utilitário `bcp.exe` ou a classe `System.Data.SqlClient.SqlBulkCopy`, tente usar as opções `-b batchsize` ou `BatchSize` para limitar o número de linhas copiadas para o servidor em cada transação. Se você estiver recriando um índice com a instrução `ALTER INDEX`, tente usar a opção `REBUILD WITH ONLINE = ON`. Para obter informações sobre tamanhos de log de transações para o modelo de compra vCore, consulte: <br/>&bull;&nbsp; [limites baseados em vCore para bancos de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para pools elásticos](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos do SQL instância gerenciada do Azure](../managed-instance/resource-limits.md).|
| 40553 |16 |A sessão foi terminada devido a uso excessivo de memória. Tente modificar a consulta para processar menos linhas.<br/><br/>A redução do número de operações `ORDER BY` e `GROUP BY` no código Transact-SQL reduz os requisitos de memória da consulta. Para o dimensionamento de banco de dados, consulte [dimensionar recursos de banco de dados individual](single-database-scale.md) e [dimensionar recursos de pool elástico](elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Erros de pool elástico

Os seguintes erros estão relacionados à criação e ao uso de pools elásticos:

| Código do erro | Severidade | Descrição | Ação Corretiva |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |O pool elástico atingiu seu limite de armazenamento. O uso do armazenamento do pool elástico não pode exceder (%d) MB. Tentando gravar dados em um banco de dados quando o limite de armazenamento do pool elástico foi atingido. Para obter informações sobre limites de recursos, consulte: <br/>&bull;&nbsp; [Limites baseados em DTU para pools elásticos](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para pools elásticos](resource-limits-vcore-elastic-pools.md). <br/> |Considere a possibilidade de aumentar as DTUs e/ou adicionar armazenamento ao pool elástico, se possível, para aumentar seu limite de armazenamento, reduzir o armazenamento usado por bancos de dados individuais dentro do pool elástico ou remover bancos de dados do pool elástico. Para o dimensionamento do pool elástico, consulte [dimensionar recursos do pool elástico](elastic-pool-scale.md). Para obter mais informações sobre como remover o espaço não utilizado dos bancos de dados, consulte [gerenciar o espaço de arquivo para bancos de dados no Azure SQL Database](file-space-manage.md).|
| 10929 | 16 |A garantia mínima de %s é %d, o limite máximo é %d e o uso atual do banco de dados é %d. No entanto, o servidor está muito ocupado para dar suporte a solicitações maiores que %d para este banco de dados. Para obter informações sobre limites de recursos, consulte: <br/>&bull;&nbsp; [Limites baseados em DTU para pools elásticos](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para pools elásticos](resource-limits-vcore-elastic-pools.md). <br/> Caso contrário, tente novamente mais tarde. DTU / vCore min por banco de dados; DTU / vCore max por banco de dados. O número total de trabalhos simultâneos (solicitações) em todos os bancos de dados no pool elástico tentou exceder o limite do pool. |Considere a possibilidade de aumentar as DTUs ou vCores do pool elástico, se possível, para aumentar o limite de funções de trabalho ou remover bancos de dados do pool elástico. |
| 40844 | 16 |O banco de dados '%ls' no servidor '%ls' é um banco de dados edição '%ls' em um pool elástico e não pode ter um relacionamento de cópia contínuo.  |N/D |
| 40857 | 16 |Pool elástico não encontrado para o servidor: '%ls', nome do pool elástico: '%ls'. O pool elástico especificado não existe no servidor especificado. | Forneça um nome de pool elástico válido. |
| 40858 | 16 |O pool elástico '%ls' já existe no servidor: '%ls'. O pool elástico especificado já existe no servidor especificado. | Forneça um novo nome de pool elástico. |
| 40859 | 16 |O pool elástico não dá suporte à camada de serviço '%ls'. A camada de serviço especificada não dá suporte ao provisionamento de pool elástico. |Forneça a edição correta ou deixe a camada de serviço em branco para o padrão. |
| 40860 | 16 |A combinação de pool elástico '%ls' e objetivo de serviço '%ls' é inválida. O pool elástico e a camada de serviço poderão ser especificados juntos somente se o tipo de recurso for especificado como “ElasticPool”. |Especifique a combinação correta de pool elástico e camada de serviço. |
| 40861 | 16 |A edição do banco de dados '%.*ls' não pode ser diferente da camada de serviço do pool elástico, que é '%.* ls'. A edição do banco de dados é diferente da camada de serviço do pool elástico. |Não especifique uma edição de banco de dados diferente da camada de serviço do pool elástico.  Observe que a edição do banco de dados não precisa ser especificada. |
| 40862 | 16 |O nome do pool elástico deverá ser especificado se o objetivo de serviço do pool elástico for especificado. O objetivo de serviço do pool elástico não identifica exclusivamente um pool elástico. |Especifique o nome do pool elástico se você estiver usando o objetivo de serviço do pool elástico. |
| 40864 | 16 |A quantidade mínima de DTUs para o pool elástico deve ser de (%d) DTUs para a camada de serviço '%.*ls'. Tentando definir uma quantidade de DTUs para o pool elástico abaixo do limite mínimo. |Tente configurar novamente as DTUs para o pool elástico de acordo com o limite mínimo. |
| 40865 | 16 |A quantidade máxima de DTUs para o pool elástico deve ser de (%d) DTUs para a camada de serviço '%.*ls'. Tentando definir uma quantidade de DTUs para o pool elástico acima do limite máximo. |Tente configurar novamente as DTUs para o pool elástico de acordo com o limite máximo. |
| 40867 | 16 |O máximo de DTUs por banco de dados deve ser de pelo menos (%d) para a camada de serviço '%.*ls'. Tentando definir o máximo de DTUs por banco de dados abaixo do limite permitido. | Considere a possibilidade de usar a camada de serviço do pool elástico que dá suporte à configuração desejada. |
| 40868 | 16 |O máximo de DTUs por banco de dados não deve exceder (%d) para a camada de serviço '%.*ls'. Tentando definir o máximo de DTUs por banco de dados acima do limite permitido. | Considere a possibilidade de usar a camada de serviço do pool elástico que dá suporte à configuração desejada. |
| 40870 | 16 |O mínimo de DTUs por banco de dados deve ser de, no máximo, (%d) para a camada de serviço '%.*ls'. Tentando definir o mínimo de DTUs por banco de dados acima do limite permitido. | Considere a possibilidade de usar a camada de serviço do pool elástico que dá suporte à configuração desejada. |
| 40873 | 16 |O número de bancos de dados (%d) e o mínimo de DTUs por banco de dados (%d) não podem exceder as DTUs do pool elástico (%d). Tentando especificar uma quantidade mínima de DTUs para bancos de dados no pool elástico que excede as DTUs do pool elástico. | Considere a possibilidade de aumentar as DTUs do pool elástico, diminuir o mínimo de DTUs por banco de dados ou diminuir o número de bancos de dados no pool elástico. |
| 40877 | 16 |Não é possível excluir um pool elástico, a menos que ele não contenha bancos de dados. O pool elástico contém um ou mais bancos de dados e, portanto, não pode ser excluído. |Remova os bancos de dados do pool elástico para excluí-lo. |
| 40881 | 16 |O pool elástico '%.*ls' atingiu o limite de banco de dados.  O limite de bancos de dados para o pool elástico não pode exceder (%d) para um pool elástico com (%d) DTUs. Tentando criar ou adicionar um banco de dados ao pool elástico quando o limite de bancos de dados do pool elástico foi atingido. | Considere a possibilidade de aumentar as DTUs do pool elástico, se possível, para aumentar o limite de bancos de dados ou remover bancos de dados do pool elástico. |
| 40889 | 16 |O limite de DTUs ou de armazenamento para o pool elástico '%.*ls' não pode ser reduzido, pois não haveria espaço de armazenamento suficiente para seus bancos de dados. Tentando reduzir o limite de armazenamento do pool elástico abaixo do seu uso de armazenamento. | Considere a possibilidade de reduzir o uso do armazenamento de bancos de dados individuais no pool elástico ou remover bancos de dados do pool para reduzir seu limite de DTUs ou de armazenamento. |
| 40891 | 16 |O mínimo de DTUs por banco de dados (%d) não pode ser superior ao máximo de DTUs por banco de dados (%d). Tentando definir o mínimo de DTUs por banco de dados acima do máximo de DTUs por banco de dados. |Verifique se o mínimo de DTUs por bancos de dados não excede o máximo de DTUs por banco de dados. |
| TBD | 16 |O tamanho do armazenamento de um banco de dados individual em um pool elástico não pode exceder o tamanho máximo permitido pelo pool elástico da camada de serviço “%.*ls”. O tamanho máximo do banco de dados excede o tamanho máximo permitido pela camada de serviço do pool elástico. |Defina o tamanho máximo do banco de dados dentro dos limites do tamanho máximo permitido pela camada de serviço do pool elástico. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Não é possível abrir o banco de dados "mestre" solicitado pelo logon. Falha no logon

Esse problema ocorre porque a conta não tem permissão para acessar o banco de dados mestre. Mas, por padrão, o SQL Server Management Studio (SSMS) tenta se conectar ao banco de dados mestre.

Para resolver esse problema, siga estas etapas:

1. Na tela de logon do SSMS, selecione **Opções** e, em seguida, selecione **Propriedades da conexão**.
2. No campo **conectar ao banco de dados** , insira o nome do banco de dados padrão do usuário como o banco de dados de logon padrão e, em seguida, selecione **conectar**.

   ![Propriedades da conexão](./media/troubleshoot-common-errors-issues/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Confirmar se um erro é causado por um problema de conectividade

Para confirmar se um erro é causado por um problema de conectividade, examine o rastreamento de pilha para quadros que mostram chamadas para abrir uma conexão como as seguintes (Observe a referência à classe **SqlConnection** ):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Quando a exceção for disparada por problemas de consulta, você notará uma pilha de chamadas semelhante à seguinte (Observe a referência à classe **SqlCommand** ). Nessa situação, [ajuste suas consultas](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Para obter diretrizes adicionais sobre o desempenho de ajuste fino, consulte os seguintes recursos:

- [Como manter índices e estatísticas do SQL Azure](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
- [Desempenho de consulta de ajuste manual no Banco de Dados SQL do Azure](./performance-guidance.md)
- [Monitorando o desempenho do banco de dados SQL do Azure usando exibições de gerenciamento dinâmico](./monitoring-with-dmvs.md)
- [Operar o Repositório de Consultas no banco de dados SQL do Azure](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)

## <a name="steps-to-fix-common-connection-issues"></a>Etapas para corrigir problemas comuns de conexão

1. Verifique se o TCP/IP está habilitado como um protocolo de cliente no servidor de aplicativos. Para obter mais informações, consulte [Configurar protocolos de cliente](/sql/database-engine/configure-windows/configure-client-protocols). Em servidores de aplicativos em que você não tem as ferramentas do SQL instaladas, verifique se o TCP/IP está habilitado executando **cliconfg.exe** (utilitário de rede de cliente SQL Server).
2. Verifique a cadeia de conexão do aplicativo para certificar-se de que ela está configurada corretamente. Por exemplo, verifique se a cadeia de conexão especifica a porta correta (1433) e o nome do servidor totalmente qualificado.
Consulte [obter informações de conexão](./connect-query-ssms.md#get-server-connection-information).
3. Tente aumentar o valor de tempo limite da conexão. É recomendável usar um tempo limite de conexão de, pelo menos, 30 segundos.
4. Teste a conectividade entre o servidor de aplicativos e o banco de dados SQL do Azure usando o [SQL Server Management Studio (SSMS)](./connect-query-ssms.md), um arquivo UDL, ping ou telnet. Para obter mais informações, consulte [Solucionando](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) problemas de conectividade e [diagnóstico para problemas de conectividade](./troubleshoot-common-connectivity-issues.md#diagnostics).

   > [!NOTE]
   > Como uma etapa de solução de problemas, você também pode testar a conectividade em um computador cliente diferente.

5. Como prática recomendada, verifique se a lógica de repetição está em vigor. Para obter mais informações sobre a lógica de repetição, consulte [solucionar falhas transitórias e erros de conexão no banco de dados SQL](./troubleshoot-common-connectivity-issues.md).

Se essas etapas não resolverem o problema, tente coletar mais dados e entre em contato com o suporte. Se seu aplicativo for um serviço de nuvem, habilite o registro em log. Esta etapa retorna um carimbo de data/hora UTC da falha. Além disso, o banco de dados SQL retorna a ID de rastreamento. Os serviços de atendimento [ao cliente da Microsoft](https://azure.microsoft.com/support/options/) podem usar essas informações.

Para obter mais informações sobre como habilitar o registro em log, consulte [habilitar o log de diagnóstico para aplicativos no serviço Azure app](../../app-service/troubleshoot-diagnostic-logs.md).

## <a name="next-steps"></a>Próximas etapas

- [Arquitetura de conectividade do Banco de Dados SQL do Azure](./connectivity-architecture.md)
- [Controles de acesso à rede do banco de dados SQL do Azure e do Azure Synapse Analytics](./network-access-controls-overview.md)