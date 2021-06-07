---
title: Exemplos do Azure PowerShell para a API de Núcleo (SQL) do Azure Cosmos DB
description: Obtenha os exemplos do Azure PowerShell para executar tarefas comuns no Azure Cosmos DB para a API de Núcleo (SQL)
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: ab9904127d085113ba09bf6fcd3616842dade14d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503293"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-core-sql-api"></a>Exemplos do Azure PowerShell para a API de Núcleo (SQL) do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A tabela a seguir inclui links para scripts do Azure PowerShell comumente usados no Azure Cosmos DB. Use os links à direita para navegar para exemplos específicos de API. Exemplos comuns são os mesmos em todas as APIs. As páginas de referência de todos os cmdlets do PowerShell do Azure Cosmos DB estão disponíveis na [Referência do Azure PowerShell](/powershell/module/az.cosmosdb). O módulo `Az.CosmosDB` agora faz parte do módulo `Az`. [Baixe e instale](/powershell/azure/install-az-ps) a última versão do módulo Az para obter os cmdlets do Azure Cosmos DB. Obtenha também a última versão na [Galeria do PowerShell](https://www.powershellgallery.com/packages/Az/5.4.0). Também é possível criar fork desses exemplos do PowerShell para o Cosmos DB em nosso repositório GitHub. Para ver como, confira [Amostras do PowerShell para o Cosmos DB no GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Para cmdlets do PowerShell para outras APIs, confira [Exemplos do PowerShell para Cassandra](powershell-samples-cassandra.md), [Exemplos do PowerShell para a API do MongoDB](powershell-samples-mongodb.md), [Exemplos do PowerShell para Gremlin](powershell-samples-gremlin.md), [Exemplos do PowerShell para a Tabela](powershell-samples-table.md)

## <a name="common-samples"></a>Exemplos comuns

|Tarefa | Descrição |
|---|---|
|[Atualizar uma conta](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize o nível de consistência padrão de uma conta do Cosmos DB. |
|[Atualizar as regiões de uma conta](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualizar as regiões de uma conta do Cosmos DB. |
|[Alterar a prioridade de failover ou disparar um failover](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Altere a prioridade de failover regional de uma conta do Azure Cosmos ou dispare um failover manual. |
|[Chaves de conta ou cadeias de conexão](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha chaves primárias e secundárias, cadeias de conexão ou regenere uma chave de conta de uma conta do Azure Cosmos DB. |
|[Criar uma conta do Cosmos com o firewall de IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta do Azure Cosmos DB com o firewall de IP habilitado. |
|||

## <a name="core-sql-api-samples"></a>Exemplos de API do Core (SQL)

|Tarefa | Descrição |
|---|---|
|[Criar uma conta, um banco de dados e um contêiner](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta, um banco de dados e um contêiner do Azure Cosmos DB. |
|[Criar uma conta, um banco de dados e um contêiner com dimensionamento automático](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta, um banco de dados e um contêiner do Azure Cosmos DB com dimensionamento automático. |
|[Criar um contêiner com uma chave de partição grande](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie um contêiner com uma chave de partição grande. |
|[Criar um contêiner sem a política de índice](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um contêiner do Azure Cosmos com uma política de índice desativada.|
|[Listar ou obter bancos de dados ou contêineres](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha um banco de dados ou contêineres. |
|[Operações de taxa de transferência](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operações de taxa de transferência para um banco de dados ou contêiner, incluindo obter, atualizar e migrar entre o dimensionamento automático e a taxa de transferência padrão. |
|[Bloquear recursos da exclusão](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos. |
|||
