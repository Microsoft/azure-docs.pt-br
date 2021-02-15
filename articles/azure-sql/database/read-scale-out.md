---
title: Ler consultas em réplicas
description: O SQL do Azure fornece a capacidade de usar a capacidade de réplicas somente leitura para cargas de trabalho de leitura, chamada escala horizontal de leitura.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 01/20/2021
ms.openlocfilehash: 5f9e7e1c96db2b60e41fe0ded69ea562cf8fcea6
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663978"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>Usar réplicas somente leitura para descarregar cargas de trabalho de consulta somente leitura
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Como parte da [arquitetura de alta disponibilidade](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability), cada banco de dados individual, banco de dados do pool elástico e instância gerenciada na camada de serviço Premium e comercialmente crítico é automaticamente provisionado com uma réplica de leitura-gravação primária e várias réplicas somente leitura secundárias. As réplicas secundárias são provisionadas com o mesmo tamanho de computação que a réplica primária. O recurso de *expansão de leitura* permite descarregar cargas de trabalho somente leitura usando a capacidade de computação de uma das réplicas somente leitura, em vez de executá-las na réplica de leitura/gravação. Dessa forma, algumas cargas de trabalho somente leitura podem ser isoladas das cargas de trabalho de leitura/gravação e não afetarão seu desempenho. O recurso destina-se aos aplicativos que incluem cargas de trabalho somente leitura logicamente separadas, como análise. Nas camadas de serviço Premium e Comercialmente Crítico, os aplicativos podem obter benefícios de desempenho usando essa capacidade adicional sem custo adicional.

O recurso de *expansão de leitura* também está disponível na camada de serviço de hiperescala quando pelo menos uma réplica secundária é criada. Várias réplicas secundárias podem ser usadas para cargas de trabalho somente leitura de balanceamento de carga que exigem mais recursos do que o disponível em uma réplica secundária.

A arquitetura de alta disponibilidade das camadas de serviço Basic, Standard e Uso Geral não inclui nenhuma réplica. O recurso de *expansão de leitura* não está disponível nessas camadas de serviço.

O diagrama a seguir ilustra o recurso.

