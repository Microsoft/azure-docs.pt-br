---
title: O que é um servidor no banco de dados SQL do Azure e o Azure Synapse Analytics?
titleSuffix: ''
description: Saiba mais sobre servidores SQL lógicos usados pelo banco de dados SQL do Azure e pelo Azure Synapse Analytics e como gerenciá-los.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 09e8ef85b6df48b6b442351135d4ae4ed55e9b45
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641072"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>O que é um SQL Server lógico no banco de dados SQL do Azure e o Azure Synapse?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

No banco de dados SQL do Azure e no Azure Synapse Analytics, um servidor é um constructo lógico que atua como um ponto administrativo central para uma coleção de bancos de dados. No nível do servidor, você pode administrar [logons](logins-create-manage.md), [regras de firewall](firewall-configure.md), [regras de auditoria](../../azure-sql/database/auditing-overview.md), políticas de detecção de [ameaças](threat-detection-configure.md)e [grupos de failover automático](auto-failover-group-overview.md). Um servidor pode estar em uma região diferente de seu grupo de recursos. O servidor deve existir antes de você poder criar um banco de dados no banco de dados SQL do Azure ou um data warehouse banco de dados no Azure Synapse Analytics. Todos os bancos de dados gerenciados por um único servidor são criados na mesma região que o servidor.

Esse servidor é diferente de uma instância do SQL Server com a qual você pode estar familiarizado no mundo local. Especificamente, não há nenhuma garantia em relação ao local dos bancos de dados ou data warehouse banco de dados em relação ao servidor que os gerencia. Além disso, nem o banco de dados SQL do Azure nem o Azure Synapse expõem qualquer recurso ou acesso em nível de instância. Por outro lado, os bancos de dados de instância em uma instância gerenciada estão todos fisicamente colocalizados da mesma maneira que você está familiarizado com SQL Server no mundo da máquina virtual ou local.

Ao criar um servidor, você fornece uma conta de logon do servidor e uma senha que tem direitos administrativos ao banco de dados mestre nesse servidor e a todos os bancos de dados criados nesse servidor. Essa conta inicial é uma conta de logon do SQL. O banco de dados SQL do Azure e o Azure Synapse Analytics dão suporte à autenticação do SQL e Azure Active Directory autenticação para autenticação. Para obter informações sobre logons e autenticação, confira [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](logins-create-manage.md). A Autenticação do Windows não é suportada.

Um servidor no banco de dados SQL e Azure Synapse:

- É criado em uma assinatura do Azure, mas pode ser movido com seus recursos independentes para outra assinatura
- É o recurso pai de bancos de dados, pools elásticos e data warehouses
- Fornece um namespace para bancos de dados, pools elásticos e data warehouse
- É um contêiner lógico com semântica de vida útil forte – excluir um servidor e excluir seus bancos de dados, pools elásticos e pools de SQK
- Participa do [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) -bancos de dados, pools elásticos e data warehouse banco de dados em um servidor herdar direitos de acesso do servidor
- É um elemento de ordem superior da identidade de bancos de dados, pools elásticos e data warehouse banco de dados para fins de gerenciamento de recursos do Azure (consulte o esquema de URL para bancos de dados e pools)
- Coloca recursos em uma região
- Fornece um ponto de extremidade de conexão para acesso ao banco de dados (`<serverName>`.database.windows.net)
- Fornece acesso aos metadados sobre os recursos independentes por meio de DMVs, conectando-se ao banco de dados mestre
- Fornece o escopo para políticas de gerenciamento que se aplicam a seus bancos de dados - logins, firewall, auditoria, detecção de ameaças e
- É restrito por uma cota na assinatura pai (seis servidores por assinatura por padrão; [consulte os Limites da assinatura aqui](../../azure-resource-manager/management/azure-subscription-service-limits.md))
- Fornece o escopo da cota de banco de dados e da cota de DTU ou vCore para os recursos que ele contém (como 45.000 DTUs)
- É o escopo de controle de versão para as funcionalidades habilitadas em recursos contidos
- Os logons de entidade de segurança no nível do servidor podem gerenciar todos os bancos de dados em um servidor
- Pode conter logons semelhantes aos em instâncias do SQL Server no seu ambiente local que recebem acesso a um ou mais bancos de dados no servidor e podem receber direitos administrativos limitados. Para obter mais informações, consulte [Logons](logins-create-manage.md).
- O agrupamento padrão para todos os bancos de dados criados em um servidor é `SQL_LATIN1_GENERAL_CP1_CI_AS` , em que `LATIN1_GENERAL` é o inglês (Estados Unidos), `CP1` a página de código 1252, `CI` não diferencia maiúsculas de minúsculas e diferencia `AS` acentos.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Gerenciar servidores, bancos de dados e firewalls usando o portal do Azure

