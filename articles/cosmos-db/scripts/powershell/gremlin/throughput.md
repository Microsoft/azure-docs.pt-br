---
title: Scripts do PowerShell para operações de taxa de transferência (RU/s) para a API do Gremlin do Azure Cosmos DB
description: Scripts do PowerShell para operações de taxa de transferência (RU/s) para a API do Gremlin
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 50f3f05bd06101caa096b8126225c7b49ef1f3fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98675481"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Operações de taxa de transferência (RU/s) com o PowerShell para um banco de dados ou grafo para o Azure Cosmos DB – API do Gremlin
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Este exemplo exige o Azure PowerShell Az 5.4.0 ou posterior. Execute `Get-Module -ListAvailable Az` para ver quais versões estão instaladas.
Se é preciso instalar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

Execute [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) para entrar no Azure.

## <a name="get-throughput"></a>Obter taxa de transferência

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-get.ps1 "Get throughput on a database or graph for Gremlin API")]

## <a name="update-throughput"></a>Atualizar a taxa de transferência

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-update.ps1 "Update throughput on a database or graph for Gremlin API")]

## <a name="migrate-throughput"></a>Migrar a taxa de transferência

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a database or graph for Gremlin API")]

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
| [Get-AzCosmosDBGremlinDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbgremlindatabasethroughput) | Obtém o valor de taxa de transferência do Banco de Dados da API do Gremlin. |
| [Get-AzCosmosDBGremlinGraphThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbgremlingraphthroughput) | Obtém o valor de taxa de transferência do Grafo da API do Gremlin. |
| [Update-AzCosmosDBGremlinDatabaseThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbgremlindatabasethroughput) | Atualiza o valor da taxa de transferência do Banco de Dados da API do Gremlin. |
| [Update-AzCosmosDBGremlinGraphThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbgremlingraphthroughput) | Atualiza o valor de taxa de transferência do Grafo da API do Gremlin. |
| [Invoke-AzCosmosDBGremlinDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbgremlindatabasethroughputmigration) | Migra a taxa de transferência de um Banco de Dados da API do Gremlin. |
| [Invoke-AzCosmosDBGremlinGraphThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbgremlingraphthroughputmigration) | Migra a taxa de transferência de um grafo da API do Gremlin. |
|**Grupos de recursos do Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).