![Réplicas somente leitura](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

O recurso de *expansão de leitura* é habilitado por padrão nos novos bancos de dados Premium, comercialmente crítico e hiperescala. Para o hiperescala, uma réplica secundária é criada por padrão para novos bancos de dados. 

> [!NOTE]
> A expansão de leitura é sempre habilitada na camada de serviço Comercialmente Crítico de Instância Gerenciada.

Se a cadeia de conexão SQL estiver configurada com `ApplicationIntent=ReadOnly` , o aplicativo será redirecionado para uma réplica somente leitura desse banco de dados ou instância gerenciada. Para obter informações sobre como usar a `ApplicationIntent` propriedade, consulte [especificando a intenção do aplicativo](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se você quiser garantir que o aplicativo se conecte à réplica primária, independentemente da `ApplicationIntent` configuração na cadeia de conexão SQL, você deve desabilitar explicitamente a expansão de leitura ao criar o banco de dados ou ao alterar sua configuração. Por exemplo, se você atualizar seu banco de dados da camada Standard ou Uso Geral para a camada Premium, Comercialmente Crítico ou hiperscale e quiser ter certeza de que todas as suas conexões continuam a ir para a réplica primária, desabilite a expansão de leitura. Para obter detalhes sobre como desabilitá-lo, consulte [habilitar e desabilitar a expansão de leitura](#enable-and-disable-read-scale-out).

> [!NOTE]
> Não há suporte para os recursos Repositório de Consultas e SQL Profiler em réplicas somente leitura. 

## <a name="data-consistency"></a>Consistência de dados

Um dos benefícios das réplicas é que as réplicas estão sempre no estado transacionalmente consistente, mas em diferentes momentos, pode haver uma pequena latência entre diferentes réplicas. A expansão de leitura dá suporte à consistência no nível da sessão. Isso significa que, se a sessão somente leitura se reconectar após um erro de conexão causado pela indisponibilidade de réplica, ela poderá ser redirecionada para uma réplica que não seja 100% atualizada com a réplica de leitura/gravação. Da mesma maneira, se um aplicativo gravar dados usando uma sessão de leitura/gravação e lê-lo imediatamente usando uma sessão somente leitura, é possível que as atualizações mais recentes não fiquem visíveis imediatamente na réplica. A latência é causada por uma operação redo assíncrona do log de transações.

> [!NOTE]
> As latências de replicação dentro da região são baixas e essa situação é rara. Para monitorar a latência de replicação, consulte [monitorando e Solucionando problemas de réplica somente leitura](#monitoring-and-troubleshooting-read-only-replicas).

## <a name="connect-to-a-read-only-replica"></a>Conectar-se a uma réplica somente leitura

Quando você habilita a expansão de leitura para um banco de dados, a `ApplicationIntent` opção na cadeia de conexão fornecida pelo cliente determina se a conexão é roteada para a réplica de gravação ou para uma réplica somente leitura. Especificamente, se o `ApplicationIntent` valor for `ReadWrite` (o valor padrão), a conexão será direcionada para a réplica de leitura-gravação. Isso é idêntico ao comportamento quando `ApplicationIntent` não está incluído na cadeia de conexão. Se o valor de `ApplicationIntent` é `ReadOnly`, a conexão é roteada para uma réplica somente leitura.

Por exemplo, a seguinte cadeia de conexão conecta o cliente a uma réplica somente leitura (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Qualquer uma das seguintes cadeias de conexão conecta o cliente a uma réplica de leitura-gravação (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Verifique se uma conexão está pronta para réplica somente leitura

Você pode verificar se você está conectado a uma réplica somente leitura executando a consulta a seguir no contexto do banco de dados. Isso retornará READ_ONLY quando você estiver conectado a uma réplica somente leitura.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> Nas camadas de serviço Premium e Comercialmente Crítico, somente uma das réplicas somente leitura pode ser acessada em um determinado momento. O hiperscale dá suporte a várias réplicas somente leitura.

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>Monitorando e Solucionando problemas de réplicas somente leitura

Quando conectado a uma réplica somente leitura, as DMVs (exibições de gerenciamento dinâmico) refletem o estado da réplica e podem ser consultadas para fins de monitoramento e solução de problemas. O mecanismo de banco de dados fornece várias exibições para expor uma ampla variedade de dados de monitoramento. 

As exibições comumente usadas são:

| Nome | Finalidade |
|:---|:---|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Fornece métricas de utilização de recursos para a última hora, incluindo CPU, e/s de dados e utilização de gravação de log em relação aos limites de objetivo de serviço.|
|[sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| Fornece estatísticas de espera agregada para a instância do mecanismo de banco de dados. |
|[sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| Fornece estatísticas de sincronização e estado de integridade da réplica. O tamanho da fila de restauração e a taxa de restauração funcionam como indicadores de latência de dados na réplica somente leitura. |
|[sys.dm_os_performance_counters](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| Fornece contadores de desempenho do mecanismo de banco de dados.|
|[sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| Fornece estatísticas de execução por consulta, como o número de execuções, o tempo de CPU usado, etc.|
|[sys.dm_exec_query_plan ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| Fornece planos de consulta em cache. |
|[sys.dm_exec_sql_text ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| Fornece o texto de consulta para um plano de consulta em cache.|
|[sys.dm_exec_query_profiles](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Fornece o progresso de consulta em tempo real enquanto as consultas estão em execução.|
|[sys.dm_exec_query_plan_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Fornece o último plano de execução real conhecido, incluindo estatísticas de tempo de execução para uma consulta.|
|[sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| Fornece IOPS de armazenamento, taxa de transferência e estatísticas de latência para todos os arquivos de banco de dados. |

> [!NOTE]
> As `sys.resource_stats` `sys.elastic_pool_resource_stats` DMVs e no banco de dados mestre lógico retornam os dados de utilização de recursos da réplica primária.

### <a name="monitoring-read-only-replicas-with-extended-events"></a>Monitorando réplicas somente leitura com eventos estendidos

Uma sessão de evento estendido não pode ser criada quando conectada a uma réplica somente leitura. No entanto, no banco de dados SQL do Azure, as definições de sessões de [eventos estendidas](xevent-db-diff-from-svr.md) no escopo do banco de dados criadas e alteradas na réplica primária são replicadas para réplicas somente leitura, incluindo réplicas geográficas e eventos de captura em réplicas somente leitura.

Uma sessão de evento estendido em uma réplica somente leitura baseada em uma definição de sessão da réplica primária pode ser iniciada e interrompida independentemente da réplica primária. Quando uma sessão de evento estendido é descartada na réplica primária, ela também é descartada em todas as réplicas somente leitura.

### <a name="transaction-isolation-level-on-read-only-replicas"></a>Nível de isolamento da transação em réplicas somente leitura

As consultas que são executadas em réplicas somente leitura são sempre mapeadas para o nível de isolamento da transação de [instantâneo](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server) . O isolamento de instantâneo usa o controle de versão de linha para evitar cenários de bloqueio onde os leitores bloqueiam gravadores.

Em casos raros, se uma transação de isolamento de instantâneo acessar os metadados de objeto que foram modificados em outra transação simultânea, ele poderá receber o erro [3961](/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error), "a transação de isolamento de instantâneo falhou no banco de dados '%. * ls ' porque o objeto acessado pela instrução foi modificado por uma instrução DDL em outra transação simultânea desde o início desta transação. Ela não é permitida porque os metadados não têm controle de versão. Uma atualização simultânea para metadados pode levar à inconsistência se combinada com isolamento de instantâneo. "

### <a name="long-running-queries-on-read-only-replicas"></a>Consultas de execução longa em réplicas somente leitura

As consultas em execução em réplicas somente leitura precisam acessar metadados para os objetos referenciados na consulta (tabelas, índices, estatísticas, etc.) Em casos raros, se um objeto de metadados for modificado na réplica primária enquanto uma consulta mantém um bloqueio no mesmo objeto na réplica somente leitura, a consulta pode [Bloquear](/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK) o processo que aplica as alterações da réplica primária à réplica somente leitura. Se essa consulta fosse executada por um longo tempo, isso faria com que a réplica somente leitura fosse significativamente dessincronizada com a réplica primária.

Se uma consulta de execução longa em uma réplica somente leitura causar esse tipo de bloqueio, ela será encerrada automaticamente. A sessão receberá o erro 1219, "sua sessão foi desconectada devido a uma operação DDL de alta prioridade", ou erro 3947, "a transação foi anulada porque a computação secundária não pôde ser refeita. Repita a transação. "

> [!NOTE]
> Se você receber o erro 3961, 1219 ou 3947 ao executar consultas em uma réplica somente leitura, repita a consulta.

> [!TIP]
> Nas camadas de serviço Premium e Comercialmente Crítico, quando conectado a uma réplica somente leitura, as `redo_queue_size` colunas e `redo_rate` no [Sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) DMV podem ser usadas para monitorar o processo de sincronização de dados, servindo como indicadores de latência de dados na réplica somente leitura.
> 

## <a name="enable-and-disable-read-scale-out"></a>Habilitar e desabilitar a expansão de leitura

A expansão de leitura é habilitada por padrão nas camadas de serviço Premium, Comercialmente Crítico e hiperescala. A expansão de leitura não pode ser habilitada nas camadas de serviço Basic, Standard ou Uso Geral. A expansão de leitura é desabilitada automaticamente em bancos de dados de hiperescala configurados com zero réplicas.

Você pode desabilitar e reabilitar a expansão de leitura em bancos de dados individuais e bancos de dados de pool elástico nas camadas de serviço Premium ou Comercialmente Crítico usando os métodos a seguir.

> [!NOTE]
> Para bancos de dados individuais e bancos de dados de pool elástico, a capacidade de desabilitar a expansão de leitura é fornecida para compatibilidade com versões anteriores. A expansão de leitura não pode ser desabilitada em instâncias gerenciadas Comercialmente Crítico.

### <a name="azure-portal"></a>Portal do Azure

Você pode gerenciar a configuração de expansão de leitura na folha **Configurar** banco de dados.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo Azure Resource Manager continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo AZ e nos módulos Azure Resource Manager são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [apresentando o novo módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

O gerenciamento da expansão de leitura no Azure PowerShell requer a versão de dezembro de 2016 Azure PowerShell ou mais recente. Para a versão mais recente do PowerShell, consulte [Azure PowerShell](/powershell/azure/install-az-ps).

Você pode desabilitar ou reabilitar a escala de leitura em Azure PowerShell invocando o cmdlet [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) e passando o valor desejado ( `Enabled` ou `Disabled` ) para o `-ReadScale` parâmetro.

Para desabilitar a expansão de leitura em um banco de dados existente (substituindo os itens pelos colchetes angulares pelos valores corretos para seu ambiente e descartando os colchetes):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Para desabilitar a expansão de leitura em um novo banco de dados (substituindo os itens nos colchetes angulares pelos valores corretos para seu ambiente e descartando os colchetes):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Para reabilitar a escala de leitura em um banco de dados existente (substituindo os itens nos colchetes angulares pelos valores corretos para seu ambiente e descartando os colchetes):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

Para criar um banco de dados com expansão de leitura desabilitada ou para alterar a configuração de um banco de dados existente, use o método a seguir com a `readScale` propriedade definida como `Enabled` ou `Disabled` , como na solicitação de exemplo a seguir.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Para obter mais informações, consulte [bancos de dados – criar ou atualizar](/rest/api/sql/databases/createorupdate).

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>Usando o `tempdb` banco de dados em uma réplica somente leitura

O `tempdb` banco de dados na réplica primária não é replicado para as réplicas somente leitura. Cada réplica tem seu próprio `tempdb` banco de dados que é criado quando a réplica é criada. Isso garante que `tempdb` é atualizável e pode ser modificado durante a execução da consulta. Se sua carga de trabalho somente leitura depender do uso de `tempdb` objetos, você deverá criar esses objetos como parte do script de consulta.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Usando a expansão de leitura com bancos de dados replicados geograficamente

Os bancos de dados secundários replicados geograficamente têm a mesma arquitetura de alta disponibilidade que os bancos de dados primários. Se você estiver se conectando ao banco de dados secundário replicado geograficamente com expansão de leitura habilitada, suas sessões com `ApplicationIntent=ReadOnly` serão roteadas para uma das réplicas de alta disponibilidade da mesma maneira que são roteadas no banco de dados gravável primário. As sessões sem `ApplicationIntent=ReadOnly` serão roteadas para a réplica primária do secundário replicado geograficamente, que também é somente leitura. 

Dessa maneira, a criação de uma réplica geográfica fornece duas réplicas somente leitura para um banco de dados primário de leitura/gravação, para um total de três réplicas somente leitura. Cada réplica geográfica adicional fornece outro par de réplicas somente leitura. As réplicas geográficas podem ser criadas em qualquer região do Azure, incluindo a região do banco de dados primário.

> [!NOTE]
> Não há nenhum rodízio automático ou qualquer outro roteamento com balanceamento de carga entre as réplicas de um banco de dados secundário replicado geograficamente.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre a oferta de hiperescala do banco de dados SQL, consulte [camada de serviço de hiperescala](service-tier-hyperscale.md).