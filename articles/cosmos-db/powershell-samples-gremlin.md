---
title: Amostras do Azure PowerShell para o Azure Cosmos DB – API do Gremlin
description: Obtenha os exemplos do Azure PowerShell para executar tarefas comuns na API do Gremlin do Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: a0bd1c732a23ca123c391521f5895eb4dcb8c879
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503361"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-gremlin-api"></a>Amostras do Azure PowerShell para o Azure Cosmos DB – API do Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

A tabela a seguir inclui links para scripts do Azure PowerShell comumente usados no Azure Cosmos DB. Use os links à direita para navegar para exemplos específicos de API. Exemplos comuns são os mesmos em todas as APIs. As páginas de referência de todos os cmdlets do PowerShell do Azure Cosmos DB estão disponíveis na [Referência do Azure PowerShell](/powershell/module/az.cosmosdb). O módulo `Az.CosmosDB` agora faz parte do módulo `Az`. [Baixe e instale](/powershell/azure/install-az-ps) a última versão do módulo Az para obter os cmdlets do Azure Cosmos DB. Obtenha também a última versão na [Galeria do PowerShell](https://www.powershellgallery.com/packages/Az/5.4.0). Também é possível criar fork desses exemplos do PowerShell para o Cosmos DB em nosso repositório GitHub. Para ver como, confira [Amostras do PowerShell para o Cosmos DB no GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Exemplos comuns

|Tarefa | Descrição |
|---|---|
|[Atualizar uma conta](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize o nível de consistência padrão de uma conta do Cosmos DB. |
|[Atualizar as regiões de uma conta](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualizar as regiões de uma conta do Cosmos DB. |
|[Alterar a prioridade de failover ou disparar um failover](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Altere a prioridade de failover regional de uma conta do Azure Cosmos ou dispare um failover manual. |
|[Chaves de conta ou cadeias de conexão](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha chaves primárias e secundárias, cadeias de conexão ou regenere uma chave de conta de uma conta do Azure Cosmos DB. |
|[Criar uma conta do Cosmos com o firewall de IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta do Azure Cosmos DB com o firewall de IP habilitado. |
|||

## <a name="gremlin-api-samples"></a>Exemplos de API do Gremlin

|Tarefa | Descrição |
|---|---|
|[Criar uma conta, um banco de dados e um grafo](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta, um banco de dados e um grafo do Azure Cosmos. |
|[Criar uma conta, um banco de dados e um grafo com dimensionamento automático](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta, um banco de dados e um grafo do Azure Cosmos com dimensionamento automático. |
|[Listar ou obter bancos de dados ou grafos](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha um banco de dados ou um grafo. |
|[Operações de taxa de transferência](scripts/powershell/gremlin/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operações de taxa de transferência para um banco de dados ou grafo, incluindo obter, atualizar e migrar entre o dimensionamento automático e a taxa de transferência padrão. |
|[Bloquear recursos da exclusão](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos. |
|||
