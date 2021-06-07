---
title: Script do PowerShell para operações de taxa de transferência (RU/s) para o contêiner ou o banco de dados de API SQL do Azure Cosmos DB
description: Script do PowerShell para operações de taxa de transferência (RU/s) para o contêiner ou o banco de dados de API SQL do Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 73fdfa06fab42a5687e3aa354e3ba92937b92605
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684285"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Operações de taxa de transferência (RU/s) com o PowerShell para um banco de dados ou contêiner da API de Núcleo (SQL) do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Este exemplo exige o Azure PowerShell Az 5.4.0 ou posterior. Execute `Get-Module -ListAvailable Az` para ver quais versões estão instaladas.
Se é preciso instalar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

Execute [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) para entrar no Azure.

## <a name="get-throughput"></a>Obter taxa de transferência

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-get.ps1 "Get throughput (RU/s) for Azure Cosmos DB Core (SQL) API database or container")]

## <a name="update-throughput"></a>Atualizar a taxa de transferência

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-update.ps1 "Update throughput (RU/s) for an Azure Cosmos DB Core (SQL) API database or container")]

## <a name="migrate-throughput"></a>Migrar a taxa de transferência

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-migrate.ps1 "Migrate between standard and autoscale throughput on an Azure Cosmos DB Core (SQL) API database or container")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBSqlDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabasethroughput) | Obtém a taxa de transferência provisionada em um Banco de Dados da API de Núcleo (SQL) do Azure Cosmos DB. |
| [Get-AzCosmosDBSqlContainerThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainerthroughput) | Obtém a taxa de transferência provisionada em um Contêiner da API de Núcleo (SQL) do Azure Cosmos DB. |
| [Update-AzCosmosDBSqlDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | Atualiza o valor da taxa de transferência de um Banco de Dados da API de Núcleo (SQL) do Azure Cosmos DB. |
| [Update-AzCosmosDBSqlContainerThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Atualiza o valor da taxa de transferência de um Contêiner da API de Núcleo (SQL) do Azure Cosmos DB. |
| [Invoke-AzCosmosDBSqlDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbsqldatabasethroughputmigration) | Migra a taxa de transferência de um Banco de Dados da API de Núcleo (SQL) do Azure Cosmos DB. |
| [Invoke-AzCosmosDBSqlContainerThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbsqlcontainerthroughputmigration) | Migra a taxa de transferência de um Contêiner da API de Núcleo (SQL) do Azure Cosmos DB. |
|**Grupos de recursos do Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).