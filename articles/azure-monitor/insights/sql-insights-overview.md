---
title: Monitorar suas implantações do SQL com o SQL insights (versão prévia)
description: Visão geral do SQL insights no Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: d0bb5c55d3f7ba0573dfe9b511f4d31dcc64ed85
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567824"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Monitorar suas implantações do SQL com o SQL insights (versão prévia)
O SQL insights é uma solução abrangente para monitorar qualquer produto na [família SQL do Azure](../../azure-sql/index.yml). O SQL insights usa [exibições de gerenciamento dinâmico](../../azure-sql/database/monitoring-with-dmvs.md) para expor os dados necessários para monitorar a integridade, diagnosticar problemas e ajustar o desempenho.  

O SQL insights executa todo o monitoramento remotamente. Os agentes de monitoramento em máquinas virtuais dedicadas se conectam aos recursos do SQL e coletam dados remotamente. Os dados coletados são armazenados em [logs de Azure monitor](../logs/data-platform-logs.md), permitindo agregação fácil, filtragem e análise de tendência. Você pode exibir os dados coletados do modelo de pasta de [trabalho](../visualize/workbooks-overview.md)do SQL insights ou pode se aprofundar diretamente nos dados usando [consultas de log](../logs/get-started-queries.md).

## <a name="pricing"></a>Preços
Não há custo direto para o SQL insights. Todos os custos são incorridos pelas máquinas virtuais que coletam os dados, os espaços de trabalho Log Analytics que armazenam os dados e as regras de alerta configuradas nos dados. 

**Máquinas virtuais**

