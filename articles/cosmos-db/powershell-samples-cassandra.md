---
title: Exemplos do Azure PowerShell para a API do Cassandra do Azure Cosmos DB
description: Obtenha os exemplos do Azure PowerShell para executar tarefas comuns na API do Cassandra do Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: 15e0f221141062b2239b588889036849d3b1d91e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505198"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-cassandra-api"></a>Exemplos do Azure PowerShell para a API do Cassandra do Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

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

## <a name="cassandra-api-samples"></a>Exemplos de API do Cassandra

|Tarefa | Descrição |
|---|---|
|[Criar uma conta, um keyspace e uma tabela](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta, um keyspace e uma tabela do Azure Cosmos. |
|[Criar uma conta, um keyspace e uma tabela com dimensionamento automático](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta, um keyspace e uma tabela do Azure Cosmos com dimensionamento automático. |
|[Listar ou obter keyspaces ou tabelas](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha keyspaces ou tabelas. |
|[Operações de taxa de transferência](scripts/powershell/cassandra/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operações de taxa de transferência para um keyspace ou uma tabela, incluindo obter, atualizar e migrar entre o dimensionamento automático e a taxa de transferência padrão. |
|[Bloquear recursos da exclusão](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos. |
|||
