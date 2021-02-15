---
title: Criar & gerenciar servidores e bancos de dados individuais
description: Saiba mais sobre como criar e gerenciar servidores e bancos de dados individuais no Azure SQL usando o portal do Azure, o PowerShell, o CLI do Azure, Transact-SQL (T-SQL) e REST-API.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: e50cce74f7291a6673e5d43f3485a1c63c81d827
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319288"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Criar e gerenciar servidores e bancos de dados individuais no Azure SQL Database

Você pode criar e gerenciar servidores e bancos de dados individuais no banco de dados SQL do Azure usando o portal do Azure, o PowerShell, o CLI do Azure, a API REST e o Transact-SQL.

## <a name="the-azure-portal"></a>O portal do Azure

Você pode criar o grupo de recursos para o banco de dados SQL do Azure antecipadamente ou ao criar o próprio servidor.

### <a name="create-a-server"></a>Criar um servidor

Para criar um servidor usando o [portal do Azure](https://portal.azure.com), crie um novo recurso de [servidor](logical-servers.md) do Azure Marketplace. Como alternativa, você pode criar o servidor ao implantar um banco de dados SQL do Azure.

  ![criar servidor](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Criar um banco de dados em branco ou de exemplo

Para criar um único banco de dados SQL do Azure usando o [portal do Azure](https://portal.azure.com), escolha o recurso de banco de dados SQL do Azure no Azure Marketplace. Você pode criar o grupo de recursos e o servidor antecipadamente ou ao criar o próprio banco de dados. Você pode criar um banco de dados em branco ou um banco de dados de exemplo com base no Adventure Works LT.

  ![criar database-1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> Para obter informações sobre como selecionar o tipo de preço do banco de dados, consulte [modelo de compra baseado em DTU](service-tiers-dtu.md) e [modelo de compra baseado em vCore](service-tiers-vcore.md).

## <a name="manage-an-existing-server"></a>Gerenciar um servidor existente

Para gerenciar um servidor existente, navegue até o servidor usando vários métodos, como de uma página de banco de dados específica, da página **SQL Servers** ou da página **todos os recursos** .

Para gerenciar um banco de dados existente, navegue até a página bancos de dados **SQL** e selecione o banco que você deseja gerenciar. A captura de tela a seguir mostra como começar a configurar um firewall de nível de servidor para um banco de dados na página **Visão geral** de um banco de dados.

   ![regra de firewall do servidor](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Para configurar propriedades de desempenho para um banco de dados, consulte [modelo de compra baseado em DTU](service-tiers-dtu.md) e [modelo de compra baseado em vCore](service-tiers-vcore.md).
> [!TIP]
> Para obter um portal do Azure início rápido, consulte [criar um banco de dados no banco de dados SQL no portal do Azure](single-database-create-quickstart.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para criar e gerenciar servidores, bancos de dados individuais e em pool e firewalls de nível de servidor com Azure PowerShell, use os seguintes cmdlets do PowerShell. Se você precisa instalar ou atualizar o PowerShell, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Para scripts de exemplo do PowerShell, consulte [usar o PowerShell para criar um banco de dados no banco de dados SQL e configurar uma regra de firewall no nível de servidor](scripts/create-and-configure-database-powershell.md) e [monitorar e dimensionar um banco de dados no banco de dados SQL usando o PowerShell](scripts/monitor-and-scale-database-powershell.md).

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

## <a name="the-azure-cli"></a>A CLI do Azure

Para criar e gerenciar servidores, bancos de dados e firewalls com [o CLI do Azure](/cli/azure), use os comandos [CLI do Azure](/cli/azure/sql/db) a seguir. Use o [Cloud Shell](../../cloud-shell/overview.md) para executar a CLI no seu navegador ou [instale-o](/cli/azure/install-azure-cli) no macOS, Linux ou Windows. Para criar e gerenciar pools elásticos, consulte [Pools elásticos](elastic-pool-overview.md).

> [!TIP]
> Para obter um CLI do Azure início rápido, consulte [criar um banco de dados SQL do Azure individual usando o CLI do Azure](az-cli-script-samples-content-guide.md). Para CLI do Azure scripts de exemplo, consulte [usar a CLI para criar um banco de dados no banco de dados SQL do Azure e configurar uma regra de firewall do banco de dados SQL](scripts/create-and-configure-database-cli.md) e [usar a CLI para monitorar e dimensionar um banco de dados no banco de dados SQL do Azure](scripts/monitor-and-scale-database-cli.md)
>

| Cmdlet | Descrição |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Cria um banco de dados|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|Lista todos os bancos de dados e data warehouses em um servidor, ou todos os bancos de dados em um pool elástico|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Lista os objetivos de serviço disponíveis e os limites de armazenamento|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Retorna os usos do banco de dados|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|Obtém um banco de dados ou data warehouse|
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

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Para criar e gerenciar servidores, bancos de dados e firewalls com o Transact-SQL, use os comandos T-SQL a seguir. Você pode emitir esses comandos usando o portal do Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)ou qualquer outro programa que possa se conectar a um servidor no banco de dados SQL e passar comandos Transact-SQL. Para gerenciar pools elásticos, consulte [Pools elásticos](elastic-pool-overview.md).

> [!TIP]
> Para um início rápido que usa o SQL Server Management Studio no Microsoft Windows, consulte [Banco de Dados SQL do Azure: usar o SQL Server Management Studio para conectar e consultar dados](connect-query-ssms.md). Para um início rápido que usa o Visual Studio Code no macOS, Linux ou Windows, consulte [Banco de Dados SQL do Azure: usar o Visual Studio Code para se conectar e consultar dados](connect-query-vscode.md).
> [!IMPORTANT]
> Não é possível criar ou excluir um servidor usando o Transact-SQL.

| Comando | Descrição |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)|Cria um novo banco de dados individual. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Modifica um banco de dados ou pool elástico. |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|Exclui um banco de dados.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retorna a edição (camada de serviço), o objetivo de serviço (tipo de preço) e o nome do pool elástico, se houver, para o banco de dados SQL do Azure ou um pool SQL dedicado no Azure Synapse Analytics. Se estiver conectado ao banco de dados mestre em um servidor no banco de dados SQL, o retornará informações sobre todos os bancos. Para o Azure Synapse Analytics, você deve estar conectado ao banco de dados mestre.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Retorna CPU, e/s e consumo de memória para um banco de dados no banco de dados SQL do Azure. Existe uma linha para cada 15 segundos, mesmo que não haja atividade no banco de dados.|
|[sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Retorna o uso da CPU e os dados de armazenamento de um banco de dado no Azure SQL Database. Os dados são coletados e agregados em intervalos de cinco minutos.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Contém estatísticas para eventos de conectividade do banco de dados SQL, fornecendo uma visão geral de êxitos e falhas de conexão de banco de dados. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Retorna conexões de banco de dados SQL do Azure bem-sucedidas, falhas de conexão e deadlocks. Você pode usar essas informações para acompanhar ou solucionar problemas da atividade de banco de dados com o Banco de Dados SQL.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Cria ou atualiza as configurações de firewall no nível de servidor para o servidor. Este procedimento armazenado só está disponível no banco de dados mestre para o logon principal do nível do servidor. Uma regra de firewall de nível de servidor só pode ser criada usando Transact-SQL depois que a primeira regra de firewall no nível do servidor foi criada por um usuário com permissões no nível do Azure|
|[sys. firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Retorna informações sobre as configurações de firewall no nível de servidor associadas ao banco de dados no banco de dados SQL do Azure.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Remove as configurações de firewall no nível de servidor do seu servidor. Este procedimento armazenado só está disponível no banco de dados mestre para o logon principal do nível do servidor.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Cria ou atualiza as regras de firewall no nível de banco de dados para seu banco de dados no banco de dados SQL do Azure. As regras de firewall do banco de dados podem ser configuradas para o banco de dados mestre e para bancos de dados de usuário no Banco de Dados SQL. As regras de firewall do banco de dados são úteis quando você usa usuários de banco de dados independentes. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Retorna informações sobre as configurações de firewall no nível de banco de dados associadas ao banco de dados no banco de dados SQL do Azure. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Remove a configuração de firewall no nível de banco de dados de um banco de dados. |

## <a name="rest-api"></a>API REST

Para criar e gerenciar servidores, bancos de dados e firewalls, use essas solicitações da API REST.

| Comando | Descrição |
| --- | --- |
|[Servidores – criar ou atualizar](/rest/api/sql/servers/createorupdate)|Cria ou atualiza um novo servidor.|
|[Servidores - Excluir](/rest/api/sql/servers/delete)|Exclui um servidor SQL.|
|[Servidores - Obter](/rest/api/sql/servers/get)|Obtém um servidor.|
|[Servidores - Listar](/rest/api/sql/servers/list)|Retorna uma lista de servidores em uma assinatura.|
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

- Para saber mais sobre como migrar um banco de dados do SQL Server para o Azure, confira [Migrar para o Banco de Dados SQL do Azure](migrate-to-database-from-sql-server.md).
- Para obter informações sobre os recursos com suporte, consulte [Recursos](features-comparison.md).
 
