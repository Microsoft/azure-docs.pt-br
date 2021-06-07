---
title: Script do PowerShell para listar e obter recursos da API do SQL do Azure Cosmos DB
description: Script do Azure PowerShell – Listar e obter operações do Azure Cosmos DB para a API do SQL
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: 849e73e61341576c4387aba8da8a648cf396052a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680845"
---
# <a name="list-and-get-databases-and-containers-for-azure-cosmos-db---sql-core-api"></a>Listar e obter bancos de dados e contêineres do Azure Cosmos DB – API do SQL (Core)
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Este exemplo exige o Azure PowerShell Az 5.4.0 ou posterior. Execute `Get-Module -ListAvailable Az` para ver quais versões estão instaladas.
Se é preciso instalar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

Execute [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) para entrar no Azure.

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-list-get.ps1 "List and get databases and containers for SQL API")]

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
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Lista contas do Cosmos DB ou obtém uma conta do Cosmos DB especificada. |
| [Get-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | Lista Bancos de Dados do Cosmos DB em uma Conta ou obtém um Banco de Dados do Cosmos DB especificado em uma conta. |
| [Get-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Lista Contêineres do Cosmos DB em um Banco de Dados ou obtém um Contêiner do Cosmos DB especificado em um Banco de Dados. |
|**Grupos de recursos do Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).