Você pode criar o grupo de recursos para um servidor antecipadamente ou ao criar o próprio servidor. Há vários métodos para obter um novo formulário de servidor SQL, seja criando um novo servidor SQL ou como parte da criação de um novo banco de dados.

### <a name="create-a-blank-server"></a>Criar um servidor em branco

Para criar um servidor (sem um banco de dados, um pool elástico ou um banco de dados data warehouse) usando o [portal do Azure](https://portal.azure.com), navegue até um formulário SQL Server (SQL Server lógico) em branco.

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Criar um banco de dados em branco ou de exemplo no banco de dados SQL do Azure

Para criar um banco de dados no banco de dados SQL usando o [portal do Azure](https://portal.azure.com), navegue até um formulário de banco de dados SQL em branco e forneça as informações solicitadas. Você pode criar o grupo de recursos e o servidor antecipadamente ou ao criar o próprio banco de dados. Você pode criar um banco de dados em branco ou um banco de dados de exemplo com base no Adventure Works LT.

  ![criar database-1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> Para obter informações sobre como selecionar o tipo de preço do banco de dados, consulte [modelo de compra baseado em DTU](service-tiers-dtu.md) e [modelo de compra baseado em vCore](service-tiers-vcore.md).

Para criar uma instância gerenciada, consulte [criar uma instância gerenciada](../managed-instance/instance-create-quickstart.md)

### <a name="manage-an-existing-server"></a>Gerenciar um servidor existente

Para gerenciar um servidor existente, navegue até o servidor usando vários métodos, como de uma página de banco de dados específica, a página **SQL Servers** ou a página **todos os recursos** .

Para gerenciar um banco de dados existente, navegue até a página **bancos de dados SQL** e clique no banco de dados que você deseja gerenciar. A captura de tela a seguir mostra como começar a configurar um firewall de nível de servidor para um banco de dados na página **Visão geral** de um banco de dados.

   ![regra de firewall do servidor](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> Para configurar propriedades de desempenho para um banco de dados, consulte [modelo de compra baseado em DTU](service-tiers-dtu.md) e [modelo de compra baseado em vCore](service-tiers-vcore.md).
> [!TIP]
> Para obter um portal do Azure início rápido, consulte [criar um banco de dados no banco de dados SQL no portal do Azure](single-database-create-quickstart.md).

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>Gerenciar servidores, bancos de dados e firewalls usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para criar e gerenciar servidores, bancos de dados e firewalls com Azure PowerShell, use os cmdlets do PowerShell a seguir. Se você precisa instalar ou atualizar o PowerShell, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Para criar e gerenciar pools elásticos, consulte [Pools elásticos](elastic-pool-overview.md).

| Cmdlet | Descrição |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Cria um banco de dados |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Obtém um ou mais bancos de dados|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Define propriedades para um banco de dados ou move um banco de dados existente para um pool, elástico|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Remove um banco de dados|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Cria um grupos de recursos|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Cria um servidor|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Retorna informações sobre servidores|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Modifica as propriedades de um servidor|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Remove um servidor|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Cria uma regra de firewall no nível de servidor |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Obtém as regras de firewall para um servidor|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Modifica uma regra de firewall em um servidor|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Exclui uma regra de firewall de um servidor.|
| New-AzSqlServerVirtualNetworkRule | Cria um [*regra da rede virtual*](vnet-service-endpoint-rule-overview.md), com base em uma sub-rede que é um ponto de extremidade de serviço de rede virtual. |

> [!TIP]
> Para obter um início rápido do PowerShell, consulte [criar um banco de dados no banco de dados SQL do Azure usando o PowerShell](single-database-create-quickstart.md). Para scripts de exemplo do PowerShell, consulte [usar o PowerShell para criar um banco de dados no banco de dados SQL do Azure e configurar uma regra de firewall](scripts/create-and-configure-database-powershell.md) e [monitorar e dimensionar um banco de dados no banco de dados SQL do Azure usando o PowerShell](scripts/monitor-and-scale-database-powershell.md).
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Gerenciar servidores, bancos de dados e firewalls usando o CLI do Azure

Para criar e gerenciar servidores, bancos de dados e firewalls com o [CLI do Azure](/cli/azure), use os comandos de [banco de dados SQL](/cli/azure/sql/db) a seguir CLI do Azure. Use o [Cloud Shell](../../cloud-shell/overview.md) para executar a CLI no seu navegador ou [instale-o](/cli/azure/install-azure-cli) no macOS, Linux ou Windows. Para criar e gerenciar pools elásticos, consulte [Pools elásticos](elastic-pool-overview.md).

| Cmdlet | Descrição |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Cria um banco de dados|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|Lista todos os bancos de dados gerenciados por um servidor ou todos os bancos de dados em um pool elástico|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Lista os objetivos de serviço disponíveis e os limites de armazenamento|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Retorna os usos do banco de dados|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|Obter um banco de dados
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Atualiza um banco de dados|
|[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)|Remove um banco de dados|
|[az group create](/cli/azure/group#az-group-create)|Cria um grupos de recursos|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Cria um servidor|
|[az sql server list](/cli/azure/sql/server#az-sql-server-list)|Lista servidores|
|[az sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Retorna os usos do servidor|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|Obtém um servidor|
|[az sql server update](/cli/azure/sql/server#az-sql-server-update)|Atualiza um servidor|
|[az sql server delete](/cli/azure/sql/server#az-sql-server-delete)|Exclui um servidor|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Cria uma regra de firewall de servidor|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Lista as regras de firewall em um servidor|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Mostra os detalhes de uma regra de firewall|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Atualiza uma regra de firewall|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Exclui uma regra de firewall|

> [!TIP]
> Para obter um CLI do Azure início rápido, consulte [criar um banco de dados no banco de dados SQL do Azure usando o CLI do Azure](az-cli-script-samples-content-guide.md). Para CLI do Azure scripts de exemplo, consulte [usar a CLI para criar um banco de dados no banco de dados SQL do Azure e configurar uma regra de firewall](scripts/create-and-configure-database-cli.md) e [usar a CLI para monitorar e dimensionar um banco de dados no banco de dados SQL do Azure](scripts/monitor-and-scale-database-cli.md).
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Gerenciar servidores, bancos de dados e firewalls usando o Transact-SQL

Para criar e gerenciar servidores, bancos de dados e firewalls com o Transact-SQL, use os comandos T-SQL a seguir. Você pode emitir esses comandos usando o portal do Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)ou qualquer outro programa que possa se conectar a um servidor e passar comandos Transact-SQL. Para gerenciar pools elásticos, consulte [Pools elásticos](elastic-pool-overview.md).

> [!IMPORTANT]
> Não é possível criar ou excluir um servidor usando o Transact-SQL.

| Comando | Descrição |
| --- | --- |
|[CREATE DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true) | Cria um banco de dados no Banco de Dados SQL do Azure. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
|[CREATE DATABASE (Azure Synapse)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Cria um novo banco de dados data warehouse no Azure Synapse. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
| [ALTER DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Modifica o banco de dados ou o pool elástico. |
|[ALTER DATABASE (Azure Synapse Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true&tabs=sqlpool)|Modifica um banco de dados data warehouse no Azure Synapse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Exclui um banco de dados.|
|[sys.database_service_objectives (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retorna a edição (camada de serviço), o objetivo de serviço (tipo de preço) e o nome do pool elástico, se houver, para um banco de dados. Se estiver conectado ao banco de dados mestre de um servidor, o retornará informações sobre todos os bancos. Para o Azure Synapse, você deve estar conectado ao banco de dados mestre.|
|[sys.dm_db_resource_stats (Banco de Dados SQL do Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Retorna CPU, e/s e consumo de memória para um banco de dados no banco de dados SQL do Azure. Existe uma linha para cada 15 segundos, mesmo se não houver nenhuma atividade no banco de dados.|
|[sys.resource_stats (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Retorna o uso da CPU e os dados de armazenamento de um banco de dado no Azure SQL Database. Os dados são coletados e agregados em intervalos de cinco minutos.|
|[sys.database_connection_stats (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Contém estatísticas para eventos de conectividade de banco de dados para o banco de dados SQL do Azure, fornecendo uma visão geral de êxitos e falhas de conexão de banco de dados |
|[sys.event_log (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Retorna conexões de banco de dados SQL do Azure bem-sucedidas, falhas de conexão e deadlocks para o banco de dados SQL do Azure. Você pode usar essas informações para controlar ou solucionar problemas de atividade do banco de dados.|
|[sp_set_firewall_rule (Banco de Dados SQL do Azure)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Cria ou atualiza as configurações de firewall no nível de servidor para o servidor. Este procedimento armazenado só está disponível no banco de dados mestre para o logon principal do nível do servidor. Uma regra de firewall de nível de servidor só pode ser criada usando Transact-SQL depois que a primeira regra de firewall no nível do servidor foi criada por um usuário com permissões no nível do Azure|
|[sys.firewall_rules (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Retorna informações sobre as configurações de firewall no nível de servidor associadas a um servidor.|
|[sp_delete_firewall_rule (Banco de Dados SQL do Azure)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Remove as configurações de firewall no nível de servidor de um servidor. Este procedimento armazenado só está disponível no banco de dados mestre para o logon principal do nível do servidor.|
|[sp_set_database_firewall_rule (Banco de Dados SQL do Azure)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Cria ou atualiza as regras de firewall no nível de banco de dados para um banco de dados no banco de dados SQL do Azure. As regras de firewall de banco de dados podem ser configuradas para o banco de dados mestre e para os bancos de dados de usuário no SQL Database. As regras de firewall do banco de dados são úteis quando você usa usuários de banco de dados independentes. Não há suporte para regras de firewall de banco de dados no Azure Synapse.|
|[sys.database_firewall_rules (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Retorna informações sobre as configurações de firewall no nível de banco de dados para um banco de dados no banco de dados SQL do Azure. |
|[sp_delete_database_firewall_rule (Banco de Dados SQL do Azure)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Remove a configuração de firewall no nível de banco de dados para um banco de dados do seu no banco de dados SQL do Azure. |

> [!TIP]
> Para um início rápido que usa o SQL Server Management Studio no Microsoft Windows, consulte [Banco de Dados SQL do Azure: usar o SQL Server Management Studio para conectar e consultar dados](connect-query-ssms.md). Para um início rápido que usa o Visual Studio Code no macOS, Linux ou Windows, consulte [Banco de Dados SQL do Azure: usar o Visual Studio Code para se conectar e consultar dados](connect-query-vscode.md).

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>Gerenciar servidores, bancos de dados e firewalls usando a API REST

Para criar e gerenciar servidores, bancos de dados e firewalls, use essas solicitações da API REST.

| Comando | Descrição |
| --- | --- |
|[Servidores – criar ou atualizar](/rest/api/sql/servers/createorupdate)|Cria ou atualiza um novo servidor.|
|[Servidores - Excluir](/rest/api/sql/servers/delete)|Exclui um servidor.|
|[Servidores - Obter](/rest/api/sql/servers/get)|Obtém um servidor.|
|[Servidores - Listar](/rest/api/sql/servers/list)|Retorna uma lista de servidores.|
|[Servidores – listar por grupo de recursos](/rest/api/sql/servers/listbyresourcegroup)|Retorna uma lista de servidores em um grupo de recursos.|
|[Servidores - Atualizar](/rest/api/sql/servers/update)|Atualiza um servidor existente.|
|[Bancos de dados – criar ou atualizar](/rest/api/sql/databases/createorupdate)|Cria um novo banco de dados ou atualiza um banco de dados existente.|
|[Bancos de dados - Delete](/rest/api/sql/databases/delete)|Exclui um banco de dados.|
|[Bancos de Dados – Obter](/rest/api/sql/databases/get)|Obtém um banco de dados.|
|[Bancos de dados – listar por pool elástico](/rest/api/sql/databases/listbyelasticpool)|Retorna uma lista de bancos de dados em um pool elástico.|
|[Bancos de dados-listar por servidor](/rest/api/sql/databases/listbyserver)|Retorna uma lista de bancos de dados em um servidor.|
|[Bancos de Dados – Atualizar](/rest/api/sql/databases/update)|Atualiza um banco de dados existente.|
|[Regras de firewall – criar ou atualizar](/rest/api/sql/firewallrules/createorupdate)|Cria ou atualiza uma regra de firewall.|
|[Regras de firewall-excluir](/rest/api/sql/firewallrules/delete)|Exclui uma regra de firewall.|
|[Regras de firewall-obter](/rest/api/sql/firewallrules/get)|Obtêm uma regra de firewall.|
|[Regras de firewall-listar por servidor](/rest/api/sql/firewallrules/listbyserver)|Retorna uma lista de regras de firewall.|

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como migrar um banco de dados SQL Server para o banco de dados SQL do Azure, consulte [migrar para o banco de dados SQL](migrate-to-database-from-sql-server.md)
- Para obter informações sobre os recursos com suporte, consulte [Recursos](features-comparison.md).