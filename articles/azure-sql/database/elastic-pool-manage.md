---
title: Gerenciar pools elásticos
description: Crie e gerencie pools elásticos do banco de dados SQL do Azure usando o portal do Azure, o PowerShell, o CLI do Azure, Transact-SQL (T-SQL) e a API REST.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1, devx-track-azurecli
ms.openlocfilehash: 9c9af6e3bc3dfd798f4b3f0cad9319aa573c425d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455992"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Gerenciar Pools elásticos no banco de dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Com um pool elástico, você pode determinar a quantidade de recursos que o pool elástico requer para lidar com a carga de trabalho de seus bancos de dados e a quantidade de recursos para cada banco de dados em pool.

## <a name="azure-portal"></a>Portal do Azure

Todas as configurações de pool podem ser localizadas em um só local: na folha **Configurar pool**. Para obter aqui, localize um pool elástico no portal do Azure e clique em **Configurar pool** na parte superior da folha ou no menu de recursos à esquerda.

A partir deste ponto, é possível fazer qualquer combinação das alterações a seguir e salvá-las em um lote:

1. Alterar a camada de serviço do pool
2. Escalar o desempenho (DTU ou vCores) e armazenar ou reduzir verticalmente
3. Adicionar ou remover bancos de dados de/para o pool
4. Definir um limite de desempenho mínimo (garantido) e máximo para os bancos de dados nos pools
5. Analise o resumo de custos para visualizar quaisquer alterações na fatura como resultado das novas seleções

