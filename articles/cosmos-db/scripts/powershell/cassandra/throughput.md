---
title: Scripts do PowerShell para operações de taxa de transferência (RU/s) para recursos da API do Cassandra do Azure Cosmos DB
description: Scripts do PowerShell para operações de taxa de transferência (RU/s) para recursos da API do Cassandra do Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 72c35cba7c058928a7b087dd5f26f741e73a202c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684359"
---
# <a name="throughput-rus-operations-with-powershell-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Operações de taxa de transferência (RU/s) com o PowerShell para um keyspace ou uma tabela do Azure Cosmos DB – API do Cassandra
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Este exemplo exige o Azure PowerShell Az 5.4.0 ou posterior. Execute `Get-Module -ListAvailable Az` para ver quais versões estão instaladas.
Se é preciso instalar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

Execute [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) para entrar no Azure.

## <a name="get-throughput"></a>Obter taxa de transferência

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-get.ps1 "Get throughput on a keyspace or table for Cassandra API")]

## <a name="update-throughput"></a>Atualizar a taxa de transferência

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-update.ps1 "Update throughput on a keyspace or table for Cassandra API")]

## <a name="migrate-throughput"></a>Migrar a taxa de transferência

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a keyspace or table for Cassandra API")]

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
| [Get-AzCosmosDBCassandraKeyspaceThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbcassandrakeyspacethroughput) | Obtém o valor de taxa de transferência do Keyspace da API do Cassandra. |
| [Get-AzCosmosDBCassandraTableThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbcassandratablethroughput) | Obtém o valor de taxa de transferência da Tabela de API do Cassandra. |
| [Update-AzCosmosDBCassandraKeyspaceThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbcassandrakeyspacethroughput) | Atualiza o valor de taxa de transferência do Keyspace da API do Cassandra. |
| [Update-AzCosmosDBCassandraTableThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbcassandratablethroughput) | Atualiza o valor de taxa de transferência da Tabela de API do Cassandra. |
| [Invoke-AzCosmosDBCassandraKeyspaceThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbcassandrakeyspacethroughputmigration) | Migra a taxa de transferência para um Keyspace da API do Cassandra. |
| [Invoke-AzCosmosDBCassandraTableThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbcassandratablethroughputmigration) | Migra a taxa de transferência para uma Tabela da API do Cassandra. |
|**Grupos de recursos do Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).