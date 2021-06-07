---
title: Exemplos da CLI do Azure para a API de Núcleo (SQL) do Azure Cosmos DB
description: Exemplos da CLI do Azure para a API de Núcleo (SQL) do Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7c7baf4de56edd25537f93095bfa6ae0402214e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93073166"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-core-sql-api"></a>Exemplos da CLI do Azure para a API do Azure Cosmos DB Core (SQL)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A tabela a seguir inclui links para exemplos de scripts de CLI do Azure para o Azure Cosmos DB. Use os links à direita para navegar para exemplos específicos de API. Exemplos comuns são os mesmos em todas as APIs. As páginas de referência de todos os comandos do Azure Cosmos DB CLI estão disponíveis na [Referência de CLI do Azure](/cli/azure/cosmosdb). Exemplos de scripts da CLI do Azure Cosmos DB também podem ser encontrados no [Repositório GitHub da CLI do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Esses exemplos exigem a CLI do Azure versão 2.12.1 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli)

Para obter exemplos da CLI do Azure para outras APIs, confira [Exemplos de CLI para o Cassandra](cli-samples-cassandra.md), [Exemplos de CLI para a API do MongoDB](cli-samples-mongodb.md), [Exemplos de CLI para o Gremlin](cli-samples-gremlin.md), [Exemplos de CLI para a Tabela](cli-samples-table.md)

## <a name="common-samples"></a>Exemplos comuns

Esses exemplos se aplicam a todas as APIs do Azure Cosmos DB

|Tarefa | Descrição |
|---|---|
| [Adicionar ou fazer failover de regiões](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Adicione uma região, altere a prioridade do failover, dispare um failover manual.|
| [Chaves da conta e cadeias de conexão](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Liste chaves da conta, chaves somente leitura, regenerar chaves e listar cadeias de conexão.|
| [Proteger com o firewall do IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta do Cosmos com o firewall do IP configurado.|
| [Proteger a nova conta com pontos de extremidade de serviço](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta do Cosmos e proteja com pontos de extremidade de serviço.|
| [Proteger a conta existente com pontos de extremidade de serviço](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Atualize uma conta do Cosmos para ser protegida com pontos de extremidade de serviço quando a sub-rede estiver finalmente configurada.|
|||

## <a name="core-sql-api-samples"></a>Exemplos de API do Core (SQL)

|Tarefa | Descrição |
|---|---|
| [Crie uma conta, um banco de dados e um contêiner do Azure Cosmos](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta, um banco de dados e um contêiner do Azure Cosmos DB para a API do Core (SQL). |
| [Criar uma conta, um banco de dados e um contêiner do Azure Cosmos com dimensionamento automático](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta, um banco de dados e um contêiner do Azure Cosmos DB com dimensionamento automático para a API do Core (SQL). |
| [Operações de taxa de transferência](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Leia, atualize e migre entre o dimensionamento automático e a taxa de transferência padrão em um banco de dados e um contêiner.|
| [Bloquear recursos da exclusão](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos.|
|||