![Folha de configuração do pool elástico](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para criar e gerenciar pools elásticos do Banco de Dados SQL e bancos de dados em pool com o Azure PowerShell, use os seguintes cmdlets do PowerShell. Se você precisa instalar ou atualizar o PowerShell, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Para criar e gerenciar os servidores para um pool elástico, consulte [criar e gerenciar servidores](logical-servers.md). Para criar e gerenciar regras de firewall, consulte [criar e gerenciar regras de firewall usando o PowerShell](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> Para ver scripts de exemplo do PowerShell, consulte [Criar pools elásticos e mover bancos de dados entre pools e fora de um pool usando o PowerShell](scripts/move-database-between-elastic-pools-powershell.md) e [Usar o PowerShell para monitorar e dimensionar um pool elástico SQL no Banco de Dados SQL do Azure](scripts/monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Cria um pool elástico.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Obtém os pools elásticos e seus valores de propriedade.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Modifica as propriedades de um pool elástico Por exemplo, use a propriedade **StorageMB** para modificar o armazenamento máximo de um pool elástico.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Exclui um pool elástico.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Obtém o status de operações em um pool elástico|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Cria um novo banco de dados em um pool existente ou como um Banco de Dados Individual. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Obtém um ou mais bancos de dados.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Define as propriedades para um banco de dados ou move um banco de dados existente para um pool elástico, para fora dele ou entre pools elásticos.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Remove um banco de dados.|

> [!TIP]
> A criação de muitos bancos de dados em um pool elástico pode levar tempo quando feito usando o portal ou cmdlets do PowerShell que criam apenas um banco de dados individual por vez. Para automatizar a criação em um pool elástico, consulte [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli"></a>CLI do Azure

Para criar e gerenciar pools elásticos do Banco de Dados SQL com a [CLI do Azure](/cli/azure), use os seguintes comandos do [Banco de Dados SQL da CLI do Azure](/cli/azure/sql/db). Use o [Cloud Shell](../../cloud-shell/overview.md) para executar a CLI no seu navegador ou [instale-o](/cli/azure/install-azure-cli) no macOS, Linux ou Windows.

> [!TIP]
> Para CLI do Azure scripts de exemplo, consulte [usar a CLI para mover um banco de dados no banco de dados SQL em um pool elástico do SQL](scripts/move-database-between-elastic-pools-cli.md) e [usar CLI do Azure para dimensionar um pool elástico do SQL no banco de dados SQL do Azure](scripts/scale-pool-cli.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Cria um pool elástico.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Retorna uma lista de pools elásticos em um servidor.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Retorna uma lista de bancos de dados em um pool elástico.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Também inclui as configurações DTU do pool disponível, limites de armazenamento e configurações por banco de dados. Para reduzir o detalhamento, os limites de armazenamento adicionais e as configurações por banco de dados ficam ocultos por padrão.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Atualiza um pool elástico.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Excluir o pool elástico.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Para criar e mover bancos de dados dentro de pools elásticos existentes ou para retornar informações sobre um pool elástico de Banco de Dados SQL com o Transact-SQL, use os seguintes comandos do T-SQL. Você pode emitir esses comandos usando o portal do Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)ou qualquer outro programa que possa se conectar a um servidor e passar comandos Transact-SQL. Para criar e gerenciar regras de firewall usando o T-SQL, consulte [Gerenciar regras de firewall usando o Transact-SQL](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Não é possível criar, atualizar ou excluir um pool elástico de Banco de Dados SQL do Azure usando o Transact-SQL. Você pode adicionar ou remover bancos de dados de um pool elástico, bem como pode usar DMVs para retornar informações sobre os pools elásticos existentes.
>

| Comando | Descrição |
| --- | --- |
|[CREATE DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Cria um novo banco de dados em um pool existente ou como um Banco de Dados Individual. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
| [ALTER DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Move um banco de dados para dentro de um pool elástico, para fora dele ou entre pools elásticos.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Exclui um banco de dados.|
|[sys.elastic_pool_resource_stats (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Retorna estatísticas de uso de recursos para todos os pools elásticos em um servidor. Para cada pool elástico, há uma linha para cada janela de relatórios de 15 segundos (quatro linhas por minuto). Isso inclui a utilização de CPU, E/S, log, consumo de armazenamento e solicitações/sessões simultâneas de todos os bancos de dados no pool.|
|[sys.database_service_objectives (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retorna a edição (camada de serviço), o objetivo de serviço (tipo de preço) e o nome do pool elástico, se houver, para um banco de dados no banco de dados SQL ou o Azure Synapse Analytics. Se estiver conectado ao banco de dados mestre em um servidor, o retornará informações sobre todos os bancos. Para o Azure Synapse Analytics, você deve estar conectado ao banco de dados mestre.|

## <a name="rest-api"></a>API REST

Para criar e gerenciar pools elásticos do Banco de Dados SQL e bancos de dados em pool, use estas solicitações da API REST.

| Comando | Descrição |
| --- | --- |
|[Pools elásticos – criar ou atualizar](/rest/api/sql/elasticpools/createorupdate)|Cria um novo pool elástico ou atualiza um pool elástico existente.|
|[Pools elásticos – Excluir](/rest/api/sql/elasticpools/delete)|Excluir o pool elástico.|
|[Pools elásticos – Obter](/rest/api/sql/elasticpools/get)|Obtém um pool elástico.|
|[Pools elásticos – listar por servidor](/rest/api/sql/elasticpools/listbyserver)|Retorna uma lista de pools elásticos em um servidor.|
|[Pools elásticos – atualizar](/rest/api/sql/elasticpools/listbyserver)|Atualiza um pool elástico existente.|
|[Atividades do pool elástico](/rest/api/sql/elasticpoolactivities)|Retorna as atividades do pool elástico.|
|[Atividades de banco de dados do pool elástico](/rest/api/sql/elasticpooldatabaseactivities)|Retorna as atividades nos bancos de dados dentro de um pool elástico.|
|[Bancos de dados – criar ou atualizar](/rest/api/sql/databases/createorupdate)|Cria um novo banco de dados ou atualiza um banco de dados existente.|
|[Bancos de Dados – Obter](/rest/api/sql/databases/get)|Obtém um banco de dados.|
|[Bancos de dados – listar por pool elástico](/rest/api/sql/databases/listbyelasticpool)|Retorna uma lista de bancos de dados em um pool elástico.|
|[Bancos de dados-listar por servidor](/rest/api/sql/databases/listbyserver)|Retorna uma lista de bancos de dados em um servidor.|
|[Bancos de Dados – Atualizar](/rest/api/sql/databases/update)|Atualiza um banco de dados existente.|

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre padrões de design para aplicativos SaaS usando pools elásticos, confira [Padrões de design para aplicativos de SaaS multilocatários com o banco de dados SQL do Azure](saas-tenancy-app-design-patterns.md).
* Para ver um tutorial de SaaS usando pools elásticos, consulte [Introdução ao aplicativo Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md).