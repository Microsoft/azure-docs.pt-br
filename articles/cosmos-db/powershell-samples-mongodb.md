---
title: Exemplos do Azure PowerShell para a API do Azure Cosmos DB para MongoDB
description: Obtenha os exemplos do Azure PowerShell para executar tarefas comuns na API do Azure Cosmos DB para MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: 2f852b1ec359033c8bc700c8a382705b4479d6de
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503344"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-api-for-mongodb"></a>Exemplos do Azure PowerShell para a API do Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

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

## <a name="mongo-db-api-samples"></a>Exemplos de API do Mongo DB

|Tarefa | Descrição |
|---|---|
|[Criar uma conta, um banco de dados e uma coleção](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta, um banco de dados e uma coleção do Azure Cosmos. |
|[Criar uma conta, um banco de dados e uma coleção com dimensionamento automático](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta, um banco de dados e uma coleção do Azure Cosmos com dimensionamento automático. |
|[Listar ou obter bancos de dados ou coleções](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha um banco de dados ou uma coleção. |
|[Operações de taxa de transferência](scripts/powershell/mongodb/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operações de taxa de transferência para um banco de dados ou uma coleção, incluindo obter, atualizar e migrar entre o dimensionamento automático e a taxa de transferência padrão. |
|[Bloquear recursos da exclusão](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos. |
|||