Para máquinas virtuais, você será cobrado com base no preço publicado na [página de preços de máquinas virtuais](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/). O número de máquinas virtuais necessárias variará com base no número de cadeias de conexão que você deseja monitorar. É recomendável alocar 1 máquina virtual de tamanho Standard_B2s para cada cadeia de conexão de 100. Consulte [requisitos de máquina virtual do Azure](sql-insights-enable.md#azure-virtual-machine-requirements) para obter mais detalhes.

**Workspaces do Log Analytics**

Para os espaços de trabalho do Log Analytics, você será cobrado com base no preço publicado na [página de preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Os espaços de trabalho Log Analytics usados pelo insights do SQL incorrerão em custos para ingestão de dados, retenção de dados e (opcionalmente) exportação de dados. Os encargos exatos variam de acordo com a quantidade de dados ingeridos, retidos e exportados. A quantidade desses dados irá variar subsequentemente com base na atividade do banco de dado e nas configurações de coleção definidas em seus [perfis de monitoramento](sql-insights-enable.md#create-sql-monitoring-profile).

**Regras de alerta**

Para regras de alerta no Azure Monitor, você será cobrado com base no preço publicado na [página de preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Se você optar por [criar alertas com o SQL insights](sql-insights-alerts.md), será cobrado por quaisquer regras de alerta criadas e quaisquer notificações enviadas.

## <a name="supported-versions"></a>Versões com suporte 
O SQL insights dá suporte às seguintes versões do SQL Server:
- SQL Server 2012 e mais recente

O SQL insights dá suporte a SQL Server em execução nos seguintes ambientes:
- Banco de Dados SQL do Azure
- Instância Gerenciada do Azure SQL
- SQL Server em máquinas virtuais do Azure (SQL Server em execução em máquinas virtuais registradas com o provedor de [máquina virtual do SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) )
- VMs do Azure (SQL Server em execução em máquinas virtuais não registradas com o provedor de [máquina virtual do SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) )

O SQL insights não tem suporte ou suporte limitado para o seguinte:
- **Instâncias não Azure**: SQL Server em execução em máquinas virtuais fora do Azure não são suportadas
- **Pools elásticos do banco de dados SQL do Azure**: não é possível coletar métricas para pools elásticos. Não é possível coletar métricas para bancos de dados em pools elásticos.
- **Camadas de serviço baixa do banco de dados SQL do Azure**: métricas não podem ser coletadas para bancos de dados em camadas de [serviço](../../azure-sql/database/resource-limits-dtu-single-databases.md) básico, S0, S1 e S2
- **Camada sem servidor do banco de dados SQL do Azure**: as métricas podem ser coletadas para bancos dados usando a camada de computação sem servidor. No entanto, o processo de coleta de métricas redefinirá o timer de atraso de pausa automática, impedindo que o banco de dados entre em um estado de pausa automática
- **Réplicas secundárias**: as métricas só podem ser coletadas para uma única réplica secundária por banco de dados. Se um banco de dados tiver mais de uma réplica secundária, somente 1 poderá ser monitorado.
- **Autenticação com Azure Active Directory**: o único método de [autenticação](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization) com suporte para monitoramento é a autenticação do SQL. Para SQL Server na VM do Azure, não há suporte para a autenticação usando Active Directory em um controlador de domínio personalizado.  

## <a name="open-sql-insights"></a>Abrir o SQL insights
Abra o SQL insights selecionando **SQL (visualização)** na seção **insights** do menu **Azure monitor** na portal do Azure. Clique em um bloco para carregar a experiência para o tipo de SQL que você está monitorando.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Informações sobre o SQL no portal do Azure.":::

## <a name="enable-sql-insights"></a>Habilitar insights do SQL 
Consulte [habilitar insights do SQL](sql-insights-enable.md) para obter instruções sobre como habilitar o SQL insights.

## <a name="troubleshoot-sql-insights"></a>Solucionar problemas do SQL insights 
Consulte [Solucionando problemas do SQL insights](sql-insights-troubleshoot.md) para obter instruções sobre como solucionar problemas do SQL insights.

## <a name="data-collected-by-sql-insights"></a>Dados coletados pelo SQL insights
O SQL insights executa todo o monitoramento remotamente. Não instalamos nenhum agente nas máquinas virtuais que executam SQL Server. 

O SQL insights usa máquinas virtuais de monitoramento dedicado para coletar dados remotamente de seus recursos do SQL. Cada máquina virtual de monitoramento terá o [agente de Azure monitor](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) e a extensão WLI (análise de informações de carga de trabalho) instaladas. A extensão WLI inclui o agente de [Telegraf](https://www.influxdata.com/time-series-platform/telegraf/)de código-fonte aberto. O SQL insights usa [regras de coleta de dados](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) para especificar as configurações de coleta de dados para o [plug-in SQL Server](https://www.influxdata.com/integration/microsoft-sql-server/)do Telegraf.

Diferentes conjuntos de dados estão disponíveis para o banco de dados SQL do Azure, Azure SQL Instância Gerenciada e SQL Server. As tabelas a seguir descrevem os dados disponíveis. Você pode personalizar quais conjuntos de dados coletar e a frequência de coleta ao [criar um perfil de monitoramento](sql-insights-enable.md#create-sql-monitoring-profile).

As tabelas a seguir têm as seguintes colunas:
- **Nome amigável**: nome da consulta, conforme mostrado na portal do Azure ao criar um perfil de monitoramento
- **Nome da configuração**: o nome da consulta, conforme mostrado na portal do Azure ao editar um perfil de monitoramento
- **Namespace**: o nome da consulta como encontrado em um espaço de trabalho log Analytics. Esse identificador aparece na tabela **InsighstMetrics** na `Namespace` propriedade da `Tags` coluna
- **DMVs**: exibições gerenciadas dinâmicas usadas para produzir o conjunto de dados
- **Habilitado por padrão**: se os dados são coletados por padrão
- **Frequência de coleta padrão**: com que frequência os dados são coletados por padrão

### <a name="data-for-azure-sql-database"></a>Dados para o banco de dado SQL do Azure 
| Nome amigável | Nome da configuração | Namespace | DMVs | Habilitado por padrão | Frequência de coleta padrão |
|:---|:---|:---|:---|:---|:---|
| Estatísticas de espera do BD | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | No | NA |
| Estatísticas de espera de DBO | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | Yes | 60 segundos |
| Administradores de memória | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 segundos |
| E/S do banco de dados | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | Yes | 60 segundos |
| Propriedades do servidor | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>sistema. bancos<br>sistema. [database_service_objectives] | Yes | 60 segundos |
| Contadores de desempenho | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | Yes | 60 segundos |
| Estatísticas de recursos | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | Yes | 60 segundos |
| Governança de recursos | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | Yes | 60 segundos |
| Requests | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Agendadores| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA  |

### <a name="data-for-azure-sql-managed-instance"></a>Dados para o Azure SQL Instância Gerenciada 

| Nome amigável | Nome da configuração | Namespace | DMVs | Habilitado por padrão | Frequência de coleta padrão |
|:---|:---|:---|:---|:---|:---|
| Estatísticas de espera | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60 segundos |
| Administradores de memória | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 segundos |
| E/S do banco de dados | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60 segundos |
| Propriedades do servidor | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | Yes | 60 segundos |
| Contadores de desempenho | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| Yes | 60 segundos |
| Estatísticas de recursos | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | Yes | 60 segundos |
| Governança de recursos | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | Yes | 60 segundos |
| Requests | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Agendadores | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA |

### <a name="data-for-sql-server"></a>Dados para SQL Server

| Nome amigável | Nome da configuração | Namespace | DMVs | Habilitado por padrão | Frequência de coleta padrão |
|:---|:---|:---|:---|:---|:---|
| Estatísticas de espera | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60 segundos | 
| Administradores de memória | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 segundos |
| E/S do banco de dados | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60 segundos |
| Propriedades do servidor | Sqlserverproperties | sqlserver_server_properties | sys.dm_os_sys_info | Yes | 60 segundos |
| Contadores de desempenho | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | Yes | 60 segundos |
| Espaço no volume | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | Yes | 60 segundos |
| SQL Server CPU | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | Yes | 60 segundos |
| Agendadores | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA |
| Requests | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Estados da réplica de disponibilidade | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | No | 60 segundos |
| Réplicas de banco de dados de disponibilidade | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | No | 60 segundos |

## <a name="next-steps"></a>Próximas etapas

- Consulte [habilitar insights do SQL](sql-insights-enable.md) para obter instruções sobre como habilitar o SQL insights
- Veja as [perguntas](../faq.md#sql-insights-preview) frequentes sobre as perguntas frequentes sobre o SQL insights
