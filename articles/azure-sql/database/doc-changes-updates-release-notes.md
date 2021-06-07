---
title: Quais são as novidades?
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba mais sobre os novos recursos e aprimoramentos de documentação do banco de dados SQL do Azure & SQL Instância Gerenciada.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: sstein
ms.openlocfilehash: 9827a40b2ebc91c17ad7b5457259b8d82565edee
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640088"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>O que há de novo no banco de dados SQL do Azure & SQL Instância Gerenciada?
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo lista os recursos do banco de dados SQL do Azure e do Azure SQL Instância Gerenciada que estão atualmente em visualização pública. Para atualizações e aprimoramentos do banco de dados SQL e do SQL Instância Gerenciada, consulte [SQL database & sql instância gerenciada service updates](https://azure.microsoft.com/updates/?product=sql-database). Para encontrar atualizações e melhorias para outros serviços do Azure, confira [Atualizações de serviço](https://azure.microsoft.com/updates).

## <a name="whats-new"></a>Novidades

A documentação do banco de dados SQL do Azure e do Azure SQL Instância Gerenciada foi dividida em seções separadas. Também atualizamos como nos referimos a uma instância gerenciada da *instância gerenciada do banco de dados SQL do Azure* para o *Azure SQL instância gerenciada*.

Fizemos isso porque alguns recursos e funcionalidades variam muito entre um único banco de dados e uma instância gerenciada, e ele se tornou cada vez mais desafiador de explicar nuances complexas entre o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada em artigos compartilhados individuais.

Esse esclarecimento entre os diferentes produtos SQL do Azure deve simplificar e simplificar o processo de trabalho com o mecanismo de banco de dados SQL Server no Azure, seja um único banco de dados gerenciado no banco de dados SQL do Azure, uma instância gerenciada totalmente acompanhada que hospeda vários bancos de dados no Azure SQL Instância Gerenciada ou o produto SQL Server familiar no local hospedado em uma máquina virtual no Azure.

Considere que se trata de um trabalho em andamento e nem todos os artigos foram atualizados ainda. Por exemplo, a documentação para instruções Transact-SQL (T-SQL), procedimentos armazenados e muitos recursos compartilhados entre o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada ainda não estão completos, então agradecemos sua paciência enquanto continuamos a esclarecer o conteúdo. 

Esta tabela fornece uma comparação rápida para a alteração na terminologia: 


|**Termo novo**  | **Termo anterior**  |**Explicação** |
|---------|---------|---------|
|**Instância Gerenciada do SQL do Azure** | *Instância gerenciada* do banco de dados SQL do Azure| O Azure SQL Instância Gerenciada é seu próprio produto na família SQL do Azure, em vez de apenas uma opção de implantação no banco de dados SQL do Azure. | 
|**Banco de Dados SQL do Azure**|Banco de dados *individual* do banco de dados SQL do Azure| A menos que seja especificado explicitamente de outra forma, o nome do produto banco de dados SQL do Azure inclui bancos e dados individuais implantados em um pool elástico. |
|**Banco de Dados SQL do Azure**|*Pool elástico* do banco de dados SQL do Azure| A menos que seja especificado explicitamente de outra forma, o nome do produto banco de dados SQL do Azure inclui bancos e dados individuais implantados em um pool elástico.  |
|**Banco de Dados SQL do Azure** |Banco de Dados SQL do Azure | Embora o termo permaneça o mesmo, ele só se aplica a implantações de banco de dados único e de pool elástico e não inclui a instância gerenciada. |
| **SQL do Azure**| N/D | Isso se refere à família de SQL Server de produtos do mecanismo de banco de dados que estão disponíveis no Azure: banco de dados SQL do Azure, SQL Instância Gerenciada do Azure e SQL Server em VMs do Azure. | 

## <a name="features-in-public-preview"></a>Inclui versão prévia pública

### <a name="azure-sql-database"></a>[Banco de Dados SQL do Azure](#tab/single-database)

| Recurso | Detalhes |
| ---| --- |
| Trabalhos de banco de dados elástico (visualização) | Para obter mais informações, consulte [Criar, configurar e gerenciar trabalhos elásticos](elastic-jobs-overview.md). |
| Consultas elásticas | Para obter mais informações, consulte [Visão geral de consulta elástica](elastic-query-overview.md). |
| Transações elásticas | [Transações distribuídas entre bancos de dados na nuvem](elastic-transactions-overview.md). |
| Editor de consultas no Portal do Azure |Para obter mais informações, confira como [Usar o editor de consultas SQL do portal do Azure para se conectar e consultar dados](connect-query-portal.md).|
|Análise de SQL|Para obter mais informações, consulte o artigo [Análise de SQL do Azure](../../azure-monitor/insights/azure-sql.md).|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Instância Gerenciada do SQL do Azure](#tab/managed-instance)

| Recurso | Detalhes |
| ---| --- |
| [Transações distribuídas](/azure/azure-sql/database/elastic-transactions-overview) | Transações distribuídas entre instâncias gerenciadas. |
| [Pools da instância](/azure/sql-database/sql-database-instance-pools) | Uma maneira conveniente e econômica de migrar instâncias do SQL menores para a nuvem. |
| [Entidades de segurança do servidor do Azure AD (logons) de nível de entidade](/sql/t-sql/statements/create-login-transact-sql) | Crie logons em nível de instância usando uma instrução [Create login from external Provider](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) . |
| [Replicação transacional](../managed-instance/replication-transactional-overview.md) | Replique as alterações de suas tabelas em outros bancos de dados do SQL Instância Gerenciada, do SQL Database ou do SQL Server. Ou atualize suas tabelas quando algumas linhas forem alteradas em outras instâncias do SQL Instância Gerenciada ou SQL Server. Para obter informações, consulte [Configurar a replicação no Azure SQL instância gerenciada](../managed-instance/replication-between-two-instances-configure-tutorial.md). |
| Detecção de ameaças |Para obter informações, consulte [Configurar a detecção de ameaças no Azure SQL instância gerenciada](../managed-instance/threat-detection-configure.md).|
| Retenção de backup de longo prazo | Para obter informações, consulte [Configurar retenção de backup de longo prazo no Azure SQL instância gerenciada](../managed-instance/long-term-backup-retention-configure.md), que está atualmente em visualização pública limitada. |

---

## <a name="new-features"></a>Novos recursos

### <a name="sql-managed-instance-h2-2019-updates"></a>Atualizações do SQL Instância Gerenciada H2 2019

- A [configuração de sub-rede auxiliada por serviço](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) é uma maneira segura e conveniente de gerenciar a configuração de sub-rede em que você controla o tráfego de dados enquanto o SQL instância gerenciada garante o fluxo ininterrupto do tráfego de gerenciamento.
- [Transparent Data Encryption (TDE) com Bring your own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) permite um cenário de BYOK (traga sua própria chave) para proteção de dados em repouso e permite que as organizações separem as tarefas de gerenciamento para chaves e dados.
- [Grupos de failover automático](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) permitem que você replique todos os bancos de dados da instância primária para uma instância secundária em outra região.
- Os [sinalizadores de rastreamento globais](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) permitem que você configure o comportamento do SQL instância gerenciada.

### <a name="sql-managed-instance-h1-2019-updates"></a>Atualizações do SQL Instância Gerenciada H1 2019

Os seguintes recursos estão habilitados no modelo de implantação do SQL Instância Gerenciada em H1 2019:
  - Suporte para assinaturas com <a href="/azure/azure-sql/managed-instance/resource-limits"> Crédito mensal do Azure para assinantes do Visual Studio </a> e maiores [limites regionais](../managed-instance/resource-limits.md#regional-resource-limitations).
  - Suporte para <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> sharepoint 2016 e sharepoint 2019 </a> e <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business central. </a>
  - Crie uma instância gerenciada com <a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">agrupamento em nível de instância</a> e um <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">fuso horário</a> de sua escolha.
  - Instâncias gerenciadas são protegidas com [firewall interno](../managed-instance/management-endpoint-verify-built-in-firewall.md).
  - Configure o SQL Instância Gerenciada para usar [pontos de extremidade públicos](../managed-instance/public-endpoint-configure.md), conexão de [substituição de proxy](connectivity-architecture.md#connection-policy) para obter melhor desempenho de rede, <a href="https://aka.ms/four-cores-sql-mi-update"> 4 VCores na geração de hardware Gen5</a> ou <a href="/azure/azure-sql/database/automated-backups-overview">configurar a retenção de backup de até 35 dias para a</a> restauração pontual. A [retenção de backup de longo prazo](long-term-retention-overview.md) (até 10 anos) está atualmente em visualização pública.  
  - As novas funcionalidades permitem que você <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">faça a restauração geográfica de seu banco de dados para outro data center usando o PowerShell</a>, [renomeie o banco de dados](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/) e [excluir o cluster virtual](../managed-instance/virtual-cluster-delete.md).
  - A nova [função de colaborador de instância](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) interna permite a conformidade de SoD (separação de imposto) com princípios de segurança e conformidade com os padrões corporativos.
  - O SQL Instância Gerenciada está disponível nas seguintes regiões do Azure governamental para GA (US Gov Texas, US Gov Arizona) e no Norte da China 2 e no Leste da China 2. Ele também está disponível nas seguintes regiões públicas: Austrália Central, Austrália Central 2, sul do Brasil, sul da França, EAU Central, Norte dos EAU, norte da África do Sul, oeste da África do Sul.

## <a name="known-issues"></a>Problemas conhecidos

|Problema  |Data descoberta  |Status  |Data resolvida  |
|---------|---------|---------|---------|
|[O procedimento sp_send_dbmail pode falhar transitóriomente quando o @query parâmetro é usado](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|Jan 2021|Tem solução alternativa||
|[As transações distribuídas podem ser executadas após a remoção de Instância Gerenciada do grupo de confiança do servidor](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|Outubro de 2020|Tem solução alternativa||
|[As transações distribuídas não podem ser executadas após Instância Gerenciada operação de dimensionamento](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|Outubro de 2020|Tem solução alternativa||
|[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) / [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql) no SQL do Azure e `BACKUP` / `RESTORE` na instrução no instância gerenciada não é possível usar o Azure ad gerenciar identidade para autenticar no armazenamento do Azure|Setembro de 2020|Tem solução alternativa||
|[A entidade de serviço não pode acessar o Azure AD e o AKV](#service-principal-cannot-access-azure-ad-and-akv)|2020 de agosto|Tem solução alternativa||
|[A restauração do backup manual sem soma de verificação pode falhar](#restoring-manual-backup-without-checksum-might-fail)|Maio de 2020|Resolvido|Junho de 2020|
|[O Agent não responde na modificação, desabilitação ou habilitação de trabalhos existentes](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Maio de 2020|Resolvido|Junho de 2020|
|[As permissões no grupo de recursos não são aplicadas ao SQL Instância Gerenciada](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|Fev 2020|Resolvido|2020 de novembro|
|[Limitação de failover manual por meio do portal para grupos de failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Tem solução alternativa||
|[As funções do SQL Agent precisam de permissões EXECUTE explícitas para logons não sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Dez 2019|Tem solução alternativa||
|[Os trabalhos do SQL Agent podem ser interrompidos pela reinicialização do processo do agente](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dez 2019|Resolvido|Mar 2020|
|[Os logons e usuários do Azure AD não têm suporte no SSDT](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|Nov 2019|Sem solução||
|[Os limites de memória OLTP na memória não são aplicados](#in-memory-oltp-memory-limits-are-not-applied)|Out 2019|Tem solução alternativa||
|[Erro incorreto retornado ao tentar remover um arquivo que não está vazio](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Out 2019|Tem solução alternativa||
|[A alteração da camada de serviço e a criação de operações de instância são bloqueadas pela restauração de banco de dados em andamento](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Set 2019|Tem solução alternativa||
|[O Resource Governor na camada de serviço comercialmente crítico talvez precise ser reconfigurado após o failover](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Set 2019|Tem solução alternativa||
|[As caixas de diálogo de Service Broker entre bancos de dados devem ser reinicializadas após a atualização da camada de serviço](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|Ago 2019|Tem solução alternativa||
|[Não há suporte para a representação de tipos de logon do Azure AD](#impersonation-of-azure-ad-login-types-is-not-supported)|Jul 2019|Sem solução||
|[@query parâmetro sem suporte no sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Abr 2019|Resolvido|Jan 2021|
|[A replicação transacional deve ser reconfigurada após o failover geográfico](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Sem solução||
|[O banco de dados temporário é usado durante a operação de restauração](#temporary-database-is-used-during-restore-operation)||Tem solução alternativa||
|[A estrutura TEMPDB e o conteúdo são recriados](#tempdb-structure-and-content-is-re-created)||Sem solução||
|[Excedendo o espaço de armazenamento com arquivos de banco de dados pequenos](#exceeding-storage-space-with-small-database-files)||Tem solução alternativa||
|[Valores de GUID mostrados em vez de nomes de banco de dados](#guid-values-shown-instead-of-database-names)||Tem solução alternativa||
|[Os logs de erros não são persistentes](#error-logs-arent-persisted)||Sem solução||
|[O escopo de transação em dois bancos de dados dentro da mesma instância não é compatível](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Tem solução alternativa|Mar 2020|
|[Os módulos CLR e os servidores vinculados às vezes não podem fazer referência ao endereço IP local](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Tem solução alternativa||
|Consistência do banco de dados não verificada usando DBCC CHECKDB após restaurar banco de dados do Armazenamento de blobs do Azure.||Resolvido|Nov 2019|
|A restauração de banco de dados pontual da camada comercialmente crítica para Uso Geral não terá sucesso se o banco de dados de origem contiver objetos OLTP na memória.||Resolvido|Out 2019|
|Recurso Database Mail com servidores de email externos (não Azure) usando conexão segura||Resolvido|Out 2019|
|Bancos de dados independentes sem suporte no SQL Instância Gerenciada||Resolvido|Ago 2019|

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>O procedimento sp_send_dbmail pode falhar transitóriomente quando o @query parâmetro é usado

O procedimento sp_send_dbmail pode falhar transitóriomente quando o `@query` parâmetro é usado. Quando esse problema ocorre, cada segunda execução do procedimento sp_send_dbmail falha com o erro `Msg 22050, Level 16, State 1` e a mensagem `Failed to initialize sqlcmd library with error number -2147467259` . Para poder ver esse erro corretamente, o procedimento deve ser chamado com o valor padrão 0 para o parâmetro `@exclude_query_output` , caso contrário, o erro não será propagado.
Esse problema é causado por um bug conhecido relacionado ao modo como sp_send_dbmail está usando a representação e o pool de conexões.
Para solucionar esse problema, empacote o código para enviar emails para uma lógica de repetição que dependa do parâmetro de saída `@mailitem_id` . Se a execução falhar, o valor do parâmetro será NULL, indicando sp_send_dbmail deve ser chamado mais uma vez para enviar um email com êxito. Aqui está um exemplo dessa lógica de repetição.
```sql
CREATE PROCEDURE send_dbmail_with_retry AS
BEGIN
    DECLARE @miid INT
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
        @mailitem_id = @miid OUTPUT

    -- If sp_send_dbmail returned NULL @mailidem_id then retry sending email.
    --
    IF (@miid is NULL)
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
END
```

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>As transações distribuídas podem ser executadas após a remoção de Instância Gerenciada do grupo de confiança do servidor

Os [grupos de confiança do servidor](../managed-instance/server-trust-group-overview.md) são usados para estabelecer a confiança entre as instâncias gerenciadas que são pré-requisitos para executar [transações distribuídas](./elastic-transactions-overview.md). Depois de remover Instância Gerenciada do grupo de confiança do servidor ou excluir o grupo, você ainda poderá executar transações distribuídas. Há uma solução alternativa que você pode aplicar para ter certeza de que as transações distribuídas estão desabilitadas e que é o [failover manual iniciado pelo usuário](../managed-instance/user-initiated-failover.md) no instância gerenciada.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>As transações distribuídas não podem ser executadas após Instância Gerenciada operação de dimensionamento

Instância Gerenciada operações de dimensionamento que incluem a alteração da camada de serviço ou o número de vCores redefinirão as configurações de grupo de confiança do servidor no back-end e desabilitarão [as transações distribuídas](./elastic-transactions-overview.md) Como alternativa, exclua e crie um novo [grupo de confiança do servidor](../managed-instance/server-trust-group-overview.md) no portal do Azure.

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>Instruções BULK INSERT e BACKUP/RESTOre não podem usar identidade gerenciada para acessar o armazenamento do Azure

As instruções BULK INSERT, BACKUP e RESTOre e a função OPENROWSET não podem ser usadas `DATABASE SCOPED CREDENTIAL` com identidade gerenciada para autenticar no armazenamento do Azure. Como alternativa, mude para autenticação de assinatura de acesso compartilhado. O exemplo a seguir não funcionará no Azure SQL (banco de dados e Instância Gerenciada):

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**Solução alternativa**: use [a assinatura de acesso compartilhado para autenticar no armazenamento](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage).

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>A entidade de serviço não pode acessar o Azure AD e o AKV

Em algumas circunstâncias, pode haver um problema com a entidade de serviço usada para acessar os serviços do Azure AD e do Azure Key Vault (AKV). Como resultado, esse problema afeta o uso da autenticação do Azure AD e da TDE (criptografia de banco de dados transparente) com o SQL Instância Gerenciada. Isso pode ser experiente como um problema de conectividade intermitente ou não ser capaz de executar instruções como criar logon/usuário do provedor externo ou executar como logon/usuário. A configuração do TDE com a chave gerenciada pelo cliente em um novo Azure SQL Instância Gerenciada também pode não funcionar em algumas circunstâncias.

**Solução alternativa**: para evitar que esse problema ocorra em seu instância gerenciada do SQL antes de executar qualquer comando de atualização ou, caso você já tenha enfrentado esse problema após os comandos de atualização, acesse portal do Azure, acesse o SQL instância gerenciada [Active Directory folha de administração](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal). Verifique se você pode ver a mensagem de erro "Instância Gerenciada precisa de uma entidade de serviço para acessar Azure Active Directory. Clique aqui para criar uma entidade de serviço ". Caso você tenha encontrado essa mensagem de erro, clique nela e siga as instruções passo a passo fornecidas até que esse erro seja resolvido.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>A restauração do backup manual sem CHECKSUM pode falhar

Em determinadas circunstâncias, o backup manual de bancos de dados que foram feitos em uma instância gerenciada sem soma de verificação pode falhar ao ser restaurado. Nesses casos, tente restaurar o backup novamente até que seja bem-sucedido.

**Solução alternativa**: Faça backups manuais de bancos de dados em instâncias gerenciadas com a soma de verificação habilitada.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>O Agent não responde na modificação, desabilitação ou habilitação de trabalhos existentes

Em determinadas circunstâncias, modificar, desabilitar ou habilitar um trabalho existente pode fazer com que o agente deixe de responder. O problema é mitigado automaticamente após a detecção, resultando em uma reinicialização do processo do agente.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>As permissões no grupo de recursos não são aplicadas ao SQL Instância Gerenciada

Quando a função do Azure do colaborador do SQL Instância Gerenciada é aplicada a um grupo de recursos (RG), ela não é aplicada ao SQL Instância Gerenciada e não tem nenhum efeito.

**Solução alternativa**: Configure uma função de colaborador do SQL instância gerenciada para usuários no nível da assinatura.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Limitação de failover manual por meio do portal para grupos de failover

Se um grupo de failover se estender entre instâncias em diferentes assinaturas ou grupos de recursos do Azure, o failover manual não poderá ser iniciado a partir da instância primária no grupo de failover.

**Solução alternativa**: Inicie o failover por meio do portal da instância geográfica-secundária.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>As funções do SQL Agent precisam de permissões EXECUTE explícitas para logons não sysadmin

Se os logons não-sysadmin forem adicionados a qualquer [função de banco de dados fixa do SQL Agent](/sql/ssms/agent/sql-server-agent-fixed-database-roles), existe um problema no qual as permissões de execução explícitas precisam ser concedidas aos procedimentos armazenados mestres para que esses logons funcionem. Se este erro for encontrado, a mensagem de erro "A permissão EXECUTE foi negada no objeto <object_name> (Microsoft SQL Server, Erro: 229)" será exibida.

**Solução alternativa**: depois de adicionar logons a uma função de banco de dados fixa do SQL Agent (SQLAgentUserRole, SQLAgentReaderRole ou SQLAgentOperatorRole), para cada um dos logons adicionados a essas funções, execute o script T-SQL abaixo para conceder explicitamente permissões de execução aos procedimentos armazenados listados.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Os trabalhos do SQL Agent podem ser interrompidos pela reinicialização do processo do agente

**(Resolvido em março de 2020)** O SQL Agent cria uma nova sessão cada vez que um trabalho é iniciado, aumentando gradualmente o consumo de memória. Para evitar atingir o limite de memória interna, que bloquearia a execução de trabalhos agendados, o processo do agente será reiniciado quando seu consumo de memória atingir o limite. Isso pode resultar na interrupção da execução de trabalhos em execução no momento da reinicialização.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Os limites de memória OLTP na memória não são aplicados

A camada de serviço Comercialmente Crítico não aplicará corretamente [os limites máximos de memória para objetos com otimização de memória](../managed-instance/resource-limits.md#in-memory-oltp-available-space) em alguns casos. O SQL Instância Gerenciada pode permitir que a carga de trabalho use mais memória para operações OLTP na memória, o que pode afetar a disponibilidade e a estabilidade da instância. As consultas OLTP na memória que estão atingindo os limites podem não falhar imediatamente. Esse problema será corrigido em breve. As consultas que usam mais memória OLTP na memória falharão mais cedo se atingirem os [limites](../managed-instance/resource-limits.md#in-memory-oltp-available-space).

**Solução alternativa**: [monitore o uso de armazenamento OLTP na memória](../in-memory-oltp-monitor-space.md) usando [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) para garantir que a carga de trabalho não esteja usando mais do que a memória disponível. Aumente os limites de memória que dependem do número de vCores ou otimize sua carga de trabalho para usar menos memória.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Erro incorreto retornado ao tentar remover um arquivo que não está vazio

SQL Server e SQL Instância Gerenciada [não permitem que um usuário remova um arquivo que não esteja vazio](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Se você tentar remover um arquivo de dados não vazio usando uma `ALTER DATABASE REMOVE FILE` instrução, o erro `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` não será retornado imediatamente. O SQL Instância Gerenciada continuará tentando descartar o arquivo e a operação falhará após 30 minutos `Internal server error` .

**Solução alternativa**: Remova o conteúdo do arquivo usando o `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` comando. Se esse for o único arquivo no grupo de arquivos, você precisará excluir dados da tabela ou da partição associada a esse grupo de arquivos antes de reduzir o arquivo e, opcionalmente, carregar esses dados em outra tabela/partição.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>A alteração da camada de serviço e a criação de operações de instância são bloqueadas pela restauração de banco de dados em andamento

`RESTORE`A instrução contínua, o processo de migração do serviço de migração de dados e a restauração pontual interna bloquearão a atualização de uma camada de serviço ou o redimensionamento da instância existente e a criação de novas instâncias até que o processo de restauração seja concluído. 

O processo de restauração bloqueará essas operações nas instâncias gerenciadas e nos pools de instância na mesma sub-rede em que o processo de restauração está em execução. As instâncias em pools de instâncias não são afetadas. Criar ou alterar as operações da camada de serviço não falharão ou atingirão o tempo limite. Eles continuarão assim que o processo de restauração for concluído ou cancelado.

**Solução alternativa**: Aguarde até que o processo de restauração seja concluído, ou cancele o processo de restauração se a operação de criação ou atualização-camada de serviço tiver prioridade mais alta.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>O Resource Governor na camada de serviço comercialmente crítica talvez precise ser reconfigurado após o failover

O recurso [resource governor](/sql/relational-databases/resource-governor/resource-governor) que permite limitar os recursos atribuídos à carga de trabalho do usuário pode classificar incorretamente alguma carga de trabalho do usuário após o failover ou uma alteração de camada de serviço iniciada pelo usuário (por exemplo, a alteração do tamanho máximo do armazenamento de instância vCore ou máximo).

**Solução alternativa**: execute `ALTER RESOURCE GOVERNOR RECONFIGURE` periodicamente ou como parte de um trabalho do SQL Agent que execute a tarefa SQL quando a instância for iniciada se você estiver usando [resource governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>As caixas de diálogo de Service Broker entre bancos de dados devem ser reinicializadas após a atualização da camada de serviço

As caixas de diálogo de Service Broker de banco de dados cruzado deixarão de entregar as mensagens para os serviços em outros bancos de dados após a operação de alteração da camada de serviço. As mensagens *não são perdidas* e podem ser encontradas na fila do remetente. Qualquer alteração de tamanho de armazenamento de instância ou vCores no SQL Instância Gerenciada fará com que um `service_broke_guid` valor na exibição [Sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) seja alterado para todos os bancos de dados. Qualquer `DIALOG` criado usando uma instrução [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) que referencie os agentes de serviço em outro banco de dados interromperá a entrega de mensagens ao serviço de destino.

**Solução alternativa**: Pare qualquer atividade que use conversas de diálogo de Service Broker de banco de dados cruzada antes de atualizar uma camada de serviço e reinicialize-as posteriormente. Se houver mensagens restantes que não são entregues após uma alteração na camada de serviço, leia as mensagens da fila de origem e reenvie-as para a fila de destino.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Não há suporte para a representação de tipos de logon do Azure AD

`EXECUTE AS USER` `EXECUTE AS LOGIN` Não há suporte para a representação usando ou das seguintes entidades de Azure Active Directory (Azure AD):
-   Usuários com alias do Azure AD. O seguinte erro é retornado neste caso: `15517` .
- Logons e usuários do Azure AD com base em aplicativos ou entidades de serviço do Azure AD. Os seguintes erros são retornados neste caso: `15517` e `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query parâmetro sem suporte no sp_send_db_mail

O `@query`parâmetro no procedimento [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) não funciona.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A replicação transacional deve ser reconfigurada após o failover geográfico

Se a replicação transacional estiver habilitada em um banco de dados em um grupo de failover automático, o administrador do SQL Instância Gerenciada deverá limpar todas as publicações no antigo primário e reconfigurá-los no novo primário após a ocorrência de um failover para outra região. Para obter mais informações, consulte [replicação](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication).

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Os logons e usuários do Azure AD não têm suporte no SSDT

SQL Server Data Tools não dão suporte total a logons e usuários do Azure AD.

### <a name="temporary-database-is-used-during-restore-operation"></a>O banco de dados temporário é usado durante a operação de restauração

Quando um banco de dados é restaurado no SQL Instância Gerenciada, o serviço de restauração primeiro criará um banco de dados vazio com o nome desejado para alocar o nome na instância. Após algum tempo, esse banco de dados será descartado e a restauração do banco de dados real será iniciada. 

O banco de dados que está no estado de *restauração* terá temporariamente um valor de GUID aleatório em vez de nome. O nome temporário será alterado para o nome desejado especificado na `RESTORE` instrução quando o processo de restauração for concluído. 

Na fase inicial, um usuário pode acessar o banco de dados vazio e, até mesmo, criar tabelas ou carrega-los nesse banco. Esse banco de dados temporário será descartado quando o serviço de restauração iniciar a segunda fase.

**Solução alternativa**: Não acesse o banco de dados que você está restaurando até ver que a restauração foi concluída.

### <a name="tempdb-structure-and-content-is-re-created"></a>A estrutura TEMPDB e o conteúdo são recriados

O `tempdb` banco de dados sempre é dividido em 12 arquivos de data e a estrutura do arquivo não pode ser alterada. Esse tamanho máximo por arquivo não pode ser alterado e não possível adicionar novos arquivos a `tempdb`. `Tempdb` é sempre recriado como um banco de dados vazio quando a instância inicia ou faz failover, e quaisquer alterações feitas no `tempdb` não serão preservadas.

### <a name="exceeding-storage-space-with-small-database-files"></a>Excedendo o espaço de armazenamento com arquivos de banco de dados pequenos

As instruções `CREATE DATABASE`, `ALTER DATABASE ADD FILE` e `RESTORE DATABASE` podem falhar porque a instância pode alcançar o limite de armazenamento do Azure.

Cada instância Uso Geral do SQL Instância Gerenciada tem até 35 TB de armazenamento reservado para o espaço em disco Premium do Azure. Cada arquivo de banco de dados é colocado em um disco físico separado. Tamanhos de disco podem ser 128 GB, 256 GB, 512 GB, 1 TB ou 4 TB. O espaço não utilizado no disco não é cobrado, mas a soma total dos tamanhos de Disco Premium do Azure não pode exceder 35 TB. Em alguns casos, uma instância gerenciada que não precisa de 8 TB no total pode exceder o limite de 35 TB do Azure em tamanho de armazenamento, devido à fragmentação interna.

Por exemplo, uma instância Uso Geral do SQL Instância Gerenciada pode ter um arquivo grande que tenha 1,2 TB de tamanho colocado em um disco de 4 TB. Ele também pode ter 248 arquivos que são 1 GB cada e que são colocados em discos de 128 GB separados. Neste exemplo:

- O tamanho do armazenamento em disco total alocado é de 1 x 4 TB + 248 x 128 GB = 35 TB.
- O total de espaço reservado para os bancos de dados na instância é de 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Este exemplo ilustra que, em determinadas circunstâncias, devido a uma distribuição específica de arquivos, uma instância do SQL Instância Gerenciada pode alcançar o limite de 35 TB reservado para um disco Premium do Azure conectado quando você pode não esperar isso.

Neste exemplo, bancos de dados existentes continuarão a funcionar e podem crescer sem problemas, desde que não sejam adicionados novos arquivos. No entanto os novos bancos de dados não podem ser criados ou restaurados porque não há espaço suficiente para novas unidades de disco, mesmo se o tamanho total de todos os bancos de dados não alcançar o limite de tamanho de instância. O erro retornado nesse caso não é claro.

Você pode [identificar o número de arquivos restantes](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) usando exibições do sistema. Se você atingir esse limite, tente [esvaziar e excluir alguns dos arquivos menores usando a instrução DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) ou alterne para a [Camada comercialmente crítica, que não tem esse limite](../managed-instance/resource-limits.md#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Valores de GUID mostrados em vez de nomes de banco de dados

Várias entradas de exibições do sistema, contadores de desempenho, mensagens de erro, XEvents e logs de erros exibem identificadores do banco de dados GUID em vez dos nomes reais do banco de dados. Não dependa desses identificadores GUID porque eles serão substituídos por nomes de banco de dados reais no futuro.

**Solução alternativa**: Use a exibição sys. databases para resolver o nome de banco de dados real do nome do banco de dados físico, especificado na forma de identificadores de banco de dados GUID:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Os logs de erros não são persistentes

Os logs de erros que estão disponíveis no SQL Instância Gerenciada não são persistidos e seu tamanho não é incluído no limite máximo de armazenamento. Os logs de erros podem ser apagados automaticamente no caso de failover. Pode haver lacunas no histórico do log de erros porque o SQL Instância Gerenciada foi movido várias vezes em várias máquinas virtuais.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>O escopo de transação em dois bancos de dados dentro da mesma instância não é compatível

**(Resolvido em Março de 2020)** A classe `TransactionScope` em .NET não funciona se duas consultas são enviadas para os dois bancos de dados dentro da mesma instância no mesmo escopo de transação:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**Solução alternativa (não é necessário desde março de 2020)**: use [SqlConnection. ChangeDatabase (cadeia de caracteres)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) para usar outro banco de dados em um contexto de conexão em vez de usar duas conexões.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Os módulos CLR e os servidores vinculados às vezes não podem fazer referência ao endereço IP local

Os módulos CLR no SQL Instância Gerenciada e servidores vinculados ou consultas distribuídas que referenciam uma instância atual às vezes não podem resolver o IP de uma instância local. Esse é um problema temporário.

**Solução alternativa**: Use conexões de contexto em um módulo CLR, se possível.

## <a name="updates"></a>Atualizações

Para atualizações e aprimoramentos do Banco de Dados SQL, consulte [atualizações do serviço de Banco de Dados SQL](https://azure.microsoft.com/updates/?product=sql-database).

Para encontrar atualizações e melhorias para outros serviços do Azure, confira [Atualizações de serviço](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuir com o conteúdo

Para contribuir com a documentação do SQL Azure, consulte o [Guia do colaborador do docs](/contribute/).
